# Yarn 설치와 Vue.js 생성

### Yarn 이란?

npm과 마찬가지로 패키지 매니저입니다. 다만, 빌드 성능이 떨어지는 npm에 비해 성능이 우수합니다.

  

### Yarn 설치

npm이 이미 설치되어 있다면 아래와 같이 yarn을 설치해 줍니다.

```shell
npm install --global yarn
```

  

### Vue 프로젝트 생성

```shell
vue create vue-devops
```

vue-devops라는 프로젝트명을 사용하게 되며 이 구간은 본인이 원하는 이름을 쓰셔도 무방합니다.

다음으로 방향키를 이용해 [Manual select features]를 선택하고 엔터를 입력해줍니다.

그리고 여러 목록이 뜰텐데 

[Unit Testing]을 반드시 체크(스페이스 바) 해주시기 바랍니다. 이후 엔터를 입력합니다.

Vue version은 [3.x] 선택후 엔터

[ESLint + Prettier] 선택후 엔터

[Lint on save] 선택후 엔터

[Jest] 선택후 엔터

[In dedicated config files] 선택후 엔터

[Use Yarn] 선택후 엔터



### 프로젝트 디렉터리로 이동후 실행

```shell
cd vue-devops
```

  

이동후 서버를 실행해봅니다.

```shell
yarn serve
```



App running at:에 적혀있는 링크를 누르시고

Welcome to Your Vue.js App 화면을 보셨다면 성공!

  

## Github에 코드 Push 및 Pages에 수동 배포

Git 레포지토리를 생성해 줍니다.

프로젝트명과 동일한 vue-devops로 생성했으며 반드시 **public**이어야 합니다.

  

### remote 설정

레포지토리 생성이 끝났다면 해당 레포지토리의 링크를 복사하여 아래와 같이 입력해 줍니다.

```shell
git remote add origin GIT링크
```

  

### 프로젝트 push

master와 main은 설정에 따라 다릅니다. 저는 master로 설정이 되어있어 master를 입력했습니다.

```shell
git push origin master
또는
git push origin main
```

  

### 배포 라이브러리 추가

```shell
yarn add gh-pages -D
```

gh는 github을 줄인 말입니다.

  

### package.json 내용 추가

```json
"scripts":{
    "predeploy": "vue-cli-service build",
    "deploy": "gh-pages -d dist",
    "clean": "gh-pages-clean"
}
```

packages.json 파일을 열어보면 이미 여러 내용이 적혀있습니다. 그 중 sciprts 영역에 위 세가지를 추가해 줍니다.

  

### 배포용 publicPath 설정

해당 설정이 있어야 github에서 호스팅하는 페이지를 열람할 수 있습니다.

프로젝트 최상단에 vue.config.js 파일을 생성해 publicPath에 생성한 레포이름으로 설정해줍니다.

```js
module.exports = {
    publicPath: "/vue-devops/",
    outputDir: "dist"
}
```

  

### 배포하기

```shell
yarn deploy
```

해당 명령어를 통해 빌드 된 정적 파일을 원격 저장소의 gh-pages 브랜치를 생성해서 푸시됩니다.

   

### 확인해보기

Github에서 생성했던 레포지토리에 들어가시고 Settings - pages 탭으로 진입하면 URL이 보입니다.

해당 URL을 클릭하여 Vue 페이지가 뜬다면 성공입니다.



# 직면한 문제

### 빈 페이지만 뜨는 경우

저는 아무런 에러가 없었으나 최종 확인 단계에서 빈 페이지만 뜨는 상황에 직면했습니다.

```js
module.exports = {
    publicPath: "/vue-devops/",
    outputDir: "dist"
}
```

알고보니 위 publicPath에서 "**vue-devops/**"로 입력을 했던 것이 문제였습니다..

저와 같은 실수를 하지 않기를 바랍니다!!
