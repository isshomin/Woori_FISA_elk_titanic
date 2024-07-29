# [우리FISA 3기 클라우드 엔지니어링] ⛴Titanic Data Kibana Viusalize🖼 미니 프로젝트
---
<h2 style="font-size: 25px;"> 개발팀원👨‍👨‍👧‍👦💻<br>
<br>

|<img src="https://avatars.githubusercontent.com/u/175369539?v=4" width="150" height="150"/>|<img src="https://avatars.githubusercontent.com/u/98442485?v=4" width="150" height="150"/>|<img src="https://avatars.githubusercontent.com/u/79312705?v=4" width="150" height="150"/>|<img src="https://avatars.githubusercontent.com/u/175371231?v=4" width="150" height="150"/>|
|:-:|:-:|:-:|:-:|
|[@김성호](https://github.com/castlhoo)|[@이연희](https://github.com/LeeYeonhee-00)|[@김상민](https://github.com/isshomin)|[@오재웅](https://github.com/ohwoong2)|
---
<br>


<details>
<summary> <h2 style="font-size: 10px;">ELK 파이프 라인🗄️과 MySQL🐬 환경 구축</summary>
<br>


## 설치 🖥️
<br><br>
- JDK17, mysql 설치
  ```bash
  sudo apt update
  sudo apt install jdk17 // jdk17 설치
  sudo apt install mysql-server // mysql 설치
  ```
<br><br>
- mysql-connector, elasticsearch, logstash, kibana 설치
```bash
 wget https://dev.mysql.com/get/Downloads/Connector-J/mysql-connector-java-8.0.23.tar.gz
 tar -xzf mysql-connector-java-8.0.23.tar.gz
 //Mysql-connector 설치
    
 wget https://artifacts.elastic.co/downloads/elasticsearch/elasticsearch-7.11.1-linux-x86_64.tar.gz
 tar -xzf elasticsearch-7.11.1-linux-x86_64.tar.gz
  //elasticsearch 설치
    
 wget https://artifacts.elastic.co/downloads/logstash/logstash-7.11.1-linux-x86_64.tar.gz
 tar -xzf logstash-7.11.1-linux-x86_64.tar.gz
 //logstash 설치
    
 wget https://artifacts.elastic.co/downloads/kibana/kibana-7.11.1-linux-x86_64.tar.gz
  tar -xzf kibana-7.11.1-linux-x86_64.tar.gz
 //kibana 설치
 ```
<br><br>

## 설정 ⚙️

- elasticsearch/config/elasticsearch.yml 수정
    <p align="left"><img src="https://github.com/user-attachments/assets/436a8f3c-859a-4846-95dd-5cf891fc65ca"></p><br><br>
- logstash 경로에 .conf파일 생성 후 수정
    ```bash
    input {
        jdbc {
            jdbc_driver_library => "/home/username/ELK/logstash-7.11.1/tools/mysql-connector-java-8.0.23/mysql-connector-java-8.0.23.jar"
            # mysql-connector 경로값 지정필수
            jdbc_driver_class => "com.mysql.cj.jdbc.Driver"
            jdbc_connection_string => "jdbc:mysql://localhost:3306/fisa"
            jdbc_user => "root"
            jdbc_password => "root"
            schedule => "* * * * *" # 매 분마다 실행
            statement => "SELECT * FROM titanic_raw"
        }
    }
    filter {
        if [cabin] {
            grok {
                match => { "cabin" => "(?<first_cabin>^[^\s]+)" }
            }
            grok {
                match => { "first_cabin" => "^[A-Za-z]*(?<cabin_number>\d+)" }
                remove_field => ["first_cabin"]
            }
            mutate {
                convert => { "cabin_number" => "integer" }
            }
        }
        mutate {
            add_field => {
                "group" => ""
            }
        }
        ruby {
            code => "
                if 
                    event.get('gender') == 'female' or (event.get('age') and event.get('age').to_f <= 18)
                    event.set('group', 'Women and Children')
                elsif event.get('gender') == 'male' and event.get('age') and event.get('age').to_f > 18
                    event.set('group', 'Men')
                else
                    event.cancel
                end
            "
        }
    }
    output {
        elasticsearch {
            hosts => ["http://localhost:9200"]
            index => "titanic"
            document_id => "%{passengerid}" # primary key로 사용할 필드
        }
    }
    ```
    <p align="left"><img src="https://github.com/user-attachments/assets/074bb43e-41f0-4370-aecc-0ef65d42e14d"></p><br>
- kibana/config/kibana.yml  [server.host](http://server.host): 0.0.0.0 추가
    <p align="left"><img src="https://github.com/user-attachments/assets/1a2d39fa-13e7-4721-a75d-ba5bd550dc91"></p><br>
- Mysql 접속 후 root 계정 비밀번호 설정
    ```bash
        sudo mysql -u root -p
        (enter)
        
        show databases;
        
        alter user 'root'@'localhost' identified with mysql_native_password by 'root';
        
        //재실행
        exit
        sudo mysql -u root -p
        root
        ```
- virtualBox port-forwarding 추가
  <p align="left"><img src="https://github.com/user-attachments/assets/1cf46606-115d-41f9-9d31-c84d4a86ebfb"></p><br>

    

## 실행 🔎

- elasticsearch 실행
     ```bash
     ./elasticsearch
     ```
     <p align="left"><img src="https://github.com/user-attachments/assets/61029f95-5a99-4c54-8990-eb6806d1d0c7"></p><br><br>
- logstash 실행
     ```bash
     ./logstash -f ../logstash.conf
     ```
     <p align="left"><img src="https://github.com/user-attachments/assets/e2ee91a9-1601-4e97-975e-85f7be44014b"></p><br>
     <br>
- kibana 실행
    ```bash
    ./kibana
    ```
    <p align="left"><img src="https://github.com/user-attachments/assets/00f37546-0511-4560-8ae2-eeef9c5e1044"></p><br>
    <br><br>

## 확인 ☑️

- Multi Elasticsearch Head 에서 연동이 되었는지 확인😎😎
  
 <p align="left"><img src="https://github.com/user-attachments/assets/a7a59970-b765-4754-b1cc-ba14403d3749"></p><br>

</details>

---

# 프로젝트 개요

- 타이타닉을 둘러싼 의혹과 루머를 파헤치는 것
- 데이터를 이용하여 루머를 확인하고, 검증까지 해보기

<br>

# 학습 목적 

 - MySQL + ELK Pipeline 환경 구축 역량 강화
 - Kibana visualize 역량 강화

<br>

---

# 실습 환경 ⛅

<p align="left"><img src="https://github.com/user-attachments/assets/404b5775-4e5d-483e-8b64-f2c9817fdf0b"></p><br>


---
# 데이터 전처리 과정 🖨

<br>

## 전처리 하기 전 dataset ⏹
|Field      |Type        |Null|Key|Default|Extra|
|-----------|------------|----|---|-------|-----|
|passengerid|int         |NO  |   |       |     |
|survived   |int         |YES |   |       |     |
|pclass     |int         |YES |   |       |     |
|name       |varchar(100)|YES |   |       |     |
|gender     |varchar(50) |YES |   |       |     |
|age        |double      |YES |   |       |     |
|sibsp      |int         |YES |   |       |     |
|parch      |int         |YES |   |       |     |
|ticket     |varchar(80) |YES |   |       |     |
|fare       |double      |YES |   |       |     |
|cabin      |varchar(50) |YES |   |       |     |
|embarked   |varchar(20) |YES |   |       |     |

<br>

### 1️⃣ 가설1을 검증을 위해 cabin field에서 알파벳을 제거 후 int 타입으로 변환 시켜줍니다.
<br>

- grok필터로 정규 표현식을 사용하여 cabin field의 값을 분석합니다. 이 필터는 cabin field에서 첫 번째 공백 전까지의 텍스트를 first_cabin 필드로 추출합니다.
    ```bash
    grok {
      match => { "cabin" => "(?<first_cabin>^[^\s]+)" }
    }
    ```
- 두 번째 grok 필터는 first_cabin field에서 알파벳 문자로 시작하는 부분을 제거하고, 숫자만 추출하여 cabin_number field에 저장합니다.
    remove_field 옵션을 사용하여 first_cabin field를 제거합니다.
    ```bash
    grok {
      match => { "first_cabin" => "^[A-Za-z]*(?<cabin_number>\d+)" }
      remove_field => ["first_cabin"]
    }
    ```
- mutate 필터는 cabin_number field를 int 타입으로 변환합니다.
    ```bash
    mutate {
      convert => { "cabin_number" => "integer" }
    }
    ```
<br><br>

### 2️⃣ 가설2를 검증을 위해 gender field와 age field를 활용하여 여성과 아이들의 data field를 생성합니다.
<br>

- add_field 옵션을 사용하여 이벤트에 새로운 필드 group을 추가합니다.
    ```bash
    mutate {
      add_field => {
        "group" => ""
      }
    }
    ```
- gender가 "female"이거나 age가 18 이하이면, group 필드를 'Women and Children'으로 설정합니다. age 값은 존재할 경우 문자열일 수 있기 때문에 to_f 메서드를 사용해 부동소수점 숫자로 변환한 후 비교합니다.
    ```bash
      if event.get('gender') == 'female' or (event.get('age') and event.get('age').to_f <= 18)
        event.set('group', 'Women and Children')
    ```
- 위의 조건이 만족되지 않으면, gender가 "male"이고 age가 18보다 크면 group 필드를 'Men'으로 설정합니다. 이 조건은 성별이 남성이며, 나이가 18세를 초과하는 경우에 해당합니다.
    ```bash
    elsif event.get('gender') == 'male' and event.get('age') and event.get('age').to_f > 18
    event.set('group', 'Men')
    ```
- 두 조건에 모두 해당하지 않는 경우, event.cancel을 호출하여 현재 이벤트를 취소합니다.
    ```bash
    else
    event.cancel
    ```
<br>

## 전처리 후 dataSet ⏩
|Field      |Type        |Null|Key|Default|Extra|
|-----------|------------|----|---|-------|-----|
|passengerid|int         |NO  |   |       |     |
|survived   |int         |YES |   |       |     |
|pclass     |int         |YES |   |       |     |
|name       |varchar(100)|YES |   |       |     |
|gender     |varchar(50) |YES |   |       |     |
|age        |double      |YES |   |       |     |
|sibsp      |int         |YES |   |       |     |
|parch      |int         |YES |   |       |     |
|ticket     |varchar(80) |YES |   |       |     |
|fare       |double      |YES |   |       |     |
|cabin      |varchar(50) |YES |   |       |     |
|embarked   |varchar(20) |YES |   |       |     |
|<span style="color:red">cabin_number</span>|<span style="color:red">int</span> |<span style="color:red">YES</span> |   |       |     |
|<span style="color:red">group</span>   |<span style="color:red">varchar(255)</span> |<span style="color:red">YES</span> |   |       |     |

<br>

---

<br>

## 가 설 1 🤨🧾

### **1. “Iceberg, Right Ahead !"**

**: “빙하발견, 우현 앞으로!” 라는 선장의 명령이 타이타닉의 비극을 불러왔다는 루머**

<p align="left"><img src="https://github.com/user-attachments/assets/0b69dcb1-7c84-4d10-86e6-aaea66e89366"></p><br>

(출처 : [https://www.stevenveerapen.com/top-10-titanic-myths-mysteries-and-misconceptions](https://www.stevenveerapen.com/top-10-titanic-myths-mysteries-and-misconceptions))

많은 사람들에 따르면, 타이타닉호가 빙하의 앞머리에 부딪혔기에 더 큰 비극과 선두에 있었던 승객의 사망률이 높았다고 한다. 과연 루머가 사실일지, 거짓일까? Cabin에는 각각의 객실번호가 있는데, 타이타닉호의 설계도에 따르면 선두에서 후미로 갈수록 객실번호가 커진다고 한다. 

<p align="left"><img src="https://github.com/user-attachments/assets/468acf75-91ca-4677-8222-d1ed9be290b6"></p><br>

객실 위치에 따른 사망률 분석하여 루머에 대한 진실 혹은 거짓을 파헤쳐 본다.

---

<br>

## 검 증 🧐🧾

<br>
<p align="left"><img src="https://github.com/user-attachments/assets/7d29e342-715d-425e-a712-90f857754052"></p><br>

### 키바나데이터분석~~~

---

## 결 론 📢

### 이 루머는 진실입니다 거짓입니다

---

<br>

## 가 설 2 🤨🧾

### 2. Women and children first!

**: 여성과 아이들이 먼저! 남성보다 여성과 아이들의 구출을 우선했다는 루머**

<p align="left"><img src="https://github.com/user-attachments/assets/739b7ca0-2e1c-402b-90fc-d19ae949f6ec"></p><br>

(출처 : [https://nypost.com/2022/01/27/8-titanic-myths-busted/](https://nypost.com/2022/01/27/8-titanic-myths-busted/))

당시, 많은 여성들과 아이들이 우선적으로 구조되어야 한다고 했고, 남성들보다 여성들과 아이들이 먼저 구명정에 탑승해서 생존률이 높다고 한다. 

여성과 아이 그리고 남성을 그룹지어서 생존률을 분석하여 루머에 대해 진실 혹은 거짓을 파헤쳐 본다.

---

<br>

## 검 증 🧐🧾

<br>
<p align="left"><img src="https://github.com/user-attachments/assets/6df24849-a277-4694-8888-7a990002243e"></p><br>

### 키바나데이터분석~~~

---

## 결 론 📢

### 이 루머는 진실입니다 거짓입니다
---

<br>

## 가 설 3 🤨🧾

### 3. Bigger proportion of third-class survived than in second

<p align="left"><img src="https://github.com/user-attachments/assets/b630d358-8248-4980-a4ea-16ad1ff6333f"></p><br>

(출처 : [https://www.stevenveerapen.com/top-10-titanic-myths-mysteries-and-misconceptions](https://www.stevenveerapen.com/top-10-titanic-myths-mysteries-and-misconceptions)

타이타닉호는 4개의 층으로 나뉘어져서 3등급, 2등급, 1등급, 옥상으로 이루어져있다. 그 중 맨 밑에 있는 3등급의 승객들은 배에서 탈출하기가 힘들었으며, 책상 또는 칸막이에 막혀서 탈출을 성공하지 못한 결과 생존률이 제일 낮다는 루머가 있다. 

2등급과 3등급 각각 승객의 생존률을 분석하여 루머에 대해 진실 혹은 거짓을 파헤쳐 본다. 

---

<br>

## 검 증 🧐🧾

<br>
<p align="left"><img src="https://github.com/user-attachments/assets/d05bb5ff-32eb-436e-b023-b3faa9e87ae7"></p><br>

### 키바나데이터분석~~~

---

## 결 론 📢

### 이 루머는 진실입니다 거짓입니다
---

<br>

## 가 설 4 🤨🧾

### 4. 'Be British, boys, be British’

<p align="left"><img src="https://github.com/user-attachments/assets/207fb1c9-9c81-42ac-ae0a-cbd8465d150f"></p><br>

(출처 : [https://www.dw.com/en/10-things-about-the-titanic-you-probably-didnt-know/a-61368562](https://www.dw.com/en/10-things-about-the-titanic-you-probably-didnt-know/a-61368562))

타이타닉호는 많은 국적의 여행객들이 탔는데, 그 중 대다수는 영국인이었다. 타이타닉이 침몰 중일때, “영국인 답게 행동해!”라는 말이 들렸는데, 신사답게 여성과 아이들을 먼저 구조시키라는 뜻이었다. 그래서 많은 사람들을 구조시키고 탈출하지 못한 영국사람들이 많아 영국인 희생자가 많았다고 한다. 

Embarked(승선장소)는 아일랜드, 프랑스, 영국으로 나뉘어져있다. 승선장소를 국적이라 추정하여 분석을 하고자 한다. ‘영국인 답게 행동해라’ 라는 루머에 대해 진실 혹은 거짓을 파헤쳐 본다.

---

<br>

## 검 증 🧐🧾

<br>
1번표
<p align="left"><img src="https://github.com/user-attachments/assets/38f5a86d-abfc-400d-8724-5d6fe0926c81"></p><br>
2번표
<p align="left"><img src="https://github.com/user-attachments/assets/08089c64-9ebb-4036-a6e4-6af1e59febd1"></p><br>
3번표
<p align="left"><img src="https://github.com/user-attachments/assets/d5e3310c-2ec0-408c-8a16-7f3caea51097"></p><br>

### 키바나데이터분석~~~

---

## 결 론 📢

### 이 루머는 진실입니다 거짓입니다
---

## 회 고 📝

### [김성호](https://github.com/castlhoo)
> 기존 가상머신에 설치를 하려했다가, 문제가 있다면 빨리 새로 설치하는 것도 방법이라는 것을 깨달았다. 필터를 통해 내가 원하는 데이터가 나올 수 있도록 하는 것이 신기하면서도, 코드에 대해 빨리 공부를 해야겠다고 생각했다.
<br>

### [이연희](https://github.com/LeeYeonhee-00)
> 2

<br>

### [김상민](https://github.com/isshomin)
> 3

<br>

### [오재웅](https://github.com/ohwoong2)
> 4

---
