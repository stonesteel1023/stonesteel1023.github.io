# [스크랩]ASP .NET으로 마이그레이션: 주요 고려 사항 

Jim Kieley 
Microsoft Corporation 

-- Jim Kieley는 Microsoft Consulting Services 팀의 수석 컨설턴트입니다. .NET 개시 이후로 Jim은 Visual Studio 팀 및 .NET을 초기에 채택해서 사용하고 있는 고객과 밀접하게 일하면서 ASP .NET과 .NET Frameworks 응용 프로그램을 만들었습니다. jkieley@microsoft.com에서 Jim을 만날 수 있습니다. 

## 요약
- 이 문서에서는 기존의 ASP 응용 프로그램을 가능한 한 효율적이고 빠르게 ASP .NET 환경으로 옮기기 위해 고려해야 할 기본 사항에 대해 알아 봅니다(18페이지/인쇄 페이지 기준). 

## 소개 
Microsoft® ASP .NET 디자이너가 ASP 응용 프로그램과의 호환성을 유지할 수 있도록 잘 처리했더라도 ASP의 웹 응용 프로그램을 ASP .NET으로 옮기기 전에 알아야 할 중요한 사항이 몇 가지 있습니다. .NET 플랫폼과 ASP .NET에서 변경하거나 소개된 기술을 완전히 이해해야 이 과정을 쉽게 진행할 수 있습니다. 

이 문서에서는 ASP 응용 프로그램을 준비하여 ASP.NET 환경에서 실행하기 위한 작업을 제대로 이해할 수 있도록 변경된 몇 가지 부분에 대해 설명합니다. 동시에 기존의 응용 프로그램을 향상시키는데 활용할 수 있는 ASP .NET의 새로운 기능에 대해서도 설명합니다. 그러나 ASP .NET의 모든 새로운 기능에 대해 광범위하게 다루는 것은 아닙니다. 대신 성공적으로 마이그레이션하기 위해 알아야 할 부분에 초점을 맞춥니다. 

ASP 응용 프로그램의 대부분은 Microsoft® Visual Basic® Scripting Edition(VBScript)을 사용하므로 Visual Basic .NET을 사용하여 ASP .NET으로 마이그레이션하는 방법을 선택한다고 가정합니다. 요구 사항은 아니지만 마이그레이션함과 동시에 언어를 변경하는 데는 부수적인 노력이 필요하며 대부분 설계와 구조상의 변화도 포함합니다. 

공존 
호환성과 마이그레이션에 관한 문제를 토론하기 전에 ASP와 ASP .NET이 공존할 수 있는 방법을 이해해야 합니다. ASP와 ASP .NET 응용 프로그램 모두는 서로에게 잘못된 영향을 주지 않고 한 서버에서 병행하여 실행할 수 있습니다. 그 이유는 기본적으로 두 기술 간에 별도의 파일 확장명(.asp와 .aspx)과 별도의 구성 모델(메타베이스/레지스트리와 XML 기반 구성 파일)을 사용하기 때문입니다. 두 시스템은 완전히 별도의 처리 엔진을 가지고 있습니다. 

하나의 응용 프로그램에서 한 부분은 ASP를 실행하고 다른 부분은 ASP .NET을 실행하는 것이 가능합니다. 이것은 급속하게 변화하는 큰 사이트를 한번에 한 부분씩 ASP .NET으로 옮겨야 할 경우에 매우 유용합니다. 전체 사이트를 한번에 이식하고 구축하는 것이 더 낫다고 생각할 수도 있습니다. 웹 응용 프로그램의 특정 클래스일 경우 그럴 수도 있지만 사이트 컨텐트와 표시의 다양한 크기, 복잡성 및 빠른 진화 때문에 용이하지 않은 사이트가 많다고 생각합니다. 무엇보다도 유익한 웹 사이트의 경우에는 새로운 기술 구현을 계속할 수 있도록 요금을 지불하는 사람들이 있으므로 필요한 부분을 이 최신 기술로 옮길 수 있습니다. 또한, 장기적인 투자로 ASP .NET으로 옮기는 것을 추진 중인 경우에는 이 기회를 이용하여 구조와 설계상에서 여러 가지를 향상시킬 수 있습니다. 이러한 상황에서는 단계별 접근법을 사용하여 공존하는 것이 절대적으로 필요합니다. 

## 호환성 문제 
응용 프로그램을 ASP .NET으로 마이그레이션하는 것이 쉽지 않지만 그렇게 어려운 것도 아닙니다. ASP .NET은 ASP와 호환이 매우 잘 됩니다. 이것은 ASP .NET이 ASP의 전체 오버홀이라는 사실을 보여줍니다. ASP .NET 디자이너의 초기 목표는 ASP와 100% 역호환되는 것이었지만 이후 장거리용 플랫폼을 향상시키는 것으로 목표를 낮추어야 했습니다. 그러나 이렇게 바꿈으로써 더 좋게 되었고 구현 작업도 많이 할 필요가 없게 되었습니다. 실제 변경 사항은 다음과 같이 분류할 수 있습니다. 

핵심 API 변경 사항 
구조상 변경 사항 
Visual Basic 언어 변경 사항 
COM 관련 변경 사항 
응용 프로그램 구성 변경 사항 
상태 관리 문제 
보안 관련 변경 사항 
데이터 액세스 
각 부분에 대해 자세하게 설명하겠습니다. 

