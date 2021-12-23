# khu_capstone

# 연구배경
- 1) 리뷰에 대한 인식 : 소비자는 상품 구매 전 유용한 리뷰를 보고 구매하려는 경향이 크다. 이커머스 업종에서 리뷰 분석은 큰 화두
- 2) 현 문제점 : 대부분의 이커머스 사이트는 유용한 리뷰를 필터링해주는 기능이 없고, 필터링해주는 기능이 있어도 상단에 위치한(유용한 리뷰)리뷰만 계속 유용함을 받는 비대칭 현상이 존재한다. 이에 따라 비대칭 현상으로 최근 리뷰들은 무시되는 문제점이 있다.
- 이에 따라 유용한 리뷰를 분류하는 모델을 만들어, 소비자에게 상품 구매 전 도움을 주고자 한다.
 
3) 그러므로 KoBERT를 활용해 text classification을 수행하여 리뷰유용성을 파악하는 모델을 만들고자 한다. 
4) 또한 소비자 입장에서 비슷한 정보를 담고 있는 리뷰들을 다 보지 않고, 대표적인 정보를 가지고 있는 리뷰들만 보고 싶어하는 경향이 있다. 
5) Bert embedding vector를 clustering하여 군집을 형성한 후 top k개 만큼 추출해 소비자한테 보여주는 모델을 만들고자 한다.

# 분석과정

전체적인 분석 과정
![model_img](https://user-images.githubusercontent.com/57586314/140477467-3afdcacd-bacc-4b04-a60d-a33825b59eb2.png)

데이터 : 올리브영 스킨/토너 카테고리 리뷰 데이터
- 리뷰에 가장 민감한 상품군인 화장품 도메인 선택
- 화장품 중 가장 보편적인 스킨 토너 데이터 선택
- 직접 크롤링 후, 약 2000개 데이터 확보

데이터 전처리 : 
- null값 데이터 제거
- 리뷰에 판매자 및 관리자가 작성한 문구 삭제
- html 태그와 필요없는 공백 삭제

데이터 labeling : 
- 피부타입에 따른 피드백 : ex - 복합성, 지성, 건성, 예민함, 홍조, 블랙헤드, 여드름에 좋다고 해서 샀는데~, 기타 자신의 피부 설명
- 타 제품과의 비교 : A제품에 비해 끈적임이 덜 해요, B제품 쓰다가 돌아왔는데
- 화장품 정보 : 끈적여요, 크리미해요, 물같아요, 향, 제형 (용량으 제외)
- 단기 효과, 느낌 : 보습이 잘 되어요, 촉촉해요, 다음날 화장이 잘 먹어요
- 장기 효과, 경과 설명 : 전후 변화, 장기적 효과(기간이 명시된)

위 기준이 속한 개수 + 주관적 추가 정보
- 기준 0개 -> 1점
- 기준 1개 -> 1~2점
- 기준 2개 -> 2~3점
- 기준 3개 -> 3~4점
- 기준 4개 -> 4~5점
- 기준 5개 -> 5점
5점 척도로 진행 후, binary label로 변경
- 5,4,3 -> 1
- 2,1 -> 0
- 5점 척도로 점수를 매긴 뒤, 다시 2진 분류로 하면 좀 더 신뢰감 있고 정교한 labeling을 할 수 있어 위와 같이 진행함(추후 5점 척도 label로도 실험 진행 예정)
- labeling은 총 5명의 평균값을 반올림하여 진행하였다.

데이터 추가(Text augmentation) :
선정이유)
- 한정된 시간 내에 많은 labeling을 수행할 수 없어 label 개수가 현저히 적었다.
- 데이터가 적을 때, 많은 데이터를 사용한 것과 비슷한 정확도를 만들 수 있는 기법이다.
- image data augmentation만 보편적으로 알려져 있는데, text augmentation의 효과는 어떤지 실험해보고자 했다.
참고논문  : Easy data augmentation techniques for boosting performance(2019 EMNLP)

4가지 데이터 추가 기법
- Synonym Replacement(SR) : 특정 단어를 유의어로 교체
- Random Insertion(RI) : 임의 단어 삽입
- Random Swap(RS) : 문장 내 임의 두 단어 위치 변경
- Random Deletion(RD) : 임의 단어 삭제

모델링 : 

모델1 BERT(multilingual)

SKT T brain KoBERT모델 finetuning

과정
1) KoBERT 모델을 활용해 binary classification 수행
2) logits과 embedding vector를 저장
3) embedding vector값을 clustering
4) 군집 내 logits순으로 내림차순 정렬
5) Top k개 리뷰 추출

# 실험결과
binary text classification<br>
acc 0.82 (max_len=64, batch_size=64, warmup_ratio=0.1, epoch=5, learning rate=5e-5)

# 향후계획
1) bert embedding vector -> sentence bert embedding vector
2) clustering 방법 -> tf-idf를 통해 코사인 유사도를 구해 군집화
