---
mode: 'agent'
model: Claude Sonnet 4
tools: ['changes', 'codebase', 'githubRepo', 'runTests', 'testFailure']
description: "코드를 리팩토링 합니다."
---

# 코드 리팩토링

선택한 C 코드를 분석하고 리팩토링해 주세요. 다음 요소들에 중점을 두어 개선안을 제시해 주세요:

## 분석 항목

1. **코드 중복 제거**
   - 반복되는 코드 패턴을 식별하고 공통 함수로 추출
   - 특히 데이터베이스 작업 및 문자열 처리에서의 중복 확인

2. **함수 분리 및 단일 책임 원칙**
   - 너무 긴 함수(20줄 이상)를 더 작고 집중된 함수로 분리
   - 각 함수가 단일 작업만 수행하도록 리팩토링

3. **데이터 접근 계층 개선**
   - SQL 쿼리 준비 및 실행 코드 표준화
   - 매개변수화된 쿼리 사용으로 SQL 인젝션 방지

4. **오류 처리 개선**
   - 일관된 오류 보고 메커니즘 구현
   - 오류 상황에서 적절한 리소스 정리

5. **메모리 관리 최적화**
   - 동적 할당된 메모리의 해제 확인
   - 버퍼 크기 검증 및 경계 검사 추가

6. **가독성 향상**
   - 명확한 변수/함수 이름 사용
   - 복잡한 조건문 단순화
   - 일관된 들여쓰기 및 포맷팅

## 출력 형식

리팩토링된 코드를 제시할 때 다음 형식을 따라주세요:

1. **변경 요약**: 무엇을 왜 변경했는지 간략히 설명
2. **리팩토링 전/후 코드**: 원본 코드와 리팩토링된 코드 비교
3. **개선 효과**: 리팩토링으로 얻어지는 이점 설명 (유지보수성, 성능, 안전성 등)

## 제약 조건

- 기존 함수 시그니처 및 API는 가능한 유지해야 함
- 리팩토링은 기능 변경이 아닌 내부 구조 개선에 초점을 맞춤
- C 언어 표준(C99/C11)을 준수하고, 외부 라이브러리 의존성 추가 지양
- 프로젝트의 코딩 스타일 가이드를 준수

## 예시 리팩토링

```c
// 리팩토링 전
void process_data(char *data, int size) {
    char buffer[100];
    strcpy(buffer, data);  // 버퍼 오버플로우 위험
    
    // 데이터 처리 코드...
    printf("처리된 데이터: %s\n", buffer);
}

// 리팩토링 후
void process_data(char *data, int size) {
    if (data == NULL) {
        fprintf(stderr, "데이터가 NULL입니다\n");
        return;
    }
    
    char buffer[100];
    if (size >= sizeof(buffer)) {
        fprintf(stderr, "데이터가 버퍼 크기를 초과합니다\n");
        return;
    }
    
    strncpy(buffer, data, sizeof(buffer) - 1);
    buffer[sizeof(buffer) - 1] = '\0';  // NULL 종료 보장
    
    // 데이터 처리 코드...
    printf("처리된 데이터: %s\n", buffer);
}