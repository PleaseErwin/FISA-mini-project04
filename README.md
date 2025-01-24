# ElasticSearch 설치

```

echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list deb https://artifacts.elastic.co/packages/7.x/apt stable main
```

```
sudo apt install -y elasticsearch=7.11.1
```

윈도우에서 VirtualBox 우분투의 포트포워딩 설정을 해 준다.
irtual Box의 포트 포워딩 설정에 Windows(Host IP)에서 Ubuntu MySQL 포트포워딩
```
sudo vi /etc/elasticsearch/elasticsearch.yml
```
yml 내부에서 ~~~~~을 수정해서 로컬이 아닌 외부에서도 ES를 사용 가능하도록 만든다.

<br>

## 💥 TroubleShooting
포트포워딩을 했음에도 외부에서 연결이 불가능
elasticsearch.yml 파일 수정

<br>

# Logstash 설치

```
sudo apt install -y logstash=1:7.11.1-1
```

<br>

## 💥 TroubleShooting
```
sudo apt-cache madison logstash
```
logstash 패키지의 사용 가능한 버전과 해당 버전의 저장소 정보 목록을 표시한다.

<br>

# Kibana 설치


```
sudo apt install -y kibana=7.11.1
```

## TroubleShooting
