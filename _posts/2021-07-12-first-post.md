---
title: "Genome-wide Association Study(GWAS) tutorial"
date: 2021-07-12 16:10:00 -0400
categories: GWAS PLINK GEMMA DecisionTree
---
  
  
  
이 포스팅에서는 Genome-wide Association Study(GWAS)가 무엇인지 간단하게 공부하고 Plink, Gemma, Decision tree를 활용해 애기장대(*Arabidopsis thaliana*)의 ovule number에 대한 association test tutorial을 진행해보겠습니다.
  
  
Quantitative Trait Loci(QTL)
-----------------------------

우리가 생물에서 관찰할 수 있는 많은 표현형(phenotype)들 중에서 하나의 유전자에 의해 결정되는 표현형이 있는가 하면(ABO 혈액형) 여러 종류의 유전자에 의해 결정되는 표현형(키, 몸무게, 피부색)도 있습니다.  
이를 polygenic inheritance라 하며 하나의 유전자가 여러 phenotype에 영향을 주는 다면 발현(pleiotropy)과 반대의 상황이라고 이해할 수 있습니다.  
Polygenic inheritance로 조절되는 형질들은 여러 유전자의 효과가 더해져 결정되며 따라서 연속적인 값으로 관찰되게 됩니다. 이를 양적 형질, Quantitative trait(QT)라 하며 양적 형질을 조절하는 유전자들의 유전적 위치를 Quantitative trait loci(QTL)이라 합니다.  




![manhattanplot](https://github.com/Haechan-Gill/Haechan-Gill.github.io/blob/main/img/Ovulenumber_Salkex_MH.png?raw=true)



```python
def print_hi(name):
  print("hello", name)
print_hi('Tom')
```
