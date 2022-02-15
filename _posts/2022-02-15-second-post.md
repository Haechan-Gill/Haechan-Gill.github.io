---
title: "Principal Component Analysis(PCA) tutorial"
date: 2022-02-15 16:10:00 -0400
categories: genomicdata eigenstrat PCA eigensoft
---


이 포스팅에서는 집단 단위의 유전체 데이터의 기본적인 내용을 학습하고 고차원의 유전체 데이터를 Principal component analysis(PCA)를 통해 차원을 축소하여 가시화 하는 방법에 대해 알아보겠습니다. 
  

SNP Genotype Data
-----------------------------
집단 유전학에서는 집단 안의 개체들 사이의 유전적 변이자료를 활용합니다. 유전 변이자료에는 microsatellite, short tandem repeats(STRs), single nucelotide polymorphism(SNP) 등이 있는데요. 차세대 시퀀싱 기술이 발전하기 전에는 전체 유전체를 시퀀싱하기 힘들었기 때문에 microsatellite나 STR 혹은 mitochondria genome과 같이 비교적 짧은 서열을 읽어 개체들 사이의 유전적 차이를 분석하곤 했습니다. 하지만 최근에는 차세대 시퀀싱 기술의 발전으로 전체 유전체를 여러 번 읽는 것이 가능해졌기에 전체 유전체 시퀀싱 결과물로부터 SNP을 찾고 각 개체의 genotype을 결정할 수 있습니다.  
이해를 돕기 위해 예를 들자면, 100명의 사람을 시퀀싱하고 그 결과물을 비교하였을 때 