## 핵심 API 변경 사항 
ASP의 핵심 API는 몇 개의 기본 개체(Request, Response, Server 등) 및 관련된 해당 메서드로 구성됩니다. 약간 변경된 사항을 제외하고 이러한 API는 ASP .NET에서도 계속 제대로 작동합니다. 모든 변경 사항은 Request 개체와 관련되어 있으며 표 1에 나타나 있습니다. 

[표 1] API 변경 사항 

- 메서드 변경 

```
`Request(항목)`  이 메서드는 ASP에서 문자열 배열을 반환하고 ASP .NET에서는 NameValueCollection을 반환합니다.  
`Request.QueryString(항목)`  이 메서드는 ASP에서 문자열 배열을 반환하고 ASP .NET에서는 NameValueCollection을 반환합니다.  
`Request.Form(항목)`  이 메서드는 ASP에서 문자열 배열을 반환하고 ASP .NET에서는 NameValueCollection을 반환합니다.  
```

변경 사항은 포함된 모든 메서드에 대해 기본적으로 동일하다는 것을 알 수 있습니다. 

액세스 중인 항목이 지정한 키에 대해 하나의 값만 포함하는 경우 코드를 수정할 필요가 없습니다. 그러나 지정된 키에 대해 여러 개의 값이 있는 경우 다른 메서드를 사용하여 값 컬렉션을 반환해야 합니니다. 또한, Visual Basic .NET에서는 컬렉션이 0을 기초로 하지만 VBScript에서는 1을 기초로 합니다. 

예를 들어, ASP에서 http://localhost/myweb/valuetest.asp?values=10&;values=20에 대한 요청의 각 쿼리 문자열 값은 다음과 같이 액세스됩니다. 
```
<%
   '10 출력
   Response.Write Request.QueryString("values")(1)

   '20 출력
   Response.Write Request.QueryString("values")(2)
%>
```
ASP .NET에서는 원하는 실제 항목을 가져오기 전에 QueryString 속성이 Values 컬렉션을 검색할 NameValueCollection 개체를 반환합니다. 컬렉션의 첫번째 항목은 1이 아니라 0이라는 인덱스를 사용하여 검색됩니다. 
```
<%
   '10 출력
   Response.Write (Request.QueryString.GetValues("values")(0))

   '20출력
   Response.Write (Request.QueryString.GetValues("values")(1))
%>
```
ASP와 ASP .NET 모두에서 다음 코드가 동일하게 작동합니다. 
```
<%
   '10과 20 출력
   Response.Write (Request.QueryString("values"))
%>
```

## 구조상 변경 사항 
구조상 변경 사항은 Active Server Pages의 레이아웃과 코드 스타일에 영향을 미칩니다. ASP .NET에서 코드가 제대로 작동하도록 하려면 다음 사항을 알아야 합니다. 

코드 블록: 함수 및 변수 선언 
ASP에서는 코드 구분 기호 사이에 서브루틴과 전역 변수를 선언할 수 있습니다. 
```
<%
   Dim X
   Dim str
   Sub MySub()
      Response.Write "이것은 문자열입니다."
    End Sub  
%>
```
그러나 ASP .NET에서는 허용되지 않습니다. 대신 모든 함수와 변수를 <script> 블록 안에 선언해야 합니다. 
```
<script language = "vb" runat = "server">
   Dim str As String
   Dim x, y As Integer

   Function Add(I As Integer, J As Integer) As Integer
      Return (I + J)
   End Function
</script> 
```

프로그래밍 언어 혼합 
ASP에서는 기본적으로 프로그래밍 언어로 VBScript 또는 Microsoft® JScript® 중에서 선택할 수 있습니다. 같은 페이지에서 스크립트 블록을 자유롭게 혼합하고 일치시킬 수 있습니다. 

ASP .NET에서는 현재 세 가지 옵션이 있습니다. C#, Visual Basic .NET 또는 JScript를 사용할 수 있습니다. VBScript이 아니라 Visual Basic .NET이라는 점에 유의하십시오. VBScript는 .NET 플랫폼에 존재하지 않기 때문입니다. VBScript는 Visual Basic .NET에 완전히 포함되었습니다. 이러한 언어 중에서 선택할 수 있지만 ASP에서처럼 같은 페이지에서 언어를 혼합할 수 없다는 점에 유의하십시오. 같은 응용 프로그램의 Page2.aspx에 Visual Basic .NET 코드를 포함하면서 Page1.aspx에 C# 코드를 포함할 수 있습니다. 다만 이러한 코드를 단일 페이지에 함께 혼합할 수 없을 뿐입니다. 

새 페이지 지시문 
ASP에서는 같은 구분 기호 블록 안의 페이지 첫번째 줄에 모든 지시문을 두어야 합니다. 예를 들면 다음과 같습니다. 
```
<%LANGUAGE="VBSCRIPT" CODEPAGE="932"%> 
```
ASP .NET에서는 다음과 같이 Language 지시문을 Page 지시문과 함께 두어야 합니다. 
```
<%@Page Language="VB" CodePage="932"%>
<%@QutputCache Duration="60" VaryByParam="none" %> 
```
지시문 줄을 필요한 만큼 가질 수 있습니다. .apsx 파일에서는 지시문을 어느 곳에나 두어도 좋지만 일반적으로 파일의 시작 부분에 둡니다. 

