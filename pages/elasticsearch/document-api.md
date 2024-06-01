## Document API

## ELK 스택 설치

- [github.com/deviantony/docker-elk](https://github.com/deviantony/docker-elk) 을 clone 
- `.env` 파일 내의 주요 패스워드 들 수정
  - 비밀번호는 8자?이상이어야 에러가 나지 않습니다.

- docker-compose up setup
  - setup-1 exited with code 0 이라는 메시지가 뜨면 정상설치 된 것 입니다.

- docker-compose up -d
- kibana 는 http://localhost:5601 로 접속 가능하며, user, password 는 아래와 같이 입력합니다.
  - user : elastic
  - password : `.env` 파일 내에 입력한 패스워드


<br/>















