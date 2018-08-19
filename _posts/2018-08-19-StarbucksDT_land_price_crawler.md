---
layout: post
title: StarbucksDT's land price crwaler using "Selenium"
excerpt: "셀레니움 사용한 전국 스타벅스DT 지점의 공시지가 크롤링"
categories: [Projects_Crwaling]
comments: true
---


# StarbucksDT_land_price crwaling using Selenium

------------

#### [ To-Do list ]

##### 1. open the page
##### 2. read csv file → make dataframe
##### 3. extract all DT stores from dataframe and append it
##### 4. write DT's address on search bar
##### 5. crawling price using selenium
##### 6. extract city name from csv file name
##### 7. save file → set file name using location name


```python
#!pip install selenium
# !pip install bs4
```


```python
from urllib import request
from selenium import webdriver
import pandas as pd
import numpy as np
import bs4
import requests
import re
import csv
from time import sleep
from selenium.common.exceptions import ElementNotVisibleException
```





## 스타벅스 DT 데이터만 따로 뽑아 저장

------

저번 포스팅의 전국 스타벅스 지점에서 DT데이터만 따로 뽑아 저장합니다.


```python
# read csv file name
city_name_list = ['서울', '경기', '광주', '대구', '대전', '부산', '울산', '인천', '강원', '경남',  '경북', '전남', '전북', '충남', '충북',  '제주', '세종']
i = 0;

while i < len(city_name_list):
    path = "C:/Users/Kavin/Projects/Starbucks_Selenium_Crawler/Starbucks_data/" # 불러올 경로 앞부분
    full_path = path + 'starbucks_' + city_name_list[i] +'.csv' # 각 지역별로 path정보 받아오기
    
    path2 = "C:/Users/Kavin/Projects/Starbucks_Selenium_Crawler/Starbucks_DT_data/" # 저장할 경로 앞부분
    full_path2 = path2 + 'starbucks_DT_' + city_name_list[i] +'.csv' # 지역별 이름으로 저장
    
    # 전체 지정이 담겨져 있는 csv파일 읽어오기
    store = pd.read_csv(full_path, engine = 'python', encoding = 'utf-8')
    store = store.drop(columns=['Unnamed: 0']) #  Unnamed컬럼 삭제
    
  
   # name에 DT포함되어 있는 데이터프레임 새로 생성
    DT_list = store[store['name'].str.contains("DT")]
    DT_list = DT_list.reset_index(drop=True)
    
    # DT지점만 따로 csv파일로 생성
    DT_list.to_csv(full_path2, header=True, index=True, encoding='euc-kr')
    
    i = i+1
```









## 홈페이지에서 스타벅스 DT의 땅값 알아보기

-----

