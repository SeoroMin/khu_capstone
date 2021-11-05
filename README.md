# khu_capstone

# 연구배경
1) 이커머스 같은 상품을 판매하는 사이트의 상품 분석 업무에서, 리뷰를 분석하는 것이 화두가 되고 있다. 
2) 소비자 관점에서는 상품을 구매하기 전 유용한 리뷰를 보고 상품을 구매하려는 경향이 크다. 또한 최근 유용함을 필터링 해주는 기능이 생기고 있으나, 비교적 횟수가 적고 최근에 적은 글들은 무시되는 경향이 있다. 
3) 그러므로 KoBERT를 활용해 text classification을 수행하여 리뷰유용성을 파악하는 모델을 만들고자 한다. 
4) 또한 소비자 입장에서 비슷한 정보를 담고 있는 리뷰들을 다 보지 않고, 대표적인 정보를 가지고 있는 리뷰들만 보고 싶어하는 경향이 있다. 
5) Bert embedding vector를 clustering하여 군집을 형성한 후 top k개 만큼 추출해 소비자한테 보여주는 모델을 만들고자 한다.

# 분석과정

전체적인 분석 과정
![model_img](https://user-images.githubusercontent.com/57586314/140477467-3afdcacd-bacc-4b04-a60d-a33825b59eb2.png)

데이터 : 올리브영 스킨/토너 카테고리 리뷰 데이터
데이터 전처리 : 
1) 광고성, 안내성 문구 삭제
2) 불필요한 문구 전처리
3) 1~5점 척도 레이블 : 4,5점 -> 유용, 1,2점 -> 유용x
모델 : SKT T brain KoBERT모델 finetuning

과정
1) KoBERT 모델을 활용해 binary classification 수행
2) logits과 embedding vector를 저장
3) embedding vector값을 clustering
4) 군집 내 logits순으로 내림차순 정렬
5) Top k개 리뷰 추출

# 실험결과
binary text classification
acc 0.82 (max_len=64, batch_size=64, warmup_ratio=0.1, epoch=5, learning rate=5e-5)

# 향후계획
1) bert embedding vector -> sentence bert embedding vector
2) clustering 방법 -> tf-idf를 통해 코사인 유사도를 구해 군집화
