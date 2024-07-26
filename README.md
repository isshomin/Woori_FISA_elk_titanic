# ⛴Titanic Data Kibana Viusalize🖼 미니 프로젝트
---
<h2 style="font-size: 25px;"> 개발팀원👨‍👨‍👧‍👦💻<br>
<br>

|<img src="https://avatars.githubusercontent.com/u/175369539?v=4" width="150" height="150"/>|<img src="https://avatars.githubusercontent.com/u/79312705?v=4" width="150" height="150"/>|<img src="https://avatars.githubusercontent.com/u/98442485?v=4" width="150" height="150"/>|<img src="https://avatars.githubusercontent.com/u/175371231?v=4" width="150" height="150"/>|
|:-:|:-:|:-:|:-:|
|[@김성호](https://github.com/castlhoo)|[@김상민](https://github.com/isshomin)|[@이연희](https://github.com/LeeYeonhee-00)|[@오재웅](https://github.com/ohwoong2)|
---
<br>


<details>
<summary> <h2 style="font-size: 30px;">ELK 파이프 라인🗄️과 MySQL🐬 연동</summary>
<br>


## 설치 🖥️

- 설치
    - JDK17, mysql 설치
    
    ```
    sudo apt update 
    sudo apt install jdk17 // jdk17 설치
    
    sudo apt install mysql-server // mysql 설치
    ```
    
    - mysql-connector, elasticsearch, logstash, kibana 설치
    
    ```
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
    

## 설정 ⚙️

- 설정
    - elasticsearch/config/elasticsearch.yml 수정
    
  <p align="left"><img src="https://github.com/user-attachments/assets/436a8f3c-859a-4846-95dd-5cf891fc65ca"></p><br>
    
    - logstash 경로에 .conf파일 생성 후 수정
        
        ```
        touch titanic.conf
        vi titanic.conf
        ```
        ```
        input {
          jdbc {
            jdbc_driver_library => "/home/username/ELK/logstash-7.11.1/tools/mysql-connector-java-8.0.23/mysql-connector-java-8.0.23.jar"
            //mysql-connector 경로값 지정필수
            jdbc_driver_class => "com.mysql.cj.jdbc.Driver"
            jdbc_connection_string => "jdbc:mysql://localhost:3306/fisa"
            jdbc_user => "root"
            jdbc_password => "root"
            schedule => "* * * * *" # 매 분마다 실행
            statement => "SELECT * FROM titanic_raw"
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
        }
        output {
          elasticsearch {
            hosts => ["http://localhost:9200"]
            index => "titanic"
        #    document_id => "%{id}" # primary key로 사용할 필드
          }
        }
        ```
        
<p align="left"><img src="https://github.com/user-attachments/assets/cca96ee4-c9b8-44ac-87f5-5985ef0556ee"></p><br>
        
    - kibana/config/kibana.yml  [server.host](http://server.host): 0.0.0.0 추가
        
  <p align="left"><img src="https://github.com/user-attachments/assets/1a2d39fa-13e7-4721-a75d-ba5bd550dc91"></p><br>

    
    - Mysql 접속 후 root 계정 비밀번호 설정
        
        ```
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

  <br>
    
<p align="left"><img src="https://github.com/user-attachments/assets/1cf46606-115d-41f9-9d31-c84d4a86ebfb"></p><br>

    

## 실행 🔎

- 실행
    - elasticsearch 실행
        
        ```
        ./elasticsearch
        ```
        <p align="left"><img src="https://github.com/user-attachments/assets/61029f95-5a99-4c54-8990-eb6806d1d0c7"></p><br>
        
    - logstash 실행
        
        ```
        ./logstash -f ../logstash.conf
        ```

        <p align="left"><img src="https://github.com/user-attachments/assets/e2ee91a9-1601-4e97-975e-85f7be44014b"></p><br>
    
    - kibana 실행
        
        ```
        ./kibana
        ```
        <p align="left"><img src="https://github.com/user-attachments/assets/00f37546-0511-4560-8ae2-eeef9c5e1044"></p><br>
        

## 확인 ☑️

- Multi Elasticsearch Head 에서 연동이 되었는지 확인😎😎
  
 <p align="left"><img src="https://github.com/user-attachments/assets/a7a59970-b765-4754-b1cc-ba14403d3749"></p><br>

</details>

---
# ⛴Titanic Data Kibana Viusalize🖼

# 미니 프로젝트

## 프로젝트 개요

- 타이타닉을 둘러싼 의혹과 루머를 파헤치는 것
- 데이터를 이용하여 루머를 확인하고, 검증까지 해보기

## 학습 목적

 - Kibana visualize 역량 강화

## 가 설

### **1. “Iceberg, Right Ahead !"**

**: “빙하발견, 우현 앞으로!” 라는 선장의 명령이 타이타닉의 비극을 불러왔다는 루머**

<p align="left"><img src="https://github.com/user-attachments/assets/b2576dc5-6ab9-4e47-a349-f18e2d4e3ca0"></p><br>

(출처 : [https://www.stevenveerapen.com/top-10-titanic-myths-mysteries-and-misconceptions](https://www.stevenveerapen.com/top-10-titanic-myths-mysteries-and-misconceptions))

많은 사람들에 따르면, 타이타닉호가 빙하의 앞머리에 부딫혔기에 더 큰 비극과 선두에 있었던 승객의 사망률이 높았다고 한다. 과연 루머가 사실일지, 거짓일까? Cabin에는 각각의 객실번호가 있는데, 타이타닉호의 설계도에 따르면 선두에서 후미로 갈수록 객실번호가 커진다고 한다. 

<p align="left"><img src="https://github.com/user-attachments/assets/a55af1b0-74b5-4ee1-8e18-a4031389670a"></p><br>

객실 위치에 따른 사망률 분석하여 루머에 대한 진실 혹은 거짓을 파헤쳐 본다.

---

### 2. Women and children first!

**: 여성과 아이들이 먼저! 남성보다 여성과 아이들의 구출을 우선했다는 루머**

<p align="left"><img src="https://github.com/user-attachments/assets/c62a8632-01ba-4d6c-b90b-4a76960d4fe7"></p><br>

(출처 : [https://nypost.com/2022/01/27/8-titanic-myths-busted/](https://nypost.com/2022/01/27/8-titanic-myths-busted/))

당시, 많은 여성들과 아이들이 우선적으로 구조되어야 한다고 했고, 남성들보다 여성들과 아이들이 먼저 구명정에 탑승해서 생존률이 높다고 한다. 

여성과 아이 그리고 남성을 그룹지어서 생존률을 분석하여 루머에 대해 진실 혹은 거짓을 파헤쳐 본다.

---

### 3. Bigger proportion of third-class survived than in second

<p align="left"><img src="https://github.com/user-attachments/assets/14466b21-b46e-49ca-b453-e1c1bd0a8900"></p><br>

(출처 : [https://www.stevenveerapen.com/top-10-titanic-myths-mysteries-and-misconceptions](https://www.stevenveerapen.com/top-10-titanic-myths-mysteries-and-misconceptions)

타이타닉호는 4개의 층으로 나뉘어져서 3등급, 2등급, 1등급, 옥상으로 이루어져있다. 그 중 맨 밑에 있는 3등급의 승객들은 배에서 탈출하기가 힘들었으며, 책상 또는 칸막이에 막혀서 탈출을 성공하지 못한 결과 생존률이 제일 낮다는 루머가 있다. 

2등급과 3등급 각각 승객의 생존률을 분석하여 루머에 대해 진실 혹은 거짓을 파헤쳐 본다. 

---

### 4. 'Be British, boys, be British’

<p align="left"><img src="https://github.com/user-attachments/assets/8afe8e55-5a73-4451-8f41-a1f8e55f99bf"></p><br>

(출처 : [https://www.dw.com/en/10-things-about-the-titanic-you-probably-didnt-know/a-61368562](https://www.dw.com/en/10-things-about-the-titanic-you-probably-didnt-know/a-61368562))

타이타닉호는 많은 국적의 여행객들이 탔는데, 그 중 대다수는 영국인이었다. 타이타닉이 침몰 중일때, “영국인 답게 행동해!”라는 말이 들렸는데, 신사답게 여성과 아이들을 먼저 구조시키라는 뜻이었다. 그래서 많은 사람들을 구조시키고 탈출하지 못한 영국사람들이 많아 영국인 희생자가 많았다고 한다. 

Embarked(승선장소)는 아일랜드, 프랑스, 영국으로 나뉘어져있다. 승선장소를 국적이라 추정하여 분석을 하고자 한다. ‘영국인 답게 행동해라’ 라는 루머에 대해 진실 혹은 거짓을 파헤쳐 본다.