ASP .NET에는 여러 가지 새로운 지시문이 추가되었습니다. 이러한 지시문이 응용 프로그램에 얼마나 유용한지 ASP .NET 설명서에서 확인해 보십시오. 

Render 함수 사용 불가 
개발자들은 ASP에서 "Render 함수"라는 것을 사용하여 유용한 작업을 수행할 수 있다는 것을 발견했습니다. Render 함수는 기본적으로 본문에 포함된 HTML 청크가 있는 서브루틴입니다. 예를 들면 다음과 같습니다. 
```
<%Sub RenderMe()
%>
<H3> 이것은 렌더링될 HTML 텍스트입니다.  </H3>
<%End Sub
RenderMe
%>
```
이러한 함수 유형을 사용하여 유용한 작업을 수행할 수 있지만 ASP .NET에서는 이 코드 유형이 더 이상 허용되지 않습니다. 다음과 같이 하면 더 향상될 수 있습니다. 이와 같이 코드와 HTML을 혼합하고 일치시키려고 할 때 갑자기 함수가 읽을 수도 관리할 수 없게 되는 것을 본 적이 있을 것입니다. ASP .NET에서 이 작업을 가장 간단히 수행하려면 다음과 같이 HTML 출력을 Response.Write 호출로 바꾸면 됩니다. 
```
<script language="vb" runat="server">
   Sub RenderMe()
      Response.Write("<H3> 이것은 렌더링될 HTML 텍스트입니다.  </H3>")
   End Sub
</script>

<%
   Call RenderMe()
%>
```
이것은 "가장 간단한 방법"일 뿐이며 반드시 가장 좋은 방법은 아닙니다. 렌더링 코드의 복잡성과 크기에 따라 사용자 정의 웹 제어를 사용하는데 유용할 수 있으며 HTML 속성을 프로그래밍 방식으로 설정하고 실제로 컨텐트에서 코드를 분리할 수 있습니다. 이렇게 하면 코드를 읽기가 훨씬 더 쉬워집니다. 

## Visual Basic 언어 변경 사항 
앞에서 말한 바와 같이 VBScript는 보다 완전하고 강력한 Visual Basic .NET과 반대됩니다. 이 절에서는 Visual Basic 언어 변경 사항과 관련하여 발생하기 쉬운 일부 문제에 대해 설명하겠습니다. 그러나 Visual Basic의 모든 변경 사항에 대해 상세하게 나열하는 것은 아닙니다. 대신 ASP/VBScript 프로그래머가 Visual Basic .NET을 사용하여 ASP .NET으로 옮길 때 발생하기 쉬운 항목에 초점을 맞춘 것입니다. 언어에 대한 모든 변경 사항에 대한 전체 목록은 Visual Basic .NET 설명서를 참조하십시오. 

Variant 데이터 유형 사용 안함 
우리는 이것을 알고 있고 이것을 좋아하며 또한 이것을 버리고 싶습니다. 물론 VARIANT 데이터 유형을 말하는 것입니다. VARIANT는 .NET의 일부가 아니므로 Visual Basic .NET에서 지원되지 않습니다. 즉, 모든 ASP 변수가 VARIANT 유형에서 Object 유형으로 바뀌고 있음을 의미합니다. 필요에 따라 응용 프로그램에 사용된 대부분의 변수를 해당하는 기본 유형으로 변경할 수 있으며 변경해야 합니다. 실제로 변수가 Visual Basic 용어의 object유형인 경우 명시적으로 ASP .NET의 Object 유형으로 선언하기만 하면 됩니다. 

Visual Basic 데이터 유형 
특별히 주의해야 할 VARIANT 유형은 Visual Basic에서 Date 유형으로 참조되는 VT_DATE입니다. Visual Basic에서 Date는 4바이트를 사용하는 실수(Double) 형식으로 저장됩니다. Visual Basic .NET에서는 Date가 8바이트 정수로 표시되는 Common Language Runtime DateTime 유형을 사용합니다. 

ASP에서는 모든 것이 VARIANT이므로 사용 방법에 따라 지정한 Date 변수가 컴파일되고 작업을 계속하게 됩니다. 그러나 원본 유형이 변경되었기 때문에 변수를 사용하여 특정 작업을 수행할 때 예기치 않은 문제가 발생할 수 있습니다. 데이터 값을 정수(Long) 값으로 COM 개체에 전달하거나 CLng을 사용하여 데이터 유형에서 특정 캐스팅 작업을 수행할 수 있다는 점에 유의하십시오. 

현재 기본값은 Option Explicit 
ASP에서는 Option Explicit 키워드를 사용할 수 있지만 기본값은 아닙니다. Visual Basic .NET에서는 이것이 바뀌었습니다. Option Explicit가 현재 기본값이므로 모든 변수를 선언해야 합니다. 보다 더 확실하게 설정을 Option Strict으로 변경하는 것이 좋습니다. 이렇게 하면 모든 변수를 특정 데이터 유형으로 선언할 수 있습니다. 추가 작업처럼 보이지만 반드시 수행해야 합니다. 그렇지 않을 경우에는 선언하지 않은 모든 변수가 Object 유형으로 되기 때문에 코드의 효율성이 떨어집니다. 대부분의 암시적 변환이 계속 작동되지만 모든 변수를 원하는 유형으로 명시적으로 선언하는 것이 더 좋고 안전합니다. 

