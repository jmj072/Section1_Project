# 다음 분기 게임 출시에 관한 분석

## 프로젝트 진행 배경
- 게임은 출시 지역에 따라 선호하는 특성이 다를것으로 예상됨
- 또한, 연도에 따른 게임의 트렌드가 있을 것으로 예상됨
- 이에따라, 다음 분기에 게임을 출시하기 위해 각 지역별로 어떤 특성을 선호하는지, 연도별로 게임의 트렌드가 어떠한지 조사하고자 함

 - **분석을 통해 확인하고자 하는 것**
	 1. 출시 지역에 따라 선호하는 게임이 있는지?
		 -  출시 지역에 따른 선호 게임 장르
		 -  출시 지역에 따른 선호 게임 Platform
		 -  출시 지역에 따른 선호 게임 Publisher
	 2. 연도별로 게임 판매량의 트렌드가 있는지?
		 -  게임 장르에 대한 트렌드
		 -  게임 Platform에 대한 트렌드
		 -  게임 Publisher에 대한 트렌드

<br>

## 프로젝트 목표
> ☑ 출시 지역에 따라 선호하는 게임이 있고, 연도별로 게임의 트렌드가 있다고 가정하고  
> 다음 분기 게임 출시를 위하여 시각화를 통해 이를 알아보고자 함
 
<br>

## 파일 구성
- Dataset
	- `vgames2.csv` : 분석에 사용한 Dataset
- Jupiter Notebook
	- `다음 분기 게임 출시에 관한 분석.ipynb` : 프로젝트에 대한 데이터 분석 및 시각화

<br>

## 1. Dataset

### 🕹️ Data Description
-   Name : 게임의 이름입니다.
-   Platform : 게임이 지원되는 플랫폼의 이름입니다.
-   Year : 게임이 출시된 연도입니다.
-   Genre : 게임의 장르입니다.
-   Publisher : 게임을 제작한 회사입니다.
-   NA_Sales : 북미지역에서의 출고량입니다.
-   EU_Sales : 유럽지역에서의 출고량입니다.
-   JP_Sales : 일본지역에서의 출고량입니다.
-   Other_Sales : 기타지역에서의 출고량입니다.

<br>

## 2. 데이터 전처리

### 2.1 출시연도
```
2009.0    1420
2008.0    1413
2010.0    1246
2007.0    1192
2011.0    1123
          ... 
13.0         2
12.0         2
86.0         1
2020.0       1
94.0         1
Name: Year, Length: 61, dtype: int64
```
- 2자리로 이루어진 값들을 확인하여, 4자리로 통일되도록 값을 수정함
-   Year의 2자리 연도를 4자리로 일괄처리
    -   20 이하 : 2000년대를 의미하므로, 앞에  `20`을 추가
    -   80 이상 : 1900년대를 의미하므로, 앞에  `19`를 추가
		
	<details>
	<summary>💻 Year 의 전처리 코드 </summary>
	<div markdown="1">  
	
	```python
	index_correct_year = list(df[(df['Year'] < 100)].index)

	for i in index_correct_year:
			if df['Year'].iloc[i] <= 20 :
					df['Year'].iloc[i] = df['Year'].iloc[i] + 2000

			elif df['Year'].iloc[i] >= 80 :
					df['Year'].iloc[i] = df['Year'].iloc[i] + 1900

	df['Year'] = df['Year'].astype(int)
	```
	
	</div>
	</details>
	
- 대부분의 데이터가 2000년대 이후의 게임들이었기 때문에, 2000년대 이후 데이터만 분석에 사용하였음

	<img src=https://user-images.githubusercontent.com/77204538/177254578-584ddd5f-c67f-4476-8a01-89eba829098a.png height=250>




<br>

### 2.2 게임 플랫폼

- 데이터에 다양한 Platform들이 포함되어 있으나, 크게 휴대용, 콘솔, PC로 나눌수 있다.
- 따라서 휴대용기기, 콘솔, PC로 category를 변경한다

	<details>
	<summary>💻 Platform 의 전처리 코드 </summary>
	<div markdown="1">  
	
	```python
	df["Platform_Groups"] = np.where(df.Platform == "PC", "PC", \
                          np.where((df.Platform == "3DS") | (df.Platform == "DS") | (df.Platform == "GB") | \
                                  (df.Platform == "GBA") | (df.Platform == "GG") | (df.Platform == "PSP") |  \
                                  (df.Platform == "PSV") | (df.Platform == "WS"), "Portable", "Console"))

	```
	
	</div>
	</details>

