---
layout : tutorials
title : 리덕스에 대한 이해
category : tutorials
summary : 이 문서는 https://medium.freecodecamp.com/why-redux-makes-sense-to-me-and-how-i-conceptualize-it-c8a3a9db15ca?ref=mybridge.co#.mw6el25e7를 번역한 내용입니다.
permalink : /tutorials/translate/understanding-redux
tags : javascript react redux flux
author : apple77y
---

![redux](https://cdn-images-1.medium.com/max/1600/1*BpaqVMW2RjQAg9cFHcX1pw.png)


리액트를 처음 배우기 시작할 때, 리액트와 관련된 기술에 대한 수많은 글을 읽었다.


특히, [이 글](https://github.com/petehunt/react-howto/blob/master/README-ko.md)이 눈에 띈다.
이 글에서는 리액트 생태계가 얼마나 복잡한지, 그리고 리액트를 사용하기 이전에 전체 생태계에 대해 알아야 하는 것에 대해 개발자들이 어떻게 느끼는지 알려주고 있다.


나는 리액트를 8개월 동안 매일 봐 왔던 사람으로서 정확히 말할 수 있는데, 지금도 나는 리액트의 전체 생태계를 거의 겉핥기식으로 이해하고 있다.


하지만 그 동안 리액트를 사용한 결과, 어느 시점에서 어떤 기술을 적절히 써먹을 수 있을지에 대한 통찰력이 생겼다. 이를테면 플럭스 아키텍처의 한 종류인 리덕스다.


> 당신은 플럭스를 언제 적용해야 할지 알 것이다. 만약 필요한지에 대해 확실치 않다면, 플럭스가 필요 없는 단계다.


이 인용문은 위에서 언급한 [이 글](https://github.com/petehunt/react-howto#learning-flux)에 있으며, 내가 리덕스에 대해 어떻게 느끼는지 아주 잘 요약한 말이다.


나는 처음부터 플럭스의 필요성에 대해 보지 않았을뿐더러, 다른 라이브러리 없이 순수 리액트만으로도 충분히 어플리케이션을 개발할 수 있다고 생각한다. 하지만 이 글에서는 리덕스의 장점에 대해 살펴볼 예정이다.


이 글에서는 왜 리덕스가 타당한지, 그리고 리덕스의 서로 다른 부분에 대해 시각적인 비유로 설명할 것이다.


## 리덕스가 타당한 이유
지난 8개월 동안 리액트를 사용하면서 어떤 형식으로 개발하려고 했었는지에 대한 설명이다.

1. 가능한 상태 값이 없는 컴포넌트로 개발하려고 했다. 왜냐하면, 이 컴포넌트는 **순수 함수 컴포넌트** 형태이기 때문이다. (순수 함수 컴포넌트: 같은 데이터를 입력할 때마다 항상 같은 UI가 반환되는 형식) 순수 함수 컴포넌트는 구조를 파악하기 쉽고, 테스트하기에도 간편하기 때문이다.

2. 컴포넌트에 상태 값을 포함할 때마다 어떤 컴포넌트에서 이 상태 값을 변경하는 지에 대해 스스로 되묻곤 했다. 바로 위에 있는 컴포넌트일지 아니면 더 높은 단계에 있는 컴포넌트일지 말이다. 왜냐하면, 상태 값이 없는 다른 컴포넌트에서도 필요로 할 수 있기 때문이다.

3. 위의 질문에 대한 대답은 리액트 어플리케이션을 개발할 때 매우 중요한 단계다. 사실 리덕스(혹은 플럭스의 다른 종류)를 사용할 때와 마찬가지로 이 질문에 대한 대답은 계속 필요하다. 왜냐하면, 이 대답으로 인해 어떤 컴포넌트가 리덕스 컨테이너 역할을 하게 될지 결정되기 때문이다.

4. 어플리케이션이 점점 커지면서 많은 컴포넌트의 상태 값이 어플리케이션 내 여기저기 분산되어 있다는 걸 느끼게 되었다. 그래서 다수의 컴포넌트에 사용될 수 있는 하나의 구조로 리팩토링할 필요성을 느끼게 되었다. 내가 여태 봐왔던 리액트의 전형적인 구조는 상태 값을 상위 컴포넌트에 전달해서, 그 값을 관리하는 부모 컴포넌트에서 상태 값을 변경하는 것이었다.

5. 이런 구조에 직면하게 될 때마다, 모든 컴포넌트의 상태 값을 최상위에 위치하는 컴포넌트로 전달하고, 자식 컴포넌트에서는 필요한 상태 값만 전달해주는 게 적절한 구조인지 되묻기 시작했다. 이러한 구조가 유용하다고 느낀 이유는 최상위 컴포넌트를 제외하고 나머지 자식 컴포넌트는 상태 값을 가지고 있지 않아도 되기 때문이다. (주의해야 할 점: 대개 당신은 아마도 어떠한 컴포넌트에 위치하든 같은 UI 상태를 가지길 원한다는 것이다.)


## 리덕스 소개
결국, 위의 고민을 통해 얻으려고 하는 것은 관심사의 분리였다.


리액트는 UI 라이브러리다. 이것은 오직 입력된 데이터를 화면에 그리는 것만 담당한다. 만약 데이터가 바뀐다면 새로운 UI만 다시 그린다.


하지만, 어플리케이션이 커지면서 상태 값과 비지니스 로직들이 여기저기 흩어지기 시작했다. 이것은 컴포넌트를 재활용하기 어렵게 만들었고, 유연성을 낮추기 시작했다.


리덕스는 어플리케이션의 컨테이너에서 데이터와 비지니스 로직을 분리하여 리액트가 순전히 뷰에만 관여할 수 있게 해준다. 리덕스는 잠재적으로 리액트 대신 다른 뷰 라이브러리로 바꿀 수 있게 해주기 때문에 어플리케이션을 더욱 유연하게 만들어준다.


## 나는 리덕스 전문가가 아니지만, 구성요소들을 시각화 해봤다.
리덕스를 처음 배울 때, 알아둬야 할 여러가지 중요 요소들이 있다. 스토어, 액션 / 액션 생성자, 그리고 리듀서 함수다. [공식 문서](http://dobbit.github.io/redux/index.html)가 아주 잘 되어있다. 그래서 이 문서를 자세히 보고 예제 코드를 직접 돌려보는 것을 강추한다. 아래에 있는 내 예제들은 이 문서를 먼저 읽는다면 이해하기 더욱 쉽다.


나는 시각적 학습자이기 때문에 나 자신에게 리덕스 개념들을 이렇게 설명했다.


![redux](https://cdn-images-1.medium.com/max/1600/1*ELf2a1L1gSkDbX6wlW3CyA.jpeg)


## 스토어 - 농구 골대
리덕스에서 전체 어플리케이션은 단 하나의 스토어만 가진다. 스토어는 자바스크립트의 기본적인 객체라고 생각하면 된다. 스토어 객체의 키는 파악하려는 어플리케이션의 상태의 이름이고, 스토어 객체의 값은 해당 상태에 일치하는 값이다.


농구 골대와 스토어에 대체 어떤 연관이 있을지 궁금해할 것 같다. 농구 경기에서 득점을 하기 위해 골대를 향해 슛을 쏘듯, 리덕스에서도 어플리케이션을 수정하기 위해서는 액션을 스토어에 보내는 방법밖에 없다. (아래 예제 참고)


만약 좀 더 자세한 기술 용어를 사용한다면, 리덕스에서 상태 값을 수정할 수 있는 유일한 방법은 스토어에 액션을 보내는 것뿐이다.


``` javascript
// 아래는 슈도 코드다.

import { createStore } from "redux";

const team1Hoop = createStore( /* 여기는 예제이므로 인자 생략 */ );

/*
 * 위에서 만든 스토어는 당신의 전체 어플리케이션 상태를 관리한다.
 * 스토어는 거대한 자바스크립트 객체 덩어리로 봐도 된다.
 * 그 객체의 키는 어플리케이션의 각 상태이고, 값은 상태에 연관된 값이다.
 *
 * 스토어에서 상태 값이 어떻게 관리되는지에 대한 실제 메커니즘은 모르지만, 내가 상상하기로는 이럴 것 같다.
 *
 * team1Hoop.__applicationState__ = {
 *   totalPoints: 0,
 *   playerList: [ list of players on team ]
 * };
 *
 */
```


## 액션 — 농구공
아직 위에서 액션이 무엇인지 깊게 설명하지 않았지만, 액션은 그냥 단순한 POJO일 뿐이다. (POJO: Plain Old JavaScript Objects, 평범한 자바스크립트 객체) 어떻게 생겼는지 코드를 통해 살펴보자.


``` javascript
/*
 * 액션은 단순 객체다.
 * 액션은 "type"이라는 속성을 반드시 가지고 있어야 되는 특정 인터페이스다.
 * 하지만 "type"을 제외하면, 필요에 따라 원하는 다른 속성을 덧붙일 수 있다.
 */

const FREE_THROW = "FREE_THROW";
const TWO_POINT_SHOT = "TWO_POINT_SHOT";

// 액션의 예
const twoPointer = {
  type: TWO_POINT_SHOT,
  payload: {
    points: 2
  }
};

// 액션의 또다른 예
const freeThrow = {
  type: FREE_THROW,
  payload: {
    points: 1
  }
};
```

어플리케이션을 전체로 놓고 봤을 때, 당신의 어플리케이션이 취할 수 있는 서로 다른 액션을 마련하고자 한다.


액션은 서술문으로 작성해야 하는 점을 알아야 한다. - 액션은 당신의 앱에서 어떠한 것들을 할 수 있는지 나타내기 때문이다. 액션은 순수 데이터다!


반복하건대, 어플리케이션 내 상태 값을 변경하기 위해서는 액션을 스토어로 쏴야 한다.


``` javascript
const team1Hoop = createStore( /* 여기는 예제이므로 인자 생략 */ );

const twoPointer = {
  type: TWO_POINT_SHOT,
  payload: {
    points: 2
  }
};

/*
 * 어플리케이션 내 상태 값을 수정하는 유일한 방법은 액션을 스토어에 액션을 보내는 것이다.
 */
team1Hoop.dispatch(twoPointer);

/*
 * 스토어 내 상태 값들은 대략 이렇게 생겼을 것이다.
 *
 * team1Hoop.__applicationState__ = {
 *   totalPoints: 2,
 *   playerList: [ list of players on team ]
 * };
 *
```


## 리듀서 — 코치와 선수

위에서 액션은 어플리케이션이 어떤 것을 할 수 있는지 나타낸다고 설명했었다. 하지만 액션은 상태 값을 "어떻게" 수정하는지에 대해서는 설명하지 않는다. 이제 리듀서에 대해 설명할 차례다.


리듀서는 두 개의 인자를 받는 순수 함수다. 첫 번째 인자로는 현재 상태 값, 두 번째로는 액션을 받는다. 반환 값으로는 다음 상태 값이다.


리듀서가 부작용이 없는 순수 함수라는 점은 아주 중요하다. 매번 같은 값을 입력할 때마다 항상 같은 반환 값을 갖는다. 순수한 함수 형태로 구성이 되어 있으면 매번 액션이 일어날 때(초기 상태 값과 액션이 구성 된 상태)마다 어떤 결과 값이 반환되는지 정확히 예측할 수 있으므로 굉장히 좋다.


매 액션마다 하나의 리듀서가 모든 상태 값을 관리하는 것은 불가능하므로 리덕스에서는 리듀서를 분리하도록 강조한다. 하나의 큰 리듀서를 작은 리듀서로 잘게 쪼개는 것은 어플리케이션 상태 값 관리하는 하나의 부분을 쪼개어서 관리한다는 것이기 때문에 좋은 의미다.


위에서 설명한 비유에 따라, 합쳐진 총괄 리듀서를 코치, 그리고 작은 리듀서를 선수로서 생각할 수 있다. 액션은 스토어를 향해 슛 하자마자, 총괄 리듀서는 그 액션을 캐치한 뒤, 쪼개진 하위 리듀서들에게 그 액션을 전달한다. 각각의 하위 리듀서는 전달받은 액션을 조사하고, 자신이 담당하고 있는 상태 값을 수정하고자 하는 것인지에 대해 판단한다. 만약 자신의 담당이라면, 새로운 상태 값을 생산한다.


여기 예제 코드를 보자.


``` javascript
// 아래는 슈도 코드다.

import { combineReducers, createStore } from "redux";

// 어플리케이션의 전체 상태 값 중 단순히 points를 관리하는 하위 리듀서
function pointsReducer(pointsState = 0, action) {
  switch (action.type) {
    case TWO_POINT_SHOT:
    case FREE_THROW:
      return pointsState + action.payload.points;
    default:
      return pointsState;
  }
}

// 어플리케이션의 전체 상태 값 중 단순히 playList를 관리하는 하위 리듀서
function playerListReducer(playerListState = [], action) {
  switch (action.type) {
    case ADD_PLAYER:
      return playerListState.concat(action.payload.player); // 리액트에서의 state는 immutable이기 때문에 직접적인 수정은 불가
    default:
      return playerListState;
  }
}

// 총괄 리듀서는 액션을 하위 리듀서로 전달한다.
const coach = combineReducer({
  points: pointsReducer,
  playerList: playerListReducer
});

const twoPointer = {
  type: TWO_POINT_SHOT,
  payload: {
    points: 2
  }
};

const team1Hoop = createStore(coach); // 스토어를 만들 때 일부분으로 리듀서를 전달한다.
team1Hoop.dispatch(twoPointer);
```


## 끝맺음
이 부분에서 아마도 이런 의문을 가질 수 있다. 하위 리듀서에서 새로운 상태 값이 생성된 이후에는 어떤 일이 일어날까?


하위 리듀서에서 새로운 상태 값이 생성된 이후, 수정된 상태 값 객체가 스토어에 저장되게 된다. 어플리케이션 상태는 단일 스토어로 존재하기 때문에, 각각의 액션이 리듀서에 퍼진 후, 새로운 상태 값이 생성되고 스토어에 저장된다.


또한 이 절차가 어떻게 실행되는지 궁금해할 수 있다. 정확히 어떻게 액션이 처음 생성될까?


리덕스는 또 다른 개념인 액션 생성자를 소개한다. 액션 생성자는 액션을 생성하고 반환하는 함수다. 액션 생성자는 리액트 컴포넌트에 종속되어 있으므로 사용자가 UI와 교류할 때마다, 액션 생성자가 실행되어 새로운 액션을 생성하고, 스토어에 액션을 보낸다.


## 결론
이 글의 목적은 리덕스의 사용법을 알려주는 것이 아니라, 시각적 학습자들을 위해 리덕스의 서로 다른 구성 요소들이 어떻게 작용하는지 시각적으료 돕기 위해서였다. 부디 이 글이 그들에게 도움이 되길 바란다.


비고: 최근에 [Dan Abramov의 비디오](https://egghead.io/lessons/javascript-redux-extracting-container-components-filterlink)를 봤는데, 그 역시 내가 말했던 순수 리액트의 한계를 언급했다. 순수 리액트로 개발을 하다보면 자식 컴포넌트에서 실제로 사용되지 않음에도 불구하고, 필히 많은 데이터를 전달할 수 밖에 없다. 그런 의미에서 이 비디오는 리덕스의 동기, 리액트의 HOC에 대해 잘 설명해주고 있다.