LET과 SET이 지원되지 않음 
MyObj1 = MyObj2와 같이 개체를 직접 다른 개체에 할당할 수 있습니다. SET이나 LET 문은 더 이상 사용되지 않습니다. 이러한 문을 사용하는 경우 제거해야 합니다. 

괄호를 사용한 메서드 호출 
ASP에서는 아래와 같이 괄호를 사용하지 않고도 개체에서 메서드를 자유롭게 호출할 수 있습니다. 
```
Sub WriteData()
   Response.Write "이것은 데이터입니다."
End Sub
WriteData 
```
그러나 ASP .NET에서는 매개 변수가 없는 메서드는 물론 모든 호출에 괄호를 사용해야 합니다. 아래 예제처럼 코드를 작성하면 ASP와 ASP .NET에서 기능을 제대로 수행할 수 있습니다. 
```
Sub WriteData()
   Response.Write("이것은 데이터입니다.")
End Sub
Call WriteData()
```
현재 기본값은 ByVal 
Visual Basic에서는 기본적으로 모든 매개 변수 인수가 참조 또는 ByRef에 의해 전달됩니다. Visual Basic .NET에서는 기본적으로 모든 변수가 값 또는 ByVal에 의해 전달되도록 변경되었습니다. "ByRef" 동작을 사용하려는 경우에는 다음과 같이 매개 변수 앞에 ByRef 키워드를 명시적으로 사용해야 합니다. 
```
Sub MyByRefSub (ByRef Value)
   Value = 53;
End Sub
```
다음 사항에 주의해야 합니다. 코드를 ASP .NET으로 옮길 때 메서드 호출에 사용된 각 매개 변수를 두, 세 번 확인하여 이 변경 사항이 실제로 원하는 것인지 확인하는 것이 좋습니다. 이 중 일부를 변경해야 할 것입니다. 

더 이상 기본 속성을 사용하지 않음 
Visual Basic .NET에는 기본 속성 개념이 없습니다. 개체 중 하나가 제공하는 기본 속성에 의존하는 ASP 코드가 있을 경우에는 다음 코드와 같이 원하는 속성을 명시적으로 참조하도록 변경해야 합니다. 
```
'ASP 구문(Column Value 속성을 암시적으로 가져오기)
Set Conn = Server.CreateObject("ADODB.Connection")
Conn.Open("TestDB")
Set RS = Conn.Execute("Select * from Products")
Response.Write RS("Name")

'ASP.NET 구문(Column Value 속성을 명시적으로 가져오기)
Conn = Server.CreateObject("ADODB.Connection")
Conn.Open("TestDB")
RS = Conn.Execute("Select * from Products")
Response.Write (RS("Name").Value)
```
데이터 유형 변경 사항 
Visual Basic .NET에서 정수 값은 현재 32비트이며 정수(Long) 유형은 64비트로 되었습니다. 

ASP .NET에서 COM 개체로 메서드를 불러오거나 Microsoft® Win32®를 호출할 때 API가 사용자 정의 Visual Basic 구성 요소 안으로 호출하는 경우 문제가 발생할 수 있습니다. 값을 정확하게 전달하거나 캐스팅할 수 있도록 하는 데 필요한 실제 데이터 유형에 특별히 주의해야 합니다. 

구조화된 예외 처리 
Visual Basic .NET에서도 허용되는 익숙한 On Error Resume Next와 On Error Goto 오류 처리 기술을 사용할 수 있지만 가장 좋은 방법은 아닙니다. Visual Basic에서는 이제 Try, Catch, Finally 키워드를 사용하여 완전히 구조화된 예외 처리를 하고 있습니다. 가능하다면 응용 프로그램 오류를 처리하는 데 보다 강력하고 일관성 있는 메카니즘을 사용할 수 있는 이 새로운 모델로 바꿔야 합니다. 

COM 관련 변경 사항 
.NET Framework와 ASP .NET 소개에서 COM은 전혀 변경되지 않았습니다. 그러나 이것이 ASP .NET에서 COM 개체를 사용할 때 작동 방법 및 COM 개체에 대해 아무런 문제가 없다 것은 아닙니다. 반드시 알아야 할 두 가지 기본 사항이 있습니다. 

스레드 모델 변경 사항 
ASP .NET 스레드 모델은 여러 스레드 아파트(MTA)입니다. 즉, 사용 중인 구성 요소가 단일 스레드 아파트(STA)에 맞게 작성된 것이라도 ASP .NET에서 특별히 주의하지 않으면 제대로 수행하거나 기능할 수 없게 됩니다. 이것은 Visual Basic 6.0 및 이전 버전을 사용하여 작성된 모든 COM 구성 요소를 포함하지만 여기에만 제한된 것은 아닙니다. 

