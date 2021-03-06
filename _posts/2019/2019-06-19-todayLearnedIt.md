---
layout : post
date : 2019-06-19 18:01
title: "Today I learned It"
tag: common
comment : true
---

# CSV 파일 validation 체크하기
  > 출처 : https://namu.wiki/w/CSV 나무위키

일반적으로 CSV파일의 무결성을 검증할 때는 한 줄의 콤마 수를 센다. 모든 줄의 콤마 수는 다 같아야 하며 더 적거나 더 많은 줄이 발견되면 오류로 판단해 걸러내는 등의 적절한 처리를 할 필요가 있다. 가장 일반적으로 발견되는 오류는 다음과 같다.
- 1. 내용에 콤마가 들어가서 한 줄의 콤마 수가 몇 개 늘어나는 경우
- 2. 줄바꿈 문자가 누락돼 한 줄의 콤마 수가 두 배로 늘어나는 경우
- 3. 내용에 줄바꿈 문자가 들어가서 두 줄 이상의 콤마 수가 정상보다 적은 경우
- 4. 줄바꿈 문자의 캐리지 리턴(CR)을 걸러내지 못해 마지막 필드의 데이터가 깨지는 경우
- 5. 따옴표가 정상적으로 닫히지 않아 임의의 필드와 레코드가 한 필드 안에 인용돼 들어간 경우
- 6. 마지막 줄의 라인 피드 누락으로 마지막 줄 데이터를 읽지 못한 경우
- 7. 첫 줄에 헤더 텍스트가 들어간 CSV를 사용할 때 첫 줄을 건너뛰지 않은 경우

# 원의 반지름 구하기

## 기본상식
* public static final double PI = 3.141592653589793D;
* 파이는 3.14, 원의 넓이는 반지름^2 * 파이

### 임포트 하는 라이브러리
> import static java.lang.Math.sqrt;

`double r = sqrt(double a);`

```
public static double sqrt(double a)

double値の正しく丸めた正の平方根を返します。特例として:

    引数がNaNまたはゼロより小さい場合、NaNが返されます。
    引数が正の無限大の場合は、正の無限大が返されます。
    引数が正のゼロまたは負のゼロの場合は、引数と同じ値が返されます。

それ以外の場合は、引数値の真の数学の平方根にもっとも近いdouble値が返されます。

パラメータ:
    a - 値。
戻り値:
    aの正の平方根。引数がNaNであるかゼロよりも小さい場合は、結果もNaN。 
```
    
* StrictMath.sqrt(a)
- double値の正しく丸めた正の平方根を返します。

# 원 그리기
> 참조 : https://today-shot.tistory.com/entry/Java-%EB%B0%B0%EC%97%B4%EC%9D%84-%EC%82%AC%EC%9A%A9%ED%95%B4-%EC%9E%85%EB%A0%A5%EB%B0%9B%EC%9D%80-%EC%A0%95%EC%88%98%EC%97%90-%EB%94%B0%EB%9D%BC-%EC%A0%90%EC%A0%90-%EC%BB%A4%EC%A7%80%EB%8A%94-%EC%9B%90-%EA%B7%B8%EB%A6%AC%EA%B8%B0-%ED%94%BC%ED%83%80%EA%B3%A0%EB%9D%BC%EC%8A%A4-%EC%A0%95%EB%A6%AC-%EC%82%AC%EC%9A%A9


