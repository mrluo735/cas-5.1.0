一.在目录结构：e:\openresource\cas-5.1.0-ws\cas-5.1.0

二.在根目录中的build.gradle中增加阿里镜象
  buildscript {
    repositories {
      ...
      maven { url "http://maven.aliyun.com/nexus/content/groups/public/" }
      ...
    }
  }
  
  subprojects {
    repositories {
      ...
      maven { url "http://maven.aliyun.com/nexus/content/groups/public/" }
      ...
    }
  }

三.在cas-5.1.0\webapp\gradle\webapp.gradle中增加依赖项目
  数据库支持,redis支持,service支持
  compile project(":support:cas-server-support-jdbc")
  compile project(":support:cas-server-support-redis-ticket-registry")
  compile project(":webapp:cas-server-webapp-session-redis")
  compile project(":support:cas-server-support-json-service-registry")
	//compile project(":support:cas-server-support-jpa-service-registry")

四.修改cas-5.1.0\webapp\resources\templates\layout.html
  把资源文件的cdn路径改成cdn.bootcss.com。如下:
  <link rel="stylesheet" href="//cdn.bootcss.com/font-awesome/4.4.0/css/font-awesome.min.css"/>
  <link type="text/css" rel="stylesheet" href="//cdn.bootcss.com/bootstrap/3.3.5/css/bootstrap.min.css"/>

  <link rel="stylesheet" th:href="@{${#themes.code('standard.custom.css.file')}}"/>

  <link rel="icon" th:href="@{/favicon.ico}" type="image/x-icon"/>

  <script type="text/javascript" src="//cdn.bootcss.com/zxcvbn/4.3.0/zxcvbn.js"></script>
  <script type="text/javascript" src="//cdn.bootcss.com/jquery/1.11.3/jquery.min.js"></script>
  <script type="text/javascript" src="//cdn.bootcss.com/jqueryui/1.11.4/jquery-ui.min.js"></script>
  <script type="text/javascript" src="//cdn.bootcss.com/jquery-cookie/1.4.1/jquery.cookie.min.js"></script>
  <script src="//www.google.com/recaptcha/api.js" async defer th:if="${recaptchaSiteKey}"></script>
  <script src="//cdn.bootcss.com/bootstrap/3.3.5/js/bootstrap.min.js"></script>

五.前端web项目编译指令
  gradle installGulp
  
  可选
  定位至/webapp下
  npm i
  
六.整个项目编译指令
  编译前需要把cas-5.1.0添加至git中
  gradle clean
  ./gradlew build install --parallel -x test -x javadoc -DskipCheckstyle=true -DskipFindbugs=true

七.修改配置文件
	https://apereo.github.io/cas/5.1.x/installation/Configuration-Properties.html
  7.1 修改\cas-5.1.0\webapp\resources\service\HTTPSandIMAPS-10000001.json
    增加http类型如下:
    "serviceId" : "^(https|http|imaps)://.*",
    
  7.2 修改\cas-5.1.0\webapp\resources\application.properties
    # cas.authn.accept.users=casuser::Mellon
    # 以json格式读取services目录
    cas.serviceRegistry.initFromJson=true
    # 注销成功跳转至service
    cas.logout.followServiceRedirects=true
    # 取消https
    cas.tgc.secure=false
    cas.warningCookie.secure=false

    cas.authn.jdbc.query[0].sql=SELECT password FROM ss_user WHERE loginName=?
    cas.authn.jdbc.query[0].healthQuery=SELECT 1
    cas.authn.jdbc.query[0].isolateInternalQueries=false
    cas.authn.jdbc.query[0].url=jdbc:mysql://localhost:3306/lmcloudplatdb?createDatabaseIfNotExist=true&useUnicode=true&characterEncoding=UTF-8&autoReconnect=true
    cas.authn.jdbc.query[0].failFast=true
    cas.authn.jdbc.query[0].isolationLevelName=ISOLATION_READ_COMMITTED
    cas.authn.jdbc.query[0].dialect=org.hibernate.dialect.MySQLDialect
    cas.authn.jdbc.query[0].leakThreshold=10
    cas.authn.jdbc.query[0].propagationBehaviorName=PROPAGATION_REQUIRED
    cas.authn.jdbc.query[0].batchSize=1
    cas.authn.jdbc.query[0].user=root
    cas.authn.jdbc.query[0].ddlAuto=create-drop
    cas.authn.jdbc.query[0].maxAgeDays=180
    cas.authn.jdbc.query[0].password=root
    cas.authn.jdbc.query[0].autocommit=false
    cas.authn.jdbc.query[0].driverClass=com.mysql.jdbc.Driver
    cas.authn.jdbc.query[0].idleTimeout=5000
    # cas.authn.jdbc.query[0].credentialCriteria=
    # cas.authn.jdbc.query[0].order=0
    # cas.authn.jdbc.query[0].dataSourceName=
    # cas.authn.jdbc.query[0].dataSourceProxy=false
    # 5.1.x以上
    cas.authn.jdbc.query[0].fieldPassword=password
    cas.authn.jdbc.query[0].fieldExpired=
    cas.authn.jdbc.query[0].fieldDisabled=
    # cas.authn.jdbc.query[0].principalAttributeList=sn,cn:commonName,givenName

    cas.authn.jdbc.query[0].passwordEncoder.type=DEFAULT
    cas.authn.jdbc.query[0].passwordEncoder.characterEncoding=UTF-8
    cas.authn.jdbc.query[0].passwordEncoder.encodingAlgorithm=MD5
    # cas.authn.jdbc.query[0].passwordEncoder.secret=
    # cas.authn.jdbc.query[0].passwordEncoder.strength=16

    # cas.authn.jdbc.query[0].principalTransformation.suffix=
    # cas.authn.jdbc.query[0].principalTransformation.caseConversion=NONE|UPPERCASE|LOWERCASE
    # cas.authn.jdbc.query[0].principalTransformation.prefix=

    # Manage session storage via Redis
    spring.session.store-type=redis
    spring.redis.host=localhost
    # spring.redis.password=secret
    spring.redis.port=6379

    ## Redis server host.
    cas.ticket.registry.redis.host=localhost
    #
    ## Database index used by the connection factory.
    cas.ticket.registry.redis.database=0
    #
    ## Redis server port.
    cas.ticket.registry.redis.port=6379
    #
    ## Login password of the redis server.
    # cas.ticket.registry.redis.password=
    #
    ## Connection timeout in milliseconds
    # cas.ticket.registry.redis.timeout=
    #
    ##
    # cas.ticket.registry.redis.pool.max-active=20
    #
    ## Max number of "idle" connections in the pool. Use a negative value to indicate an unlimited number of idle connections.
    # cas.ticket.registry.redis.pool.maxIdle=8
    #
    ## Target for the minimum number of idle connections to maintain in the pool. This setting only has an effect if it is positive.
    # cas.ticket.registry.redis.pool.minIdle=0
    #
    ## Max number of connections that can be allocated by the pool at a given time. Use a negative value for no limit.
    # cas.ticket.registry.redis.pool.maxActive=8
    #
    ## Maximum amount of time (in milliseconds) a connection allocation should block
    #  before throwing an exception when the pool is exhausted. Use a negative value to block indefinitely.
    # cas.ticket.registry.redis.pool.maxWait=-1

    # cas.ticket.registry.redis.crypto.signing.key=
    # cas.ticket.registry.redis.crypto.signing.keySize=512
    # cas.ticket.registry.redis.crypto.encryption.key=
    # cas.ticket.registry.redis.crypto.encryption.keySize=16
    # cas.ticket.registry.redis.crypto.alg=AES
