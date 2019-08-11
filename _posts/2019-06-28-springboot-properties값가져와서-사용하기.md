---
layout: post
title: Spring Boot 의 .properties 선언한 값 가져오기
comments: true
categories: [Web]
---

## properties 에 선언하기
제가 개인 프로젝트를 하면서 공부를 하고 있습니다. sftp를 이용해서 파일전송을 할려고 하는데 접속정보는 중요하기 때문에 코드에 바로 선언하지 않았습니다. 이 정보를 properties파일에 선언하고 쓸려고 합니다. 값을 가져오는 방법은 간단합니다. 예를 들어 저의 properties에는 다음과 같이 값이 선언되어 있습니다.
~~~
sftp.port=22
sftp.username=jayasou
sftp.password=secret319
sftp.dir=/home/jayasou
~~~

이런 식으로 sftp 접속정보를 등록합니다. 여기서 dir은 sftp 파일 전송 시 기본이되는 루트경로라고 생각하시면 됩니다. 단지 sftp접속을 위해서는 굳이 적어두지 않아도 됩니다. 그럼 java코드에는 어떻게 가져오면 될까요. 가져오는 방법은 다음과 같습니다.

## java 코드에서 properties 값 가져오기
~~~
    @Value("${sftp.username}")
    private String username;
    
    @Value("${sftp.host}")
    private String host;
    
    @Value("${sftp.port}")
    private int port;
    
    @Value("${sftp.password}")
    private String password;
    
    @Value("${sftp.dir}")
    private String dir;
~~~
이런식으로 가져옵니다. 하지만 이렇게 가져올 때는 클래스 선언부에 @Component라고 선언하고 빈으로 등록해주어야 합니다. 기본적인 코드 양식은 다음과 같습니다.
~~~
@Component
public class example {
    @Value("${...}")
    private String value;
}
~~~

## Component로 등록한 클래스 이용하기
저는 하나의 클래스에는 sftp 접속정보만 담겨져 있고 다른 하나의 클래스는 접속정보를 가진 클래스를 이용하여 접속, 파일전송 을 담당하는 서비스 클래스를 만들었습니다. 결합도를 낮추기 위해 interface 클래스에 기능만 명시해 두고 그 클래스를 상속받아 직접 구현한 클래스를 만들어두었습니다.
> Config -> Service -> ServiceImpl

이라고 생각하시면 됩니다. 만드는 방법은 @Service를 사용하는 것과 동일합니다.
1. Interface
~~~
public interface SftpService {
    public void connect();
    public void upload();
    public void disconnect();
}
~~~

2. interface 를 상속받아 구현하기
~~~
@Component
public class SftpServiceImpl implements SftpService {
    private final SftpConfig config;
    public SftpServiceImpl(SftpConfig config) {
        this.config = config;
    }
    
    @Override
    public void connect() {
    
    }
    
    @Override
    public void upload() {
    
    }
    
    @Override
    public void disconnect() {
    
    }
}
~~~

이런 구조를 가지게 되었습니다. 여기서 많은 고민을 했던 부분이 있습니다. sftp접속 시 많은 시간이 걸립니다. 이런 점은 파일을 업로드할 때마다 connection과 disconnection이 이루어진다면 비효율적이라고 생각이 들었습니다. 그럼 빈을 생성될 때 접속이 되게 하면은 어떨까요. 웹서버가 종료될 때 접속종료가 되면은 어떨까요. 이런 부분도 고민을 해보았지만 결국 처음의 방법으로 진행을 했습니다. sftp 접속이 계속 될 경우 위험하지 않을까 란 생각을 했기 때문입니다. 아닐 수도 있겠지만 ..

# 빈생성 시 연결하기, 서버 종료시 접속종료하기
Component라고 선언부에 선언을 하면 맨 처음 하나의 빈으로 생성이 됩니다. 이때 빈으로 생성되면서 실행할 수 있는 함수가 있습니다. 함수의 선언부에 @PostConstruct 이렇게 적어두면 됩니다. 서버가 종료시 특정함수를 실행시키고 싶다면 @PreDestory 라고 선언하면 됩니다.
~~~
@Component
public class config {
    @PostConstruct
    pulic void init() {
        log.info("init()");
    }
    @PostConstruct
    public void destory() {
        log.info("destory()");
    }
}
~~~
이렇게 코드를 적어둔다면 프로그램 실행 시 init이라는 글자를 콘솔창으로 볼 수 있습니다. DB와 연결을 할 때도 구동시에 연결이 되는 것처럼 하나의 빈으로 생성하여 프로그램 시작할 때 sftp접속을 하여 유지를 할 수 있습니다. 그럼 실제 코드상으로는 file upload만 하면은 돕니다.