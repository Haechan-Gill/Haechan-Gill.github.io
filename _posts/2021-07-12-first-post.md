---
title: "Genome-wide Association Study(GWAS) tutorial"
date: 2021-07-12 16:10:00 -0400
categories: GWAS PLINK GEMMA
---
  
  
  
이 포스팅에서는 Genome-wide Association Study(GWAS)와 linear mixed model에 대해 간략하게 공부하고 이를 응용한 Genome-wide Efficient Mixed Model Association(GEMMA) 프로그램을 이용해  association test 을 진행해보겠습니다.  
  
  
Quantitative Trait Loci(QTL)
-----------------------------

우리가 생물에서 관찰할 수 있는 많은 형질들 중에서 ABO 혈액형과 같이 불연속적인 값을 가지는 표현형이 있는가 하면 키, 몸무게, 혈압 등과 같이 연속적인 값으로 측정될 수 있는 형질들도 있습니다.  
이렇게 집단에서 연속적인 분포를 가지고 있는 형질들을 양적 형질, Quantitative trait(QT)라고 합니다.  
무엇이 양적 형질을 결정하는가에 대해 R.A. Fisher는 유전적 요소와 환경적 요소가 양적 형질을 결정한다는 Multiple Factor Hypothesis를 주장합니다.  
Fisher는 양적 형질($T$)을 집단의 평균값($\mu$)에 유전적 요소($g$)와 환경적 요소($e$)의 합으로 표현할 수 있다고 가설을 세웠으며 이를 수식으로 나타내면 아래와 같습니다.  
  
$$
T = \mu + g + e
$$
  
그리고 Fisher는 양적 형질을 결정하는 유전적 요소는 다수의 유전자에 의해 결정될 것이라 가정하였고 후대의 유전학자들은 하나의 형질이 여러 유전자에 의해 조절되는 현상을 polygenic inheritance라 이름 붙였습니다.  
양적 형질에 관여하는 유전자들의 좌위(locus)를 우리는 Quantitative trait locus(QTL)이라 하며 우리는 특정 양적 형질의 QTL이 몇 개나 있을지, 염색체 상에서 어디에 있을지, 각각의 QTL마다 형질에 기여하는 정도가 어느정도인지 관심을 가지게 됩니다. GWAS는 특정 형질에 관여하는 QTL을 찾는 연구를 뜻하며 이 포스팅에서는 GEMMA를 활용해 GWAS 실습을 해보겠습니다.  
  
그럼 이제부터는 GWAS를 하기 위한 수학적 배경지식에 대해 공부해보겠습니다.  
  
  
Mathematical Background
-------------------------------
  
GEMMA는 linear mixed model을 기반으로 SNP genotype과 형질 데이터 사이의 association을 테스트합니다. 여기서 linear mixed model이란 일반적인 linear model에 랜덤효과(random effect)를 추가시킨 모형입니다. 기본적인 linear model은 아래와 같은 수식으로 표현됩니다.  
  
$$
y = x\beta + \epsilon, \epsilon \sim N(0,I\sigma^2)
$$
  
여기서 $y$를 반응변수, $x$를 설명변수라 하며 반응변수가 설명변수에 대한 선형함수라는 가정이 들어가 있습니다. 설명번수는 실험에서 가설을 검증하기 위해 변화시키는 조작변인으로, 반응변수는 조작변인에 영향을 받아 값이 변하는 종속변인이라 이해할 수 있겠습니다.  
$x$에 곱해진 $\beta$는 effect size라 하며 간단하게 직선의 기울기라 이해할 수 있습니다. $\beta$의 부호가 양수라면 설명변수가 증가할 때 반응변수 역시 증가할 것이고 음수인 경우에는 둘의 증감이  서로 반대일 것입니다. 그리고 $\beta$의 절대값은 설명변수에 의해 반응변수의 값이 변하는 정도를 의미합니다.  
$\epsilon$은 오차항에 해당하며 평균이 0인 어떤 정규분포를 따른다고 가정합니다.  

