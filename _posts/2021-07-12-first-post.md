---
title: "Genome-wide Association Study(GWAS) tutorial"
date: 2021-07-12 16:10:00 -0400
categories: GWAS PLINK GEMMA DecisionTree
---
  
  
  
이 포스팅에서는 Genome-wide Association Study(GWAS)가 무엇인지 간단하게 공부하고 Genome-wide Efficient Mixed Model Association(GEMMA) 프로그램을 이용해 애기장대(*Arabidopsis thaliana*)의 ovule number에 대한 association test tutorial을 진행해보겠습니다.  
  
  
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
양적 형질에 관여하는 유전자들의 좌위(locus)를 우리는 Quantitative trait locus(QTL)이라 하며 우리는 특정 양적 형질의 QTL이 몇 개나 있을지, 염색체 상에서 어디에 있을지, 각각의 QTL마다 형질에 기여하는 정도가 어느정도인지 관심을 가지게 됩니다.  
GWAS는 특정 형질에 관여하는 QTL을 찾는 연구를 뜻하며 이 포스팅에서는 GEMMA를 활용해 GWAS 실습을 해보겠습니다.  
그럼 이제부터는 GWAS를 하기 위한 수학적 배경지식에 대해 공부해보겠습니다.  
  
  
Mathematical Background
-------------------------------
  
GEMMA는 linear mixed model을 기반으로 SNP genotype과 형질 데이터 사이의 association을 테스트합니다. 여기서 linear mixed model이란 일반적인 linear model에 랜덤효과(random effect)를 추가시킨 모형입니다. 기본적인 linear model은 아래와 같은 수식으로 표현됩니다.  
  
$$
y = x\beta + \epsilon, \epsilon ~ N(0,I\sigma^2)
$$
  
여기서 $y$를 반응변수, $x$를 설명변수라 하며 반응변수가 설명변수에 대한 선형함수라는 가정이 들어가 있습니다. 설명번수는 실험에서 가설을 검증하기 위해 변화시키는 조작변인으로, 반응변수는 조작변인에 영향을 받아 값이 변하는 종속변인이라 이해할 수 있겠습니다.  
$\x$에 곱해진 $\beta$는 effect size라 하며 간단하게 직선의 기울기라 이해할 수 있습니다. $\beta$의 부호가 양수라면 설명변수가 증가할 때 반응변수 역시 증가할 것이고 음수인 경우에는 둘의 증감이 서로 반대일 것입니다. 그리고 $\beta$의 절대값은 설명변수에 의해 반응변수의 값이 변하는 정도를 의미합니다.  
$\epsilon$은 오차항에 해당하며 평균이 0인 어떤 정규분포를 따른다고 가정합니다.  
linear model에서 목표로 하는 것은 주어진 반응변수와 설명변수의 관계를 가장 잘 설명할 수 있는 $\beta$와 $\sigma^2$의 추정량을 찾는 것입니다. 그 방법 중의 하나로 대표적인 것이 최소제곱 추정법이 있습니다. 최소제곱 추정법은 본 포스팅의 핵심이 아니므로 관심 있으신 분들은 다음을 참고해보시길 바랍니다.  
  
그렇다면 linear "mixed" model은 linear model과 어떤 차이점이 있을까요? 앞서 설명드렸다시피 linear mixed model은 단순 선형모형에서 랜덤효과가 추가됩니다. linear mixed model을 수식으로 나타내면 아래와 같습니다.  
  
$$
y = x\beta + zu + \epsilon, u ~ N(0 \epsilon ~ N(0,I\sigma^2)
$$
  
  
![manhattanplot](/img/Ovulenumber_Salkex_MH.png)
  
  
  
```python
def print_hi(name):
  print("hello", name)
print_hi('Tom')
```
