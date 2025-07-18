<!-- ---
applyTo: "**"
--- -->

# 작은 도서관 시스템 

## 프로젝트 개요
C 기반 도서관 관리 시스템으로 SQLite3 Amalgamation 데이터베이스를 사용하여 도서, 회원, 대출/반납을 관리합니다.

# 주요 기능:

- 도서 등록, 검색, 수정, 삭제
- 회원 관리 : 등록, 정보 수정, 연체 상태 확인
- 대출/반납 프로세스


## 아키텍처
- **모듈화 구조**: 각 기능별로 독립적인 헤더/소스 파일 페어
  - `book.h/book.c` - 도서 관리
  - `member.h/member.c` - 회원 관리  
  - `loan.h/loan.c` - 대출/반납 관리
  - `database.h/database.c` - SQLite 데이터베이스 추상화 레이어
- **정적 라이브러리**: 핵심 기능은 `library_core` 라이브러리로 분리
- **테스트 분리**: Google Test 기반 C++ 테스트 (`tests/`)

## 빌드 시스템
- **주요 빌드 도구**: CMake (권장) 

## 데이터베이스 
- **CMakeLists.txt에 추가** : CMakeLists.txt에 SQLite3 Amalgamation 추가 
  - SQLite3 Amalgamation 파일은 프로젝트 내 `src/external/sqlite/` 디렉토리에 위치
  
  add_library(sqlite3 STATIC external/sqlite/sqlite3.c)
  target_include_directories(sqlite3 PUBLIC external/sqlite)

- **초기화 순서**: `initialize_database()` → `create_tables()` → `create_indexes()`
- **연결 관리**: `get_db_connection()`으로 전역 연결 획득
- **쿼리 실행**: `execute_query()`와 `prepare_statement()` 사용

## 테스트
- **테스트 프레임워크**: Google Test
- **Google Test 위치** : 프로젝트 내 `src/external/googletest/` 디렉토리에 위치
- **테스트 구조**: 각 모듈별로 `tests/` 디렉토리에 테스트 파일 생성
  - `tests/book_tests.cpp` - 도서 관련 테스트
  - `tests/member_tests.cpp` - 회원 관련 테스트
  - `tests/loan_tests.cpp` - 대출/반납 관련 테스트
  

## 네이밍 컨벤션
- **함수**: snake_case with verbs (`add_book`, `get_book_by_id`)
- **변수**: snake_case (`book_id`, `member_name`)
- **상수**: UPPER_SNAKE_CASE (`MAX_INPUT_SIZE`, `DEFAULT_LOAN_DAYS`)
- **구조체**: CamelCase (`Book`, `Member`, `Loan`)
- **파일**: snake_case with extensions (`.h`, `.c`)

## 개발 워크플로우
1. 헤더 파일에 구조체와 함수 프로토타입 정의
2. 소스 파일에 함수 구현
3. 테스트 파일에 Google Test 기반 단위 테스트 작성
4. CMake로 빌드 후 테스트 실행으로 검증