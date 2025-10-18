# API 문서

RealWorld Spring Boot 구현체의 RESTful API 명세서입니다.

## 기본 정보

- **Base URL**: `http://localhost:8080/api`
- **인증 방식**: JWT (JSON Web Token)
- **인증 헤더 형식**: `Authorization: Token {JWT_TOKEN}`
- **응답 형식**: JSON

## 인증 (Authentication)

### 회원가입

사용자 계정을 생성합니다.

**Endpoint**: `POST /api/users`

**인증 필요**: No

**요청 본문**:
```json
{
  "user": {
    "username": "jacob",
    "email": "jake@jake.jake",
    "password": "jakejake"
  }
}
```

**응답**:
```json
{
  "user": {
    "email": "jake@jake.jake",
    "token": "jwt.token.here",
    "username": "jacob",
    "bio": "",
    "image": null
  }
}
```

### 로그인

기존 사용자 로그인을 처리합니다.

**Endpoint**: `POST /api/users/login`

**인증 필요**: No

**요청 본문**:
```json
{
  "user": {
    "email": "jake@jake.jake",
    "password": "jakejake"
  }
}
```

**응답**:
```json
{
  "user": {
    "email": "jake@jake.jake",
    "token": "jwt.token.here",
    "username": "jacob",
    "bio": "I work at statefarm",
    "image": null
  }
}
```

### 현재 사용자 조회

로그인된 사용자의 정보를 조회합니다.

**Endpoint**: `GET /api/user`

**인증 필요**: Yes

**응답**:
```json
{
  "user": {
    "email": "jake@jake.jake",
    "token": "jwt.token.here",
    "username": "jacob",
    "bio": "I work at statefarm",
    "image": null
  }
}
```

### 사용자 정보 수정

현재 로그인된 사용자의 정보를 수정합니다.

**Endpoint**: `PUT /api/user`

**인증 필요**: Yes

**요청 본문**:
```json
{
  "user": {
    "email": "jake@jake.jake",
    "bio": "I like to skateboard",
    "image": "https://i.stack.imgur.com/xHWG8.jpg"
  }
}
```

**응답**:
```json
{
  "user": {
    "email": "jake@jake.jake",
    "token": "jwt.token.here",
    "username": "jacob",
    "bio": "I like to skateboard",
    "image": "https://i.stack.imgur.com/xHWG8.jpg"
  }
}
```

## 프로필 (Profile)

### 프로필 조회

특정 사용자의 프로필을 조회합니다.

**Endpoint**: `GET /api/profiles/{username}`

**인증 필요**: Optional

**응답**:
```json
{
  "profile": {
    "username": "jacob",
    "bio": "I work at statefarm",
    "image": "https://static.productionready.io/images/smiley-cyrus.jpg",
    "following": false
  }
}
```

### 사용자 팔로우

특정 사용자를 팔로우합니다.

**Endpoint**: `POST /api/profiles/{username}/follow`

**인증 필요**: Yes

**응답**:
```json
{
  "profile": {
    "username": "jacob",
    "bio": "I work at statefarm",
    "image": "https://static.productionready.io/images/smiley-cyrus.jpg",
    "following": true
  }
}
```

### 사용자 언팔로우

특정 사용자를 언팔로우합니다.

**Endpoint**: `DELETE /api/profiles/{username}/follow`

**인증 필요**: Yes

**응답**:
```json
{
  "profile": {
    "username": "jacob",
    "bio": "I work at statefarm",
    "image": "https://static.productionready.io/images/smiley-cyrus.jpg",
    "following": false
  }
}
```

## 게시글 (Articles)

### 게시글 목록 조회

게시글 목록을 조회합니다. 필터링 및 페이징이 가능합니다.

**Endpoint**: `GET /api/articles`

**인증 필요**: Optional

**쿼리 파라미터**:
- `tag`: 태그 필터 (optional)
- `author`: 작성자 필터 (optional)
- `favorited`: 즐겨찾기한 사용자 필터 (optional)
- `limit`: 페이지 크기 (기본값: 20)
- `offset`: 오프셋 (기본값: 0)

**응답**:
```json
{
  "articles": [{
    "slug": "how-to-train-your-dragon",
    "title": "How to train your dragon",
    "description": "Ever wonder how?",
    "body": "It takes a Jacobian",
    "tagList": ["dragons", "training"],
    "createdAt": "2016-02-18T03:22:56.637Z",
    "updatedAt": "2016-02-18T03:48:35.824Z",
    "favorited": false,
    "favoritesCount": 0,
    "author": {
      "username": "jake",
      "bio": "I work at statefarm",
      "image": "https://i.stack.imgur.com/xHWG8.jpg",
      "following": false
    }
  }],
  "articlesCount": 1
}
```

### 피드 게시글 조회

팔로우한 사용자들의 게시글을 조회합니다.

**Endpoint**: `GET /api/articles/feed`

**인증 필요**: Yes

**쿼리 파라미터**:
- `limit`: 페이지 크기 (기본값: 20)
- `offset`: 오프셋 (기본값: 0)

**응답**: 게시글 목록 조회와 동일

### 게시글 조회

특정 게시글을 조회합니다.

**Endpoint**: `GET /api/articles/{slug}`

