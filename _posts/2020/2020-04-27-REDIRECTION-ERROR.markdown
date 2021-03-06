# 브라우저에 뜨는 에러메시지 : 리다이렉션한 횟수가 너무 많습니다

## 가장 먼저, 쿠키를 지워서 해결되는지 확인
- 크롬의 경우 브라우저 설정에서 쿠키 삭제 
- CTR + SHIFT + F5 시도

## 그 다음 해결안될 때 ( 출처 : [디스프로그래머님 블로그](https://this-programmer.com/entry/%EB%A6%AC%EB%8B%A4%EC%9D%B4%EB%A0%89%EC%85%98%ED%95%9C-%ED%9A%9F%EC%88%98%EA%B0%80-%EB%84%88%EB%AC%B4-%EB%A7%8E%EC%8A%B5%EB%8B%88%EB%8B%A4))

- 가끔씩 뭔가 뚜렷한 전조증상 없이 이 에러가 뜨는 경우가 있는데 
이유는 서버에서 링크이동이 반복해서 이뤄지게 됐을 때 브라우저가 임의로 이 에러를 일으키는 것이다. 
예를 들어 localhost라는 주소에 들어갔을 때 해당 웹페이지에 localhost/move로 리다이렉션시키는 스크립트가 짜여져 있고, 
localhost/move라는 곳으로 이동했을 때 localhost로 다시 리다이렉션시키는 스크립트가 있다면 이 오류가 뜨게 된다.

- 내가 이 오류를 만났을 땐 다음과 같은 상황이었다. 
django로 프로젝트를 제작하던 도중 로그인이 된 상태이거나 로그인을 성공하면 
redirect_to라는 GET키값의 밸류값으로 이동시키거나 혹은 home으로 이동시켜야 했는데 
request에 뭐가 담겼는지 확인하기 위해 계속 다시 login페이지로 리다이렉션을 하는 코드를 짜놨었다. 
상식적으로 생각했을 때 로그인된 상태면 login페이지가 또 보이지 않아야하므로 다른 페이지로 이동시켰어야 했는데 
위에도 말했다시피 request에 담긴 값을 확인하기 위해 계속 login페이지를 불러오게 만든 것이다. 
그렇게 계속해서 login페이지를 호출하는 로직이 완성되자(login이 된 상태면 login페이지를 불러온다 
* ∞) 내 브라우저는 "리다이렉션한 횟수가 너무 많습니다"라는 오류를 뱉음으로서 서버에 대한 무한 리다이렉션을 방지시켜줬다.

- 한번은 워드프레스로 작업했을 때였는데 ssl을 적용하는 과정이었다. 
워드프레스는 엔진 특성상 루트 url을 db에 저장해놓고 작동하는데 
ssl을 적용하고 나서 db에 있는 url을 바꾸지 않았더니 ssl로 인해 웹서버가 계속 클라이언트의 요청을 https가 달려있는 url로 보내려고 하고, 
워드프레스 엔진은 요청을 db에 저장돼있는 url인 http로 보내려고 해서 서로 각축이 일어났었는데 
그때 바로 또 "리다이렉션한 횟수가 너무 많습니다"라는 오류가 출력됐다.


- 브라우저에서 제공하는 일종의 보호장치라고 생각하면 될 것이다. 
해당 문제에 부딪혔을 때는 해당 현상이 발생한 페이지에서 redirect하는 부분을 주의깊게 살펴보면 해결이 가능할 것이다. 
그 어떤 언어나 프레임워크로 짜여진 웹이라고 해도 위 문제는 브라우저에서 저 문장만 덩그러니 나오기 때문에 디버깅하는 데 당황할 수 있다. 
당황하지 말고 리다이렉션에 관한 부분만 주의깊게 생각해보면 해결될 것이다.

## 워드프레스의 경우 좀더 자세히
(출처 : https://www.thewordcracker.com/basic/%EC%9B%8C%EB%93%9C%ED%94%84%EB%A0%88%EC%8A%A4-%EB%A6%AC%EB%94%94%EB%A0%89%EC%85%98%EC%9D%B4-%EB%84%88%EB%AC%B4-%EB%A7%8E%EC%8A%B5%EB%8B%88%EB%8B%A4-%EC%98%A4%EB%A5%98/)

### 이 오류는 보통 설정에 구성 오류가 있기 때문에 일생하지만 이보다 복잡한 경우도 있습니다. 다음의 경우에 이 오류를 볼 수 있습니다.

- 워드프레스 주소 URL과 사이트 주소 URL이 다르거나 틀린 경우

- 사이트를 리디렉트하도록 설치된 플러그인의 구성이 잘못될 경우

- `.htaccess`에 문제가 있는 경우

- 삭제된 사이트와 동일한 URL로 네트워크 내에서 새로운 사이트를 만드는 경우

- 하나의 IP에 여러 개의 사이트나 네트워크 사용

- 리디렉션 코드가 삽입된 경우

가장 일반적인 구성 오류는 워드프레스 주소(URL)와 사이트 주소(URL)를 잘못 설정하는 것입니다.

워드프레스 대시보드의 "설정" > "일반" 페이지에서 "워드프레스 주소"와 "사이트 주소"를 설정할 수 있습니다.

### 워드프레스 사이트 주소(URL)과 사이트 주소(URL)의 차이점(site_url()과 home_url() 차이)

참고로 `site_url()`이라고 하면 보통 "사이트 주소(URL)"라고 적힌 값을 의미할 것이라고 생각할 것입니다. 
하지만 실제로는 그렇지 않습니다. site_url()은 워드프레스 코어 파일이 설치되어 있는 곳으로 위의 그림에서 "워드프레스 주소(URL)"에 해당합니다. 
그러므로 워드프레스가 www.abc.com/wp에 설치되어 있다면 
"워드프레스 주소(URL)" 부분에는 www.abc.com/wp를 입력해야 합니다. home_url()은 위의 그림에서 "사이트 주소(URL)"에 설정되는 주소가 됩니다.

다소 혼란스럽죠? 
"워드프레스 주소(URL)"는 워드프레스 코어 파일이 설치되어 있는 주소로 site_url()로 호출되고, 
"사이트 주소(URL)"는 홈 주소(home_url())이다라고 구분하면 좋을 듯 합니다. (그래도 헷갈릴 것 같습니다.)

#### WP_HOME – Home 주소

- homeurl
- home_url()
- 사이트 주소 (URL)

#### WP_SITEURL – 워드프레스 코어 파일 경로 (https://codex.wordpress.org/Giving_WordPress_Its_Own_Directory)

- siteurl

- site_url()

- 워드프레스 주소 (URL)

### 일반적인 해결 방법

자 그럼, 본론으로 들어가서 어떤 경우에 오류가 나는지를 살펴봅시다. 
워드프레스 주소(URL)에 http://www.abc.com으로 설정하고 사이트 주소(URL)에 http://abc.com으로 설정하면 오류가 발생합니다.

또, 다른 경우로 서버 설정에서 www를 사용하도록 설정한 상태에서 

워드프레스 주소와 사이트 주소에는 www를 생략하면 리디렉션 오류가 발생할 수 있습니다.

그리고 하나 중요한 점은 사이트 설정에서 끝에 슬래시(/)를 절대로 넣으면 안 된다고 합니다.

```
http://www.abc.com/
```

※ 한글 도메인을 사용하는 경우에 '리디렉션한 횟수가 너무 많습니다' 오류가 발생하면 한글 도메인 주소를 퓨니코드로 변환하여 입력해보시기 바랍니다. https://www.inplaza.com/puny/index.php 페이지에서 퓨니코드로 변환할 수 있습니다.
