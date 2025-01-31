# 🔨 ELK 설치
> 대용량 실데이터를 시각화하기 위한 ELK 설치
<br>

## ElasticSearch

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
이 상태로는 VirtualBox에서 포트포워딩을 하더라도 외부에서 연결이 불가능하다.
yml 내부에서 ~~~~~을 수정해서 로컬이 아닌 외부에서도 ES를 사용 가능하도록 만든다.


<br>

## Logstash

```
sudo apt install -y logstash=1:7.11.1-1
```


<br>

## Kibana


```
sudo apt install -y kibana=7.11.1
```

## 💥 TroubleShooting
```
sudo apt-cache madison logstash
```
logstash 패키지의 사용 가능한 버전과 해당 버전의 저장소 정보 목록을 표시한다.

<br>

# 🔍 Visualization
> OO카드 실데이터를 이용한 시각화 및 분석
<br>

### 1. 나이별 평균 이용금액

<details>
<summary>QueryDSL 보기</summary> 

```
GET card_data/_search
{
  "size": 0,
  "aggs": {
    "card_data": {
      "terms": {
        "field": "AGE",
        "size": 20
      },
      "aggs": {
        "avg_custs": {
          "avg": {
            "field": "TOT_USE_AM"
          }
        }
      }
    }
  }
}
```
</details>

<br>

