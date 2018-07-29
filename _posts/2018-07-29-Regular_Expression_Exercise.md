---
layout: post
title: Regular Expression Exercise
excerpt: "정규표현식 연습문제 풀이"
categories: [Regular Expression]
comments: true
---



# 정규표현식 연습문제 풀이

[연습문제](https://regexone.com/lesson/introduction_abcs)를 풀고 이를 노트북으로 정리

https://regex101.com/ 활용해보기

## #1 All Lessons

### Lesson1.  An Introduction, and the ABCs

| Task | Text |
| :---: | :---: |
| Match | abcdefg |
| Match | abcde |
| Match | abc |

Answer : abc

### Lesson1 1/2. The 123s 

| Task | Text |
| :---: | :---: |
| Match | abc123xyz |
| Match | define "123" |
| Match | 	var g = var g 123; |

Answer : 123

### Lesson2. The Dot

| Task | Text |
| :---: | :---: |
| Match | cat. |
| Match | 	896. |
| Match |  ?=+. |
| Skip | abc1 |

Answer : .+[^abc1]

### Lesson3. Matching specific characters

| Task | Text |
| :---: | :---: |
| Match | can |
| Match | man |
| Match | fan |
| Skip | dan |
| Skip | ran |
| Skip | pan |

Answer : [c|m|f]an

### Lesson4. Excluding specific characters

| Task | Text |
| :---: | :---: |
| Match | hog |
| Match | dog |
| Skip | bog |

Answer : [h|d]og

### Lesson5. Character ranges

| Task | Text |
| :---: | :---: |
| Match | 	Ana |
| Match | Bob |
| Match | Cpc |
| Skip | aax |
| Skip | bby |
| Skip | ccz |

Answer : [A-C].+

### Lesson6. Catching some zzz's

| Task | Text |
| :---: | :---: |
| Match | wazzzzzup |
| Match | wazzzup |
| Skip | wazup |

Answer : ^waz{2,5}up

### Lesson7. Mr. Kleene, Mr. Kleene

| Task | Text |
| :---: | :---: |
| Match | aaaabcc |
| Match | aabbbbc |
| Match | aacc |
| Skip | a |

Answer : a*b*c+

### Lesson8. Characters optional

| Task | Text |
| :---: | :---: |
| Match | 	1 file found? |
| Match | 	2 files found? |
| Match |	24 files found?|  |
| Skip | 	No files found. |

Answer : \d.+ 

### Lesson9. All this whitespace

| Task | Text |
| :---: | :---: |
| Match | 1.   abc |
| Match | 	2.	abc |
| Match |	3.           abc|  |
| Skip | 4.abc |

Answer: [1-3].\s+abc

### Lesson10. Starting and ending

| Task | Text |
| :---: | :---: |
| Match | 	Mission: successful |
| Skip | Last Mission: unsuccessful |
| Skip | Next Mission: successful upon capture of target |

Answer : ^[^Next|Last|].+[^un]successful$

### Lesson11. Match Groups

주의점: 제시된 캡쳐그룹만을 따로 잡아내야함

| Task | Text |
| :---: | :---: |
| Capture | file_record_transcript.pdf |
| Capture | file_07241999.pdf |
| Skip | testfile_fake.pdf.tmp |

Capture Groups
`file_record_transcript`  `file_07241999`

Answer : ^(file\w+).pdf

### Lesson12. Nested groups

| Task | Text |
| :---: | :---: |
| Capture | Jan 1987 |
| Capture | May 1969 |
| Capture | Aug 2011 |

Capture Groups
`Jan 1987`  `1987` `May 1969` `1969` `Aug 2011`  `2011`

Answer : (\w+ (\d+))
* 문장내에서 위의 두가지 캡쳐그룹을 잡아내는 정규식 : \b([B-Z].+|(19\d+))\b

### Lesson13. More group work

| Task | Text |
| :---: | :---: |
| Capture | 	1280x720 |
| Capture | 	1920x1600 |
| Capture | 	1024x768 |

Capture Groups
`1280` `720` `1920` `1600` `1024` `768`

Answer : ((\d+)x(\d+))

### Lesson14. More group work

| Task | Text |
| :---: | :---: |
| Match | I love cats |
| Match | I love dogs |
| Skip | I love logs |
| Skip | I love cogs |

Answer : I love (cats|dogs)

### Lesson15. Other special characters

| Task | Text |
| :---: | :---: |
| Match | The quick brown fox jumps over the lazy dog. |
| Match | There were 614 instances of students getting 90.0% or above. |
| Match | The FCC had to censor the network for saying &$#*@!. |

Answer : .*

## #2 Practice Problems

### Problem 1: Matching a decimal numbers

| Task | Text |
| :---: | :---: |
| Match | 3.14529 |
| Match | 	-255.34 |
| Match | 128 |
| Match | 1.9e10 |
| Match | 	123,340.00 |
| Skip | 720p |

Answer : .+\d$

### Problem 2: Matching phone numbers

| Task | Text | Captrue Groups |
| :---: | :---: |
| Capture | 415-555-1234| `415` |
| Capture | 650-555-2345 | `650`|
| Capture |	(416)555-3456 | `416`|
| Capture | 202 555 4567 | `202`|
| Capture |  4035555678 | `403`|
| Capture | 1 416 555 9292 | `416`|

Answer : 1?[\s]?\(?(\d{3})\)?[\s-]?\d{3}[\s-]?\d{4}

### Problem 3: Matching emails

| Task | Text | Captrue Groups |
| :---: | :---: |
| Capture | tomtom@hogwarts.com | `tom` |
| Capture | tom.riddle@hogwarts.com	 | `tom.riddle`|
| Capture | tom.riddle+regexone@hogwarts.com | `tom.riddle`|
| Capture | tom@hogwarts.eu.com	 | `tom`|
| Capture | potter@hogwarts.com	 | `potter`|
| Capture | harry@hogwarts.com	 | `harry`|
| Capture | hermione+regexone@hogwarts.com	| `hermione`|

Answer : ((\w+)[+.]?\w+)

### Problem 4:  Matching HTML

| Task | Text | Captrue Groups |
| :---: | :---: |
| Capture |  <a\>This is a link</a\> | `a` |
| Capture |  <a\ href='https://regexone.com'>Link</a\>	 | `a`|
| Capture |	<div\ class='test_style'>Test</div\> | `div`|
| Capture | <div\>Hello <span\>world</span\></div\> | `div`|

Answer : <(a|div)>?.+\w?<(/a|/div)>

### Problem 5: Matching specific filenames

| Task | Text | Captrue Groups |
| :---: | :---: |
| Skip |  .bash_profile |  |
| Skip | 	workspace.docwork	 | |
| Capture |	img0912.jpg | `img0912` `jpg` |
| Capture |updated_img0912.png | `updated_img0912` `png` |
| Skip | 		documentation.html	 | |
| Capture | favicon.gif | `favicon` `gif` |
| Skip | img0912.jpg.tmp | |
| Skip | access.lock | |

Answer : (\w+).(jpg|png|gif)$

### Problem 6: Trimming whitespace from start and end of line

| Task | Text | Captrue Groups |
| :---: | :---: |
| Capture | 			The quick brown fox... | `The quick brown fox... ` |
| Capture | 		   jumps over the lazy dog. 	 |`jumps over the lazy dog.` |

Answer : ^\s*(\w.+)$

### Problem 7: Extracting information from a log file

| Task | Text | Captrue Groups |
| :---: | :---: |
| Skip |  	W/dalvikvm( 1553): threadid=1: uncaught exception |  |
| Skip | 	E/( 1553): FATAL EXCEPTION: main  | |
| Skip |	E/( 1553): java.lang.StringIndexOutOfBoundsException |  |
| Capture | 	E/( 1553):   at widget.List.makeView(ListView.java:1727) | `makeView` `ListView.java` `1727` |
| Capture | E/( 1553):   at widget.List.fillDown(ListView.java:652) | `fillDown` `ListView.java` `652` |
| Capture | 	E/( 1553):   at widget.List.fillFrom(ListView.java:709) | `fillFrom` `ListView.java` `709` |

Answer : \w.*List.(makeView|fillDown|fillFrom).+(ListView.java).(\w+).

Solution(캡쳐그룹만 잡기) : (\w+)\(([\w\.]+):(\d+)\)

* \( : '('를 나타냄

### Promblem8: Parsing and extracting data from a URL

| Task | Text | Captrue Groups |
| :---: | :---: |
| Capture |  ftp://file_server.com:21/top_secret/life_changing_plans.pdf | `ftp` `file_server.com` `21` |
| Capture | https://regexone.com/lesson/introduction#section | `https` `regexone.com`|
| Capture | file://localhost:4040/zip_file | `file` `localhost` `4040` |
| Capture | https://s3cur3-server.com:9999/ | `https` `s3cur3-server.com` `9999`|
| Capture |  market://search/angry%20birds | `market` `search`|

Answer : (\w+)://([\w\-\.]+).(\d+)?.*
