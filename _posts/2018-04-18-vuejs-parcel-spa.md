---
layout: post
title:  "Vuejs SPA boilerplate"
category: Web
tags: [nodejs, vuejs, server]
---

**Vuejs** 웹 프레임워크를 사용한 **SPA**(Single Page Application) boilerplate를 소개한다. 서버는 **nodejs** 로 간단하게 구현하였고 웹 어플리케이션 번들러는 **parceljs** 를 사용하였다.

<!-- more -->

## **DEMO**

https://github.com/jgtonys/vuejs-parcel-express-boilerplate


## **간단한 재료 소개**

이 SPA boilerplate를 제작하는데 사용된 재료(조미료)들을 소개하겠다.

Material|Description
:----:|:----:
[Vuejs](https://vuejs.org/)| Frontend Javascript Framework
[Vuex](https://vuex.vuejs.org/en/intro.html)|Vuejs state management library
[Vuetify](https://vuetifyjs.com/ko/)|Material Design Component Framework
[Vue Router](https://router.vuejs.org/en/essentials/getting-started.html)|SPA Frontend Router
[Sequelize](http://docs.sequelizejs.com/)|Nodejs ORM DB management
[Sqlite3](https://www.sqlite.org/index.html)|Simple SQL Database Engine
[JWT](https://jwt.io/)|JSON Web Token
[Parcel](https://ko.parceljs.org/)|Web Application Bundler

<br>
재료들을 보면 조금 생소한 녀석들이 있을 수 있다. 어떤 오픈소스인지 알아보고 싶다면 위의 링크를 참조하면 좋을 것 같다.

## **구조 잡기**

![backbone]({{ site.baseurl }}/public/img/boilerplate-backbone.PNG)

위 사진은 위 Demo의 boilerplate 뼈대이다. 일단 이렇게 구조를 잡은 이유는 서버와 클라이언트를 완벽하게 분리하고, 렌더링되는 모든 것을 클라이언트에서 처리하는 SPA를 만들기 위해서이다.

서버는 Nodejs Express, 클라이언트는 Vuejs로 완벽하게 분리시켜 놓은 모습이다. 클라이언트는 모두 위 사진의 client 폴더에서 독립적으로 돌아갈 수 있다. 폴더의 구조는 사실 내가 스스로 개발하기에 편한 방식으로 만든 것이고, 여러 다른 모습으로 구조를 짤 수도 있다.

## **구현 기능**

### **parcel bundler**

![parcel]({{ site.baseurl }}/public/img/parcel.png)

**번들러** 란 무엇인가? 부터 간략하게 설명하겠다.<br> 우리는 개발을 할 때 파일들을 분리하여 작업한다. 이는 개발 편의성과 가독성을 위해 필수적인 사항이다. 또한 MVC, MVVM 패턴으로 아름다운 프로그램을 만들기 위해서도 코드의 분리는 필수적이다.<br>
코드를 분리하여 웹 어플리케이션을 구성할 경우 모든 분리된 파일들은 각자의 요청과 응답을 받게 되고 이는 속도 감퇴라는 큰 단점 뿐만 아니라 예상치 못한 버그 등으로 인해 개발에 어려움이 생기게 된다.<br>
이를 해결하기 위해 웹 어플리케이션으로 띄우기 전에 생성된 프론트 자원들을 하나로 묶어(번들링)주는 것이 번들러이다.

대표적인 번들러는 **webpack** 이 있다. 그러나 조금 무겁다는 단점이 있어서(프로젝트가 커지면 컴파일 시간이 매우 오래걸림) 이 boilerplate를 가볍게 만들기에는 조금 과한 감이 있어서 **parcel** 을 사용하게 되었다.

![client-list]({{ site.baseurl }}/public/img/client-list.PNG)

위 사진에는 클라이언트 폴더의 자원들을 보여주고 있다. assets, components, js, scss 를 모두 번들링하여 하나의 js와 css로 컴파일하고 옵션에 따라 minify하여 출력한다. 이렇게 나타난 로컬 html,js,css를 이제 서버는 그대로 띄워주고 요청된 정보를 백그라운드에서 제공하는 역할을 해 준다.


### **Database**

![sequelize]({{ site.baseurl }}/public/img/sequelize.png)

내가 만든 boilerplate에서는 간단한 user signin 및 signup 기능이 구현되어 있다. 데이터베이스의 설정이 필요한데, 사용한 데이터베이스는 sqlite3 이고, 이를 sql문들을 다닥다닥붙여서 만드는 것이 아니라 ORM 을 사용하여 코드로 데이터베이스를 자동화 하기로 했다.

선택한 ORM은 **sequelize** 인데 설정코드를 넣어놓으면 자동으로 DB table을 생성하고 객체로서 테이블을 가져올 수 있다.

아래는 config/db.js 파일의 모습이다.

*example)*
```javascript
const _ = require('lodash');
const path = require('path');
const fs = require('fs');
const config = require('./config');
const Sequelize = require('sequelize');
const sequelize = new Sequelize(config.db.name, config.db.username, config.db.password, {
	dialect: 'sqlite',
  storage: './test.sqlite',
	// timezone: 'Asia/Seoul',
	logging: process.env.NODE_ENV == 'production' ? false : console.log
});
const modelPath = path.join(__dirname, '../app/models/');

let models = {};
fs.readdirSync(modelPath).forEach((file) => {
	let modelName = file.split('.')[0];
	models[modelName] = sequelize.import(modelPath + file);
});

Object.keys(models).forEach((modelName) => {
	if (models[modelName].associate) {
		models[modelName].associate(models);
	}
});

module.exports = _.extend(models, { Sequelize, sequelize });
```

ORM을 사용한 장점은 서버에서 javascript코드로 db를 조작하는 것이 매우 편리하다는 데에 있다. demo boilerplate를 돌려보면 사용자가 직접 관여 할 필요 없이 db가 말씀하게 작동하는 것을 볼 수 있다.

### **JWT SessionStorage**

![jwt]({{ site.baseurl }}/public/img/jwt.png)

JWT란 JSON Web Token의 줄임말이다. 기존의 많은 로그인 기능들이 세션(서버)에 물려 있던 것과 다르게, 세션이 없이 로컬PC의 토큰을 기반으로 인증되는 방법이다. facebook,twitter,github,google 등등 최근의 모던 웹서비스에서 매우 많이 사용되고 있고 또 사용법도 간단하다.

서버 기반 인증의 문제점과 토큰 기반 시스템에 대한 장단점들은 다른 블로그 및 사이트에서 정말 잘 설명을 해 놓아서 따로 설명하지 않겠다.

JWT에 대한 config 파일은 다음과 같다.

*example)*
```javascript
const jwt = require('jsonwebtoken');
const config = require('./config');

module.exports.generate = (data) => {
	return jwt.sign(data, config.session.secretKey, { expiresIn: '6h' });
};

module.exports.verify = (token) => {
	try {
		let decoded = jwt.verify(token, config.session.secretKey);
		return decoded;
	} catch(err) {
		return false;
	}
};
```

라우터가 페이지를 라우팅할 때, 헤더의 토큰을 verify하여 인증된 사용자인지 확인하고 로그인된 사용자만이 접근할 수 있는 기능을 구현 할 수 있다.

또한 SessionStorage를 구현하여 로그인된 사용자가 다시 웹페이지에 접속했을 때 인증된 토큰을 사용할 수 있도록 하였다. 마찬가지로 SessionStorage도 잘 설명된 글이 많으므로 설명은 패스한다.

아 참 그리고 password는 bcrypt 모듈을 사용해서 암호화 되도록 만들었다. DB에 들어가 있는 값을 보면 알 수 있다.


## **기타 사용된 기능**

**vuex**

![vuex]({{ site.baseurl }}/public/img/vuex.png)

vuejs 개발에 있어서 구현할 수 있는 통합 이벤트 버스 및 상태,변형 관리 시스템. 모든 공유된 데이터와 이벤트를 관리하는 컨트롤러의 역할을 할 수 있다.

**vuetify**

![vuetify]({{ site.baseurl }}/public/img/vuetify.jpg)

css로 직접 버튼 만들고 레이아웃 짜는 것은 사실 이제 조금 비효율적이다. 전부터 사람들은 부트스트랩으로 만들어진 디자인을 가져다 쓰는 방식을 택하기 시작했다.

vuejs에 특화된 css framework인 vuetify는 이쁘고 실용적인 디자인들을 선보인다. 전체적인 테마와 기능들을 이 boilerplate에 넣어서 만들었다.


## **마치며**

프론트와 백앤드를 분리하고 싶어서 시작했던 프로젝트가 나름 많은 큼직큼직한 오픈소스들의 합작이 되었다. 처음에는 이벤트 버스며 JWT며 어려운 것 투성이였는데, 만들고 나니 뿌듯하다. 이로서 웹 서비스 시대의 흐름을 조금 탔나 하고 보면 아직도 할게 많고 내일 보면 또 새로운 기술이 나와 있겠지..

남들이 올려놓은 boilerplate만 가져다가 썼는데 내가 만드는 날도 오는구나.

![compile]({{ site.baseurl }}/public/img/compile.PNG)

오픈소스를 얼마나 넣었으면 이정도 기능에 js가 1.5MB나 나오는건지..그래도 parcel 컴파일 정말 빠르다.