ASPCOMPAT 속성 
코드를 변경하지 않고도 STA 구성 요소를 사용할 수 있게 되어 다행이라고 생각할 것입니다. 사용자가 해야 할 일은 호환성 속성 aspcompat=true를 ASP .NET 페이지의 <%@Page> 태그에 포함시키는 것입니다. 예를 들면 <%@Page aspcompat=true Language=VB%>입니다. 이 속성을 사용하면 페이지가 STA 모드에서 실행되므로 구성 요소가 계속 제대로 작동하는지 확인합니다. 이 태그를 지정하지 않고 STA 구성 요소를 사용하려고 하면 실행 시 예외가 발생합니다. 

이 속성을 true로 설정하면 관리되지 않는 ASP 내장 개체에 액세스해야 하는 COM+ 1.0 구성 요소를 페이지에서 호출할 수 있습니다. 이러한 구성 요소는 ObjectContext 개체를 통해 액세스할 수 있습니다. 

이 태그를 true로 설정하면 성능이 조금 떨어집니다. 따라서 꼭 필요한 경우에만 수행하는 것이 좋습니다. 

초기 바인딩과 지연 바인딩 
ASP에서 COM 개체에 대한 모든 호출은 IDispatch 인터페이스를 통해 실행됩니다. 이것은 실제 개체에 대한 호출이 실행 시 IDispatch를 통해 간접적으로 처리되므로 "지연 바인딩"이라고 합니다. ASP .NET에서도 원하는 경우 이 유형으로 구성 요소를 호출할 수 있습니다. 
```
Dim Obj As Object
Obj = Server.CreateObject("ProgID")
Obj.MyMethodCall
```
구성 요소를 액세스할 때 이 방법을 사용할 수 있지만 선호하지는 않습니다. ASP .NET에서는 초기 바인딩을 활용하여 다음과 같이 개체를 직접 만들 수 있습니다. 
```
Dim Obj As New MyObject
MyObject.MyMethodCall()
```
초기 바인딩은 모든 형식을 지원하는 구성 요소와 상호 작용을 할 수 있습니다. COM 구성 요소에서 초기 바인딩을 활용하려면 Visual Basic 6.0 프로젝트에 COM 참조를 추가하는 것과 같은 방법으로 프로젝트에 참조를 추가해야 합니다. Visual Studio .NET을 사용할 경우, COM 구성 요소 위쪽에 관리된 프록시 개체가 관련 장면에 만들어지므로 COM 구성 요소를 .NET 구성 요소처럼 직접 다룰 수 있습니다. 

이 경우 성능이 매우 높아집니다. 프록시 개체 때문에 추가 계층을 도입하였으므로 COM 상호 운용성을 사용할 때 관련된 약간의 오버헤드가 있습니다. 그러나 대부분의 경우, 발생하는 상호 작용에 대한 실제 CPU 지침이 간접적인 IDispatch 호출에 필요한 것보다 여전히 실제로 적기 때문에 별로 중요하지 않습니다. 잃는 것보다 얻는 것이 더 많습니다. 물론 이상적인 상황에서는 새로 작성되고 관리된 개체를 사용할 수 있지만 여러 해 동안 COM 구성 요소에 투자 해야 하기 때문에 항상 바로 사용할 수 있는 아닙니다. 

OnStartPage와 OnEndPage 메서드 
레거시 OnStartPage와 OnEndPage 메서드 사용에 대해서도 고려해야 합니다. 이러한 메서드에 의존하여 ASP 기본 개체를 액세스하는 경우에는 ASPCOMPAT 지시문과 Server.CreateObject를 사용하여 아래와 같이 초기 바운드 방법으로 구성 요소를 만들어야 합니다. 
```
Dim Obj As MyObj
Obj = Server.CreateObject(MyObj)
Obj.MyMethodCall()
```
"ProgID" 대신 초기 바운드 방법으로 실제 유형을 사용했다는 점에 유의하십시오. 이렇게 하려면 Visual Studio 프로젝트에서 COM 구성 요소에 대한 참조를 추가해야 초기 바운드 래퍼 클래스가 만들어집니다. 이 방법은 Server.CreateObject를 계속 사용해야 하는 경우에만 수행해야 합니다. 

COM 요약 
표 2는 COM 구성 요소를 가능한 한 효율적으로 사용하기 위해 수행해야 할 사항에 대한 요약입니다. 

[표 2] 레거시 COM 개체에 대한 ASP .NET 설정 

COM 구성 요소 유형/메서드 ASP .NET 설정/절차 
사용자 정의 STA(Visual Basic 구성 요소 또는 "Apartment"로 표시된 다른 구성 요소)  ASPCOMPAT를 사용하고 초기 바인딩을 사용  
사용자 정의 MTA(ATL 또는 "Both"나 "Free"로 표시된 사용자 정의 COM 구성 요소)  ASPCOMPAT를 사용하지 않고 초기 바인딩를 사용  
기본 개체(ObjectContext를 통해 액세스)  ASPCOMPAT를 사용하고 초기 바인딩을 사용  
OnStartPage, OnEndPage  ASPCOMPAT를 사용하고 Server.CreateObject(Type)를 사용  

이러한 설정은 구성 요소가 COM+로 구축되었는지 여부와 관계 없이 적용됩니다. 