![1-2](https://github.com/user-attachments/assets/85c58ab7-f6a1-4ae0-9e59-8a9b9ea35931)

<br><br>


### 2. 연령대별 평균 소비 분야

<details>
<summary>QueryDSL 보기</summary>

```
GET card_data/_search
{
  "size": 0,
  "aggs": {
    "card_data": {
      "terms": {
        "field": "AGE",
        "size": 20
      },
      "aggs": {
        "INTERIOR_AM": {
          "avg": {
            "field": "INTERIOR_AM"
          }
        },
        "INSUHOS_AM": {
          "avg": {
            "field": "INSUHOS_AM"
          }
        },
        "OFFEDU_AM": {
          "avg": {
            "field": "OFFEDU_AM"
          }
        },
        "TRVLEC_AM": {
          "avg": {
            "field": "TRVLEC_AM"
          }
        },
        "FSBZ_AM": {
          "avg": {
            "field": "FSBZ_AM"
          }
        },
        "SVCARC_AM": {
          "avg": {
            "field": "SVCARC_AM"
          }
        },
        "DIST_AM": {
          "avg": {
            "field": "DIST_AM"
          }
        },
        "PLSANIT_AM": {
          "avg": {
            "field": "PLSANIT_AM"
          }
        },
        "CLOTHGDS_AM": {
          "avg": {
            "field": "CLOTHGDS_AM"
          }
        },
        "AUTO_AM": {
          "avg": {
            "field": "AUTO_AM"
          }
        }
      }
    }
  }
}
```
</details>

<br>

![2-2](https://github.com/user-attachments/assets/793f11ff-b8ee-449e-8911-5241b57020e8)

보험/병원은 연령대가 높아질수록 늘어난다.<br>
요식업은 전체 연령대에 고르게 분포해있는 편이다.<br>
의류/신변잡화는 전체 연령대에 매우 고르게 분포해 있다.<br>
사무통신/서적/학원 소비는 30~50 사이에서만 주로 나타난다.<br>
자동차/연료/정비는 55를 중간값이자 최대값으로 갖는다.<br>


<br><br>

### 3. 연령대별 회원등급 수

<details>
<summary>QueryDSL 보기</summary>

```
GET card_data/_search
{
  "size": 0,
  "aggs": {
    "card_data": {
      "terms": {
        "field": "AGE",
        "size": 20
      },
      "aggs": {
        "MBR_RK": {
          "terms": {
            "field": "MBR_RK",
            "size": 5
          }
        }
      }
    }
  }
}
```
</details>

<br>

![3-2](https://github.com/user-attachments/assets/b6d6e6bf-f1a3-4e09-bb62-f648074b5986)

21: VVIP<br>
22: VIP<br>
23: 플래티넘<br>
24: 골드<br>
25: 해당없음<br>

<br><br>


### 4. 라이프 스테이지별 평균 이용금액

<details>
<summary>QueryDSL 보기</summary>

```
GET card_data/_search
{
  "size": 0,
  "aggs": {
    "card_data": {
      "terms": {
        "field": "LIFE_STAGE",
        "size": 10
      },
      "aggs": {
        "avg_total": {
          "avg": {
            "field": "TOT_USE_AM"
          }
        }
      }
    }
  }
}
```

</details>

<br>

![4-2](https://github.com/user-attachments/assets/b6976851-dff7-4304-b687-b4080d3920f8)

UNI : 대학생<br>
NEW_JOB : 사회초년생<br>
NEW_WED : 신혼<br>
CHILD_BABY : 자녀 영유아<br>
CHILD_TEEN : 자녀 의무교육<br>
CHILD_UNI : 자녀 대학생<br>
GOLLIFE : 중년기타 (신혼부부 이후 or 자녀 없음)<br>
SECLIFE : 2nd Life<br>
RETIR : 은퇴<br>


<br><br>

### 5. 라이프 스테이지별 평균 소비 분야

<details>
<summary>QueryDSL 보기</summary>

```

```

</details>

<br>



## 회고


## 아쉬웠던 점
날짜에 따른 카드 신규 가입자의 증감 추이를 살펴보고 증감 폭이 큰 날짜 전후로 어떤 이벤트 또는 사건이 있었는지 확인해보고 신규 가입자를 늘릴 방법을 찾아보려고 했다.
하지만 고객의 카드 입회년월 데이터가 명세서에는 YYYYMM으로 나와있었던 반면 실제 데이터 형식은 ~~~ 로 되어 있었다.
해당 데이터가 무엇을 나타내는지 알아보기 어려워 해당 분석 방법은 실행하지 못한 것이 아쉽다.

<br><br>

  255  sudo apt upgrade
  256  wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo gpg --dearmor -o /usr/share/keyrings/elastic-archive-keyring.gpg
  257  echo "deb https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
  258  sudo apt install -y elasticsearch
  259  sudo apt update
  260  sudo apt install -y elasticsearch
  261  sudo apt update
  262  wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo tee /usr/share/keyrings/elasticsearch-keyring.asc
  263  echo "deb [signed-by=/usr/share/keyrings/elasticsearch-keyring.asc] https://artifacts.elastic.co/packages/8.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-8.x.list
  264  sudo apt install -y elasticsearch
  265  sudo apt update
  266  sudo apt install -y elasticsearch
  267  sudo systemctl status elasticsearch.service
  268  sudo systemctl status elasticsearch
  269  sudo systemctl start elasticsearch
  270  sudo systemctl start elasticsearch.service
  271  sudo systemctl status elasticsearch
  272  sudo systemctl enable elasticsearch.service
  273  curl -X GET "localhost:9200/"
  274  curl -X GET "http://localhost:9200"
  275  curl -X GET "http://localhost:9200/"
  276  sudo netstat
  277  sudo netstat -tuln
  278  sudo netstat -tuln | grep 9200
  279  sudo cat /etc/elasticsearch/elasticsearch.yml
  280  sudo systemctl status elasticsearch
  281  exit
  282  which java
  283  which javac
  284  which elasticsearch
  285  which elasticsearch.service
  286  history
  287  which java
  288  clear
  289  ls
  290  ls /
  291  ls /etc/
  292  ls -l
  293  ls -l
  294  ls /etc/elasticsearch/
  295  suod ls /etc/elasticsearch/
  296  sudo ls /etc/elasticsearch/
  297  sudo cat /etc/elasticsearch/elasticsearch.yml
  298  systemctl status elasticsearchsu
  299  sudo journalctl -u elasticsearch
  300  sudo vi /etc/elasticsearch/elasticsearch.yml
  301  sudo systemctl restart elasticsearch
  302  sudo systemctl restart elasticsearch.service
  303  sudo systemctl status elasticsearch
  304  curl http://127.0.0.1:9200
  305  wget
  306  history
  307  curl http://127.0.0.1:80
  308  sudo systemctl stop elasticsearch
  309  sudo systemctl status elasticsearch
  310  sudo systemctl status kibana
  311  sudo systemctl status logstash
  312  sudo dpkg --purge elasticsearch
  313  sudo rm -rf /etc/elasticsearch /var/lib/elasticsearch /var/log/elasticsearch
  314  ls /var/lib/elasyicsearch
  315  wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo tee /usr/share/keyrings/elasticsearch-keyring.asc
  316  echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list
  317  sudo apt update
  318  wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
  319  sudo apt update
  320  sudo ls /etc/apt/trusted.gpg
  321  sudo ls /etc/apt
  322  sudo rm -f /etc/apt/trusted.gpg.d/elastic.gpg
  323  wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo apt-key add -
  324  sudo ls /etc/apt/trusted.gpg.d
  325  sudo apt update
  326  sudo ls /etc/apt/trusted.gpg
  327  sudo ls /etc/apt/trusted.gpg/
  328  sudo apt update
  329  sudo apt install -y elasticsearch=7.11.1 logstash=7.11.1 kibana=7.11.1
  330  sudo apt install -y elasticsearch=7.11.1
  331  sudo systemctl status elasticsearch.service
  332  sudo apt install -y logstash=7.11.1
  333  history
  334  sudo vi /etc/elasticsearch/elasticsearch.yml
  335  sudo systemctl start elasticsearch.service
  336  sudo systemctl status elasticsearch.service
  337  sudo systemctl enable elasticsearch.service
  338  curl http://127.0.0.1:9200
  339  sudo apt install -y kibana=7.11.1
  340  sudo systemctl status kibana.service
  341  sudo systemctl start kibana.service
  342  sudo systemctl status kibana.service
  343  sudo journalctl -u elasticsearch
  344  sudo journalctl -u kibana
  345  history
  346  ls /etc/kibana/
  347  sudo ls /etc/kibana/
  348  sudo vi /etc/kibana/kibana.yml
  349  sudo systemctl restart kibana.service
  350  sudo systemctl status kibana.service
  351  sudo vi /etc/kibana/kibana.yml
  352  sudo vi /etc/elasticsearch/elasticsearch.yml
  353  sudo apt install -y logstash=7.11.1
  354  sudo app-key list
  355  sudo apt-key list
  356  /etc/apt/trusted.gpg.d
  357  ls /etc/apt/trusted.gpg.d
  358  ls /etc/apt/trusted.gpg
  359  vi /etc/apt/trusted.gpg
  360  ls /etc/apt/trusted.gpg.d
  361  history
  362  sudo apt-cache madison logstash
  363  sudo apt install -y logstash=1:7.11.1-1
  364  ubuntu -version
  365  os -version
  366  -version
  367  --version
  368  lsb_release -a
  369  exit
  370  sudo systemctl status kibana
  371  sudo systemctl enable kibana
  372  sudo systemctl status kibana
  373  sudo systemctl status elasticsearch.service
  374  history
  375  sudo vi /etc/elasticsearch/elasticsearch.yml
  376  sudo systemctl status kibana
  377  sudo systemctl start kibana
  378  sudo systemctl status kibana
  379  history



