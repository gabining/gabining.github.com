---
layout: post
title: Starbucks_Korea webpage crawling using "Selenium"
excerpt: "셀레니움 사용한 javascript기반 웹페이지 크롤링하기"
categories: [Projects_Crwaling]
comments: true
---


# Javascript기반 스타벅스 웹페이지 크롤링

-------



#### selenium과 bs4를 설치해 줍니다


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
from time import sleep
```


```python
print ("BeautifulSoup version:%6.6s" % bs4.__version__)
```

    BeautifulSoup version: 4.6.0







## chromedriver + selenium 을 사용한 크롤링



```python
# 다운받은 chromedriver의 path설정과 크롤링주소 설정
path=r"C:\Users\Kavin\Downloads\chromedriver_win32\chromedriver"
driver=webdriver.Chrome(path)
driver.get("https://www.istarbucks.co.kr/store/store_map.do")
sleep(5)
```





스타벅스 홈페이지에서 지역이름을 리스트로 받아옵니다.

```python
# 스타벅스 홈페이지 내에서 지역 리스트 배열로 받아오기
location_search = driver.find_element_by_class_name('loca_search')
location_search.click()
sleep(5)

city_name_list = [el.text for el in driver.find_elements_by_xpath("//*[@id='container']/div/form/fieldset/div/section/article[1]/article/article[2]/div[1]/div[2]/ul/li")]
city_name_list
```


    ['서울',
     '경기',
     '광주',
     '대구',
     '대전',
     '부산',
     '울산',
     '인천',
     '강원',
     '경남',
     '경북',
     '전남',
     '전북',
     '충남',
     '충북',
     '제주',
     '세종']



### #1 세종시 제외한 16개 시/도 크롤링


```python
# test코드(수정 요망)
i = 0
while i < len(city_name_list)-1:
    # 지역선택 클릭하기
    location_search = driver.find_element_by_class_name('loca_search')
    location_search.click()
    sleep(5)
    
    # 시 선택
    city= driver.find_element_by_class_name('sido_arae_box')
    city_li = city.find_elements_by_tag_name('li')
    city_li[i].click()
    sleep(5)
    
    #구군에서 '전체' 선택하도록 하기
    gugun = driver.find_element_by_class_name('gugun_arae_box')
    gu_li = gugun.find_element_by_tag_name('li')
    gu_li.click()
    sleep(5)

    source = driver.page_source
    bs = bs4.BeautifulSoup(source,'lxml')
    entire = bs.find('ul', class_='quickSearchResultBoxSidoGugun')
    li_list = entire.find_all('li')
    
    name_list = []
    address_list = []

    # DT점명 가져오기
    for name in li_list:
        name_list.append(name.find('strong').text)

    # DT주소 가져오기
    for address in li_list:
        address_list.append(address.find('p').text)
        
        
    # csv파일 저장하기 - path, filename 설정(지역별로 파일명 상이)
    path = "C:/dataitgirls/" #저장하고 싶은 경로 앞부분 
    full_path = path + 'starbucks_' + city_name_list[i] +'.csv' #현재 경로 + 파일명
            
    df = pd.DataFrame({'name':name_list, 'address':address_list})
    
    # 'address' 칼럼에 전화번호 제거
    df['address'] = df['address'].str.replace(r'\d{2,3}-\d{3,4}-\d{4}', '')
    
    # csv파일로 저장
    df.to_csv(full_path, header=True, index=True, encoding='euc-kr')
    
    i = i+1
```

위 코드 실행 결과 아래와 같이 각 시별 csv파일이 자동 생성됩니다

![Image Alt 텍스트]({{"img/starbucks_selenium(1).png"| relative_url}})









### #2 세종시 크롤링

세종시의 경우에는 세종시 선택 후 '전체'에 해당하는 구/군 선택이 없으므로 바로 전체 지점을 크롤링합니다


```python
# 세종시는 구/군 선택이 없으므로 while문 반복 이후 따로 크롤링