공시지가 정보를 [http://www.onnara.go.kr/OnnaraServiceMA/onnaraCommon/mainPage.do](http://www.onnara.go.kr/OnnaraServiceMA/onnaraCommon/mainPage.do)에서 가져옵니다


```python
# 다운받은 chromedriver의 path설정과 크롤링주소 설정
path=r"C:\Users\Kavin\Downloads\chromedriver_win32\chromedriver"
driver=webdriver.Chrome(path)
driver.get("http://www.onnara.go.kr/OnnaraServiceMA/onnaraCommon/mainPage.do")
sleep(10)
```


```python
driver.find_element_by_xpath('/html/body/div[3]/div[7]/div/button').click()
sleep(5)
```


```python
driver.find_element_by_xpath('//*[@id="guide"]/div[1]/a').click()
sleep(5)
```


```python
# price값에서 ','제외한 숫자데이터만 뽑아옵니다
def get_numeric(data):
    match = re.search('>(.+)<', data)
    if match:
        return int(match.group(1).replace(',',''))    
    return None
```











# 전체 지역 공시지가 크롤링 코드

-----




```python
# csv 파일을 읽어오고 데이터프레임에 넣은 후 공시지가 컬럼 새로 추가

city_name_list = ['서울', '경기', '광주', '대구', '대전', '부산', '울산', '인천', '강원', '경남',  '경북', '전남', '전북', '충남', '충북',  '제주', '세종']
price_list = []
i = 0

while i < len(city_name_list):
    path = "C:/Users/Kavin/Projects/Starbucks_Selenium_Crawler/Starbucks_DT_data/" # 불러오고 싶은 경로 앞부분 
    full_path = path + 'starbucks_DT_' + city_name_list[i] +'.csv' #현재 경로 + 파일명

    # 전체 지정이 담겨져 있는 csv파일 읽어오기
    store = pd.read_csv(full_path, engine = 'python', encoding = 'euc-kr')
    store = store.drop(columns=['Unnamed: 0']) #  Unnamed컬럼 삭제
    j=0
    
    while j < len(store):
        
        # 읽어온 csv파일에서 주소명을 키워드에 담기
        address_keyword = store['address'][j]
        
        # 검색창에 주소 입력
        address_search = driver.find_element_by_xpath('//*[@id="totalSerarch"]')
        address_search.clear()
        address_search.send_keys(address_keyword)
        sleep(2)
        
        # 찾기버튼 누르기
        search = driver.find_element_by_xpath('//*[@id="headerMap"]/section[1]/a[2]')
        search.click()
        sleep(5)
        
        # 리스트에서 첫번째 항목 클릭
        click_address = driver.find_element_by_xpath('//*[@id="searchMenu"]/div[1]/ul/li[1]/div/div/div/div[2]/ul/li[2]/div/div[1]/div/ul/li/ul')
        click_address.click()
        sleep(5)
        
        # 공시지가 가져오기
        source = driver.page_source
        bs = bs4.BeautifulSoup(source,'lxml')
        price_list.append(bs.select('#infoJiga'))


        j = j + 1
        
    # 가져온 공시지가 리스트를 데이터프레임 'price'칼럼에 넣기 
    store['price'] = price_list
    
    # price칼럼에서 ',' 제외한 숫자만 가져오기
    store['price'] = store['price'].astype('str')
    store['price'] = store['price'].apply(get_numeric)

    # 저장할 경로 설정 및 파일 저장   
    path2 = "C:/Users/Kavin/Projects/Starbucks_Selenium_Crawler/Starbucks_DT_price/"
    full_path2 = path2 + 'starbucks_DT_price_' + city_name_list[i] +'.csv'
    store.to_csv(full_path2, header=True, index=True, encoding='euc-kr')
    

    
    i = i + 1
```

위 코드를 실행한 결과, 아래와 같이 'WebDriverException'이 발생했습니다.

지도 이동이 불가능하다는 경고창이 발생하게 되었을때 어떻게 해결해야하는지에 대한 코드 추가가 필요해 보입니다.


    WebDriverException: Message: unknown error: Element <a href="#" title="지도검색" class="btn_go">...</a> is not clickable at point (437, 31). Other element would receive the click: <div class="sweet-overlay" tabindex="-1" style="opacity: 1; display: block;"></div>
      (Session info: chrome=68.0.3440.106)
      (Driver info: chromedriver=2.41.578737 (49da6702b16031c40d63e5618de03a32ff6c197e),platform=Windows NT 10.0.17134 x86_64)







## 추가해야할 부분

<u>**1) 지도 이동 불가능하다는 경고창 에러 해결**</u>

<u>**2) 주소 검색시 결과값 나오지 않는 에러 해결**</u>

3) 정확한 지도 데이터 가져오기(정확한 도로명주소로 검색하여 비교 후 색인) → 미완성



현재 1번과 2번은 진행하였고, 3번은 아직 진행하지 못하였습니다.





### 1) 지도 이동 불가능 시 0값 처리 후 크롤링하기