<br>

-	멀티플랫폼으로 출시한 게임에 대한 특성 생성
	-	대형 AAA급 게임의 경우는, 콘솔, PC에서 모두 플레이 할 수 있는 멀티플랫폼 게임을 출시하는 경우가 많다.
	-	멀티플랫폼으로 출시된 경우도 매출에 영향을 줄 수 있으므로, 특성을 생성해 시각화 한다
		

		<img src=https://user-images.githubusercontent.com/77204538/177254574-c66dc9c6-1503-4c47-9de8-6b5ef271e322.png height=350>

		-	멀티플랫폼 게임 : 콘솔, PC, 휴대용 기기와 같이 다양한 기기로 즐길 수 있는 게임을 의미함
			
			
			
	<details>
	<summary>💻 Multiplatform 의 전처리 코드 </summary>
	<div markdown="1">  
	
	```python
	df["Multiplatform"] = df.duplicated(["Name"], keep=False) # 중복된 모든 행에 True 표시
	df["Multiplatform"] = df["Multiplatform"].map({True: 1, False:0})
	```
	
	</div>
	</details>



<br>

### 2.3 각 지역 게임 판매량

- 데이터에 `K, M`과 같은 문자열이 포함되어 Data 형식이 Object type 으로 변경됨
- 따라서 단위를 `M (Million)`으로 통일하고 숫자형으로 변경 하였음
- 전세계 판매량을 확인하기 위해, `Total_Sales` 특성을 생성하였음

	<details>
	<summary>💻 NA_Sales, EU_Sales, JP_Sales, Other_Sales 의 전처리 코드 </summary>
	<div markdown="1">  
	
	```python
	# 판매량의 K와 M 문자열 처리
	df['NA_Sales'] = df['NA_Sales'].replace({'K': '*0.001', 'M': ""}, regex=True).map(pd.eval)
	df['EU_Sales'] = df['EU_Sales'].replace({'K': '*0.001', 'M': ""}, regex=True).map(pd.eval)
	df['JP_Sales'] = df['JP_Sales'].replace({'K': '*0.001', 'M': ""}, regex=True).map(pd.eval)
	df['Other_Sales'] = pd.to_numeric(df['Other_Sales'].replace({'K': '*0.001', 'M': ""}, regex=True).map(pd.eval))
	```
	
	</div>
	</details>
	

	<details>
	<summary>💻 Total_Sales 특성 생성 </summary>
	<div markdown="1">  
	
	```python
	# 전세계 판매량을 반영하기 위해, 새로운 feature 생성
	df["Total_Sales"] = df.NA_Sales + df.EU_Sales + df.JP_Sales + df.Other_Sales
	```
	</div>
	</details>
	

<br>

## 3. 데이터 시각화
### 3.1 출시 지역에 따른 게임 선호 분석
-  출시 지역에 따른 선호 게임 장르

	<img src=https://user-images.githubusercontent.com/77204538/171592385-e0f1e020-b94d-4cdf-b88e-7be84114c453.png>

	- 북미, 유럽, 그 외 지역 : **액션, 스포츠, 슈터** 장르 선호
	- 일본 : **롤플레잉, 액션, 기타(Misc)** 장르 선호

<br>

- 출시 지역에 따른 선호 게임 플랫폼

	<img src=https://user-images.githubusercontent.com/77204538/171592434-4efa968d-00fa-4002-8366-549fb0d05406.png height=400>

	- 북미, 그 외 지역: 콘솔 게임 선호
	- 유럽 : 콘솔. PC 게임 선호
	- 일본 : 휴대용 기기 게임 선호
 
 <br>
 
 - 출시 지역에 따른 싱글 플랫폼/멀티 플랫폼 선호 

	<img src=https://user-images.githubusercontent.com/77204538/171592496-d2837aae-f91e-4a80-9d3b-658e9a60d709.png height=400>

	- 북미, 유럽, 그 외 지역: 멀티 플랫폼 선호
	- 일본 : 싱글 플랫폼 선호

<br>

- 출시 지역에 따른 선호 게임 개발사
	
	<img src=https://user-images.githubusercontent.com/77204538/171592619-238f342a-99e2-4a56-9cb0-20b56621f248.png height=450>

	- 북미 유럽, 그 외 지역: Nintendo, Microsoft ( 또는 Electronic Arts), Activision
	- 일본 : Nintendo, Capcom, Square Enix


