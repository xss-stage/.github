<img src = "https://raw.githubusercontent.com/xss-stage/.github/e5a1bfbfb1882be45ba42c58b27218830015004a/Frame%206.svg" width = "450" height = "auto"/>   

#

xss-stage는 이미 다양한 xss공격을 필터링하는 라이브러리가 있지만, 라이브러리 마다 적용방식이 제각각이며, 여러 xss 라이브러리를 함께 사용해야 하는 상황(예 : json을 필터링 해주지 못하는 [lucy-xss](https://github.com/naver/lucy-xss-servlet-filter) 와 json을 필터링해주는 다른 xss 라이브러리를 함께 사용해야할때)에서 다양한 라이브러리를 함께 적용하기 어려운 문제를 해결하고 일관된 방식으로 적용하는것을 돕기 위해 탄생했습니다.   

[***Press Star***](https://github.com/xss-stage/xss-core/stargazers)   
   
[![Hits](https://hits.seeyoufarm.com/api/count/incr/badge.svg?url=https%3A%2F%2Fgithub.com%2Fxss-stage&count_bg=%23FF4848&title_bg=%232D2D2D&icon=&icon_color=%23E7E7E7&title=hits&edge_flat=false)](https://hits.seeyoufarm.com) [![made with love](https://camo.githubusercontent.com/c6c5b56fc051557203c6dffa4242b41b09ff22f6303da15e47162a5c1691e8a5/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f4d616465253230776974682d4c6f76652d2d2545322539442541342d726564)](https://camo.githubusercontent.com/c6c5b56fc051557203c6dffa4242b41b09ff22f6303da15e47162a5c1691e8a5/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f4d616465253230776974682d4c6f76652d2d2545322539442541342d726564)  
![needed jdk version](https://img.shields.io/badge/JDK-8-blue)   
![api-version](https://img.shields.io/badge/xss--core-1.2-3F9DE5)  ![api-version](https://img.shields.io/badge/xss--extension--string-1.2-92CE64)  ![api-version](https://img.shields.io/badge/xss--extension--json-1.2.1-F29494)


## Overview
xss-stage를 사용하면 다음과 같은 장점이 있습니다.
  
1. **일관적이고 쉬운 적용 방식** - 각 상황에 효율적인 다양한 라이브러리를 일관된 적용방식으로 적용할 수 있습니다.
2. **쉬운 확장과 커스텀** - XssFiltering 인터페이스를 구현하고 스프링 빈으로 등록하면, 이 구현체를 이용해 대상을 필터링 할 수 있습니다.
3. **선택적인 Xss filtering** - 꼭 필요한 파라미터와 url만 Xss filtering 대상으로 지정할 수 있습니다.
4. **낮은 라이브러리 의존성** - Xss core에서 제공하는 인터페이스를 통해 의존성을 줄여, 언제든지 구체적인 필터링 라이브러리를 교체할 수 있습니다.
5. **검증된 filtering 구현체들** - Xss stage 확장인 xss-extension-string, xss-extension-json는 각각 navercorp-lucy, jackson을 사용해 구현되어 있습니다.
6. **요청에서 진행되는 xss filtering** - Xss stage는 요청시에 xss filtering이 진행되기 때문에 응답이 더 많은 상황에서 더 효율적일 수 있습니다.
7. **한곳에서 관리하는 xss filtering 정책** - 정책 관리 파트에서 구현한 XssFiltering구현체를 등록하고, 다른 파트에서 @Xss 어노테이션을 통해 구현체를 선택하는 방식으로 한 곳에서 애플리케이션 전체적인 Xss filtering 정책을 관리할 수 있습니다.
   
## Extensions
core : [xss-core](https://github.com/xss-stage/xss-core)    
string-extension : [xss-extension-string](https://github.com/xss-stage/xss-extension-string) - naver의 lucy-xss 라이브러리를 사용해 구현되어 있습니다.    
json-extension : [xss-extension-json](https://github.com/xss-stage/xss-extension-json) - jackson의 databind를 사용해 구현되어 있습니다.

## Usage
xss-stage에서 사용할 수 있는 인터페이스와 확장법은 모두 xss-core 라이브러리에 작성되어 있습니다.   
Custom Xss Filter가 필요한 상황이 아니라면, 다음 인터페이스로 Xss-filtering을 진행할 수 있습니다.   
만약 Custom Xss Filter가 필요하다면, [xss-core-extension](https://github.com/xss-stage/xss-core#extension)를 참조하세요.
   
> `@XssFiltering` : 메소드에 마킹가능하며, 마킹된 메소드는 XssFiltering의 대상이 됩니다.   
> `@Xss` : `@XssFiltering`이 마킹된 메소드의 파라미터에 마킹가능하며, 마킹된 파라미터를 대상으로 Xss filtering이 진행됩니다. 
> `@Xss`는 String filterName() 메소드를 갖고있으며, 이 메소드에 값을 설정하는것으로 파라미터를 필터링할때 사용할 XssFilter를 결정할 수 있습니다.
> filterName()은 value()와 동일하며, 이 둘 모두 생략된다면, 파라미터의 클래스명을 모두 소문자로 변경한 값으로 XssFilter를 결정합니다.
   
다음은 실제 사용예시 입니다.

``` Java

@RestController
public class Example{
  
    @XssFiltering // 이 어노테이션이 마킹된 메소드는 XssFiltering의 대상이 됩니다.
    @GetMapping("/example")
    public Object helloworld(@Xss String param1, @Xss("json") SomeObject param2, @Xss("string") String param3, String param4){
        // @XssFiltering 어노테이션이 마킹된 메소드의 파라미터에 @Xss 어노테이션을 마킹함으로써 Xss safe한 객체를 얻을 수 있습니다.
        // @Xss의 value()에 어떠한 값도 들어가지 않는다면, 마킹된 파라미터의 클래스 이름을 모두 소문자로 변경한 값이 됩니다.
        // @Xss의 value()에 값을 넣음으로써, 이 파라미터를 필터링 하는데 사용할 XssFilter 구현체를 선택할 수 있습니다.
        // @Xss에 들어갈 수 있는 값은 xss-extension 레포지토리를 참고하세요.
        ...
    }
  
}

```

## Download
#### xss-stage는 jitpack을 이용해 배포되고 있으며, Spring에 종속적인 라이브러리로 관련 의존성을 필요로 합니다.
``` gradle
plugins {
    id 'org.springframework.boot' version '2.7.1'
    id 'io.spring.dependency-management' version '1.1.0'
    id 'java'
}

repositories {
    mavenCentral()
    maven {url 'https://jitpack.io'} // xss-stage는 jitpack을 이용해 배포되고 있습니다. 따라서, 이 저장소를 등록해줘야 다운로드 가능합니다.
}

dependencies {
    implementation 'org.springframework.boot:spring-boot-starter' 
    implementation 'org.springframework.boot:spring-boot-starter-aop' // xss-core는 Spring-aop를 이용해 구현되어있습니다.
}
```

- xss-stage 라이브러리를 사용하기 위해 xss-core 라이브러리를 다운로드 합니다. xss-core는 xss-extension들을 통합하고 XssFilter구현체들을 요청 파라미터에 적용하는 역할을 합니다.
``` gradle
dependencies {
    implementation 'com.github.xss-stage:xss-core:1.2'
}
```

- String 형태의 파라미터를 필터링할 필요가 있다면, xss-extension-string 라이브러리를 다운로드 합니다.
``` gradle
dependnecies {
    implementation 'com.github.xss-stage:xss-extension-string:1.2'
}
```

- Json 형태의 파라미터(Json -> 객체)를 필터링할 필요가 있다면, xss-extension-json 라이브러리를 다운로드 합니다.
``` gradle
dependencies {
    implementation 'com.github.xss-stage:xss-extension-json:1.2.1'
}
```

- 모든 의존성을 다운로드 받는다면 gradle파일은 다음과 같이 됩니다.
``` gradle
plugins {
    id 'org.springframework.boot' version '2.7.1'
    id 'io.spring.dependency-management' version '1.1.0'
    id 'java'
}

repositories {
    mavenCentral()
    maven {url 'https://jitpack.io'}
}

dependencies {
   implementation 'com.github.xss-stage:xss-core:1.2'
   implementation 'com.github.xss-stage:xss-extension-string:1.2'
   implementation 'com.github.xss-stage:xss-extension-json:1.2.1'
   
   implementation 'org.springframework.boot:spring-boot-starter'
   implementation 'org.springframework.boot:spring-boot-starter-aop'
}
```
