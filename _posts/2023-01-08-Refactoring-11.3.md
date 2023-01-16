---
title:  "[리펙터링] 11.3 플래그 인수 제거하기"
date:   2023-01-08 12:17:17 +9
categories: [Book]
tags: [refactoring, study]
---

# 배경

## 플래그 인수를 사용하는 함수 예시

플래그 인수란 호출되는 함수가 실행할 로직을 호출하는 쪽에서 선택하기 위해 전달하는 인수이다.

```jsx
function bookConcert(aCustomer, isPremium) {
  if (isPremium) {
    //프리미엄 예약용 로직
  } else {
    //일반 예약용 로직
  }
}
```

위의 코드같은 경우가 플래그 인수를 사용하는 함수이다.

## 플래그 인수가 될 수 있는 값들

- Boolean
  - ex) true
- enum(열거형)
  - ex) CustomerType.PREMIUM
- 문자열
  - “premium”

## 플래스 인수를 제거해야 하는 이유

플래그 인수가 있다면 호출할 수 있는 함수들이 어떤 기능을 하고, 어떻게 호출해야 하는지 이해하기 어려워지기 때문이다. 11장은 API리펙터링이다. 따라서 내가 만든 함수를 다른 사람이 호출해서 사용할 일이 많다는
것이다. 그런데 이 때 플래그 인수가 있으면 사용자가 함수가 어떤 기능을 하는지 판별하기도 어렵고, 플래그 인수에 어떤 값을 넘겨야 하는지도 알아내야 한다. 이 중에서 제일 좋지 않은 것은 Boolean 타입의
플래그 인수이다. 플래그 인수로 true를 넘긴다는 것이 어떤 의미를 가지는지 모호하기 때문이다.

## 플래그 인수 판별법

- 호출하는 곳에서 프로그램에 사용되는 데이터가 아닌 리터럴 값을 건넨다.
- 호출되는 함수는 그 인수를 다른 함수에 전달하는 데이터가 아닌 제어 흐름을 결정하는 데 사용해야 한다.

## 플래그 인수를 사용해도 되는 경우

함수 하나에서 플래그 인수를 두 개 이상 사용하면 플래그 인수를 써야 하는 합당한 근거가 될 수 있다. 왜냐하면 플래그 인수 없이 구현하려면 플래그 인수들의 가능한 조합 수만큼의 함수를 만들어야 하기 때문이다.

하지만 다른 관점에서 보자면, 플래그 인수가 둘 이상이면 함수 하나에서 너무 많은 기능을 담당하고 있다는 신호이므로 같은 로직을 조합해내는 더 간단한 함수를 만들 방법을 고민해봐야 한다.

# 예시

## 플래그 인수를 제거하고 함수를 분리하는 예시

다음은 배송일자를 계산하는 함수이다.

```jsx
//첫 번째 사용처
aShipment.deliveryDate = deliveryDate(anOrder, true);

//두 번째 사용처
aShipment.deliveryDate = deliveryDate(anOrder, false);
```

위 코드를 보면 각각 true와 false가 무엇을 의미하는지 알기 어렵다. 기능을 알기 위해서는 deliveryDate라는 함수를 들여다봐야한다.

```jsx
function deliveryDate(anOrder, isRush) {
  if (isRush) {
    let deliverTime;
    if (["MA", "CT"]).
    includes(anOrder.deliveryState)
  )
    deliveryTime = 1;
  else
    if (["NY", "NH"].includes(anOrder.deliveryState)) deliveryTime = 2;
    else deliveryTime = 3;
    return anOrder.placedOn.plusDays(1 + deliveryTime);
  } else {
    let deliveryTime;
    if (["MA", "CT", "NY"]).
    includes(anOrder.deliveryState)
  )
    deliveryTime = 2;
  else
    if (["ME", "NH"].includes(anOrder.deliveryState)) deliveryTime = 3;
    else deliveryTime = 4;
    return anOrder.placedOn.plusDays(2 + deliveryTime);
  }
}
```

위의 예시의 경우 호출하는 쪽에서 불리언 리터럴을 사용해서 어느 쪽 코드를 실행할지 결정하는 전형적인 플래그 인수를 사용하는 함수이다. 따라서 이 경우 10.1절의 조건문 분해하기를 적용해 아래와 같이함수를 분리하는
것이 좋다.

```jsx
function rushDeliveryDate(anOrder) {
  let deliverTime;
  if (["MA", "CT"]).
  includes(anOrder.deliveryState)
)
  deliveryTime = 1;
else
  if (["NY", "NH"].includes(anOrder.deliveryState)) deliveryTime = 2;
  else deliveryTime = 3;
  return anOrder.placedOn.plusDays(1 + deliveryTime);
}

function regularDeliveryDate(anOrder) {
  let deliveryTime;
  if (["MA", "CT", "NY"]).
  includes(anOrder.deliveryState)
)
  deliveryTime = 2;
else
  if (["ME", "NH"].includes(anOrder.deliveryState)) deliveryTime = 3;
  else deliveryTime = 4;
  return anOrder.placedOn.plusDays(2 + deliveryTime);
}
```

위 코드처럼 함수를 분리한 후 호출문은 아래와 같이 변경하면 된다.

```jsx
//첫 번째 사용처
aShipment.deliveryDate = rushDeliveryDate(anOrder);

//두 번째 사용처
aShipment.deliveryDate = regularDeliveryDate(anOrder);
```

# 래핑 함수로 처리하는 예시

만약 함수 내에서 플래그 인수를 까다로운 조건으로 사용한다면 함수를 분리하는 것이 어려울 수 있다.
다음은 까다로운 버전의 deliveryDate()이다.

```jsx
function deliveryDate(anOrder, isRush) {
  let result;
  let deliveryTime;
  if (anOrder.deliveryState === "MA" || anOrder.deliveryState === "CT")
    deliveryTime = isRush ? 1 : 2;
  else if (anOrder.deliveryState === "NY" || anOrder.deliveryState === "NH") {
    deliveryTime = 2;
    if (anOrder.deliveryState === "NH" && !isRush)
      deliveryTime = 3;
  } else if (isRush)
    deliveryTime = 3;
  else if (anOrder.deliveryState == "ME")
    deliveryTIme = 3;
  else
    deliveryTime = 4;
  result = anOrder.placedOn.plusDays(2 + deliveryTime);
  if (isRush) result = result.minusDays(1);
  return result;
}
```

위의 코드 같은 경우 isRush를 최상위 분배 조건으로 뽑아내는 것이 큰 일이 될 수 있으므로 래핑 함수를 사용해서 플래그 인수를 제거할 수 있다. 래핑 함수는 다음과 같이 작성하면 된다.

```jsx
function rushDeliveryDate(anOrder) {
  return deliveryDate(anOrder, true);
}

function regularDeliveryDate(anOrder) {
  return deliveryDate(anOrder, false);
}
```

래핑 함수로 처리한 경우 원본 함수인 `deliveryDate` 의 가시범위를 제한하거나, 함수 이름을 `deliveryDateHelperOnly` 같은 것으로 바꿔서 직접 호출하지 말라는 뜻을 명시하면 좋다.
