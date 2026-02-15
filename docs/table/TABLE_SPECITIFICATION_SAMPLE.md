# Members
> 사용자(member) 공개 정보 및 메타 정보를 저장하는 테이블입니다.

- 테이블명: 사용자
- 물리 테이블명: members

<br>

## 테이블 관계
- Member(1) - Post(N)

<br>

## 컬럼 정의
| No | 컬럼명           | 타입         | NOT NULL | 기본값           |  PK |  FK | 설명 / 설계 근거                    |
| -: | -------------- | ----------- | :---: | ----------------- | :-: | :-: | -------------------------------- |
|  1 | id             | BIGINT      |   ✅  | AUTO_INCREMENT    |  ✅  | -  | 사용자 식별을 위한 대리키               |
|  2 | nickname       | VARCHAR(20) |   ✅  | -                 |  -  |  -  | 공개 정보: 닉네임 (2~20자)            |
|  3 | account_status | VARCHAR(20) |   ✅  | `PENDING`         |  -  |  -  | 계정 상태                           |
|  5 | created_at     | DATETIME    |   ✅  | CURRENT_TIMESTAMP |  -  |  -  | 생성 시각                           |
|  6 | updated_at     | DATETIME    |   ✅  | CURRENT_TIMESTAMP |  -  |  -  | 수정 시각                           |
|  4 | deleted_at     | DATETIME    |   -   | -                 |  -  |  -  | 탈퇴 처리 시각 (soft delete)         |

### Enum(account_status)
- `PENDING`: 비활성 사용자(회원 가입 진행 필요)
- `ACTIVE`: 활성 사용자(회원 가입 완료)
- `WITHDRAWN`: 회원 탈퇴
- `BLACKLISTED`: 계정 정지

<br>

## 인덱스 정의
| 인덱스명 | 컬럼 | 설계 근거 |
|---|---|---|
|uidx_members_nickname|nickanme|중복 방지 및 빠른 조회|

<br>

## 제약조건 및 규칙
- 필수 입력
  - 사용자는 닉네임을 필수로 입력해야 합니다.
  - 닉네임은 영어 대/소문자, 숫자만 허용합니다.
  - 비공개 정보(비밀번호, 주소지 등)는 [member_secrets](/MEMBER_SECRESTS.md)에 저장합니다.
- 회원탈퇴
  - 회원 정보는 소프트 삭제처리, 회원이 작성한 글은 하드삭제 됩니다.
  - 14일 이후 `account_status`를 `PENDING`으로 변경, `deleted_at`를 `null`로 설정되며 재가입이 가능해집니다.
  - 계정 정지의 경우 `account_status`를 `BLACK`로 변경되며, 재가입이 불가능합니다. `deleted_at`은 정지 일자로 기록되며 재설정되지 않습니다.

<br>

## DDL
```sql
CREATE TABLE members (
  id BIGINT AUTO_INCREMENT PRIMARY KEY,
  nickname VARCHAR(20) NOT NULL,
  account_status VARCHAR(20) NOT NULL DEFAULT 'PENDING',
  created_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP,
  updated_at DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP ON UPDATE CURRENT_TIMESTAMP,
  deleted_at DATETIME NULL,
  CONSTRAINT uidx_members_nickname UNIQUE (nickname)
);
```

<br>

## 변경 이력
|일자|변경내용|작성자
|---|---|---|
|2026.01.01|최초생성|아무개|