<br>


### 3.2 연도별 게임의 트렌드 분석

- 연도별 게임 판매량 트렌드

	<img src=https://user-images.githubusercontent.com/77204538/171592712-5b809ff4-7a02-4a4d-9cdc-7ca4cf98d427.png height=450>

	- **북미, 유럽 지역**의 게임 시장이 제일 크다

<br>

- 연도별 게임 장르의 트렌드

	<img src=https://user-images.githubusercontent.com/77204538/171592811-0db28062-b75a-464f-b29b-edc8c3dd384f.png height=450>

	- 최근까지 **Action** 장르의 판매량이 많았다

<br>

-	게임 플랫폼의 트렌드

	<img src=https://user-images.githubusercontent.com/77204538/171592928-05c42055-d675-4a08-bec2-1abf740f00bc.png width=950 height=350>

	- **콘솔 게임**이 게임 시장의 주요한 플랫폼, 그 다음으론 휴대용 기기 게임
	- **멀티 플랫폼 게임**의 매출이 더 높다

- 게임 개발사의 트렌드

	<img src=https://user-images.githubusercontent.com/77204538/171593111-1e007fef-abaa-4eed-9f4e-a451cd57929d.png height=450>

	- **Nintendo, Electronic Arts 사**의 게임 판매량이 많았다

<br>

## 4. 결론
다음 분기에 출시할 게임은?
- 액션, 슈터 장르의 게임
- 개발 비용을 고려하여, *콘솔 대상의 싱글 플랫폼으로 출시한 이후 멀티 플랫폼 게임으로 전환* 
- Nintendo 사의 게임은 휴대용 기기 게임이 주류이므로, Electronic Arts사의 액션/슈터 게임인 *Battle Filed, Mass effect, Medal of Honor, Star Wars* 게임을 참고하여 개발하도록 함

<br>

## 5. 한계점 및 개선사항
### 한계점
- 콘솔 게임으로 그룹화 하였을 때, 다른 하위 그룹이 있을 가능성이 있음
	- ex) XBOX와 PS 그룹으로 나누어 질 수 있으며, 게임 매출에 차이가 있거나 양상이 다를 수 있음

 - 멀티플랫폼 게임 중, 하위 호환에 대한 사항은 고려하지 않음 ⇒ 중복 데이터 가능성이 있음
 	- ex) PS4의 경우, PS3에도 구동될 수 있도록 함께 출시하는 경우가 있음.

- 시리즈 게임이나 리마스터/리메이크 게임의 경우, 전작의 게임 판매량에 영향을 받을 가능성이 있음
	- ex) 시리즈 게임 : Call of Duty, Grand theft auto ..
	- ex) 리마스터/리메이크 게임 : The Last of us,Demon`s Souls ..


### 개선사항
- 콘솔 게임 시장이 가장 큰 시장으로 나타났으니, 그 안에서 XBOX와 Playstation 중 어떤 기기가 그 시장을 주도하고 있는지 조사할 필요가 있음
	- XBOX 게임, PS 게임 각각 하위호완 기기도 지원하는지 여부를 나타내는 특성을 생성하여 게임 판매량에 영향을 주는지 확인
- 시리즈 게임이나 리마스터/리메이크의 게임 판매량 영향력 조사를 위해, 게임 이름이 유사한 경우를 확인해 특성을 생성하여 확인할 것
- 2000년대 이후의 모바일 게임에 대한 데이터를 추가로 수집하여 조사한다면, 콘솔/PC/휴대용 기기 게임시장과 어떤 차이가 있는지 확인할 수 있음
	- 국내 게임사의 경우, 모바일 게임 시장이 더 크므로, 모바일 시장과의 비교를 통해 더 좋은 인사이트를 얻을 수 있음 ([관련뉴스기사](https://www.donga.com/news/Economy/article/all/20220309/112238232/1))

		<img src="https://dimg.donga.com/wps/NEWS/IMAGE/2022/03/09/112238227.1.jpg">


<br>

*프로젝트의 자세한 사항은 다음 [PPT 자료](https://drive.google.com/file/d/1mkkusVUg-RSBBw0UINAJIvDa36pufX7U/view?usp=sharing)를 확인해주세요*
