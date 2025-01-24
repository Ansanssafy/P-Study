# GraphQL(2)
## REST API의 한계
### Overfetching
- 요청에 비해 너무 많은 데이터(정보)가 오는 경우
### UnderFetching
- 요청에 비해 정보가 부족한 경우
```memo
ex)도서 정보와, 도서 정보 간략 보기의 경우
- 똑같은 책의 정보를 가져오는 API지만 서로 필요한 정보의 양이 다르다
- Overfetching의 경우 너무 많은 데이터를 가져오고 반대의 경우 양이 너무 부족하다
- 최적화를 위해서는 도서 정보를 받는 API가 2개 필요하지만 이건 유지보수 측에서 별로
```

## GraphQL에서 등록, 수정, 삭제, 조회는 어떻게 하는가?
### GraphQL에서는 보통 모든 요청을 POST요청으로만 보낸다
- GraphQL 요청의 복잡성을 POST가 더 잘 처리가능
- Body를 통해 쿼리와 변수를 함께 전달하는 구조가 효율적
- GET요청은 URL 길이 제한 등으로 비효율적이라 사용이 제한적
<br>
```js
POST http://localhost:4000/graphql
--body--
{
    //graphql
}
```
- 조회 : query <br>
  : 저장된 데이터 가져오기
- 등록/수정/삭제 : mutation <br>
  : 저장된 데이터 수정하기 (Creatr, Delete, Update)
- 구독 : subscroption <br>
  : 특정 이벤트가 발생 시 대응하는 데이터를 실시간으로 클라이언트에게 전송
  
```memo
- REST API의 GET 요청은 GraphQL에서 Query를 이용해 원하는 데이터 요청
- CREATE, DELETE와 같은 저장된 데이터를 수정할 때는 Mutation을 이용해 요청
- 실시간 업데이트를 구현하기 위해 구독(Subscription)을 이용해 구현
```
<br>

## GraphQL Client
- 클라이언트(프런트엔드)에서 GraphQl 서버와 통신하기 위한 도구
- GraphQl은 서버와의 데이터 요청 방식에 유연성을 제공, 이를 쉽게 관리하고 최적화하기 위해 클라이언트 라이브러리 사용

## 대표적인 GraphQL Client 
### 1. Apollo Client
- 가장 널리 사용되는 클라이언트
- 강력한 캐싱 시스템 제공
- 다양한 상태 관리 통합(React, Vue 등 쉽게 사용 가능)
- 로컬 상태 관리 기능 지원
### 2. Relay
- 대규모 애플리케이션에서 최적화된 성능 제공
- 엄격한 스펙과 구조화된 데이터 관리
- Apollo보다 어렵지만 성능과 최적화 측면에서 강력
### 3. Urql
- 간단하고 경량화된 클라이언트
- 비교적 배우고 쉽고 가볍다
- 필요에 따라 확장 가능한 플러그인 아키텍처 제공
### 4. GraphQl Request
- 가장 기본적이고 가벼운 클라이언트
- 단순히 요청을 보내고 응답을 받는 역할만 집중
- 캐싱, 상태 관리는 직접 구현해야함
<br>
<br>

# GraphQL을 이용한 프로젝트 구성
## BackEnd
### 1. Spring Boot GraphQL 설정
- build.gradle이나 pom.xml에 의존성 추가
```groovy
// Gradle
implementation 'com.graphql-java-kickstart:graphql-spring-boot-starter:12.0.0'
implementation 'com.graphql-java-kickstart:graphiql-spring-boot-starter:12.0.0'
implementation 'com.graphql-java-kickstart:graphql-java-tools:12.0.0'
```
### 2. GraphQL 스키마 정의
- src/main/resources/graphql/schema.graphqls에 스키마 작성
```graphql
type User {
  id: ID!
  name: String!
  email: String!
}

type Post {
  id: ID!
  title: String!
  content: String!
  author: User
}

type Query {
  getUser(id: ID!): User
  getPosts: [Post]
}

```
### 3. Resolver 클래스 생성
- GraphQL의 요청을 처리
```java
@Component
public class QueryResolver implements GraphQLQueryResolver {
    private final UserService userService;
    private final PostService postService;

    public QueryResolver(UserService userService, PostService postService) {
        this.userService = userService;
        this.postService = postService;
    }

    public User getUser(Long id) {
        return userService.findById(id);
    }

    public List<Post> getPosts() {
        return postService.findAll();
    }
}
```
## FrontEnd
### 1. Apollo Client에서 스프링부트 GraphQL 서버와 연동
```js
import React from 'react';
import ReactDOM from 'react-dom/client';
import './styles/index.css';
import App from './components/App';
import { ApolloProvider, ApolloClient, InMemoryCache, HttpLink } from '@apollo/client';

// Apollo Client 설정
const client = new ApolloClient({
  link: new HttpLink({
    uri: 'http://localhost:8080/graphql', // Spring Boot의 GraphQL 엔드포인트(백엔드 주소)
  }),
  cache: new InMemoryCache(),
});

const root = ReactDOM.createRoot(document.getElementById('root'));

root.render(
  <React.StrictMode>
    <ApolloProvider client={client}>
      <App />
    </ApolloProvider>
  </React.StrictMode>
);

```
### 2. GraphQL 쿼리 작성
```js
import React from 'react';
import { useQuery, gql } from '@apollo/client';

const GET_USER = gql`
  query GetUser($id: ID!) {
    getUser(id: $id) {
      id
      name
      email
    }
  }
`;

const UserComponent = ({ userId }) => {
  const { loading, error, data } = useQuery(GET_USER, {
    variables: { id: userId },
  });

  if (loading) return <p>Loading...</p>;
  if (error) return <p>Error: {error.message}</p>;

  return (
    <div>
      <h1>{data.getUser.name}</h1>
      <p>Email: {data.getUser.email}</p>
    </div>
  );
};

export default UserComponent;
```







<미구현..다음 시간...>

# FrontEnd에서 GraphQL 사용해보기
## 패키지 설치
```memo
npm install graphql apollo-server
npm install @apollo/client
```
