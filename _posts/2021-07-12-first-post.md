---
title: "Genome-wide Association Study(GWAS) tutorial"
date: 2021-07-12 16:10:00 -0400
categories: GWAS PLINK GEMMA DecisionTree
---
  
  
  
이 포스팅에서는 Genome-wide Association Study(GWAS)가 무엇인지 간단하게 공부하고 Plink, Gemma, Random forest를 활용해 애기장대(*Arabidopsis thaliana*)의 ovule number에 대한 association test tutorial을 진행해보겠습니다.
  
  
Quantitative Trait Loci(QTL)
-----------------------------

우리가 생물에서 관찰할 수 있는 많은 형질들 중에서 ABO 혈액형과 같이 불연속적인 값을 가지는 표현형이 있는가 하면 키, 몸무게, 혈압 등과 같이 연속적인 값으로 측정될 수 있는 형질들도 있습니다.  
이렇게 집단에서 연속적인 분포를 가지고 있는 형질들을 양적 형질, Quantitative trait(QT)라고 합니다.  
무엇이 양적 형질을 결정하는가에 대해 R.A. Fisher는 유전적 요소와 환경적 요소가 양적 형질을 결정한다는 Multiple Factor Hypothesis를 주장합니다.  
Fisher는 양적 형질($T$)을 집단의 평균값($\mu$)에 유전적 요소($g$)와 환경적 요소($e$)의 합으로 표현할 수 있다고 가설을 세웠으며 이를 수식으로 나타내면 아래와 같습니다.  
$$T = \mu + g + e$$  
그리고 Fisher는 양적 형질을 결정하는 유전적 요소는 다수의 유전자에 의해 결정될 것이라 가정하였고 후대의 유전학자들은 하나의 형질이 여러 유전자에 의해 조절되는 현상을 polygenic inheritance라 이름 붙였습니다.  
양적 형질에 관여하는 유전자들의 좌위(locus)를 우리는 Quantitative trait locus(QTL)이라 하며 우리는 특정 양적 형질의 QTL이 몇 개나 있을지, 염색체 상에서 어디에 있을지, 각각의 QTL마다 형질에 기여하는 정도는 어떻게 다른지에 대해 관심을 가지게 됩니다.  
GWAS는 특정 형질에 관여하는 QTL을 찾는 연구를 뜻하며 오늘 배울 Plink, Gemma, Random forest등의 방법을 활용할 것입니다.  
그럼 이제부터는 GWAS를 하기 위한 수학적 배경지식에 대해 공부해보겠습니다.  
  
  
Mathematical Background
-------------------------------
  
  
  
  
  
![manhattanplot](/img/Ovulenumber_Salkex_MH.png)
  
  
  
```python
def print_hi(name):
  print("hello", name)
print_hi('Tom')
```