```python
# apply to original code

# csv 파일을 읽어오고 데이터프레임에 넣은 후 공시지가 컬럼 새로 추가

city_name_list = ['서울', '경기', '광주', '대구', '대전', '부산', '울산', '인천', '강원', '경남',  '경북', '전남', '전북', '충남', '충북',  '제주', '세종']
price_list = []
i = 0

while i < len(city_name_list):
    path = "C:/Users/Kavin/Projects/Starbucks_Selenium_Crawler/Starbucks_DT_data/" # 불러오고 싶은 경로 앞부분 
    full_path = path + 'starbucks_DT_' + city_name_list[i] +'.csv' #현재 경로 + 파일명

    # 전체 지정이 담겨져 있는 csv파일 읽어오기
    store = pd.read_csv(full_path, engine = 'python', encoding = 'euc-kr')
    store = store.drop(columns=['Unnamed: 0']) #  Unnamed컬럼 삭제
   
    j=0
    
    while j < len(store):
        
        # 읽어온 csv파일에서 주소명을 키워드에 담기
        address_keyword = store['address'][j]
        

        # 검색창에 주소 입력
        address_search = driver.find_element_by_xpath('//*[@id="totalSerarch"]')
        address_search.clear()
        address_search.send_keys(address_keyword)
        sleep(2)
        
        # 찾기버튼 누르기
        search = driver.find_element_by_xpath('//*[@id="headerMap"]/section[1]/a[2]')
        search.click()
        sleep(5)
        
        # 리스트에서 첫번째 항목 
        click_address = driver.find_element_by_xpath('//*[@id="searchMenu"]/div[1]/ul/li[1]/div/div/div/div[2]/ul/li[2]/div/div[1]/div/ul/li/ul')
        click_address.click()
        sleep(5)
        
        # 만약 지도이동 불가능 에러창이 나오게 된다면 price_list에 공백값을, 아니면 공시지가 값을 넣는다
        try:
            if len(driver.find_elements_by_class_name('sa-confirm-button-container')) > 0:
                driver.find_element_by_xpath('/html/body/div[3]/div[7]/div/button').click()
                price_list.append('')
                sleep(2)
        except ElementNotVisibleException as e:
                source = driver.page_source
                bs = bs4.BeautifulSoup(source,'lxml')
                price_list.append(bs.select('#infoJiga'))
                sleep(2)
        print(price_list[j])    
        j = j + 1
        
    
    # 가져온 공시지가 리스트를 데이터프레임 'price'칼럼에 넣기 
    store['price'] = price_list
    
    # price칼럼에서 ',' 제외한 숫자만 가져오기
    store['price'] = store['price'].astype('str')
    store['price'] = store['price'].apply(get_numeric)

    # 저장할 경로 설정 및 파일 저장   
    path2 = "C:/Users/Kavin/Projects/Starbucks_Selenium_Crawler/Starbucks_DT_price/"
    full_path2 = path2 + 'starbucks_DT_price_' + city_name_list[i] +'.csv'
    store.to_csv(full_path2, header=True, index=True, encoding='euc-kr')
    

    
    i = i + 1
```

위 코드를 돌리다 보니 주소 검색 시에 주소리스트에 아무 값도 나오지 않는 경우가 발생하여 이를 해결하고자 아래와 같이 코드 수정을 추가로 진행하였습니다.







### 2) 주소 검색 시 값이 나오지 않을 때 에러 처리


