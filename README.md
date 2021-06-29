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

  

#### remote 설정

레포지토리 생성이 끝났다면 해당 레포지토리의 링크를 복사하여 아래와 같이 입력해 줍니다.

```shell
git remote add origin GIT링크
```

  

#### 프로젝트 push

master와 main은 설정에 따라 다릅니다. 저는 master로 설정이 되어있어 master를 입력했습니다.

```shell
git push origin master
또는
git push origin main
```

  

#### 배포 라이브러리 추가

```shell
yarn add gh-pages -D
```

gh는 github을 줄인 말입니다.

  

#### package.json 내용 추가

```json
"scripts":{
    "predeploy": "vue-cli-service build",
    "deploy": "gh-pages -d dist",
    "clean": "gh-pages-clean"
}
```

packages.json 파일을 열어보면 이미 여러 내용이 적혀있습니다. 그 중 sciprts 영역에 위 세가지를 추가해 줍니다.

  

#### 배포용 publicPath 설정

해당 설정이 있어야 github에서 호스팅하는 페이지를 열람할 수 있습니다.

프로젝트 최상단에 vue.config.js 파일을 생성해 publicPath에 생성한 레포이름으로 설정해줍니다.

```js
module.exports = {
    publicPath: "/vue-devops/",
    outputDir: "dist"
}
```

  

#### 배포하기

```shell
yarn deploy
```

해당 명령어를 통해 빌드 된 정적 파일을 원격 저장소의 gh-pages 브랜치를 생성해서 푸시됩니다.

   

#### 확인해보기

Github에서 생성했던 레포지토리에 들어가시고 Settings - pages 탭으로 진입하면 URL이 보입니다.

해당 URL을 클릭하여 Vue 페이지가 뜬다면 성공입니다.



#### 빈 페이지만 뜨는 경우

저는 아무런 에러가 없었으나 최종 확인 단계에서 빈 페이지만 뜨는 상황에 직면했습니다.

```js
module.exports = {
    publicPath: "/vue-devops/",
    outputDir: "dist"
}
```

알고보니 위 publicPath에서 "**vue-devops/**"로 입력을 했던 것이 문제였습니다..

저와 같은 실수를 하지 않기를 바랍니다!!



# 배포 자동화

Github Actions workflow를 이용해 배포 자동화를 해보겠습니다.

쉽게 말해 개발자가 Push/Pull Request와 같은 이벤트에 반응하여 트리거하도록 액션을 구성할 수 있습니다.

#### workflow 생성

Github Actions 메뉴에 들어가신후 **Set up this workflow**를 클릭하여 작성한 후 커밋합니다.

workflows 폴더에 .yml 파일이 생성된 것을 볼 수 있습니다.

  

#### workflow 동작 확인

다시 Actions 메뉴로 들어가 1개의 workflow가 build 되었는지 확인할 수 있습니다.

  

#### App.vue 수정

자동화 확인을 위해 Welcome to 부분을 수정해 봅시다. Your ~ 를 본인의 이름을 넣어봅니다.

저는 "Welcome to 강승현 App"으로 수정해줬습니다.



#### workflow PULL

workflow를 통해 생성된 yml 파일을 수정해야 하므로 pull을 통해 yml 파일을 가져옵니다.

```shell
git pull origin master
또는
git pull origin main
```

이후 .github/workflows 폴더 내에 .yml 파일이 pull 된 것을 볼 수 있습니다.



#### .yml파일 수정

```yaml
deploy:
    runs-on: ubuntu-latest
    steps:
    - name: Checkout source code
      uses: actions/checkout@master
    
    - name: Set up Node.js
      uses: actions/setup-node@master
      with:
        node-version: 14.x
    
    - name: Install dependencies
      run: yarn install
    
    - name: Build page
      run: yarn build
      env:
        NODE_ENV: production
    
    - name: Deploy to gh-pages
      uses: peaceiris/actions-gh-pages@v3
      with:
        github_token: ${{ secrets.GITHUB_TOKEN }}
        publish_dir: ./dist
```

저희는 jobs 내에 위의 내용을 추가합니다.

이후 해당 파일을 commit&push 해줍니다.



```shell
git add .
git commit -m "커밋 메세지"
git push origin master 또는 main
```



그런 다음 해당 workflow가 제대로 작동되는지 확인해줍니다.

시간이 좀 소요되니 1분정도 여유를 가지시면 됩니다.



# 자동 배포 실패 해결 방안

자동배포 과정이 늘 순탄하지만은 않을겁니다. 때로는 에러가 발생할텐데, 이런 상황에서 에러난 페이지를 배포하여 에러를 적나라하게 보여주기 보다는 차라리 이전 버전(정상적으로 작동)을 보여주는 환경을 만들어보겠습니다.



#### .yml 단위테스트 추가

.yml 파일에 yarn install과 build 사이에 단위 테스트를 추가해줍니다.

```yaml
    - name: Test unit
      run: yarn test:unit
```



#### 테스트 실패 유도

실패를 일으키는 방법엔 여러가지가 있지만 HelloWorld.vue에 h1 태그를 건들여 보겠습니다.

주어진 msg props를 사용하지 않고 다른 텍스트로 수정하여 props 에러를 유발해 봅시다.

{{msg}}를 지우고 일반 텍스트 입력후 Commit & Push 하시면



Build는 성공하지만 Deploy에서 실패하게 됩니다.

그럼에도 불구하고 우리의 배포 페이지는 이전에 배포된 상태를 유지하고 있죠.



#### 버그 수정

이제 수정했던 부분을 다시 돌리시고 props 에러를 해결하여 배포하면 정상적으로 배포되는 것을 확인할 수 있습니다.

