---
layout: article
title: "[Spring] SpringBoot-React 연동"
excerpt: Axios를 통한 SpringBoot-React 연동
categories:
  - 'Web'
tags:
  - 'Web'
  - 'Back-End'
  - 'Spring'
last_modified_at: 2021-10-28T08:06:00-05:00
mode: immersive
header:
  theme: dark
article_header:
  type: overlay
  theme: dark
  background_color: '#203028'
  background_image:
    gradient: 'linear-gradient(135deg, rgba(34, 139, 87 , .4), rgba(139, 34, 139, .4))'
toc: true
comment: true
---


SpringBoot와 React를 연동하여 간단한 Rest API서버를 구현해 보았습니다. 

## Spring Controller
우선 Spring에서는 `@Controller` 대신 `@RestController`를 사용하였는데 `@Controller`가 주로 View를 반환하기 위해 사용되는것과 달리 `@RestController`는 Json형태로 객체 데이터를 반환하기 위해 사용됩니다.

Spring - controller
```java
@RestController
public class TestController {
    @GetMapping("/home")
    public String getHome () {
        return "Hello World";
    }
}
```

## CORS (Cross-Origin Resource Sharing)

>CORS는 추가 HTTP헤더를 사용하여 한 출처에서 실행중인 웹 애플리케이션이 다른 출처의 선택한 자원에 접근할 수 있는 권한을 부여하도록 브라우저에 알려주는 체제입니다. 웹 애플리케이션은 리소스가 자신의 출처(도메인, 프로토콜, 포트)와 다를 때 교차 출처 HTTP 요청을 실행합니다.[CORS](https://developer.mozilla.org/ko/docs/Web/HTTP/CORS)

SpringBoot는 8080번 포트에서 실행되고, React 서버의 경우 3000번 포트에서 실행되어 CORS에러가 발생할 수 있습니다. 이를 해결하기 위해 React에서 Proxy설정을 진행합니다.

React서버가 있는 폴더의 `package.json`파일에서 다음의 내용을 추가합니다.
```json
...
"proxy": "http://localhost:8080"
...
```

이후 curl을 통해 확인해 보면 다른 포트로도 Spring의 내용이 출력되는것을 볼 수 있습니다.
```
$curl localhost:3000/
Hello World
```

## Axios 
Axios는 Browser 및 Node.js를 위한 Promise api 기반 HTTP 비동기 통신 라이브러리입니다.

Spring과 React의 연동을 위해서 React서버가 있는 폴더에서 `yarn add axios`를 실행하여 axios를 설치합니다.

## React - App.js

React에서 다음의 코드를 입력하여 Spring의 `/home`의 내용을 받아오도록 해주면

```js
import React, {useState, useEffect} from 'react';
import logo from './logo.svg';
import './App.css';

function App() {
    const [message, setMessage] = useState("");
    useEffect(() => {
        fetch('/home')
            .then(response => response.text())
            .then(message => {
                setMessage(message);
            });
    }, [])

    return (
        <div className="App">
            <header className="App-header">
                <img src={logo} className="App-logo" alt="logo"/>
                <h1 className="App-title">{message}</h1>
            </header>
            <p className="App-intro">
                To get started, edit <code>src/App.js</code> and save to reload.
            </p>
        </div>
    )
}

export default App;
```

다음과 같이 `Hello World`가 정상적으로 출력되는것을 볼 수 있습니다.
![image](https://user-images.githubusercontent.com/28651727/139222645-46d2cfc9-9ef4-4a55-a1b0-df7adc2c0feb.png)