# ELK 파이프 라인🗄️과 MySQL🐬 연동
---
### 개발팀원👨‍👨‍👧‍👦💻

|<img src="https://avatars.githubusercontent.com/u/175369539?v=4" width="150" height="150"/>|<img src="https://avatars.githubusercontent.com/u/79312705?v=4" width="150" height="150"/>|<img src="https://avatars.githubusercontent.com/u/98442485?v=4" width="150" height="150"/>|<img src="https://avatars.githubusercontent.com/u/175371231?v=4" width="150" height="150"/>|
|:-:|:-:|:-:|:-:|
|[@김성호](https://github.com/castlhoo)|[@김상민](https://github.com/isshomin)|[@이연희](https://github.com/LeeYeonhee-00)|[@오재웅](https://github.com/ohwoong2)|
---



## 설치 🖥️

- 설치
    - JDK17, mysql 설치
    
    ```bash
    sudo apt update 
    sudo apt install jdk17 // jdk17 설치
    
    sudo apt install mysql-server // mysql 설치
    ```
    
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
    

## 설정 ⚙️

- 설정
    - elasticsearch/config/elasticsearch.yml 수정
    
    ![ela서치수정.png](ELK%20%E1%84%91%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%91%E1%85%B3%20%E1%84%85%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AB%F0%9F%97%84%EF%B8%8F%E1%84%80%E1%85%AA%20MySQL%F0%9F%90%AC%20%E1%84%8B%E1%85%A7%E1%86%AB%E1%84%83%E1%85%A9%E1%86%BC%2011292040d0b647528903b48adf39b061/ela%25EC%2584%259C%25EC%25B9%2598%25EC%2588%2598%25EC%25A0%2595.png)
    
    - logstash 경로에 .conf파일 생성 후 수정
        
        ```bash
        touch logstash.conf
        vi logstash.conf
        
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
        }
        output {
          elasticsearch {
            hosts => ["http://localhost:9200"]
            index => "titanic"
        #    document_id => "%{id}" # primary key로 사용할 필드
          }
        }
        ```
        
        ![conf.png](ELK%20%E1%84%91%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%91%E1%85%B3%20%E1%84%85%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AB%F0%9F%97%84%EF%B8%8F%E1%84%80%E1%85%AA%20MySQL%F0%9F%90%AC%20%E1%84%8B%E1%85%A7%E1%86%AB%E1%84%83%E1%85%A9%E1%86%BC%2011292040d0b647528903b48adf39b061/conf.png)
        
    - kibana/config/kibana.yml  [server.host](http://server.host): 0.0.0.0 추가
        
        ![kibanayml.png](ELK%20%E1%84%91%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%91%E1%85%B3%20%E1%84%85%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AB%F0%9F%97%84%EF%B8%8F%E1%84%80%E1%85%AA%20MySQL%F0%9F%90%AC%20%E1%84%8B%E1%85%A7%E1%86%AB%E1%84%83%E1%85%A9%E1%86%BC%2011292040d0b647528903b48adf39b061/kibanayml.png)
        
    
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
        
    - Dbeaver connection 생성
        
        ![dbeaver설정.png](ELK%20%E1%84%91%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%91%E1%85%B3%20%E1%84%85%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AB%F0%9F%97%84%EF%B8%8F%E1%84%80%E1%85%AA%20MySQL%F0%9F%90%AC%20%E1%84%8B%E1%85%A7%E1%86%AB%E1%84%83%E1%85%A9%E1%86%BC%2011292040d0b647528903b48adf39b061/dbeaver%25EC%2584%25A4%25EC%25A0%2595.png)
        
    

## 실행 🔎

- 실행
    - elasticsearch 실행
        
        ```bash
        ./elasticsearch
        ```
        
        ![ela실행.png](ELK%20%E1%84%91%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%91%E1%85%B3%20%E1%84%85%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AB%F0%9F%97%84%EF%B8%8F%E1%84%80%E1%85%AA%20MySQL%F0%9F%90%AC%20%E1%84%8B%E1%85%A7%E1%86%AB%E1%84%83%E1%85%A9%E1%86%BC%2011292040d0b647528903b48adf39b061/ela%25EC%258B%25A4%25ED%2596%2589.png)
        
    - logstash 실행
        
        ```bash
        ./logstash -f ../logstash.conf
        ```
        
        ![logstash실행.png](ELK%20%E1%84%91%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%91%E1%85%B3%20%E1%84%85%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AB%F0%9F%97%84%EF%B8%8F%E1%84%80%E1%85%AA%20MySQL%F0%9F%90%AC%20%E1%84%8B%E1%85%A7%E1%86%AB%E1%84%83%E1%85%A9%E1%86%BC%2011292040d0b647528903b48adf39b061/logstash%25EC%258B%25A4%25ED%2596%2589.png)
        
    - kibana 실행
        
        ```bash
        ./kibana
        ```
        
        ![kibana실행.png](ELK%20%E1%84%91%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%91%E1%85%B3%20%E1%84%85%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AB%F0%9F%97%84%EF%B8%8F%E1%84%80%E1%85%AA%20MySQL%F0%9F%90%AC%20%E1%84%8B%E1%85%A7%E1%86%AB%E1%84%83%E1%85%A9%E1%86%BC%2011292040d0b647528903b48adf39b061/kibana%25EC%258B%25A4%25ED%2596%2589.png)
        

## 확인 ☑️

- Multi Elasticsearch Head 에서 연동이 되었는지 확인😎😎

![확인.png](ELK%20%E1%84%91%E1%85%A1%E1%84%8B%E1%85%B5%E1%84%91%E1%85%B3%20%E1%84%85%E1%85%A1%E1%84%8B%E1%85%B5%E1%86%AB%F0%9F%97%84%EF%B8%8F%E1%84%80%E1%85%AA%20MySQL%F0%9F%90%AC%20%E1%84%8B%E1%85%A7%E1%86%AB%E1%84%83%E1%85%A9%E1%86%BC%2011292040d0b647528903b48adf39b061/%25ED%2599%2595%25EC%259D%25B8.png)