응용 프로그램 구성 변경 사항 
ASP에서는 모든 웹 응용 프로그램 구성 정보가 시스템 레지스트리와 IIS 메타베이스에 저장됩니다. 서버에 올바른 관리 도구가 종종 설치되어 있지 않기 때문에 설정을 보거나 수정하기가 매우 어렵습니다. ASP .NET은 간단하며 읽을 수 있는 XML 파일을 바탕으로 완전히 새로운 구성 모델을 도입했습니니다. 각 ASP .NET 응용 프로그램마다 주 응용 프로그램 디렉토리에 있는 고유의 Web.Config 파일이 있습니다. 여기에서 웹 응용 프로그램의 사용자 정의 구성, 동작, 보안을 제어합니다. 

여러분도 마찬가지로 인터넷 서비스 관리자 스냅인으로 이동하여 ASP .NET 응용 프로그램에 대한 설정을 조사하고 변경하고 싶을 것입니다. 그러나, 이제 구성 모델이 두 가지로 완전히 구분된다는 점에 유의하십시오. 일부 보안 설정을 제외하고 IIS 관리 도구를 사용하여 만든 다른 모든 설정의 대부분은 ASP .NET 응용 프로그램에서 무시됩니다. 구성 설정을 Web.Config 파일에 배치해야 합니다. 

.NET에서는 응용 프로그램 구성 자체가 하나의 문서이므로 여기에서는 자세하게 설명하지 않습니다. 표 3은 파일에 설정할 수 있는 보다 흥미로운 구성에 대한 것입니다. 이외에도 더 많은 것들이 있다는 것을 명심하십시오. 

[표 3] Web.Config 설정 예제 

설정 설명 
<appSettings>  사용자 정의 응용 프로그램 설정을 구성합니다.  
<authentication>  ASP .NET 인증 지원을 구성합니다.  
<pages>  페이지별 구성 설정을 식별합니다.  
<processModel>  IIS 시스템에서 ASP .NET 프로세스 모델 설정을 구성합니다.  
<sessionState>  세션 상태 옵션을 지정합니다.  

이러한 설정에 대한 프로그래밍 방식 액세스를 간소화하는 .NET 기본 클래스 라이브러리에서 사용할 수 있는 클래스가 있습니다. 

## 상태 관리 
응용 프로그램에서 Session이나 Application 기본 개체를 사용하여 상태 정보를 저장하는 경우에는 ASP .NET에서도 아무 문제 없이 사용할 수 있습니다. 이러한 장점이 추가됨에 따라 상태 저장소 위치에 대한 옵션이 두 개 더 생겼습니다. 

상태 관리 옵션 
ASP .NET에 마침내 단일 웹 서버를 넘어서 웹 그룹을 통해 상태 관리를 지원하는 상태 저장소 모델에 대한 옵션이 추가되었습니다. 

다음과 같이 web.config 파일의 <sessionState> 섹션에서 상태 관리 옵션을 구성합니다. 
```
<sessionState 
   mode="Inproc" 
   stateConnectionString="tcpip=127.0.0.1:42424" 
   sqlConnectionString="data source=127.0.0.1;user id=sa;password="    cookieless="false" 
      timeout="20"
/>
```
모드 속성은 상태 정보를 저장할 위치를 지정합니다. 옵션으로 Inproc, StateServer, SqlServer 또는 Off가 있습니다. 

[표 4] 세션 상태 저장소 정보 

옵션 설명 
Inproc  세션 상태가 이 서버에 로컬로 저장됩니다(ASP 스타일).  
StateServer  세션 상태가 원격 또는 잠정적으로 로컬에 위치한 상태 서비스 프로세스에 저장됩니다.  
SqlServer  세션 상태가 SQL Server 데이터베이스에 저장됩니다.  
Off  세션 상태가 해제됩니다.  

이러한 다른 옵션 중 하나를 사용하는 경우 StateConnectionString과 sqlConnectionString이 반드시 필요합니다. 응용 프로그램마다 하나의 저장소 옵션을 사용할 수 있습니다. 

## COM 구성 요소 저장 
Session이나 Application 개체의 레거시 COM 구성 요소에 대한 참조를 저장하는 데 의존하는 경우에는 응용 프로그램 내에서 새로운 상태 저장소 메커니즘인 StateServer나 SqlServer를 사용할 수 없습니다. 이 경우 Inproc를 사용해야 합니다. .NET에서 연속적으로 자체 나열될 수 있는 개체가 필요하지만 COM 구성 요소는 할 수 없기 때문입니다. 반면에 새로 만드는 관리된 구성 요소는 상대적으로 쉽게 연속적으로 나열할 수 있으므로 새 상태 저장소 모델을 사용할 수 있습니다. 

성능 
성능을 높이기 위해서는 반드시 해야 할 일이 있습니다. 대부분의 경우에서 Inproc의 성능이 제일 높고 그 다음으로 StateServer와 SqlServer 순입니다. 선택한 옵션이 성능 목표와 일치하는지 확인하려면 응용 프로그램으로 자체 테스트를 수행해야 합니다. 

