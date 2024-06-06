## Mapping 

Mapping 을 하는 방법은 크게 2가지가 있습니다.

- Index 를 생성하면서 Mapping 을 설정하는 방식
- `_mapping` API 를 통해서 설정하는 방식



<br/>



## 참고자료

- https://idea-sketch.tistory.com/63
- https://bk-investing.tistory.com/45
- https://www.elastic.co/guide/en/elasticsearch/reference/current/mapping.html
- https://logz.io/blog/elasticsearch-mapping/



<br/>



## 1\) Index 를 생성하면서 Mapping 을 설정하는 방식

```python
# Click the Variables button, above, to create your own variables.
PUT logs_comments_202406_02_backup
{
  "mappings": {
    "properties": {
      "title": {
        "type": "text",
        "fields": {
          "keyword": {
            "type": "keyword"
          }
        }
      },
      "content": {
        "type": "text",
        "fields": {
          "keyword": {
            "type": "keyword"
          }
        }
      },
      "author":{
        "type": "text"
      },
      "created_at": {
        "type": "date"
      }
    }
  }
}

```



위의 mapping 정보는 아래와 같이 `GET 인덱스명/_mapping` 으로 확인 가능합니다.

```python
GET logs_comments_202406_02_backup/_mapping
```

<br/>



출력결과

```plain
{
  "logs_comments_202406_02_backup": {
    "mappings": {
      "properties": {
        "author": {
          "type": "text"
        },
        "content": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword"
            }
          }
        },
        "created_at": {
          "type": "date"
        },
        "title": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword"
            }
          }
        }
      }
    }
  }
}
```

<br/>



## 2\) `_mapping` API 를 통해서 설정하는 방식

`PUT 인덱스명/_mapping` 명령을 통해 이미 생성되어 있는 인덱스에 필드를 추가할수 있는데 이 때 mapping API 를 통해 데이터 타입을 매핑해줄 수 있습니다. 주의할 점이 하나 있습니다. 한번 설정한 Mapping 에 필드를 추가하는 것은 가능하지만, 수정,삭제는 불가능하다는 점입니다. 

- 한번 설정한 Mapping 필드를 추가하는 것은 가능
- 한번 설정한 Mapping 필드는 수정,삭제는 불가능



<br/>

아래는 방금 전 추가한 logs\_comments\_202406\_02\_backup 인덱스에 새로운 필드를 추가하면서 mapping 을 지정하는 예제입니다.

```python
PUT logs_comments_202406_02_backup/_mapping
{
  "properties": {
    "updated_at":{
      "type": "date"
    }
  }
}
```

<br/>



출력결과

```bash
{
  "acknowledged": true
}
```

<br/>



변경된 정보를 확인해봅니다.

```python
GET logs_comments_202406_02_backup/_mapping
```

<br/>



출력결과

```plain
{
  "logs_comments_202406_02_backup": {
    "mappings": {
      "properties": {
        "author": {
          "type": "text"
        },
        "content": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword"
            }
          }
        },
        "created_at": {
          "type": "date"
        },
        "title": {
          "type": "text",
          "fields": {
            "keyword": {
              "type": "keyword"
            }
          }
        },
        "updated_at": {
          "type": "date"
        }
      }
    }
  }
}
```

<br/>



## mapping 작업시 사용하는 mapping 파라미터

indexing 시에 Analyzer 가 데이터를 어떻게 저장할지에 대한 기준을 정의하는 필드 들입니다.

- analyzer : 어떤 분석기를 사용할지를 명시합니다. 분석기는 형태소 분석을 수행하며, 기본값으로 설정된 분석기는 StandardAnalyzer 입니다.
- coerce : 자동 변환을 허용할지에 대한 여부를 지정하는 필드입니다. 예를들어 "98" 이라는 데이터를 Integer 로 변환하는 경우를 예로 들 수 있습니다.
- fielddata : 힙 공간에 생성하는 메모리 캐시입니다. 메모리 부족현상이 발생할 수 도 있습니다. text 타입을 aggregation 하고 싶을 때 사용하는 필드입니다. (참고: Tag Cloud)
- doc\_values : 기본 캐시 또는 파일 시스템 캐시를 이용해서 디스크 데이터에 빠르게  접근하는 필드입니다.

