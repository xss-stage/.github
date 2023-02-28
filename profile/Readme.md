# xss-stage
xss-stage는 다양한 xss 라이브러리를 통합하고 쉽게 적용하기 위해 탄생한 라이브러리 입니다.   
xss-core는 자유롭게 XssFilter를 등록하고 확장하여 사용할 수 있으며, 요청단에서 xss 필터링을 진행하기 때문에, 응답과정에서 필터링을 진행하는 방식보다 특정 상황(응답이 요청보다 많은 상황)에서 더 효율적일 수 있습니다.   
   
[![made with love](https://camo.githubusercontent.com/c6c5b56fc051557203c6dffa4242b41b09ff22f6303da15e47162a5c1691e8a5/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f4d616465253230776974682d4c6f76652d2d2545322539442541342d726564)](https://camo.githubusercontent.com/c6c5b56fc051557203c6dffa4242b41b09ff22f6303da15e47162a5c1691e8a5/68747470733a2f2f696d672e736869656c64732e696f2f62616467652f4d616465253230776974682d4c6f76652d2d2545322539442541342d726564)  
![api-version](https://img.shields.io/badge/xss--core-1.1-3F9DE5)  ![api-version](https://img.shields.io/badge/xss--extension--string-1.1-92CE64)  ![api-version](https://img.shields.io/badge/xss--extension--json-1.1-F29494)

## Extensions
core : [xss-core-1.1](https://github.com/xss-stage/xss-core)    
string-extension : [xss-extension-string-1.1](https://github.com/xss-stage/xss-extension-string)   
json-extension : [xss-extension-json-1.1](https://github.com/xss-stage/xss-extension-json)

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

- xss-stage 라이브러리를 사용하기 위해 xss-core 라이브러리를 다운로드 합니다. xss-core는 xss-extension들을 통합하고 요청 파라미터에 적용하는 역할을 합니다.
``` gradle
dependencies {
    implementation 'com.github.xss-stage:xss-core:1.1'
}
```

- String 형태의 파라미터를 필터링 하기 위해, xss-extension-string 라이브러리를 다운로드 합니다.
``` gradle
dependnecies {
    implementation 'com.github.xss-stage:xss-extension-string:1.1'
}
```

- Json 형태의 파라미터를 필터링 하기 위해, xss-extension-json 라이브러리를 다운로드 합니다.
``` gradle
dependencies {
    implementation 'com.github.xss-stage:xss-extension-json:1.1'
}
```

## Useage

``` Java

@RestController
public class Example{
  
    @XssFiltering // 이 어노테이션이 마킹된 메소드는 XssFiltering의 대상이 됩니다.
    @GetMapping("/example")
    public Object helloworld(@Xss String param1, @Xss("json") SomeObject param2, @Xss("string") String param3, String param4){
        // @XssFiltering 어노테이션이 마킹된 메소드의 파라미터에 @Xss 어노테이션을 마킹함으로써 Xss safe한 객체를 얻을 수 있습니다.
        // 
        ...
    }
  
}

```
