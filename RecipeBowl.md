 # RecipeBowl

## 사전에 진행됐던 연구
1. recipe representation을 배우는 모델<br>
 2020년에 발표된 Reciptor에서는 RecipeBowl과 마찬가지 Set Transformer를 이용했는데 unsupervised 방식이었고, pre-trained embedding을 이용한 학습(RecipeBowl)이라면 더 성능이 좋을 것임
2. food pairing(식재료 1:1 비교)<br>
 식재료의 맛분자를 분석 -> 식재료 추천<br>
 <br>

## RecipeBowl이 하려는 것
jointly recommends ingredients and recipes(재료와 레시피를 동시에 추천)<br>
### Input과 Output
Input : tag(유저 요구사항 ex. oven, chicken, dinner), 보유 식재료 리스트<br>
Output : likely to go well with the input set(같이 쓰면 좋을 재료), + candidate recipe<br>

### 학습방법
정상적 레시피 데이터(Recipe1M)에서 재료를 하나 뺀 데이터셋을 만들어서 학습<br>
tf-idf가 높은 식재료를 타겟(하나 빼는 식재료)으로 선택<br>
<br>

## 모델 동작 방식
식재료들은 FlavorGraph상의 노드에 임베딩된 vector형태로 존재<br>
- RecipeBowl의 Encoder, Decoder<br>
Encoder : 재료 벡터를 인코딩<br>
Decoder : 재료 추론, 레시피 추론 두 가지를 함<br>
<br>

## /RecipeBowl/data
iid : ingredient id<br>
rid : recipe id<br>
로 추정됨<br>
1. iid2fgvec_full.pkl<br>
EncoderModules의 load_flavorgraph_vectors함수에서 flavorgraph에서 만들어진 벡터 데이터를 로드함 -> embeding vector space로 활용<br>
2. iid2ingr_full.pkl<br>
...? 활용되는 곳이 없음..
3. iid2sim_sorted_top100_full.pkl<br>
유사도 순으로 정렬한 top100 데이터들<br>
4. iids_full.pkl<br>
식재료 id 전체 리스트<br>
5. rid2info_full.pkl<br>
레시피 정보들이 들어있음. rid2info[rid]['vec_reciptor']로 접근하면 reciptor에서 만든 레시피 벡터값이 있고 그것을 활용함<br>
6. rid2sorted_iids_full.pkl<br>
레시피에 있는 식재료 리스트를 tf-idf순으로 정렬한듯 한데, num_targets(default=1; config에서 수정 가능)만큼의 식재료를 제외해주고 나머지를 dataset으로 만들 때 사용됨<br>
7. rid2vec.pkl<br>
600dim 레시피 벡터 데이터<br>
<br>
8. test_ingrs_full.pkl<br>
9. train_ingrs_full.pkl<br>
10. valid_ingrs_full.pkl<br>
위 세 개는 모델의 input으로 넣을 레시피들의 dataset임<br>
11. vocab.bin<br>
flavorgraph의 임베딩 벡터를 사용하지 않는다고 할 때 그냥 워드벡터를 가져오는데 그 워드벡터 데이터가 이것임<br>

<br>

## self Q&A
Q. flavorgraph는 어디에 쓰인다는건가?<br>
A. 식재료를 벡터로 만들 때만 쓰임<br>
<br>
Q. recipebowl을 어떻게 활용할 수 있을까?<br>
A. 디코더의 q,k,v를 어떻게 잘 바꾸면 되지 않을까 하는, 잠깐 생각해서 나온 아이디어가 있다.<br>