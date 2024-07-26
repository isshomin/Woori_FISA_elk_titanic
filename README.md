# ⛴Titanic Data Kibana Viusalize🖼 미니 프로젝트
---


|<img src="https://avatars.githubusercontent.com/u/175369539?v=4" width="150" height="150"/>|<img src="https://avatars.githubusercontent.com/u/79312705?v=4" width="150" height="150"/>|<img src="https://avatars.githubusercontent.com/u/98442485?v=4" width="150" height="150"/>|<img src="https://avatars.githubusercontent.com/u/175371231?v=4" width="150" height="150"/>|
|:-:|:-:|:-:|:-:|
|[@김성호](https://github.com/castlhoo)|[@김상민](https://github.com/isshomin)|[@이연희](https://github.com/LeeYeonhee-00)|[@오재웅](https://github.com/ohwoong2)|
---



<details>
<summary> <h2 style="font-size: 30px;">ELK 파이프 라인🗄️과 MySQL🐬 연동</summary>
<br>
<h2 style="font-size: 25px;"> 개발팀원👨‍👨‍👧‍👦💻<br>


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
        touch logstash.conf
        vi logstash.conf
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
        
<p align="left"><img src="https://github.com/user-attachments/assets/b947109f-f821-4667-9fdd-b5b4fc860051"></p><br>
        
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
- 
 <p align="left"><img src="https://github.com/user-attachments/assets/a7a59970-b765-4754-b1cc-ba14403d3749"></p><br>

</details>
