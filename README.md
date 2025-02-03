# 🔨 ELK 설치
> 대규모 실데이터를 시각화하기 위한 ELK 설치
<br>

## ElasticSearch

<br>
설치 전 윈도우에서 우분투 ELK에 직접 접속할 수 있도록 VirtualBox에서 포트포워딩 설정을 해 준다.<br><br>

![port](https://github.com/user-attachments/assets/3a866ca7-d310-468b-934a-cff8c1f01d54)

포트 9200 - ElasticSearch <br>
포트 5601 - Kibana <br>
<br>

```
wget -qO - https://artifacts.elastic.co/GPG-KEY-elasticsearch | sudo tee /usr/share/keyrings/elasticsearch-keyring.asc
echo "deb https://artifacts.elastic.co/packages/7.x/apt stable main" | sudo tee /etc/apt/sources.list.d/elastic-7.x.list
sudo apt update
```
gpg key 등록 → 레포지토리 url값 인식(저장소 등록) → update 후 elasticsearch install

<br>

```
sudo apt install -y elasticsearch=7.11.1
```

<br>

```
sudo vi /etc/elasticsearch/elasticsearch.yml
```
이 상태로는 VirtualBox에서 포트포워딩을 하더라도 외부에서 연결이 불가능하다.<br>
elasticsearch.yml 파일을 수정해서 로컬이 아닌 외부에서도 ES를 사용 가능하도록 만든다.


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

```
sudo vi /etc/kibana/kibana.yml
```
설치 후 kibana.yml 파일을 수정
<br><br>
(사진)
원격에서 접근 가능하도록 server.host를 0.0.0.0 으로 설정
<br>

(사진)
Kibana가 ElasticSearch와 연결할 수 있도록 elasticsearch.hosts를 ~~~으로 설정

<br>

## 💥 TroubleShooting

### 1. Logstash 설치 시 버전명 오류

```
sudo apt install -y logstash=7.11.1
```
해당 명령어를 입력해서 Logstash를 설치하려 했을 때 오류가 발생했다.

<br>

```
sudo apt-cache madison logstash
```
해당 명령어는 Logstash 패키지의 사용 가능한 버전과 해당 버전의 저장소 정보를 표시한다.

<br>

![madison](https://github.com/user-attachments/assets/fd43d34c-985d-471d-88ae-0afabeb6981b)

<br>

```
sudo apt install -y logstash=1:7.11.1-1
```
표기된 대로 7.11.1이 아니라 1:7.11.1-1 버전을 설치하는 것으로 해결되었다.

<br><br>

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
![5-2](https://github.com/user-attachments/assets/7afd88c5-49f5-4012-b395-33cc8193db75)

<br>
보험/병원은 연령대가 높아질수록 늘어난다.<br>
요식업은 전체 연령대에 고르게 분포해있는 편이다.<br>
의류/신변잡화는 전체 연령대에 매우 고르게 분포해 있다.<br>
사무통신/서적/학원 소비는 30~50 사이에서만 주로 나타난다.<br>
자동차/연료/정비는 55를 중간값이자 최대값으로 갖는다.<br>


<br><br>

### 3. 연령대별 회원등급별 수

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

<br>

| 21 | 22 | 23 | 24 | 25 |
|----|----|----|----|----|
| VVIP | VIP | 플래티넘 | 골드 | 해당없음 |

<br>
가장 회원수가 많은 고객의 연령대는 40대이며, VIP 이상인 회원의 수가 가장 많은 연령대 또한 40대이다.<br>
추후 더 높은 등급으로 올라갈 가능성이 다른 연령대보다 높으므로 소비 금액의 경계선에 있는 회원들에게 추가로 고급 혜택 서비스를 제공한다.

<br><br>


### 4. 생애 주기별 평균 이용금액

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

<br>

| UNI | NEW_JOB | NEW_WED | CHILD_BABY | CHILD_TEEN | CHILD_UNI | GOLLIFE | SECLIFE | RETIR | TEEN |
|-----|---------|---------|------------|------------|-----------|---------|---------|-------|------|
|대학생|사회초년생|신혼|자녀 영유아|자녀 의무교육|자녀 대학생|중년기타|2nd Life|은퇴|청소년|

<br>
자녀를 둔 고객의 평균 이용금액이 월등하게 높다.<br>


<br><br>

### 5. 생애 주기별 평균 소비 분야

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

![6-2](https://github.com/user-attachments/assets/5ec173d1-3125-4336-ba83-7049269a2f34)
<br>

<br><br>


### 6. 생애 주기별 신용카드/체크카드 평균 이용 금액

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
        "TOT_USE_AM": {
          "avg": {
            "field": "TOT_USE_AM"
          }
        },
        "CRDSL_USE_AM": {
          "avg": {
            "field": "CRDSL_USE_AM"
          }
        },
        "CNF_USE_AM": {
          "avg": {
            "field": "CNF_USE_AM"
          }
        }
      }
    }
  }
}
```

</details>

![7-2](https://github.com/user-attachments/assets/fb6a31d9-a718-4e6e-b979-07656d55c95d)
<br>


<br><br>


### 7. 연령별 신용카드/체크카드 평균 이용 금액

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
        "size": 10
      },
      "aggs": {
        "TOT_USE_AM": {
          "avg": {
            "field": "TOT_USE_AM"
          }
        },
        "CRDSL_USE_AM": {
          "avg": {
            "field": "CRDSL_USE_AM"
          }
        },
        "CNF_USE_AM": {
          "avg": {
            "field": "CNF_USE_AM"
          }
        }
      }
    }
  }
}
```

</details>

![8-2](https://github.com/user-attachments/assets/37a6694a-c283-488c-8ebc-141cd84cfc3f)
<br>


## 회고
Kibana는 클릭과 드래그로 x축과 y축을 쉽게 조절할 수 있고, 결과를 한눈에 보기 좋게 표현할 수 있다.
query문을 날려 json형식으로 저장된 데이터를 출력하는 것과 비교하면 시각화라는 기능은 현대 사회에서는 필수적인 기능이라 생각한다.
편의성이나 한 눈에 볼 수 있다는 점에서 정말 좋은 도구라 생각한다.
자동화 파이프라인을 잘만 구축한다면 사람이 해야 할 일은 매우 줄어들 것이다.

<br>

## 아쉬웠던 점
날짜에 따른 카드 신규 가입자의 증감 추이를 살펴보고 증감 폭이 큰 날짜 전후로 어떤 이벤트 또는 사건이 있었는지 확인해보고 신규 가입자를 늘릴 방법을 찾아보려고 했다.<br>
하지만 고객의 카드 입회년월 데이터가 명세서에는 YYYYMM으로 나와있었던 반면 실제 데이터 형식은 ~~~ 로 되어 있었다. 해당 데이터가 무엇을 나타내는지 알아보기 어려워 해당 분석 방법을 실행하지 못한 것이 아쉽다.
> 내가 실수한듯...데이터 제대로 들어와있음

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




