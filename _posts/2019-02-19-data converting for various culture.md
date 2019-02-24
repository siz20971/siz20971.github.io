---
layout: post
title: 국가, 문화권에 따른 소수점 표기법 및 유의점
categories: Development
---

글로벌 서비스를 제공하는 소프트웨어에서 데이터를 변환하는 과정에서 국가별 표기법을 고려하지 않는다면 문제 발생의 여지가 많다.

아래는 국가별로 다른 몇가지 예시이다.

1. 한국 등등
   * 1,234.45
2. 독일 등등
   * 1.234,45
3. 프랑스 등등
   * 1 234,45

```c#
// c#
float ConvertValue(string str)
{
    float result = 0;
    float.TryParse(str, out result);
    return result;
}
```

위와 같은 코드를 작성했을때, 값을 변환하는 경우 시스템의 국가 세팅에 따라 결과에 차이가 있다.

0.99 라는 값을 문자열로 읽어, 변환하는 경우 결과는 아래와 같다.

1. 한국 등등
   * 0.99 (정상)
2. 독일 등등
   * 99 (.은 1000단위 구분을 위한 문자이기에 변환은 성공)
3. 프랑스 등등
   * 0 (변환 실패 : 숫자 표기법에서 .을 사용하지 않기때문에)


c#에서는 InvariantCulture를 사용하여 문화권에 영향을 받지 않고 변환되도록 해야만 한다.

ex)
```c#
// c#
float ConvertValue(string str)
{
    float result = 0;
    float.TryParse(str, NumberStyles.Float,  CultureInfo.InvariantCulture, out val))
    return result;
}
```