linear model에서 목표로 하는 것은 주어진 반응변수와 설명변수의 관계를 가장 잘 설명할 수 있는 $\beta$와 $\sigma^2$의 추정량을 찾는 것입니다. 그 방법 중의 하나로 대표적인 것이 최소제곱 추정법이 있습니다. 최소제곱 추정법은 본 포스팅의 핵심이 아니므로 관심 있으신 분들은 다음을 참고해보시길 바랍니다.  
[최소제곱 추정법](https://en.wikipedia.org/wiki/Least_squares)
  
그렇다면 linear "mixed" model은 linear model과 어떤 차이점이 있을까요? 앞서 설명드렸다시피 linear mixed model은 단순 선형모형에서 랜덤효과가 추가됩니다. linear mixed model을 수식으로 나타내면 아래와 같습니다.  
  
$$
y = x\beta + zu + \epsilon, u \sim N(0,G),  \epsilon \sim N(0,R)
$$
  
기존의 linear model에도 있었던 $x$와 $\beta$는 이제 고정효과(fixed effect)를 설명하는 항으로 이해할 수 있으며 추가된 $z$와 $u$는 랜덤효과(random effect)를 설명하는 항입니다. 일반적으로 고정효과가 우리가 관심을 가지는 부분이며 측정이 가능한 요소들입니다. 그리고 그 효과($\beta$)가 상수로 고정되어 있다고 가정합니다. 이에비해 랜덤효과는 어떤 확률분포로부터 샘플링된 효과로 고정적이지 않다고 가정한 효과 입니다. 따라서 그 효과에 해당하는 $u$가 상수가 아닌 정규분포에서 샘플링된 확률변수라고 가정하게 됩니다.  
예를 들어 약물의 효능을 확인하기 위해 환자들을 모집해 반은 약물을 투여하고 나머지 반은 가짜 약물을 투여했다고 합시다. 이때 저희가 알고자 하는 것은 약물의 효과이며 이는 처리했느냐 처리 하지 않았느냐로 통제할 수 있는 고정적인 변수입니다. 하지만 모집한 환자들은 전체 집단으로부터 랜덤하게 샘플링 되었기 때문에 반응변수에는 약물에 의한 효과 외에도 각각의 환자에 의한 효과도 함께 포함될 것입니다. 우리는 환자 개개인의 효과 보다는 약물의 효과에 관심이 있기 때문에 환자에 의한 효과를 어떤 확률분포에서 샘플링 된것으로 보아 랜덤효과로 처리하는 것입니다. 이때 약물에 의한 효과가 없다면 $\beta$가 0에서 통계적으로 유의미하게 벗어나 있지 않을 것이며 약물에 의한 효과가 있다면 $\beta$가 0에서 벗어나 있을 것입니다.  
  
GEMMA에서 가정한 통계 모델은 기본적으로 linear mixed model입니다. 반응변수 $y$에 해당하는 부분은 양적 형질에 해당하고 고정효과는 SNP genotype에 해당합니다. 랜덤효과는 조사한 샘플의 집단 구조(population structure 혹은 population stratification)에 의한 효과입니다. GEMMA는 SNP genotype에 의한 고정효과가 통계적으로 유의미한지 테스트하며 귀무가설은 $\beta = 0$이며 대립가설은 $\beta \neq 0$ 입니다. 따라서 귀무가설 하에 p-value가 계산되며 그 p-value가 낮을 수록 귀무가설을 기각하는, 다시 말해 형질과 상관관계가 강한 SNP이 됩니다. GEMMA 알고리즘의 통계적 이론과 모델에 대한 더 자세한 내용은 논문과 메뉴얼을 참고하시기 바랍니다.  
  
  
Running GEMMA
--------------------------
### 1. Data 준비  
GEMMA는 기본적으로 PLINK bianry PED file format, BIMBAM file format 총 2종류의 input file format을 사용할 수 있습니다. 이번 실습에서는 PLINK bianry PED file format을 이용해 실습하도록 하겠습니다. 그리고 toy data로는 GEMMA를 설치했을 때 함께 제공되는 example 디렉토리의 mouse data를 사용하겠습니다. 총 두 가지 phenotype에 대한 데이터(percentage of CD8+ cell, mean corpuscular hemoglobin(MCH))가 제공됩니다. GEMMA 설치 및 제공된 toy 데이터에 대한 자세한 내용은 아래를 참고하시기 바랍니다.  
[GEMMA installation](http://www.xzlab.org/software.html)  
[Mouse complex tratis toy data](https://www.nature.com/articles/ng1840)  
GEMMA를 실행하기 위해선 PLINK binary PED file이 필요합니다. PLINK binary PED file은 .bed, .bim, .fam 확장자를 가진 3개의 파일로 .bim은 사용된 SNP에 대한 정보, .fam은 사용된 개체들에 대한 정보, .bed는 각 개체별 SNP genotype data가 binary format으로 저장되어 있습니다. 여기서 각 개체별로 측정한 형질의 데이터는 .fam 파일의 6번째 column에 들어가게 됩니다. 여러 종류의 형질을 조사한 경우에는 각 형질별로 따로 .fam 파일을 만들 필요 없이 6번째 column 이후부터 column by column으로 각 개체별 형질 데이터를 추가하면 됩니다. PLINK file foramt에 대한 자세한 내용은 아래의 링크를 참고하시기 바랍니다.  
[PLINK](https://zzz.bwh.harvard.edu/plink/)  
  
### 2. Relatedness matrix 만들기  
linear mixed model에서 random effect가 따르는 정규분포의 분산을 정해주어야 합니다. GEMMA에서는 샘플들로부터 계산한 relatedness matrix를 사용하며 relatedness matrix에는 계산하는 방법에는 centred genotype을 사용하는 방법과 standardized genotype을 사용하는 두 가지 방법이 있습니다. 이 실습에서는 standardized relatedness matrix를 사용하도록 하겠습니다. example 디렉토리에서 아래와 같은 코드를 실행해봅시다. 
```bash
gemma -bfile mouse_hs1940 -gk 2 -o mouse_hs1940_0726
```
-bfile은 사용할 PLINK binary file의 이름을 지정하는 parameter입니다. 따라서 mouse_hs1940.bed, mouse_hs1940.bim, mouse_hs1940.fam 3개의 파일이 input으로 들어가게 됩니다. -gk는 만들 relatedness matrix의 종류를 결정하는 parameter로 1은 centred, 2는 standardized입니다. -o는 output file의 이름을 지정하는 parameter입니다. relatedness matrix는 output 디렉토리 안에 만들어지며 mouse_hs1940_0726.sXX.txt라는 이름으로 확인할 수 있습니다.  
  
  
### 3. Association test
Association test에는 2번째 단계에서 만들었던 relatedness matrix도 input으로 넣어줘야 합니다. example 디렉토리에서 아래와 같은 코드를 실행해봅시다.  
```bash
gemma -bfile mouse_hs1940 -k ./output/mouse_hs1940_0726.sXX.txt -lmm 4 -n 1 -o mouse_hs1940_0726
```
추가된 parameter를 살펴보자면, -k는 사용할 relatedness matrix를 지정하며 -lmm은 p value를 계산하기 위한 test를 지정하는 parameter인데 4를 주면 가능한 3개 test에 대해 모두 p value를 계산해 output에 써줍니다. 마지막으로 -n은 사용할 형질 데이터를 지정하는 parameter로 .fam 파일의 6번째 column이 1에 해당하며 다른 column의 데이터를 사용하고 싶다면 6번째 column부터 숫자를 매겨주면 됩니다. (예를 들어 .fam 파일 상에서 8번째 column의 형질 데이터를 사용해 테스트하고 싶다면 -n 3을 주면 됩니다.)  
결과물은 마찬가지로 output 폴더에 저장되며 mouse_hs1940_0726.assoc.txt라는 이름으로 저장됩니다.  
  
  
### 4. 결과 확인 및 plotting
.assoc.txt 파일을 열어보면 아래와 같은 결과를 확인할 수 있습니다. 
![assic file example](/img/GEMMA_result.PNG)
각 SNP에 대한 기본적인 정보(위치한 염색체, 이름, 위치, missing number, nucelotide allele, allele frequency)와 추정된 effect size(beta), 각 test에서 계산된 p value 값을 확인할 수 있습니다.(p_wald, p_lrt, p_score) 이 중에서 likelihood ratio test(lrt)로 계산된 p value와 R을 이용해 manhattan plot과 qq plot을 그려보도록 하겠습니다. 
manhattan plot을 그리기 위해 R에서 qqman library가 필요하고 qq plot을 그리기 위해 GWASTools library가 필요합니다. 필요한 library를 다운받으셨다면 다음의 코드를 실행해 보겠습니다.  
```r
setwd('C:\\R\\GEMMA_tutorial')
library(qqman)
library(GWASTools)

df <- read.table("mouse_hs1940_0726.assoc.txt",sep='\t',stringsAsFactors = F, header=T)
df
```
df에 GEMMA로 얻은 assoc.txt 파일이 저장되어 있음을 확인할 수 있습니다. GWASTools에서 manhattan 함수를 사용하면 손쉽게 manhattan plot을 그릴 수 있습니다. 이때 manhattan 함수는 SNP이 위치한 염색체 번호, SNP의 이름, SNP의 위치, p value가 다음과 같은 이름의 column으로 저장되어 있어야 합니다: 'CHR', 'SNP', 'BP', 'P' 따라서 assoc.txt 파일의 column 이름을 바꿔주어야 합니다.  
```r
names(df)[1] <- c("CHR")
names(df)[2] <- c("SNP")
names(df)[3] <- c("BP")
names(df)[14] <- c("P") 
```
이제 manhattan 함수를 사용해 manhattan plot을 그리고 결과물을 확인해봅시다. manhattan plot에서 x축은 각 SNP이 위치한 염색체와 염색체 상의 base position입니다. 그리고 y축은 각 SNP별로 계산된 p value에 -log10을 취한 값이기 때문에 높이 솟아오른 부분이 형질과 association이 강한 SNP이 됩니다.  
```r
manhattan(df, main = "Title", ylim = c(0, 10), cex = 0.6, cex.axis = 0.9, col = c("grey", "skyblue"),
          genomewideline = F,suggestiveline=F)
```
  
![manhattanplot](/img/Mouse_toydata_manhattan_plot.png)
  
위의 manhattan plot에서 17번 염색체에서 굉장히 높은 peak가 관찰됩니다. 17번 염색체를 아래의 코드를 이용해 확대하여 관찰해 보겠습니다. 전체 df 대신 subset 함수를 이용해 CHR이 17인 데이터만 사용하였습니다.  
```r
manhattan(subset(df, CHR == 17), main = "CHR 17", ylim = c(0, 10), cex = 0.6, cex.axis = 0.9, col = c("grey", "skyblue"),
          genomewideline = F,suggestiveline=F)
```

![manhattanplot_chr17](/img/Inkedmanhattan_chr17.jpg)

확대해보니 두 개의 peak가 관찰됩니다. 여기서 3e+07에서 4e+07 사이를 다음의 코드를 이용해 확대하여 관찰해 보겠습니다. xlim을 설정하면 특정 구역을 지정해 plot을 그릴 수 있습니다.
```r
manhattan(subset(df, CHR == 17), main = "CHR 17", ylim = c(0, 10), cex = 0.9, cex.axis = 0.9, col = c("black", "skyblue"),
          ,xlim=c(3e+07,4e+07),genomewideline = F,suggestiveline=F)
```
![manhattanplot_chr17_zoomin](/img/manhattan_chr17_zoomin.png)

manhattan plot 외에 p value의 qq plot도 GWAS 결과물을 잘 정리할 수 있는 방법 중의 하나입니다. qq plot은 아래의 코드를 실행해 그릴 수 있습니다. 
```r
qqPlot(df$P, main = 'QQ plot')
```

![qqplot](/img/qqplot.png)
  
  
qqman library의 manhattan 함수와 GWASTools의 qqPlot 함수의 자세한 설명은 아래의 메뉴얼을 참고하시기 바랍니다.  
[qqman manhattan](https://cran.r-project.org/web/packages/qqman/vignettes/qqman.html)
[GWASTools qqplot](https://rdrr.io/bioc/GWASTools/man/qqPlot.html)
  
  
### 5. 결과 분석
일반적으로 GWAS 굉장히 많은 수의 SNP을 테스트하기 때문에 false positive에 취약합니다. 따라서 Bonferroni correction이나 False discovery rate correction과 같은 multiple hypothesis correcion을 해주어야 합니다. 그리고 correction 거친 후에도 유의미하다고 해서 그 SNP이 항상 형질과 생물학적인 연관성이 있다고 단정지어선 안됩니다. GWAS는 association이 있으리라 예상되는 QTL 후보를 선별하는 과정으로 이해해야 하며 실제로 형질에 영향을 줄 것인지에 대한 validation이 필수적입니다.  
validation은 기본적으로 association이 있는 SNP 근처에 분포하고 있는 유전자를 리스트업하는 것에서 시작합니다. 만약 리스트업한 유전자들의 기능이 이미 잘 밝혀져 있다면 그 기능들을 살펴서 테스트한 형질에 영향을 주는 메커니즘이나 인과관계를 설명하는 것이 일반적입니다. 만약 기능이 잘 밝혀져 있지 않은 유전자이거나 모델 생물을 이용해 GWAS를 했다면 후보 유전자를 knock out 혹은 knock down시켜 테스트한 형질이 영향을 받는지 추가 실험을 통해 validation을 해야 합니다.  
  
  
Summary and Future study
------------------------------------
이번 포스팅에서는 GWAS와 linear mixed model에 대해 알아보았고 GEMMA와 toy data를 이용해 직접 association test를 해보았습니다. 그리고 그 결과물이 의미하는 바를 이해하고 R에서 manhattan plot과 qq plot으로 나타내었습니다.  
이번 실습에서는 하나의 형질에 대한 linear mixed model을 사용 하였는데 이를 Univariate linear mixed model이라고 합니다. 두 종류 이상의 형질 데이터를 한꺼번에 association test할 수도 있습니다. 이를 Multivariate linear mixed model이라고 하며 이는 GEMMA에서 지원하는 기능입니다. 향후에는 Multivariate linear mixed model을 공부해 이를 어떤 생명과학 데이터에 적용할 수 있을지 공부해볼 필요가 있겠습니다.  
그리고 서로 다른 두 종을 이용해 GWAS를 할 수도 있습니다. 예를들어 여러 pathogen과 host 조합에서 병이 심한 정도를 측정한 다음에 association test를 할 수 있습니다. 이러한 연구는 식물과 식물의 병원체 박테리아에서 진행된 바 있고 사람과 사람의 microbiome 사이에서 응용될 수 있습니다. 이를 two-way mixed effects method라 하며 하나의 종에서는 유의미하지 않았던 유전자가 two-way mixed effects method를 통해 검출되기도 합니다. 보다 자세한 내용은 아래의 논문을 참고하시기 바랍니다.  
[two-way mixed effects method](https://www.pnas.org/content/115/24/E5440.short)


  
  