**인증 필요**: Optional

**응답**:
```json
{
  "article": {
    "slug": "how-to-train-your-dragon",
    "title": "How to train your dragon",
    "description": "Ever wonder how?",
    "body": "It takes a Jacobian",
    "tagList": ["dragons", "training"],
    "createdAt": "2016-02-18T03:22:56.637Z",
    "updatedAt": "2016-02-18T03:48:35.824Z",
    "favorited": false,
    "favoritesCount": 0,
    "author": {
      "username": "jake",
      "bio": "I work at statefarm",
      "image": "https://i.stack.imgur.com/xHWG8.jpg",
      "following": false
    }
  }
}
```

### 게시글 작성

새로운 게시글을 작성합니다.

**Endpoint**: `POST /api/articles`

**인증 필요**: Yes

**요청 본문**:
```json
{
  "article": {
    "title": "How to train your dragon",
    "description": "Ever wonder how?",
    "body": "You have to believe",
    "tagList": ["reactjs", "angularjs", "dragons"]
  }
}
```

**응답**: 게시글 조회와 동일

### 게시글 수정

기존 게시글을 수정합니다.

**Endpoint**: `PUT /api/articles/{slug}`

**인증 필요**: Yes (작성자만 가능)

**요청 본문**:
```json
{
  "article": {
    "title": "Did you train your dragon?",
    "description": "So you trained your dragon?",
    "body": "You have to believe"
  }
}
```

**응답**: 게시글 조회와 동일

### 게시글 삭제

게시글을 삭제합니다.

**Endpoint**: `DELETE /api/articles/{slug}`

**인증 필요**: Yes (작성자만 가능)

**응답**: HTTP 200 OK

### 게시글 즐겨찾기

게시글을 즐겨찾기에 추가합니다.

**Endpoint**: `POST /api/articles/{slug}/favorite`

**인증 필요**: Yes

**응답**: 게시글 조회와 동일 (`favorited: true`, `favoritesCount` 증가)

### 게시글 즐겨찾기 취소

게시글을 즐겨찾기에서 제거합니다.

**Endpoint**: `DELETE /api/articles/{slug}/favorite`

**인증 필요**: Yes

**응답**: 게시글 조회와 동일 (`favorited: false`, `favoritesCount` 감소)

## 댓글 (Comments)

### 댓글 추가

게시글에 댓글을 추가합니다.

**Endpoint**: `POST /api/articles/{slug}/comments`

**인증 필요**: Yes

**요청 본문**:
```json
{
  "comment": {
    "body": "His name was my name too."
  }
}
```

**응답**:
```json
{
  "comment": {
    "id": 1,
    "createdAt": "2016-02-18T03:22:56.637Z",
    "updatedAt": "2016-02-18T03:22:56.637Z",
    "body": "His name was my name too.",
    "author": {
      "username": "jake",
      "bio": "I work at statefarm",
      "image": "https://i.stack.imgur.com/xHWG8.jpg",
      "following": false
    }
  }
}
```

### 댓글 목록 조회

게시글의 댓글 목록을 조회합니다.

**Endpoint**: `GET /api/articles/{slug}/comments`

**인증 필요**: Optional

**응답**:
```json
{
  "comments": [{
    "id": 1,
    "createdAt": "2016-02-18T03:22:56.637Z",
    "updatedAt": "2016-02-18T03:22:56.637Z",
    "body": "His name was my name too.",
    "author": {
      "username": "jake",
      "bio": "I work at statefarm",
      "image": "https://i.stack.imgur.com/xHWG8.jpg",
      "following": false
    }
  }]
}
```

### 댓글 삭제

댓글을 삭제합니다.

**Endpoint**: `DELETE /api/articles/{slug}/comments/{commentId}`

**인증 필요**: Yes (댓글 작성자만 가능)

**응답**: HTTP 200 OK

## 태그 (Tags)

### 태그 목록 조회

모든 태그 목록을 조회합니다.

**Endpoint**: `GET /api/tags`

**인증 필요**: No

**응답**:
```json
{
  "tags": [
    "reactjs",
    "angularjs",
    "dragons"
  ]
}
```

## 에러 응답

모든 API는 에러 발생 시 다음 형식으로 응답합니다:

```json
{
  "errors": {
    "body": [
      "can't be empty"
    ]
  }
}
```

**일반적인 HTTP 상태 코드**:
- `200 OK`: 성공
- `201 Created`: 리소스 생성 성공
- `401 Unauthorized`: 인증 실패 또는 토큰 없음
- `403 Forbidden`: 권한 없음
- `404 Not Found`: 리소스를 찾을 수 없음
- `422 Unprocessable Entity`: 유효성 검증 실패

## 구현 노트

### JWT 토큰
- JWT 토큰은 `application.yaml`에서 설정된 서명 키로 생성됩니다
- 토큰 유효 시간은 기본적으로 설정 파일에서 관리됩니다
- 토큰은 사용자의 username을 subject로 포함합니다

### 인증 처리
- `/users/**` 경로는 인증 없이 접근 가능합니다
- 나머지 모든 엔드포인트는 JWT 토큰 인증이 필요합니다
- Optional 인증은 토큰이 있으면 사용자 정보를 포함하고, 없으면 익명으로 처리됩니다
