# EOS CDT build

```console

```
```console

```
## 1. Git repository setting
[https://github.com/eosio/eosio.cdt](https://github.com/eosio/eosio.cdt) 에서 Fork 한다.

현재 CDT 의 최신 릴리즈 버전은 v1.4.1 이다.

이 버전으로 local 에 clone 한다. 앞서 수동으로 했던 recursive 를 한 번에 한다.

```console
>git clone --recursive $MY_REPOSITORY/eosio.cdt --branch v1.4.1
```

공식 홈페이지에 따르면 약 30분 걸린다고 한다.

Clone 이 끝나면 다음과 같이 현재 브랜치에 버전 정보를 마킹을 해주자.
```console
>cd eosio.cdt
>git checkout -b v1.4.1
```


## 2. 빌드 수행
```console
>./build.sh
```