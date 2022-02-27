---
title: "SNP Genotype Matrix data and PLINK tutorial(1)"
date: 2022-02-15 16:10:00 -0400
categories: SNP genotypematrix PLINK
---


이 포스팅에서는 집단 단위의 유전체 데이터의 기본적인 내용을 학습하고 고차원의 유전체 데이터를 행렬의 형태로 어떻게 표현하는가에 대해 알아보겠습니다.
  

SNP Genotype Data
-----------------------------
집단 유전학에서는 집단 안의 개체들 사이의 유전적 변이자료를 활용해 개체들 간의 유전적인 차이나 자연선택이나 진화의 신호를 탐색합니다. 유전 변이자료에는 microsatellite, short tandem repeats(STRs), single nucelotide polymorphism(SNP) 등이 있는데요. 차세대 시퀀싱 기술이 발전하기 전에는 전체 유전체를 시퀀싱하기 힘들었기 때문에 microsatellite나 STR 혹은 mitochondria genome과 같이 비교적 짧은 서열을 읽어 개체들 사이의 유전적 차이를 분석하곤 했습니다. 하지만 최근에는 차세대 시퀀싱 기술의 발전으로 전체 유전체를 여러 번 읽는 것이 가능해졌기에 전체 유전체 시퀀싱 결과물로부터 SNP을 찾고 각 개체의 genotype을 결정할 수 있습니다.   
  
예를 들어 사람의 염색체 1번 100번째 뉴클레오타이드가 SNP이라고 가정해보겠습니다. SNP은 다형성(polymorphism), 즉 개체들 사이에서 이 자리에 서로 다른 뉴클레오타이드를 가지고 있다는 뜻이므로 이 자리에서는 적어도 두 가지 종류 이상의 뉴클레오타이드가 집단에서 관찰되어야 합니다. 두 가지 뉴클레오타이드, 아데닌(A)과 티민(T)이 관찰된다고 가정해봅시다. 그렇다면 이 SNP에서 대립유전자는 'A'와 'T' 두 종류이고 이배체(diploid)인 사람이 이 SNP에서 가질 수 있는 유전자형(genotype)은 'AA','AT','TT', 세 종류가 됩니다. 

따라서 우리는 각 SNP에 대하여 그에 해당하는 어떤 개체의 유전자형을 정리해 놓을 수 있습니다.  
사람1: AA TA GG CC GA TA ...  
사람2: AA TT CC CC GG TT ...  
따라서 우리는 'A','T','G','C', 네 종류의 문자를 이용해 사람들의 유전자형을 행렬의 형태로 데이터화 할 수 있습니다. (여기서는 행은 개체들에 해당하고 열은 SNP에 해당한다고 볼 수 있는데 포맷에 따라 그 반대로 정리하는 경우도 있습니다.) 그런데 SNP을 그 대립유전자의 종류 개수로 구분해보면 과반수가 biallelic(대립 유전자가 2종류)이며 그 이상인 triallelic이거나 tetraallelic은 그 수가 굉장히 적기 때문에 보통 biallelic SNP만을 선별해 사용하게 됩니다. 그렇다면 biallelic SNP의 경우 2개의 대립유전자를 항상 그 빈도가 더 낮은 minor allele과 그 빈도가 상대적으로 높은 major allele로 나눌 수 있습니다. 이를 이용하면 각 SNP별로 major allele과 minor allele을 따로 기록해두고 SNP genotype matrix는 minor allele의 개수 혹은 major allele의 개수로 나타내어 0,1,2에 해당하는 정수형태로 변환할 수 있습니다.  
예를 들어, 어떤 SNP에서 major allele이 'A'이고 minor allele이 'T'일 때, major allele의 개수로 genotype을 변환하면 'AA'는 2, 'AT'는 1, 'TT'는 0에 해당하는 것입니다.  

  
  
  

이번 포스팅에서는 SNP genotype matrix란 무엇인가에 대해 알아보았습니다. 이어지는 포스팅에서는 PLINK 프로그램을 이용하여 genotype matrix 데이터를 핸들링하고 기본적인 요약 통계량 계산 및 집단 유전학 분석을 해보도록 하겠습니다.