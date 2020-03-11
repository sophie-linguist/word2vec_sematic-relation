* 작업물을 올리는 용도로 사용하고 있는 github
  - 코퍼스는 국립국어원 언어정보나눔터에서 받을 수 있음
  - Word2Vec 모델은 용량 제한이 있어서 올리지 못함. github 초보라...이후 보완 필요

## Word2Vec이 표상하는 의미 관계

* 개인 프로젝트로 수행해 본 내용임.  
* 국립국어원의 세종형태분석말뭉치(문어)를 이용함. 


### 배경 지식: 의미 관계(Sementic Relation) 

Source : Lobner, Sebastian. 2013. Understanding Semantics (2nd edition). Routledge.

*개념 분류의 정확성을 따지기보다는 개념에 대해 조금 더 자세히 생각해본다는 측면에서 참고* 

1. **Synonymy**
    - **Two expressions are synonymous iff they have the same meaning**
    - While total synonymy is rare or trivial, there are many cases of **partial synonymy**. 
    - 완전한 동의어는 찾기 Personal Computer - PC 같은 관계일 수 있지만 매우 드물다.
    - 일상적으로는 어떤 맥락에서 동일한 의미로 쓰이는 표현의 쌍을 말한다. 
    
    
2. **Hyponymy**
    - An expression A is a hyponym of of an expression B -  ans B a hyperonym of A iff
        - the meaning of B is a proper part of the meaning of A and 
        -  A is a logical suordinate of B, i.e. A entails B. 
    - '오리'는 '새'의 하위어이고, '새'는 '오리'의 상위어이다. 어떤 것이 오리이면, 그것은 새이다(함의 관계).
    
    
3. **Oppositions**
    * **Antonymy**
        - Two expressions are antonyms iff they express two opposite extremes out of a range of possibilities.
        - 두 개의 표현이 서로 가능성의 범위를 벗어나는 반대편 극단을 가리킨다면 Antonymy이다. 
        - old/young, old/new, big/small, thick/thin
        - Antonymy는 논리적으로 양립 불가능하지만(imcompatable) 상보적이지는 않다(not complementary)
      
    * **Directional opposites**
        - Two expressions are directional opposites iff they express opposite cases with respect to a common axis
        - 두 개의 표현이 같은 축에서 서로 반대 방향의 경우를 표현한다면 Directional opposites
        - in front of/behind, right of /left of, below/above 
     
    * **Complementary opposition**
        - Two expressions are complementaries iff they express an either-or alternative in some domain     
        - 두 개의 표현이 어떤 도메인에서 이분법적인 대안을 표현하는 경우 complementaries
        - *not even* means the same as *odd*. 
        - 범주형 변수가 되기 좋은 유형(이라고 생각함)
        
    * **Heteronymy**
        - Expressions are heteronyms iff they denote alternatives in some domain of more than two possibilities.
        - 여러 표현이 특정 도메인에서 서로 대안이 되는 경우 heteronyms
        - Sunday, Monday, Tuesday, Wednesday, Friday, Satureday  
        - 범주형 변수가 되기 좋은 유형(이라고 생각함)
        
    * **Converses**
        - Two expressions are converses of each other iff they express the same relation between two entities, but with reversed roles
        - 두 개의 표현이 두 개체간의 같은 관계를 표현하지만, 개체의 역할만 바꾸는 경우 converse
            - x is above y <=> y is below x
            - x is before y <=> y is after x
            - x borrows z from y <=> y lends z to x
 
4. **Lexical Fields**
    * **A lexical field is a group of lexemes that fulfils the following conditions:**
        - the lexemes are of the same word class (동일한 품사)
        - their meanings have something in common (의미에 공통점이 있음)
        - they are interrelated by precisely definable meaning relations (정확하게 정의할 수 있는 의미 관계에 의해 상호 연관)
        - the group is complete in terms of the relevant meaning relations (관련된 의미 관계의 측면에서 완전한 그룹) 
        - {Sunday, Monday, Tuesday, Wednesday, Thursday, Friday, Saterday, Sunday} * 순환적 순서 관계 
        - {above, below, infront of, behind, right of, left of} 
        - {adult, women, man, child, girl, boy} * 성별과 연령으로 구성된 어휘장
        
    * **Taxonomies**
        - A set of expressions is a taxonomy iff:
            - they form a concepture hierarchy in terms of hyponymy
            - hyponyms denote sub-kinds of what their hyperonyms denote. 
            - animal- (horse, cow, pig, dog, cat, elephant, tiger, crocodile...)
            - dog - (huskey, terrier, basset, collie)
            
    * **Mereology, Meronymies**
        - A set of expressions forms a mereology iff they form a hierarchy in terms of holonyms and meronyms, where A is a **meronym** of B, and B is **holonym** of A, iff A denotes constitutive parts of the kind of thing that B denotes.
        - body - (head, neck, trunk, leg, arm...)
    
### 질문
    
   1. Word2Vec은 주로 어떤 의미 관계를 나타내는가? 
       - Word2vec은 단어의 빈도와 순서를 동시에 고려하는 알고리즘이다. 
       - 즉, 어떤 표현의 벡터가 유사하다면 이들은 유사한 도메인의 유사한 맥락에서 대체될 수 있는 표현들일 것이다.  
       - Complementary opposition, Heteronymy와 같은 반의 관계에 속한 표현들이 이 조건에 부합한다.
       - 확인해 보자. 
    
    
   2. 그렇다면 Word2Vec을 통해 얻은 단어의 벡터는 어떻게 사용하는 것이 좋을까?
   

### DATA

21세기 세종계획 세종형태분석말뭉치 (약 1,000어절 규모)

### 작업 순서

1. **전처리**
    1. 어절 단위로 분리된 형태분석 말뭉치를 p 태그 단위(문장? 문단? 단위로) 변환한다.
    2. 곧은 따옴표(",')를 <따옴표>로 치환한다. 
    3. "/"를 "_" 로 치환한다. 
    4. 형용사파생접미사(XSA), 동사파생접미사(XSV)는 바로 앞의 형태와 연결하여 형용사, 동사로 치환한다. (공부+하 => 공부하/VV)
    5. 보조용언(VX)은 바로 앞의 형태와 연결하며 V태그를 준다. (올려다+보 => 올려다_보/V)
    6. NNG(일반명사) 태그가 있는 모든 표현을 'N'으로 치환한다 (Optional, 명사 외 다른 요소에 집중하고 싶다면)

2. **구 탐지(Phrase detection, gensim library)**
    1. 전처리 결과를 모두 합친 후 gensim library의 phrase detection을 2회 실행한다. (자주 쓰이는 최대 4 형태소까지 결합)
    2. 전처리 결과에 형태분석 말뭉치에 phrase detection 결과를 반영한다. 

3. **Word2Vec**
    1. Word2Vec을 실시하여 단어 벡터를 구한다. 
    2. 각 의미 관계를 대표하는 단어와 유사한 단어들을 확인한다. 
    3. t-sne를 이용하여 표현 간의 관계를 확인한다.