<br/>



## 데이터 타입

### Keyword(*)

> 자료조사 더 필요

키워드 형태로 사용할 데이터에 적합한 데이터 타입입니다. 분석기를 거치지 않으며 원문 그대로 인덱싱합니다. 검색시 필터링, 정렬, <br/>



### Text

문장 형태의 데이터에 적합한 타입입니다. Full Text Search 가 가능하며, 특정 단어 검색이 가능합니다. 만약 정렬, 집계가 필요할 경우 Text, Keyword 를 멀티 필드로 지정합니다. 이렇게 하면 목적에 맞게 활용이 가능해집니다.

Text 타입의 경우 인덱싱 시에 Analyzer 가 데이터를 문자열로 인식해서 분석을 하며 기본적으로 사용되는 분석기는 StandardAnalyzer 입니다.<br/>

<br/>



### Array

Array 내의 데이터는 모두 같은 타입이어야 합니다. 인덱스의 특정 필드에 배열이 입력되는 순간 자동으로 Array 로 저장됩니다.

- e.g. `"gender": ["male", "female"]` 

<br/>



### Boolean

true, false 를 지정할 수 있는 타입입니다. 문자가 들어와도 boolean 으로 해석되어 저장되는 것이 가능합니다.<br/>

<br/>



### Date

날짜 형식으로 인식될 수 있는 포맷은 아래와 같으며 이 외에도 ISO8601 형식으로 오는 모든 경우 Date 로 인식됩니다.

- "2024-12-25"
- "2024-12-25T12:25:25"
- "2024-12-25T12:25:25+09:00"
- "2024-12-25T12:25:25.428Z"



만약 다른 타입으로 데이터가 올 경우 `text`, `keyword` 로 저장되게 됩니다. `epoch_millis`, `epoch_second` 도 가능합니다.<br/>

만약 원하는 형식으로 데이터가 저장되기를 원할 경우 `format` 옵션을 지정해줍니다.

- e.g. `"format": "yyyy-MM-dd HH:mm:ss||yyyy/MM/dd||epoch_millis"`
  - 위의 예시는 2024-12-25 14:25:25, 2024/12/25, epoch_millis 로 모두 받을 수 있도록 지정하고 있습니다.



### IP

IP 주소 데이터를 저장하는 데에 사용되는 타입입니다. IPv4, IPV6 타입 모두를 지원할 수 있습니다.<br/>



### Range

숫자, 날짜, IP 등과 같은 데이터를 다룰 수 있으며 범위를 지정할 수 있는 데이터에 사용하는 데이터 타입입니다.

- integer\_range
- float\_range
- long\_range
- double\_range
- date\_range
- ip\_range

데이터의 범위는 gt, lt, gte, lte 를 이용해서 지정합니다.<br/>



### Object

JSON 에서는 내부 객체를 계층적으로 중첩해서 표현이 가능한데, 한 필드 안에서 또 다른 객체를 타입하도록 Object 타입으로 선언하는 것이 가능합니다. 한 요소가 여러 하위 정보를 가질 경우에 필드를 Object 타입으로 정의합니다.<br/>



### Nested(*)

> 자료조사 더 필요

Object 객체 배열을 독립적으로 색인하고 쿼리하는 형태의 데이터 타입입니다. 객체 내에 배열의 형태로 저장가능합니다.





## Metadata Field

indexing 할 데이터를 어떤 방식으로 저장할지를 정의하는 설정들입니다

- `_index` : 인덱스 명
- `_type`: Document 가 속한 매핑의 타입 정보
- `_id` : Document 를 식별할 수 있는 키값
- `_source` : Document 의 데이터 입니다. json 형식입니다.
- `_field_names` : null 이 아닌 값을 포함하는 문서의 모든 필드
- `_routing` : 특정 문서를 특정 샤드에 저장하기 위해 사용하는 필드