```python
# apply to original code

# csv 파일을 읽어오고 데이터프레임에 넣은 후 공시지가 컬럼 새로 추가

city_name_list = ['서울', '경기', '광주', '대구', '대전', '부산', '울산', '인천', '강원', '경남',  '경북', '전남', '전북', '충남', '충북',  '제주', '세종']
price_list = []
i = 0

while i < len(city_name_list):
    price_list.clear()

    path = "C:/Users/Kavin/Projects/Starbucks_Selenium_Crawler/Starbucks_DT_data/" # 불러오고 싶은 경로 앞부분 
    full_path = path + 'starbucks_DT_' + city_name_list[i] +'.csv' #현재 경로 + 파일명

    # 전체 지정이 담겨져 있는 csv파일 읽어오기
    store = pd.read_csv(full_path, engine = 'python', encoding = 'euc-kr')
    store = store.drop(columns=['Unnamed: 0']) #  Unnamed컬럼 삭제
   
    j=0
    
    while j < len(store):
        
        # 읽어온 csv파일에서 주소명을 키워드에 담기
        address_keyword = store['address'][j]
        

        # 검색창에 주소 입력
        address_search = driver.find_element_by_xpath('//*[@id="totalSerarch"]')
        address_search.clear()
        address_search.send_keys(address_keyword)
        sleep(2)
        
        # 찾기버튼 누르기
        search = driver.find_element_by_xpath('//*[@id="headerMap"]/section[1]/a[2]')
        search.click()
        sleep(5)
       
    	# 주소 검색시 값이 나오면 주소 목록 중 첫번째 값 선택하여 공시지가 값 가져오기
        if len(driver.find_elements_by_xpath('//*[@id="searchMenu"]/div[1]/ul/li[1]/div/div/div/div[2]/ul/li[2]/div/div[1]/div/ul/li[1]')) > 0:
            click_address = driver.find_element_by_xpath('//*[@id="searchMenu"]/div[1]/ul/li[1]/div/div/div/div[2]/ul/li[2]/div/div[1]/div/ul/li[1]/ul')
            click_address.click()
            sleep(8)
            
            # 만약 지도이동 불가능 에러창이 나오게 된다면 price_list에 공백값을, 아니면 공시지가 값을 넣는다
            try:
                if len(driver.find_elements_by_class_name('sa-confirm-button-container')) > 0:
                    driver.find_element_by_xpath('/html/body/div[3]/div[7]/div/button').click()
                    price_list.append('')
                    sleep(2)
            except ElementNotVisibleException as e:
                    source = driver.page_source
                    bs = bs4.BeautifulSoup(source,'lxml')
                    price_list.append(bs.select('#infoJiga'))
                    sleep(2)
	
    	# 주소 검색시 값이 나오지 않으면 price_list에 공백값 넣기
        else:
            price_list.append('')
            sleep(2)
            
        print(price_list[j])
        j = j + 1
        
    
    # 가져온 공시지가 리스트를 데이터프레임 'price'칼럼에 넣기 
    store['price'] = price_list
    
    # price칼럼에서 ',' 제외한 숫자만 가져오기
    store['price'] = store['price'].astype('str')
    store['price'] = store['price'].apply(get_numeric)

    # 저장할 경로 설정 및 파일 저장   
    path2 = "C:/Users/Kavin/Projects/Starbucks_Selenium_Crawler/Starbucks_DT_price/"
    full_path2 = path2 + 'starbucks_DT_price_' + city_name_list[i] +'.csv'
    store.to_csv(full_path2, header=True, index=True, encoding='euc-kr')
    
    i = i + 1
```

    [<em class="letter" id="infoJiga">1,591,000</em>]
    [<em class="letter" id="infoJiga">1,288,000</em>]
    [<em class="letter" id="infoJiga">436,000</em>]
    [<em class="letter" id="infoJiga">1,712,000</em>]
    [<em class="letter" id="infoJiga">1,478,000</em>]
    [<em class="letter" id="infoJiga">950,000</em>]
    
    [<em class="letter" id="infoJiga">765,300</em>]
    [<em class="letter" id="infoJiga">352,000</em>]
    [<em class="letter" id="infoJiga">655,200</em>]
    [<em class="letter" id="infoJiga">808,000</em>]
    [<em class="letter" id="infoJiga">1,060,000</em>]
    [<em class="letter" id="infoJiga">675,000</em>]
    [<em class="letter" id="infoJiga">1,113,000</em>]

2번코드 실행 시 위와 같이 공시지가 값이 크롤링 되면서 리스트에 잘 담기게 됩니다.

이후 정규표현식을 통해 숫자 데이터만 store['price']에 담아줍니다.

코드 전체 실행 이후 아래와 같이 전국 DT점의 가격이 포함된 csv파일이 저장됩니다.

![Image Alt 텍스트]({{"img/starbucks_selenium(2).png"| relative_url}})