ASP와 ASP .NET 간에 상태 공유 
응용 프로그램에 ASP와 ASP .NET 페이지 모두를 포함할 수 있다고 해도 기본 Session이나 Application 개체에 저장된 상태 변수를 공유할 수 없다는 점에 유의해야 합니다. 응용 프로그램이 완전히 마이그레이션될 때까지 이 정보를 양 시스템에 복제하거나 사용자 정의 솔루션을 사용해야 합니다. Session과 Application 개체를 가능하면 사용하지 않는 것이 좋습니다. 반면에 이러한 객체를 광범위하게 사용하는 경우 우선 주의하면서 상태 공유를 위한 단기적인 사용자 정의 솔루션을 사용해야 합니다. 

## 보안 관련 변경 사항 
보안은 상당히 주의를 기울여야 할 부분입니다. 여기에서는 ASP .NET 보안 시스템의 개요에 대해 간단하게 설명하겠습니다. 자세한 내용은 ASP .NET 보안 설명서를 참조하십시오. 

ASP .NET 보안은 기본적으로 web.config 파일 보안 섹션의 설정에서 조정됩니다. ASP .NET은 IIS와 제휴하여 응용 프로그램에 대한 완전한 보안 모델을 제공합니다. IIS 보안 설정은 실제로 시행되고 ASP에서와 유사한 방법으로 ASP .NET 응용 프로그램에 적용되는 응용 프로그램 설정의 일부입니다. 물론 여러 추가적인 향상된 기능이 있습니다. 

인증 
ASP .NET은 인증을 위해 표 5에 표시된 다른 옵션을 지원합니다. 

[표 5] ASP .NET 인증 옵션 

유형 설명 
Windows  ASP .NET은 Windows 인증을 사용합니다.  
Forms  쿠기 기반 사용자 정의 로그인 양식입니다.  
Passport  Passport Service가 제공되는 외부 Microsoft입니다.  
None  인증이 수행되지 않습니다.  

새로운 Passport 인증 옵션을 제외하고 ASP와 같은 옵션을 사용합니다. 예를 들어, 다음 구성 섹션에서는 응용 프로그램에 Windows 기반 인증을 사용할 수 있습니다. 
```
<configuration>
   <system.web>
      <authentication mode="Windows"/>
   </system.web>
</configuration>
```
인증 
사용자가 인증을 받으면 액세스하려는 리소스를 인증하는 데 초점을 맞출 수 있습니다. 다음 예제는 모두에게 액세스가 거부되었지만 "jkieley"와 "jstegman"에게는 부여된 액세스를 보여줍니다. 
```
<authorization>
   <allow users="NORTHAMERICA\jkieley, REDMOND\jstegman"/>
   <deny users="*"/>
</authorization> 
```
가장 
가장이란 실행 중인 엔티티의 신분으로 개체가 코드를 실행하는 과정을 말합니다. ASP에서 가장하면 인증된 사용자로서 코드를 실행할 수 있습니다. 또한 특수 신분을 사용하여 익명으로 실행할 수도 있습니다. 기본적으로 ASP .NET에서는 요청 단위 가장을 하지 않습니다. 이 점이 ASP와 다릅니다. 이 기능에 의존하는 경우에는 다음과 같이 web.config 파일에서 이것을 적용할 수 있어야 합니다. 
```
<identity>
   <impersonation enable = "true"/>
</identity>
```

## 데이터 액세스 
마이그레이션에서 초점을 맞추어야 할 다른 주요 부분은 데이터 액세스입니다. ADO .NET의 도입과 함께 새롭고 강력한 방법으로 데이터를 가져올 수 있게 되었습니다. 데이터 액세스 자체가 큰 주제이므로 그 방법은 문서의 범위를 벗어납니다. 대부분 전과 같이 ADO를 사용할 수 있지만 ASP .NET 응용 프로그램 내에서 데이터 액세스 메서드를 향상시키는 수단으로 ADO .NET을 사용하는 것이 좋습니다. 

## ASP .NET 준비 
쉽게 발생할 수 있는 문제 대부분에 대해 알아보았으므로 최종적으로 ASP .NET으로 옮겼을 때 발생할 수 있는 문제에 대한 대비를 위해 현재 할 수 있는 방법에 대해 궁금할 것입니다. 몇 가지만 수행해도 프로세스를 무난하게 처리할 수 있습니다. 그 이후에는 ASP .NET으로 옮기지 않더라도 이러한 제안 사항은 ASP 코드에 유용할 것입니다. 

Option Explicit 사용 
좋은 방법이지만 아직도 사용하지 않는 사람이 있습니다. Option Explicit를 사용하여 ASP에서 변수를 선언하도록 하면 적어도 변수를 정의하는 위치나 사용 방법을 다룰 수 있습니다. ASP .NET으로 옮긴 다음에는 Option Strict를 사용하는 것이 좋습니다. Option Explicit가 Visual Basic .NET의 기본이 되지만 보다 강력한 Option Strict를 사용하면 모든 변수가 정확한 데이터 유형으로 선언되도록 할 수 있습니다. 이렇게 하려면 추가 작업이 필요하겠지만 장기적으로는 매우 가치가 있다는 것을 알게 될 것입니다. 

기본 속성 사용 금지 
설명한 바와 같이 기본 속성은 더 이상 사용할 수 없습니다. 속성을 명시적으로 액세스하는 것은 그렇게 어렵지 않습니다. 이렇게 하면 코드를 읽기 쉬우며 나중에 이식할 때도 시간을 절약할 수 있습니다. 

