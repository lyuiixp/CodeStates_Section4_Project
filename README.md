# 흑색종 피부병변 이미지 데이터를 통한 이미지 분류

## 데이터 선정이유
- 흑색종은 피부암 사망의 압도적 다수를 차지하면서도 조기 발견 시에 생존률이 95%이상
- 해당 이미지를 학습한 모델을 서비스하면 피부암 예방 및 조기발견등에 긍정적일 것이라 생각해 선정하였습니다.

## 1. 데이터 수집

![image](https://user-images.githubusercontent.com/39218451/221792590-a8d8dcbb-5c97-4569-a9de-18259bb955b3.png)

- HAM10000 dataset(kaggle) 
- 흑색종 검출을 위한 7개로 분류된 피부병변 이미지
>- 'akiec': 'Actinic keratoses(광선 각화증)' 0
>- 'bcc': 'Basal cell carcinoma(기저세포암종)' 1
>- 'bkl': 'Benign keratosis-like lesions(양성 병변 각화증)' 2
>- 'df': 'Dermatofibroma(피부섬유종)' 3
>- 'mel': 'Melanoma(흑색종)' 4
>- 'nv': 'Melanocytic nevi(멜라닌세포 모반)' 5
>- 'vasc': 'Vascular lesions(혈관성 병변)' 6
- 10015개의 이미지파일(600x450)

![image](https://user-images.githubusercontent.com/39218451/221784740-c4337eee-f9d5-4189-971d-14237c2d675a.png)

>- lesion_id(7470 unique values) : 병변 부위
>- image_id(10015 unique values) : 이미지(lesion_id 확대)
>- dx : 피부병변
>- dx_type : 병변 확인방법
  >>- histo : 조직병리학
  >>- follow_up : 추적검사
  >>- consensus : 전문가 의견
  >>- confocal : 공초점 현미경 검사
>- age : 환자나이
>- sex : 환자성별
>- localization : 병변부위 위치


## 2. 데이터 전처리/EDA
- EDA
![image](https://user-images.githubusercontent.com/39218451/221790217-a7417c1d-3492-4d68-95a9-60fab4d32e1b.png)

>- nv(멜라닌세포 모반)의 병변이 대다수
>- 병변은 주로 조직검사와 추적검사를 통해 확인됨
>- 등과 하체에 주로 발생
>- 40~50대에서 가장 많이 발병

- 전처리
>- 결측치 제거
>- 병변 별 인덱스 카테고리 ,경로 추가
>- 경로를 통해 이미지를 불러온 후 numpy배열로 변환
>- 타겟 레이블 선택(병변 별 인덱스)
>- 타겟 레이블 원-핫 인코딩
>- train/valid/test data 분리

| DataSet | Size |
| --- | --- |
| Train | 6372 |
| Valid | 1594 |
| Test | 1992 |

## 3. 모델 선택
- CNN, resnet모델 성능비교
- ImageDataGenerator를 사용하여 입력이미지 데이터 증강
- cnn모델에 k-fold crossvalidation 적용
- Accuracy : 0.7294 -> 0.7475로 향상

![image](https://user-images.githubusercontent.com/39218451/221793170-9fabc8d6-5697-4a9a-ae75-0d77dba6b11e.png)


## 4. 결론
- 0.95정도의 정확도를 가진 모델이지만 test데이터에 확인한 결과 0.75정도의 정확도를 보여 어느정도 과적합 된 것으로 보임
- 타겟 데이터 imbalanced한 점이 이유로 보여짐

## 5. 한계 및 개선방안
- Imbalanced한 이미지 데이터 처리방법
>- 다운샘플링 대신 치핑(원본이미지를 작은타일로 분할)
>- 거의 동일한 클래스 병합
>- 특정 클래스 리샘플링(소수 클래스 오버샘플링) : 오버샘플링 클래스의 과적합 초래하는 단점 존재
>- 손실함수 조정(focal loss)(파이썬에 binary, sparse존재)

- 아쉬운점
>- AI hub의 피부질환진단 의료이미지(32종류의 질환 및 정상피부 데이터)를 사용했으면 데이터 양질적 측면에서 좋았을 것
>- 불균형한 이미지데이터 처리하지 못한 점