# 지역선택 클릭하기
loca = driver.find_element_by_class_name('loca_search')
loca.click()
sleep(5)
    
# 시 선택
loca = driver.find_element_by_class_name('sido_arae_box')
li = loca.find_elements_by_tag_name('li')
li[16].click()
sleep(5)

source = driver.page_source
bs = bs4.BeautifulSoup(source,'lxml')
entire = bs.find('ul', class_='quickSearchResultBoxSidoGugun')
li_list = entire.find_all('li')

name_list = []
address_list = []

# DT점명 가져오기
for name in li_list:
    name_list.append(name.find('strong').text)

# DT주소 가져오기
for address in li_list:
    address_list.append(address.find('p').text)
    
df_Sejong = pd.DataFrame({'name':name_list, 'address':address_list})

# 'address' 칼럼에 전화번호 제거
df_Sejong['address'] = df_Sejong['address'].str.replace(r'\d{2,3}-\d{3,4}-\d{4}', '')

# csv파일로 저장
df_Sejong.to_csv("C:/dataitgirls/starbucks_세종.csv", header=True, index=True, encoding='euc-kr')
```






### #3 크롤링 자동화 시키기

스타벅스 홈페이지에서 **<u>'시'와 '군/구' 부분이 새로 추가/제거 되어도 크롤링이 가능하도록</u>** ```if ~ else```문을 활용해 코드를 수정했습니다.

```python
# while문을 돌면서 각 지역별 스타벅스 지점명과 주소 크롤링 - 세종시 포함
i = 0
while i < len(city_name_list):
    # 지역선택 클릭하기
    location_search = driver.find_element_by_class_name('loca_search')
    location_search.click()
    sleep(5)
    
    # 시 선택
    city= driver.find_element_by_class_name('sido_arae_box')
    city_li = city.find_elements_by_tag_name('li')
    city_li[i].click()
    sleep(5)
    
    # 세종시가 구/군에 해당하는 정보를 가지고 있는지 없는지를 판별
    is_Sejong = [ el.text for el in driver.find_elements_by_xpath("//*[@id='container']/div/form/fieldset/div/section/article[1]/article/article[2]/div[2]")]


    # 세종시일때 (구/군 선택 X)
    if len(is_Sejong[0]) == 0:
        pass
    
    # 세종시가 아닌 시일때 (구/군 선택 O)
    else:
        #구군에서 '전체' 선택하도록 하기
        gugun = driver.find_element_by_class_name('gugun_arae_box')
        gu_li = gugun.find_element_by_tag_name('li')
        gu_li.click()
        sleep(5)
        
    source = driver.page_source
    bs = bs4.BeautifulSoup(source,'lxml')
    entire = bs.find('ul', class_='quickSearchResultBoxSidoGugun')
    li_list = entire.find_all('li')

    name_list = []
    address_list = []

    # DT점명 가져오기
    for name in li_list:
        name_list.append(name.find('strong').text)

    # DT주소 가져오기
    for address in li_list:
        address_list.append(address.find('p').text)


    # csv파일 저장하기 - path, filename 설정(지역별로 파일명 상이)
    path = "C:/dataitgirls/" #저장하고 싶은 경로 앞부분 
    full_path = path + 'starbucks_' + city_name_list[i] +'.csv' #현재 경로 + 파일명

    df = pd.DataFrame({'name':name_list, 'address':address_list})

    # 'address' 칼럼에 전화번호 제거
    df['address'] = df['address'].str.replace(r'\d{2,3}-\d{3,4}-\d{4}', '')

    # csv파일로 저장
    df.to_csv(full_path, header=True, index=True, encoding='euc-kr')
    
    i = i+1
```



크롤링 코드는 [https://brunch.co.kr/@jk-lab/18](https://brunch.co.kr/@jk-lab/18) 의 글을 참고하여 작성하였습니다