괄호와 Call 키워드 사용 
이 문서의 앞 부분에서 설명한 것처럼 가능한 곳에서 괄호와 Call 문을 사용합니다. ASP .NET에서는 강제로 괄호가 사용됩니다. Call 문 사용은 앞으로 더 잘 대비할 수 있는 규칙을 추가하는데 도움이 됩니다. 

포함 파일 중첩 금지 
말처럼 쉽지 않지만 가능하면 포함 파일을 중첩하지 말아야 합니다. 분명히 말해서 포함 파일이 다른 포함 파일을 포함하는 부분을 없애도록 해야 합니다. 따라서 정말 필요한 파일이 있는 다른 파일을 포함했기 때문에 다른 위치의 포함 파일에 정의된 전역 변수에 코드가 의존하게 되고 이 파일에 액세스하게 됩니다. 

ASP.NET으로 마이그레이션할 때 전역 변수와 루틴을 클래스 라이브러리로 대부분 옮기게 되는데 이 경우 모두에 대한 액세스 권한을 얻는 지점에 대한 정확한 그림이 있으면 훨씬 수행하기 쉽습니다. 변수와 루틴 등을 옮겨야 하고 여러 파일에 중복된 루틴 이름을 변경하게 됩니다. 

유틸리티 함수를 단일 파일로 구성 
마이그레이션 과정에 사용되는 하나의 전략은 서버쪽 포함 파일에 있는 유틸리티 함수와 코드를 모두 Visual Basic 또는 C# 클래스 라이브러리로 마이그레이션하는 것입니다. 이렇게 하면 결국 여러 번 해석된 ASP 파일이 아닌 모든 코드를 코드가 속하는 개체에 배치하게 됩니다. 미리 코드를 구성하면 앞으로 시간이 절약됩니다. 논리 파일에 서브루틴을 그룹화할 수 있어야 VB나 C# 클래스 집합을 쉽게 만들 수 있습니다. 이러한 함수는 처음에 COM 개체에 있었을 것입니다. 

서버쪽 포함 파일에 혼합된 전역 변수나 상수가 여러 개 있는 경우에도 모두 단일 파일에 배치하는 것이 좋습니다. ASP .NET으로 옮기면 전역 또는 상수 데이터를 수용할 클래스를 쉽게 만들 수 있습니다. 이렇게 하면 시스템을 보다 분명하게 유지 관리할 수 있습니다. 

컨텐트의 코드를 가능한 한 많이 제거 
말처럼 쉽지는 않지만 가능하면 HTML 컨텐트에서 코드를 분리해야 합니다. 함수 본문에서 코드와 스크립트를 혼합하는 함수를 삭제합니다. 이렇게 하면 ASP .NET에서 이상적인 모델인 관련 코드를 훨씬 활용하기 좋습니다. 

<% %> 블록 안에 함수 선언 금지 
ASP.NET에서는 지원되지 않습니다. 함수를 <script> 블록 안에 선언해야 합니다. 이 기술에 대한 예제를 보려면 이 문서의 앞 부분에 있는 구조상 변경 사항 절을 참조하십시오. 

Render 함수 사용 금지 
앞에서 설명한 바와 같이 "render 함수"를 사용하지 말아야 합니다. 코드를 변경하거나 준비할 수 있으면 이러한 종류의 함수를 작성할 때 Response.Write 블록을 사용해야 합니다. 

명시적으로 리소스 해제(Close 메서드 호출) 
close()를 명시적으로 호출하거나 사용 중인 개체와 리소스에 존재하는 메서드를 삭제해야 합니다. 삭제 시 Visual Basic과 VBScript가 얼마나 관대한지 알고 있습니다. 일반적으로 바로 삭제하는 것이 좋지만 .NET과 가비지 컬렉션으로 옮기므로 개체를 삭제해야 하는 정확한 시기를 알 수 없습니다. 삭제하고 리소스를 명시적으로 릴리스할 수 있다면 해야 합니다. 

언어 혼합 금지 
가능하면 같은 페이지에서 서버쪽 VBScript와 JScript를 혼합하지 말아야 합니다. 일반적으로 이렇게 하면 프로그래밍을 제대로 할 수 없습니다. 이것은 또한 새로운 컴파일 모델로 인해 페이지당 하나의 인라인 <% %> 언어만을 필요로하는 ASP .NET에 대한 마이그레이션 문제입니다. 지금까지와 같은 방법으로 클라이언트쪽 스크립트를 계속 만들 수 있습니다. 

## 요약 
지금까지 본 바와 같이 응용 프로그램을 ASP .NET으로 이동하기 전에 알아야 할 사항이 몇가지 있지만 간략히 설명한 변경 사항의 대부분은 상대적으로 구현하기가 쉽습니다. 

사이트가 큰 경우, 이 프로세스가 끝나면 지금까지 거치면서 수정해 온 수 많은 비사용 코드, 비효율성, 명백한 버그 등을 보고 놀랄 것입니다. 또한, 일반적으로 ASP .NET과 .NET 플랫폼에 추가된 새롭고 강력한 많은 기능을 활용할 수 있습니다. 
