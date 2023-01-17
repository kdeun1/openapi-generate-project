openapi-generate-cli 를 사용하여 API와 동일한 Model, 정형화된 API 코드 자동 생성
========================

> 작성된 [블로그 글](https://velog.io/@kdeun1/OpenAPI-Generator%EB%A5%BC-%EC%82%AC%EC%9A%A9%ED%95%98%EC%97%AC-API%EC%99%80-%EB%8F%99%EC%9D%BC%ED%95%9C-Model%EA%B3%BC-%EC%A0%95%ED%98%95%ED%99%94%EB%90%9C-API%EC%BD%94%EB%93%9C-%EC%9E%90%EB%8F%99%EC%83%9D%EC%84%B1%ED%95%98%EA%B8%B0)을 확인하면 이 프로젝트를 만든 이유와 적용 방법 및 결론 등의 내용을 자세히 확인할 수 있습니다.

# 1. 도입 배경
프로젝트 초기에 있었던 API 구현에 대한 문제를 해결하고 싶었고, 어떻게 하면 Type-Safe하게 API를 구현할 수 있을지에 대해 다시 찾아보기 시작했다.
FEConf 2020에서 [B2] OpenAPI Specification으로 타입-세이프하게 API 개발하기: 희망편 VS 절망편 세션과 TypeScript로 Open API JSON Schema Test하기 블로그 글을 인상 깊게 보았고, 현재 상태의 기술 부채를 해결하고 싶었다.

## 1.1 Swagger UI의 API 구조와 FE API 구조가 달랐던 이유
- API 서버 JAVA 코드의 controller와 method의 관계가 점진적으로 변경되는 사이에 FE 프로젝트에는 이전 방식의 구조가 유지가 되었다.
- FE의 API 구현 TypeScript 코드의 퀄리티가 초기와 비교해서 많이 변경되었다. 하지만 코드 리펙토링이 되지 않았다.
- 평상시에는 코드리뷰를 하면서 퀄리티를 유지하였다. 하지만 일정이 빠듯해서 갑작스럽게 추가인원이 급격하게 투입되었으며, 일정 시간동안에는 코드 머지만 하게 되었다. 
  개발자마다 구현하는 방식이 통일되지 않아 코드 작성 방식이 달랐다.
- API 구현부를 UI/컴포넌트 단위로 개발하시는 분도 계시고, BE의 도메인 단위로 개발하시는 분도 계셨다. 초기에 API 구현부의 스탠다드 구조를 정하지 못했기 때문에 
  발생한 문제였다.

## 1.2 해결하고 싶었던 기술 부채들
- API의 OAS와 FE의 API 구조가 1:1 관계로서 동일한 구조를 가지고 싶었다. 동일한 구조를 가지기 때문에 중복된 코드를 없애고 유지보수적 이점을 가지고 싶었고, 
결과적으로 번들 사이즈를 줄이고 싶었다.
- Swagger UI의 문서를 기준으로 OAS의 Model을 FE API의 Type과 동일한 타입을 가지고 싶었다. 결과적으로 정확한 커뮤니케이션, 개발을 전제로 하는 
  Type-Safe하게 개발하고 싶었다.
- API 구현부의 TypeScript 코드의 동일한 퀄리티를 가지고 싶었다. 개발자의 역량에 따라 좌지우지되지 않고, 요즘 유행하는 hygen과 같은 기능을 구현하고 싶었다.
- API 구현부의 동일한 구조(폴더, 파일의 규칙)를 가지고 싶었다.
- 위의 부분을 모두 자동화하고 싶었다. 100%까지는 아니더라도 어느정도 생성해줄 수 있는 편리한 도구를 사용하고 싶었다.

****

# 2. openapi-generator 사용

## 2.1 선택한 이유
openapi-generator가 openapi-typescript-codegen에 비해 star 수도 많았고 공식문서가 더 상세하였으며, 지원하는 언어도 많았고, FE 
CONF 2020의 [B2] OpenAPI Specification으로 타입-세이프하게 API 개발하기: 희망편 VS 절망편 세션에서 사용했기 때문에 
openapi-generator를 선택하였다.

## 2.2 사용법

### json 파일 사용하기
swagger ui의 json 링크를 사용하거나 json파일을 다운받은 후 openapi-generator-cli의 input 경로에 추가한다.

### openapi-generator-cli 설치
```
npm install @openapitools/openapi-generator-cli -g
```

### 실행
```
npm run generate
```

### script 옵션
```
-g : generator를 설정하는 옵션이고 typescript-axios를 선택하였다.
-i : input을 의미하며, yaml/json 파일의 위치를 지정한다
-o : output을 의미하며, 코드를 생성할 위치를 지정한다.
-c : generator 설정파일의 위치를 의미한다.
-t : 커스텀 template 설정파일의 위치를 의미한다.
```

### config 파일
```
{
  "spaces": 2,
  "modelPackage": "src/model",
  "apiPackage": "src/api",
  "supportsES6": true,
  "withNodeImports": false,
  "useSingleRequestParameter": true,
  "enumNameSuffix": "",
  "withSeparateModelsAndApi": true
}
```

### template
api 코드 생성하는 template은 `apiInner.mustache` 파일입니다. custom template을 사용하시려면 [retrieving templates](https://openapi-generator.tech/docs/templating/#retrieving-templates) 
내용을 확인해주세요.

#### template에 필요한 mustache 문법
template의 내용을 변경하려면 [mustache.js 깃허브](https://github.com/janl/mustache.js)와 [mustache 메뉴얼](https://mustache.github.io/)을 참고하시기 
바랍니다.

****

3. 아쉬운 점과 시행착오
- mustache 문법의 한계점
  - 아직 내가 못찾은건지 잘 모르겠는데 다양한 문법이 지원되지 않아 구현의 한계가 있음 
- 파라미터를 모두 하나의 객체로 구현하도록 템플릿을 만들었음
  - 파라미터가 하나도 없는 경우 중괄호를 삭제하는 후처리 필요
- API 코드 생성 이후에 다듬어야 함
  - url path 수정
  - api instance 수정
- 연계 할 JSON파일의 valid가 필요
- _delete 코드가 나타남
  - generate의 script에 `--reserved-words-mappings delete=delete` 코드 옵션을 추가한다.

