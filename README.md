# SpringBaseProject

# 스프링 기본 프로젝트
	-Oracle 11g Express Edition 사용(일반판보다 간단함, Developer 설치해줘야함)
	-LomBok 설치 
	-MyBatis 사용 
	-히카리 cp사용 
	-Junit은 Maven 안하고 로컬추가
## 깃 프로젝트 설정할것 
	-이클립스 UTF 8 설정
	-RootContext(RootConfig) 에서 DB 사용자 변경 
	-기본 테스트용 TimeMapper와 homeController가 있음 삭제 후 사용 
	-모든 패키지 삭제후 사용
		-> 몇가지 예제로 넣어둔거임
	-프로젝트 속성 -Web Project Settings - Path를  / 로 바꿈 
	-JUnit 테스트로 datasource 확인
	-log4jdbc를 원치않을경우 아래 스프링설정 관련 태그 가기
	-ResponseBody로 Json을 내보내기 하지 않는 경우 Maven에서 Jackson-databind를 제거


--------------------------------------------------------------------------------------
<Spring의 특징 및 의존성 주입>

# 책들어가기전에 수업내용 몇개 추가
	ORM(Object Relationship Mapping) : SQL 분리 프레임 워크
		MyBatis : 국내개발자 많이사용-> 한국어 api도있음
		hibernate : 어려움 국내에서는 잘 안씀

	외부 클래스 가져오는 방법 
		Maven : Spring
		Gradle : Android

	https://mvnrepository.com/



# 스프링 설정
### 개발툴 설치
		스프링 개발 STS(Spring Tool Suite)이나 Eclipse+Spring Plugin 으로 사용
		Eclipse => help탭 - Install new software => https://spring.io/tools3/sts/all/ 
		에서 update Sites Eclipse 버젼 확인후 다운로드
	
### WorkSpace UTF-8 설정
		자바 , jsp 개발 코딩마다 인코딩이 달라서 문제가 될 수 있기 때문에 미리 Workspace 의
		인코딩을 변경해줌
		Window -> Preferences-> General -> Workspace , Text file encoding 변경
		Web 탭 -> CSS ,HTML, JSP 모두 인코딩 변경
	
### 톰캣 
		-http://tomcat.apache.org/whichversion.html 보면 8버젼은 톰캣9 쓰라고 되어있음
		-다운로드 https://tomcat.apache.org/download-90.cgi
		이클립스에서 서버 추가할때 다운받은 경로 넣어주고 생성
	
### 프로젝트 구성 및 생성
		-Spring Legacy Project -> Spring MVC Project 로 생성
		-src/main/java : 기본 코드경로
		-src/main/resources : 실행할때 참조하는 경로(주로 설정파일 넣음)
		-sevlet-context.xml : 웹과 관련된 스프링 설정 파일(컨트롤러 설정)
		-root-context.xml : 스프링 프레임워크에서 관리해야 하는 객체(bean)를 설정하는 설정 파일
		-web.xml : 톰캣 설정 파일
		-pom.xml : Maven 설정

### 프로젝트 기본설정
		-오래된 버젼을 기본값을 가지고 있기 때문에 최신버젼으로 설정이 필요함
		1) 스프링버젼 업그레이드
			https://mvnrepository.com/ 에서 Spring Core 보면 최신버젼나와 있음
			properties 태그안에 입력
			<java-version>1.8</java-version> <!-- 스프링에 사용되는 자바버젼 -->
			<org.springframework-version>5.1.3.RELEASE</org.springframework-version> <!-- 스프링버젼 -->
			
		2) Maven 컴파일러 자바버젼 변경 : Maven에 컴파일에 사용되는 java버젼
			plugin태그안에
			artifactID 가 maven-compiler-plugin인것을찾아서
			source와 target 태그를 1.8로 변경
		
		3) 톰캣 절대경로 설정
			프로젝트 속성 -Web Project Settings - Path를  / 로 바꿈
			
			-서버 생성할때 root 잡히는데 그때 프로젝트 root경로로 생성 (절대경로 바꾸기전에 서버생성하면 
			그걸로 반영,서버에서 변경해야함)
			
### context 설정
		1)XML
			(1) root-context.xml : bean(스프링 프레임워크에서 관리하는 객체)을 설정하는 파일
				<1> nameSpaces 에서 context 추가
				<2> 컴포넌트 스캔위치 
					<context:component-scan base-package="패키지주소">
					</context:component-scan>
				<3> beans graph에서 생성된 bean 을 확인할 수 있음


		2)JAVA
			- root-context.xml 대신 RootConfig.java
			  web-context.xml 대신 WebConfig.java 를 생성해줌
			  servlet-context.xml 대신 ServletConfig.java 
				1. @EnableWebMvc/ WebMvcConfigurer 인터페이스를 구현하는 방식
				2. @Configuration/ WebMvcConfigurationSupport Class를 상속하는 방식 
					- > 일반 @Configuration 우선 순위가 구분되지 않는 경우 사용
			- web은 Tomcat 구동과 관련된 설정, 나머지 두 파일은 스프링과 관련된 설정

			(1)xml 삭제 : sevlet-context.xml,root-context.xml,web.xml 삭제
			(2)web.xml 삭제시 에러발생(xml로 하기로되어있어서 없다는것을 알려줘야함) : pom.xml에 다음추가
				<plugin>
					<groupId>org.apache.maven.plugins</groupId>
						    <artifactId>maven-war-plugin</artifactId>
						    <version>3.2.2</version>
					<configuration>
					    <failOnMissingWebXml>false</failOnMissingWebXml>
					</configuration>
				</plugin>
				
			(3)각 xml에 맞게 JAVA 생성
				RootConfig : 
					@Configuration //설정파일 어노테이션
					@ComponentScan(basePackages={"패키지주소"})
					public class RootConfig {}

				WebConfig : AbstractAnnotationConfigDispatcherServletInitializer 을 상속받으면 3가지 추상메소드 생성

					protected Class<?>[] getRootConfigClasses() {
						// TODO Auto-generated method stub
						return new Class[] {RootConfig.class};
					} //RootConfig.class로 rootContext.xml 대신하기때문에 이렇게 작성

	

### Lombok 설치 
		Lombok : 컴파일 시 흔하게 코드를 작성하는 기능들을 완성해주는 라이브러리 => getter setter 같은 귀찮은 작업 해결
		@Data : Lombok에서 가장 자주 사용됨
			   @ToString, EqualsAndHashCode, @Getter/@Setter, @requiredArgsConstructor 
				--> 모두 결합한 형태로 한 번에 생성
		@Log4j : 로그 객체 생성

		@Setter, @Setter(onMethod_=@Autowired)
		
		1. Lombok 홈페이지에서 다운로드 설치
		2. Maven 설정 :
			<dependency>
				<groupId>org.projectlombok</groupId>
				<artifactId>lombok</artifactId>
				<version>1.18.0</version>
				<scope>provided</scope>
			</dependency>
		
### 몇가지 Maven 설정
		Maven을 이용한 라이브러리는 사용자폴더 .m2폴더 repository에 저장됨
		Maven 다운받다 오류날때 두가지방법
			1) .m2폴더에서 해당라이브러리 삭제
			2) 강제업데이트 alt+f5누르고 force update of snapshots/releases


		Maven - scope
	* compile : 기본영역으로 아무것도 지정되지 않은 경우 사용됨. compile 의존관계에 있는 것은 프로젝트의 모든 클래스에서 사용가능함. 
	또한, 이와 같은 의존관계는 의존관계에 있는 프로젝트에 포함됨.
	* provided : compile 과 매우 유사히지만, 실행시 의존관계를 제공하는 JDK나 컨테이너에 대해서 적용됨. 
	예를 들어, JEE에 대한 웹 어플리케이션을 만드는 경우, 웹 컨테이너가 서블릿 API와 Java EE API관련 
	클래스들을 제공하기 때문에 provided 영역으로 의존관계가 세팅되어야 함. 
	이 영역은 컴파일과 테스트의 클래스패스 용으로 사용되며, 자동영역임.
	* runtime : 의존관계가 컴파일시 필요하지 않지만, 실행시 필요함을 의미함. 
	실행시와 테스트 클래스패스에 속하지만, 컴파일 클래스패스에는 속하지 않음.
	* test : 일반적인 어플리케이션 사용에 대해서는 의존관계가 필요없고, 테스트 컴파일과 실행 시점에만 사용됨.
	* system : 명시적으로 해당 JAR를 포함하는 것이 제공되어야 한다는 것을 제외하고 provided와 유사함. 
	artifact는 항상 사용가능하며 레파지토리에서 검색하지 않음.
	* import (Maven 2.0.9 이후에서만 적용) : 이 영역은 <dependencyManagement>에서 pom의 의존관계에 대해서 사용됨.
	지정된 POM이 해당 POM의 <dependencyManagement> 영역에 있는 의존관계로 대체됨을 의미함. 
	이것들이 대체되기 때문에 import 영역의 의존관계들은 실질적으로 의존에 대한 제약에 대해 관여하지 않음.
		1) Junit
		
			(1) Maven으로 할시 테스트중 Assert 클래스 임포트 안됨
				패키지속성 - JAVA biuld path- add libraries - junit 4 선택 - 자동 4.12 버젼 설치됨
				maven에서 junit 모두 지움
			(2) Maven 으로 할 경우는 다음과 같음(난 안함)
				<dependency>
					<groupId>junit</groupId>
					<artifactId>junit</artifactId>
					<version>4.12</version>
					<scope>test</scope>
				</dependency>

		2)Servlet 버젼 3.1.0버젼 이상으로 올리기(기존 2.5)
### 테스트 
		1)Maven 설정
			<dependency>
				<groupId>org.springframework</groupId>
				<artifactId>spring-test</artifactId>
				<version>${org.springframework-version}</version>
			</dependency>
		2)테스트 어노테이션
			(1)@Runwith(SpringJUnit4ClassRunner.class) : 테스트 코드가 스프링을 실행하는 역할을 할 것이라는 것을 표시 
			(2)@ContextConfiguration("file:src/main/webapp/WEB-INF/Spring/root-context.xml") : 스프링 빈 등록(빈(객체)를 스프링 내에 객체로 등록)
				자바일경우 @ContextConfiguration(classes={RootConfig.class})
			(3)@Log4j : Lombok이용해서 로그를 기록하는 Logger를 변수로 생성

			(4)@Test : JUnit에서 테스트할 대상을 표시

	

### 데이터베이스 설정
		1)오라클설치 : 책에서는 11g 사용 , System ,SYS계정 패스워드 , SID , 포트 중요, Sql Developer까지 설치
		2)계정생성 :
			(1)SQL : 
				Create user
			
			(2)SQL Developer :
				1> developer 왼쪽 탭에서 다른사용자 오른쪽클릭 사용자 생성
				2> 이름 패스워드 , 기본테이블 USERS, 임시테이블 Temp
				3> 부여된 롤 탭에서 CONNECT 권한 부여
		3)포트변경 : 
			11g버젼에서는 8080 포트로 웹환경에서 데이터베이스 접근하는데 톰캣서버랑 포트가 같아서 변경해야함
			(1) 변경방법 :
				1>sys 계정(롤 Sysdba)으로 접속
				2>select dbms_xdb.gethttpport() from dual; --포트확인
				3>exec dbms_xdb.sethttpport(9090); --겹칠시 다른포트 변경
		
		4)JDBC 연결
			(1) 드라이버 라이브러리 설치
				- Maven 에서는 11g버젼용 jdbc 드라이버가 존재 x , 직접 임포트해야함
				<1> sqldeveloper\jdbc\lib 에 JDK8버젼용 드라이버존재
				<2> Project 속성 -> Java Build Path -> 외부 라이브러리로 등록 
				<2-1> Project의 WebContent\WEB-INF\lib 에 드라이버 넣음 ->Java Build Path-> ADD Library -> Web App Libraries 선택
				
				//프로젝트 패킹할때 라이브러리 포함시킬지 선택하는것
				//테스트는 정상 , 톰캣 jdbc 오류 => Deployment Assembly 확인
				<3> Project 속성 -> Deployment Assembly -> jar 파일 추가 
		5)DBCP (커넥션풀)
			(1) DBCP 라이브러리 설치
				<!-- https://mvnrepository.com/artifact/com.zaxxer/HikariCP -->
				<dependency>
				    <groupId>com.zaxxer</groupId>
				    <artifactId>HikariCP</artifactId>
				    <version>3.2.0</version>
				</dependency>

			- 책에서는 2.7.4 버젼을 사용함

			(2) datasource 설정 : XML , JAVA방식
				-https://github.com/brettwooldridge/HikariCP 가면 설정 정보를 얻을 수 있음
					주로 hikariconfig 빈만들고 datasource에 넣음
				-DataSource는 DBCP를 이용하여 Connection을 얻을 수 있음
				-밑에 MyBatis 에서 SqlSession은 이 DataSource를 가져갔기때문에 똑같이 Connection을 얻을 수 있음
				1> root-context.xml
					<bean id="hikariConfig" class="com.zaxxer.hikari.HikariConfig">
						<property name="driverClassName" value="oracle.jdbc.driver.OracleDriver"></property>
						<property name="jdbcUrl" value="jdbc:oracle:thin:@localhost:1521:SID명"></property>
						<property name="username" value="계정명"></property>
						<property name="password" value="비밀번호"></property>
					</bean>

					<bean id="dataSource" class="com.zaxxer.hikari.HikariDataSource" destroy-method="close">
						<constructor-arg ref="hikariConfig"/>
					</bean>

				2> RootConfig 클래스
					@Bean
					public DataSource dataSource(){
						HikariConfig hikariConfig = new HikariConfig();
						hikariConfig.setDriverClassName("oracle.jdbc.driver.OracleDriver");
						hikariConfig.setJdbcUrl("jdbc:oracle:thin:@localhost:1521:SID명");
						hikariConfig.setUsername("계정명");
						hikariConfig.setPassword("비밀번호");

						HikariDataSource dataSource = new HikariDataSource(hikariConfig);
						return dataSource;
					}
		6) MyBatis 
			Spring - Mybatis-Spring - MyBatis - DB 순으로 연결되어 있음
			
			(1) Maven 
				spring-jdbc/spring-tx : 스프링에서 데이터베이스 처리와 트랜잭션 처리 
				(MyBatis와 무관해보이지만 안할경우 에러발생)
				mybatis/mybatis-spring: MyBatis와 스프링 연동 라이브러리

				<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis -->
				<dependency>
				    <groupId>org.mybatis</groupId>
				    <artifactId>mybatis</artifactId>
				    <version>3.4.6</version>
				</dependency>

				<!-- https://mvnrepository.com/artifact/org.mybatis/mybatis-spring -->
				<dependency>
				    <groupId>org.mybatis</groupId>
				    <artifactId>mybatis-spring</artifactId>
				    <version>1.3.2</version>
				</dependency>

				<!-- https://mvnrepository.com/artifact/org.springframework/spring-jdbc -->
				<dependency>
				    <groupId>org.springframework</groupId>
				    <artifactId>spring-jdbc</artifactId>
				    <version>${org.springframework-version}</version>
				</dependency>

				<!-- https://mvnrepository.com/artifact/org.springframework/spring-tx -->
				<dependency>
				    <groupId>org.springframework</groupId>
				    <artifactId>spring-tx</artifactId>
				    <version>${org.springframework-version}</version>
				</dependency>

			(2) SQLSessionFactory
				MyBatis에서 핵심적인객체는 SQLSession 과 SQLSessionFactory임
				SQLSessionFactory로 SQLSession 생성
				SQLSession으로 Connection 생성(DataSource)과 원하는 SQL 전달, 결과 리턴을 받음
				->즉 SQL Session은 DataSource에 Sql 기능 추가
				1> root-context.xml 
					<bean id="sqlSessionFactory" class="org.mybatis.spring.SqlSessionFactoryBean">
						<property name="dataSource" ref="dataSource"></property>
					</bean>
				2> RootConfig 클래스
					@Bean
					public SqlSessionFactory sqlSessionFactory() throws Exception{
						SqlSessionFactoryBean sqlSessionFactory = new SqlSessionFactoryBean();
						sqlSessionFactory.setDataSource(dataSource());
						return (SqlSessionFactory) sqlSessionFactory.getObject();
					}

			(3) Mapper
				XML이나 JAVA로 Mapper를 만듦
				Mapper는 SQL을 입력하고 결과를 받는 객체
				
				1> Mapper를 bean에 등록하려면 다음 과정이 필요 
					-root-context.xml에서 NameSpaces클릭 Mybatis-spring 클릭
					-RootContext에 다음 추가
						<mybatis-spring:scan base-package="com.bumbing.mapper"/>
					-Mapper Interface가 TimeMapper일경우 bean 에는 timeMapper로 올라감
				2> Mapper 는 Java , Xml 두가지형태로 SQL 수가 적을경우 JAVA, 많을경우 XML 추천
					<1>JAVA
						-Mapper 패키지생성
						-Mapper 인터페이스생성
						- @Select 어노테이션사용 
						ex:
						@Select("SELECT sysdate FROM dual") 
						public String getTime();
							
					<2>XML
						-src/main/resources 가 기본경로 이기 때문에
						-패키지형식으로 com/bumbing/mapper 폴더 생성
						-Mapper XML 태그 사용하여 XML 만듦
						-Select 태그등 사용하여 쿼리작성
						-namespace는 경로가 좋음 "com.bumbing.mapper.TimeMapper"

						ex: 예제 홈피에서 따옴
						<!DOCTYPE mapper
						    PUBLIC "-//mybatis.org//DTD Mapper 3.0//EN"
						    "http://mybatis.org/dtd/mybatis-3-mapper.dtd">

						<mapper namespace="com.bumbing.mapper.TimeMapper">
							<resultMap type="org.apache.ibatis.submitted.rounding.User" id="usermap">
								<id column="id" property="id"/>
								<result column="name" property="name"/>
								<result column="funkyNumber" property="funkyNumber"/>
								<result column="roundingMode" property="roundingMode"/>
							</resultMap>

							<select id="getUser" resultMap="usermap">
								select * from users
							</select>
							<insert id="insert">
							    insert into users (id, name, funkyNumber, roundingMode) values (
								#{id}, #{name}, #{funkyNumber}, #{roundingMode}
							    )
							</insert>
							
							<resultMap type="org.apache.ibatis.submitted.rounding.User" id="usermap2">
								<id column="id" property="id"/>
								<result column="name" property="name"/>
								<result column="funkyNumber" property="funkyNumber"/>
								<result column="roundingMode" property="roundingMode" typeHandler="org.apache.ibatis.type.EnumTypeHandler"/>
							</resultMap>
							<select id="getUser2" resultMap="usermap2">
								select * from users2
							</select>
							<insert id="insert2">
							    insert into users2 (id, name, funkyNumber, roundingMode) values (
								#{id}, #{name}, #{funkyNumber}, #{roundingMode, typeHandler=org.apache.ibatis.type.EnumTypeHandler}
							    )
							</insert>

						</mapper>




				3> Root 추가 ( Mapper class 사용할경우)
					<1>root-context.xml
						<mybatis-spring:scan base-package="mapper패키지경로"/>
					
					<2>RootConfig
						클래스 선언부에 다음 추가
						@MapperScan(basePackages={"mapper패키지경로"}) 
				
			(4) log4jdbc-log4j2 설정
				PreparedStatement 에서 ? 로 처리하는데 ?의 값이 재대로 되었는지 확인하는 기능
				1>Maven 
					<!-- https://mvnrepository.com/artifact/org.bgee.log4jdbc-log4j2/log4jdbc-log4j2-jdbc4 -->
					<dependency>
					    <groupId>org.bgee.log4jdbc-log4j2</groupId>
					    <artifactId>log4jdbc-log4j2-jdbc4</artifactId>
					    <version>1.16</version>
					</dependency>
				2> root-context
					-다음과같이 바꿈
					<bean id="hikariConfig" class="com.zaxxer.hikari.HikariConfig">
						<property name="driverClassName" value="net.sf.log4jdbc.sql.jdbcapi.DriverSpy"></property>
						<property name="jdbcUrl" value="jdbc:log4jdbc:oracle:thin:@localhost:1521:XE"></property>
						<property name="username" value="SPRING"></property>
						<property name="password" value="java"></property>
					</bean>
				3> resource 에 다음파일추가
				 	파일명 : log4jdbc.log4j2.properties
					내용 : log4jdbc.spylogdelegator.name=net.sf.log4jdbc.log.slf4j.Slf4jSpyLogDelegator
					
				4> 원치않을시 삭제
					위 순서대로 삭제 
					
				
						
			
# 어노테이션 정리

## Sping
	@Autowired
	@Component : bean 설정
	@ComponentScan : rootConfig 에서 bean 등록할 곳 정의할때
	Spring-Test
	@Runwith(SpringJUnit4ClassRunner.class) : 테스트 코드가 스프링을 실행하는 역할을 할 것이라는 것을 표시 
	@ContextConfiguration("file:src/main/webapp/WEB-INF/Spring/root-context.xml") : 스프링 빈 등록(빈(객체)를 스프링 내에 객체로 등록)
		자바일경우 @ContextConfiguration(classes={RootConfig.class})
	@Log4j : Lombok이용해서 로그를 기록하는 Logger를 변수로 생성
	@Test : JUnit에서 테스트할 대상을 표시

## Lombok
	@Setter
	@Data
	@Log4j
	@ToString
	@EqualsAndHashCode
	@RequiredArgsConstructor : class - @NonNull : member : @NonNull인 member로 생성자를 만듦
	@AllArgsConstructor : 모든 Member로 생성자 만듦

## MyBatis 
	@Select("select쿼리")


# Dependency Injection : 디자인패턴에 나옴,
	1) 정의 : 
		A클래스에서 B클래스를 참조하면 강력한 의존성이 생김 => 테스트하기힘들고 유연하지도않음
		그래서 A클래스에서 B인터페이스로 연결하면,즉 행위를 정의하면 나중에 B클래스만들어서 대입시켜주면됨 => 테스트하기쉽고 유연함
		예 : 자동차 클래스에 바퀴 인터페이스, 차문 인터페이스 등으로 정의하면 
		내가 바퀴에 A사 바퀴 클래스, B사 바퀴 클래스를 원하는대로 맞출 수 있음
		위에서는 의존성에대해서 설명한것이고 DI 는 그 인터페이스에 클래스를 자동으로 주입시켜주는 모델임 => 얼마나 쉬워지고 간편해지는지 알 수 있음
	2) 어노테이션
		(1) @Autowired : bean을 자동 등록
	
	3) 주입방법 : 주입 어노테이션있을 경우
		(1) Setter로 등록
		(2) 생성자로 등록
			
	
# 스프링의 주요 특징
  (1) POJO 기반의 구성 : 일반적인 java 코드를 이용해서 객체를 구성하는 방식(POJO)을 그대로 스프링에서 사용 가능
	#POJO(Plain Old Java Object) : API 없이(extends(x),implements(x)) 만들어진 객체
  (2) 의존성 주입(DI)을 통한 객체 간의 관계 구성
	# 의존성 : 하나의 객체가 다른 객체의 상태에 따라 영향을 받는 것
	# Bean : ApplicationContext(필요한 객체 생성 및 주입)가 관리하는 객체들
  (3) AOP(Aspect-Oriented-Programming) 지원 //뒷부분 설명나오는데 나중에 추가
  (4) 편리한 MVC구조
  (5) WAS(Web Application Server)의 종속적이지 않은 개발 환경

 

<MyBatis>
	흔히 SQL 매핑 프레임워크로 분류,
	JDBC 코드의 복잡하고 지루한 작업을 피하는 용도로 많이 사용

	- 자동 Connection close() 기능
	- preparedstatement 처리
	- #{prop} 와 같이 속성 지정하면 내부적으로 자동 처리
	- 리턴 타입을 지정하는 경우 자동으로 객체 생성 및 ResultSet 처리
	
	# MyBatis는 두 개 이상의 데이터를 파라미터로 전달하기 위해서
	1) 별도의 객체 구성
	2) Map을 이용
	3) @Param을 이용해서 이름을 사용
		 #{}


# Spring Mapper,Service,Controller

## Spring MVC Contoller
	1. 특징
		- HttpServletRequest, HttpServletResponse 를 거의 사용할 필요 없이 필요한 기능 구현
		- 다양한 타입의 파라미터 처리, 다양한 타입의 리턴 타입 사용 가능
		- get, post 등 전송 방식에 대한 처리를 어노테이션으로 가능
		- 상속/인터페이스 대신에 어노테이션만으로도 필요한 설정 가능
### Annotation 
	1)URL Mapping
		@RequestMapping(value="/",method={RequestMethod.GET,RequestMethod.POST})	
		@GetMapping
		@PostMapping
	2)Parameter
		@RequestParam("getName") dataType id: 파라메터명과 사용하는 식별자명이 다를 경우 사용
		@InitBinder : 데이터받안온것을 다른데이터형식으로 바꿔서 넣고싶을때 사용(ex 2019-01-01 String을 Date형식으로 바꿀때)
		@DateTimeFormat : InitBinder같은기능인데 날짜만가능
		@ModelAttribute : 받은 데이터를 기본형일지라도 다음페이지에 전달 
### Binding (파라메터 수집)
	1) 자동 파라메터 받아오기 기능 
		-DTO 생성자로 자동 입력해서 DTO만 출력받을 수 있다.
		-기본형은 파라메터받고 Respose 로 가지 않음 , 참조형은 다음 페이지까지 파라메터 전송
			ex :get : num=10&str=aa , num은 기본형 aa는 참조형(문자열)
		-같은 이름의 데이터가 여러개 들어올 경우 ArrayList로 받기가능
			ex :get : ids=10&ids=15&ids=20
			   :@RequestParam("ids") ArrayList<Integer> ids
			   :@RequestParam("ids") Integer[] ids	 (배열도 가능)

### Controller의 리턴 타입
	1. String : jsp를 이용하는 경우에는 jsp 파일의 경로와 파일이름을 나타내기 위해서 사용
		- redirect : 리다이렉트 방식으로 처리하는 경우
		- forward : 포워드 방식으로 처리하는 경우
	2. void : 메소드명.jsp로 이동
	3. vo, dto : 주로 json 타입의 데이터를 만들어서 반환하는 용도로 사용
		- jackson-databind 라이브러리를 pom.xml에 추가
			<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
			<dependency>
			    <groupId>com.fasterxml.jackson.core</groupId>
			    <artifactId>jackson-databind</artifactId>
			    <version>2.9.8</version>
			</dependency>
	4. ResponseEntity : response 할 때 Http 헤더 정보와 내용을 가용하는 용도로 사용
	5. Model, ModelAndView : Model로 데이터를 반환하거나 화면까지 같이 지정하는 경우에 사용(최근에는 많이 x)
	6. HttpHeaders : 응답에 내용 없이 Http 헤더 메시지만 전달하는 용도로 사용

# 파일 업로드 처리
	1. pom.xml 에 commons-fileupload 라이브러리 추가
		<!-- https://mvnrepository.com/artifact/commons-fileupload/commons-fileupload -->
		<dependency>
		    <groupId>commons-fileupload</groupId>
		    <artifactId>commons-fileupload</artifactId>
		    <version>1.3.3</version>
		</dependency>
	2. Servlet-context.xml에 multipartResolver 추가
		
	   - maxUploadSize : 한 번의 Request로 전달될 수 있는 최대의 크기(최대업로드 가능한 바이트크기)
	   - maxUploadSizePerFile : 하나의 파일 최대 크기
	   - maxInMemorySize : 메모리상에서 유지하는 최대의 크기
					-> 이 이상의 데이터는 uploadTempDir에 임시 파일 형태로 보관
	   - defaultEncoding : 업로드하는 파일의 이름이 한글일 경우 깨지는 문제를 처리
	3. form에 enctype="multipart/form-data" 추가
	
# Controller의 Exception 처리
	1. @ExceptionHandler :  해당 메소드가 () 들어가는 예외 타입을 처리한다는 것을 의미
					속성으로는 Exception 클래스 타입을 지정할 수 있다.
					ex ) @ExceptionHandler(Exception.class)
	   @ControllerAdvice : 해당 객체가 스프링의 컨트롤러에서 발생하는 예외를 처리하는 존재임을 명시하는 용도
	2.. @ResponseEntity를 이용하는 예외 메세지 구성
	
	3. 처리방법은 책 154p
# Naming Convevntion(명명 규칙)
	1. -Controller : 스프링 mvc에서 동작하는 Controller 클레스를 설계할 때 사용
	2. -Service, -ServiceImpl : 비즈니스 영역을 담당하는 인터페이스는 '-Service'를 사용
				       인터페이스를 구현하는 클래스는 '-ServiceImpl'이라는 이름을 사용
	3. -DAO, -Repository : DAO(DATA-Access-Object)나 Repository(저장소)라는 이름으로 영역을 따로 구성하는 것이 보편적
				    (Mybatis의 Mapper 인터페이스 활용도 가능)
	4. VO, DTO : 둘 다 데이터를 담고 있는 객체를 의미한다는 공통점이 있음
			vo의 경우, 주로 Read Only의 목적이 강하고 데이터 자체도 Immutable(불변)하게 설계하는 것이 정석
			DTO는 주로 데이터 수집의 용도가 좀 더 강하다.

# 패키지
	- .config : 프로젝트와 관련된 설정 클래스들의 보관 패키지
	- .controller : 스프링 mvc의 controller들의 보관 패키지
	- .service : 스프링의 service 인터페이스와 구현 클래스 패키지
	- .domain : vo, dto 클래스들의 패키지
	- .mapper : mybatis mapper 인터페이스 패키지
	- .exception : 웹 관련 예외처리 패키지
	- .aop : 스프링의 aop 관련 패키지
	- .security : 스프링 Security 관련 패키지
	- .util : 각종 유틸리티 클래스 관련 패키지

# 요구사항
	ex) 고객은 새로운 게시물을 등록할 수 있어야 한다

#DB 
	- 테이블을 생성하고 여러 개의 데이터를 추가하는데 이런 의미 없는 데이터를 '토이데이터(toy data)' 
	  혹은 더미 데이터(dummy data)라고 한다. 


# 일반적으로 웹 프로젝트는 3-tier 방식으로 구성
	Presentation - Business - Persistence Tier
	화면계층	비즈니스계층		영속(데이터)계층
	
	1. Presentation Tier(화면계층) : 화면에 보여주는 기술을 사용하는 영역
			- Servlet/JSP , Spring MVC를 이용한 화면 구성
	2. Business Tier(비즈니스 계층) : 비즈니스 로직을 담고 있는 영역
			-> 고객이 언하는 요구 사항을 반영하는 계층
			-> -Service
	3. Persistence Tier(영속 or 데이터 계층) : 데이터를 어떤 방식으로 보관하고 사용하는가에 대한 설계가 들어가는 영역
			-> 일반적으로 데이터베이스를 많이 이용
			-> 경우에 따라서 네트워크 호출이나 원격 호출 등의 기술이 접목 가능
			-> MyBatis, mybatis-spring

# 영속계층 작업(데이터계층 Persistence Tier) : 데이터베이스를 기준으로 해서 설계
	# CRUD : Create, Read, Update, Delete

	1. 테이블의 칼럼 구조를 반영하는 vo(value object) 클래스 생성
	2. mybatis의 mapper 인터페이스의 작성/xml 처리
	3. 작성한 mapper 인터페이스 테스트
	
	- VO 클래스	: 테이블 설계를 기준으로 작성
	- Mapper : DAO
	- Mapper.xml : DB Query문
		* <mapper> 의 namespace 속성값을 Mapper 인터페이스와 동일한 이름을 주어야 함
		* <select> 태그의 id 속성값은 메소드의 이름과 일치하게 작성
		* resultType 속성 값은 select 쿼리의 결과를 특정 클래스의 객체로 만들기 위해 설정
		* <![CDATA[쿼리]]> 는 부등호를 사용하기 위해 사용
		* MyBatis는 내부적으로 JDBC의 PreparedStatement를 활용하고 필요한 파라미터를 처리하는
		  '?'에 대한 치환은 '#{속성}'을 이용해서 처리


# 비즈니스 계층(Business Tier) : 로직을 기준으로 해서 처리 
	-  -Service / -ServiceImpl 
	1. -Service (interface)
		: 메소드를 설계할 때는 로직의 이름을 붙이는 것이 관례
		: 반환해야 할 데이터가 있는 'select'를 해야 하는 메소드는 리턴 타입을 지정할 수 있음
			ex) 특정 게시물을 가져올때, 전체리스트를 구할 때 
	2. -ServiceImpl (-Service 인터페이스 구현)
		- @Service : 계층 구조상 주로 비즈니스 영역을 담당하는 객체임을 표시하기 위해 사용
				: 패키지를 읽어 들이는 동안 처리
		- 정상적으로 작동하기 위해서는 -Mapper 객체가 필요
		- @AllArgsConstructor : 모든 파라미터를 이용하는 생성자를 만듦
		- 스프링의 빈으로 인식하기 위해 RootConfig에 @Service 어노테이션이 있는 패키지를 추가해야 함
			@ComponentScan(basePackages="패키지명")

# 프레젠테이션(웹) 계층
	- Controller는 하나의 클래스 내에서 여러 메소드를 작성하고
	  @RequestMapping 등을 이용해서 URL을 분기하는 구조로 작성할 수 있음
	- @Controller : 스프링 빈으로 인식할 수 있도록 함
	- @RequestMapping : 요청에 대해 어떤 Controller, 어떤 메소드가 처리할지를 맵핑하기 위한 어노테이션
	- -Controller는 -Service에 대해 의존적이므로 @AllArgsConstructor를 이용해 생성자를 만드로 자동으로 주입하도록 함
		(만일 생성자를 만들지 않을 경우 @Setter(onMethod_={@Autowired})를 이용해서 처리)
	- RedirectAttributes :  작업이 끝난 후 다시 목록 화면으로 이동하기 위함
				리턴 시에는 'redirect:'를 사용함 -> 스프링mvc가 내부적으로 response.sendRedirect()를 처리해줌
			# rttr.addAttribute로 전달한 값은 url뒤에  붙으며, 리프레시해도 데이터가 유지된다.
			# rttr.addFlashAttribute로 전달한 값은 url뒤에 붙지 않는다. 
				일회성이라 리프레시할 경우 데이터가 소멸한다.
				또한 2개이상 쓸 경우, 데이터는 소멸한다. 
				따라서 맵을 이용하여 한번에 값전달해야한다.
	
# 한글이 깨지는 경우
	- WebConfig 클래스에서 필터를 getServletFilters()를 재정의

#  modal
	: 기본적으로 <div>를 화면에 특정 위치에 보여주고 배경이 되는 <div>에 배경색을 입혀서 처리
	: 활성화된 <div>를 선택하지 않고는 다시 원래의 화면을 볼 수 없도록 막기 때문에 메시지를 보여주는데 좋음


# @GetMapping / @PostMapping
	: URL을 배열로 처리할 수 있음 -> 하나의 메소드로 여러 URL을 처리 
	ex) @GetMapping({"/get", "/modify"})


# DATABASE
	# order by 는 데이터가 많아질 경우 시간이 오래걸리기 때문에 사용하지 않는 것이 좋음
		--> 힌트 사용
	- 힌트 자체는 SQL로 처리되지 않기 때문에 그 뒤에 칼럼명이 나오더라도 별도의 ','로 처리되지 않음
	SELECT /*+ Hint naame (param..) */ column name, ... FROM table name ..
	ex) select /*+ index_desc(tbl_board pk_board) */ * from tbl_board where bno >0;

	- FULL 힌트 : 테이블의 모든 데이터를 스캔
		select /*+ FULL(tbl_board) */ * from tbl_board order by bno desc;
	
	# ROWNUM : 실제 데이터가 아니고 테이블에서 데이터를 추출한 후에 처리되는 변수
		-> 상황에 따라서 그 값이 매번 달라질 수 있음
		-> 이를 사용해서 데이터에 순번을 붙여 페이지 처리를 함
		-> 반드시 1이 포함되도록 해야 함

		1. 필요한 순서로 정렬된 데이터에 rownum을 붙인다.
		2. 처음부터 해당 페이지의 데이터를 rownum<=30과 같은 조건을 이용해서 구한다.
		3. 구해놓은 데이터를 하나의 테이블처럼 간주하고 인라인뷰로 처리한다.
		4. 인라인뷰에서 필요한 데이터만을 남긴다.


# 페이징 처리를 하기 위해서 
	1) 페이지 번호(pageNum)
	2) 한 페이지당 몇 개의 데이터(amount)
  가 필요함

# 페이지 보여주기
	1) 브라우저 주소창에서 페이지 번호를 전달해서 결과를 확인
	2) JSP에서 페이지 번호를 출력
	3) 각 페이지 번호에 클릭 이벤트 처리
	4) 전체 데이터 개수 반영해서 페이지 번호 조절

# 페이징 처리할 때 필요한 정보들
	1) 현재 페이지 번호(page)
	2) 이전과 다음으로 이동 가능한 링크의 표시 여부(prev, next)
		this.prev = this.startPage>1;
		this.next = this.endPage<realEnd;
	3) 화면에서 보여지는 페이지의 시작 번호와 끝 번호(startPage, endPage)
		this.endPage = (int)(Math.ceil(페이지번호/10.0))*10;
			(Math.ceil : 소수점을 올림으로 처리)
		realEnd =(int) (Math.ceil((total *1.0)/amout));
		if(realEnd <this.endPage){
			this.endPage=realEnd;
		}
		this.startPage = this.endPage-9;

# 페이지 번호 이벤트 처리
	- <a> 태그의 href 속성을 이용하는 방법을 사용
		-> 직접 링크를 처리하는 방식의 경우 검색 조건이 붙고 난 후에 처리가 복잡
	- JavaScript를 통해서 처리

#검색 : 검색 조건/키워드
	<select> 관리자용, <checkbox> 주로 사용

	- 제목/내용/작성자와 같이 단일 항목 검색
		ex) select * from (select /*+index_desc(tbl_board pk_board) */ rownum rn, bno, title, content, writer, regdate, updatedate
			from tbl_board where title like '%Test%' and rownum <=20) where rn>10;
	- 제목 or 내용, 제목 or 작성자, 내용 or 작성자, 제목 or 내용 or 작성자와 같은 다중 항목 검색
		-> and와 or이 섞여있는 sql을 작성할 때에는 우선 순위 연산자인 '()'을 이용해서 or조건들을 처리해야 함
		ex)
			select * from (select /*+index_desc(tbl_board pk_board)*/ 
			rownum rn, bno, title, content, writer, regdate, updatedate
			from tbl_board where (title like '%이%' or content like '%이%') and rownum<=20)
			where rn<10;

	- if / choose(when, otherwise) / trim(where, set) / foreach
		1) if : test라는 속성과 함께 특정한 조건이 true가 되었을 때 포함된 sql을 사용하고자 할 때 작성
			- 검색 조건이 'T'면 제목(title)이 키워드(keyword)인 항목을 검색
			- 검색 조건이 'C'면 내용(content)이 키워드(keyword)인 항목을 검색
			- 검색 조건이 'W'면 작성자(writer)이 키워드(keyword)인 항목을 검색
			# if 안에 들어가는 표현식은 OGNL 표현식을 이용
		2) choose(when, otherwise) : 여러 상황들 중 하나의 상황에서만 동작
				java의 'if~else'나 jstl <choose>와 유사
			-<otherwise>는 모든 조건이 충족되지 않을 경우에 사용
		3) trim, where, set 
			- 단독으로 사용되지 않고 <if>, <choose>와 같은 태그들을 내포하여 sql들을 연결해주고
			  앞 뒤에 필요한 구문들(and, or, where 등)을 추가하거나 생략하는 역할
			- trim은 태그의 내용을 앞의 내용과 관련되어 원하는 접두/접미를 처리할 수 있음
				prefix, suffix, prefixOverrides, suffixOverides 속성 지정 가능
		4) foreach
			- List, 배열, 맵 등을 이용해서 루프 처리 가능
			- 주로 in 조건에서 많이 사용
			- 경우에 따라 복잡한 where 조건을 만들때에도 사용
			- 배열이나 List를 이용하는 경우 item 속성만을 이용하면 되고 
			  map의 형태로 key와 value를 이용해야 할 때에는 index와 item 속성 둘 다 이용
	
	- sql, include와 검색 데이터의 개수 처리
		1. Mybatis는 <sql>이라는 태그를 이용해서 sql의 일부를 별도로 보관하고 필요한 경우
		   include시키는 형태로 사용 가능
		   id 속성을 이용해서 필요한 경우에 동일한 sql의 일부를 재사용 할 수 있게 해줌

# 화면에서 검색 조건 처리
	- 페이지 번호가 파라미터로 유지되었던 것처럼 검색 조건과 키워드도 항상 화면 이동시 같이 전달되어야 함
	- 화면에서 검색 버튼을 클릭하면 새로 검색을 한다는 것으로 1페이지로 이동해야함
	- 한글의 경우 get 방식으로 이동하는 경우 문제가 생길 수 있으므로 주의

# UriComponentsBuilder 
	- 웹페이지에서 매번 파라미터 유지하는 것이 힘들 때 
		-> 여러 개의 파라미터들을 연결해서 url의 형태로 만들어 주는 기능을 가짐
	- 주로 javascript를 사용할 수 없는 상황에서 링크를 처리해야 하는 상황에서 사용

	ex) public String getListLink() {		**Criteria.java
		UriComponentsBuilder builder = UriComponentsBuilder.fromPath("")
				.queryParam("pageNum", this.pageNum)
				.queryParam("amount", this.getAmount())
				.queryParam("type", this.getType())
				.queryParam("keyword", this.getKeyword());
		
		return builder.toUriString();
		}
	   ** Controller에서 간결해짐
		    @PostMapping("/remove")
		public String remove(@RequestParam("bno") Long bno, @ModelAttribute("cri") Criteria cri, RedirectAttributes rttr){
			log.info("remove..." + bno);
			if (service.remove(bno)) {
				rttr.addFlashAttribute("result", "success");
			}
			/*
			rttr.addAttribute("pageNum", cri.getPageNum());
			rttr.addAttribute("amount", cri.getAmount());
			rttr.addAttribute("type", cri.getType());
			rttr.addAttribute("keyword", cri.getKeyword());*/
			
			return "redirect:/board/list"+cri.getListLink();
		}
#JSON -> jackson-databind 라이브러리 pom.xml에 추가
		<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.core/jackson-databind -->
		<dependency>
		    <groupId>com.fasterxml.jackson.core</groupId>
		    <artifactId>jackson-databind</artifactId>
		    <version>2.9.8</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/com.fasterxml.jackson.dataformat/jackson-dataformat-xml -->
		<dependency>
		    <groupId>com.fasterxml.jackson.dataformat</groupId>
		    <artifactId>jackson-dataformat-xml</artifactId>
		    <version>2.9.8</version>
		</dependency>
		<!-- https://mvnrepository.com/artifact/com.google.code.gson/gson --> 자바 인스턴스를 제이슨 타입의 문자열로 변환
		<dependency>
		    <groupId>com.google.code.gson</groupId>
		    <artifactId>gson</artifactId>
		    <version>2.8.5</version>
		</dependency>

# REST (Representational State Transfer)
	- 하나의 URI는 하나의 고유한 리소스(Resource)를 대표하도록 설계+전송방식-> 원하는 작업 지정
		URI+ get/post/put/delete/...
	
	1. @RestController : Controller가 REST 방식을 처리하기 위한 것임을 명시
		- 문자열 반환
			- 순수한 데이터를 반환하는 형태 : 다양한 포맷 데이터를 전송할 수 있음
			- 주로 일반 문자열이나 json, xml등을 사용
			- 기존 @Controller는 문자열을 반환하는 경우에만 jsp 파일의 이름으로 처리하지만
			  @RestController의 경우에는 순수한 데이터가 됨
		- 객체의 반환 
			- Json이나 xml 이용
		- ResponseEntity
			- 데이터와 함께 http 헤더의 상태 메시지 등을 같이 전달하는 용도로 사용
		- @PathVariable : url 경로의 일부를 파라미터로 사용할 때 사용
			- '{}'을 이용해서 변수명을 지정함
		  @RequestBody : json 데이터를 원하는 타입의 객체로 변환해야 하는 경우에 주로 사용
			- 전달된 요청의 내용을 이용해서 해당 파라미터의 타입으로 변환 요구

	- rest방식으로 동작하는 url을 설계할 때는 pk를 기준으로 작성하는 것이 좋음
		- pk만으로도 조회, 수정 , 삭제가 가능하기 때문
		- 댓글의 목록은 pk를 사용할 수 없어서 파라미터로 필요한 게시물의 번호와 페이지 번호 정보들을
		  url에서 표현하는 방식을 사용
		- 주의해야 하는 점은 브라우저나 외부에서 서버를 호출할 때 데이터의 포맷과 서버에서 보내주는
		  데이터의 타입을 명확히 설계해야 함

# 모듈 패턴 : 관련 있는 함수들을 하나의 모듈처럼 묶음으로 구성하는 것

# 시간 처리
	 - xml이나 json 형태로 데이터를 받을 때는 순수하게 숫자로 표현되는 시간 값이 나옴 -> 변환하는 것이 좋음
	 - displayTime() 을 적용하면 24시간이 지난 댓글은 날짜만 표시되고, 24시간 이내의 글은 시간으로 표시


#AOP	. 관점 지향 프로그래밍
	- 파라미터가 올바르게 들어왔는지
	- 이 작업을 하는 사용자가 적절한 권한을 가진 사용자인지
	- 이 작업에서 발생할 수 있는 모든 예외는 어떻게 처리해야 하는지
	==> 개발자가 염두에 두어야 하는 일들은 별도의 '관심사'로 분리하고 핵심 비즈니스 로직만을 작성할 것을 권장
	==> 과거에 개발자가 작성했던 '관심사+비즈니스 로직'을 분리해서 별도의 코드로 작성, 실행할 때 이를 결합
	==> 기존의 코드를 수정하지 않고, 원하는 기능들과 결합할 수 있는 패러다임

	- target : 순수한 비즈니스 로직, 어떠한 관심사들과도 관계를 맺지 않음, 순수한 코어(core)
	- proxy : target을 전체적으로 감싸고 있는 존재
		   내부적으로 target을 호출하지만 중간에 필요한 관심사들을 거쳐서 target을 호출하도록 자동 혹은 수동으로 작성
		   대부분 스프링aop 기능을 이용해서 자동으로 생성되는(auto proxy)방식을 이용
	- JoinPoint : target 객체가 가진 메소드
			외부에서의 호출은 proxy 객체를 통해서 target 객체의 joinpoint를 호출하는 방식이라고 할 수 있음
	- Pointcut : target에는 여러 메소드가 존재해서 어떤 메소드에 관심사를 결합할 것인지 결정해야함
		      관심사과 비즈니스 로직이 결합되는 지점
		1. execution(@execution)
			메소드를 기준으로 pointcut 설정
		2. within(@within)
			특정한 타입(클래스)을 기준으로 pointcut 설정
		3. this
			주어진 인터페이스를 구현한 객체를 대상으로  pointcut을 설정
		4. args(@args)
			특정한 파라미터를 가지는 대상들만을 pointcut으로 설정
		5. @annotation
			특정한 어노테이션이 적용된 대상들만을 pointcut으로 설정
	- 관심사(concern) 
		1. Aspect : 추상적인 개념
		2. Advice : Aspect를 구현한 코드
			(1) Before Advice
				Target의 Joinpoint를 호출하기 전에 실행되는 코드, 코드 실행 자체에는 관여X
			(2) After Returning Advice
				모든 실행이 정상적으로 이루어진 후에 동작하는 코드
			(3) After Throwing Advice
				예외가 발생한 뒤에 동작하는 코드
			(4) After Advice
				정상적으로 실행되거나 예외가 발생했을 때 구분 없이 실행되는 코드
			(5) Around Advice
				메소드의 실행 자체를 제어할 수 있는 가장 강력한 코드
				직접 대상 메소드를 호출하고 결과나 예외를 처리할 수 있음
		=> 스프링 3 이후에는 어노테이션만으로도 모든 설정이 가능(or xml)
			
	- AOP 기능은 주로 일반적인 java api를 이용하는 클래스(pojo)들에 적용
	- controller에 적용이 불가능한 것은 아니지만 controller의 경우 인터셉터나 필터 등을 이용
	- 주로 서비스 계층에 aop 적용
	
	# LogAdvice : 로그를 기록 => log.info~ 기능 대신
	# @AfterThrowing 지정된 대상이 예외를 발생한 후에 동작하면서 문제를 찾을 수 있도록 도와줌
	# @Before :어떤 위치에 Advice를 적용할 것인지 결정하는 pointcut, 설정시에 args를 이용하면 간단히 파라미터를 구할 수 있음	

	# @Around : 직접 대상 메소드를 실행할 수 있는 권한을 가짐, 메소드의 실행 전과 후에 처리가 가능
	   ProceedingJoinPoint : @Around와 같이 결합해서 파라미터나 예외 등을 처리할 수 있음
	# @Around가 적용되는 메소드의 경우 리턴 타입이 void가 아닌 타입으로 설정하고 
		메소드의 실행 결과 역시 직접 반환하는 형태로 작성해야 한다.

# 스프링에서 트랜잭션 관리 
	- 트랜잭션(Transaction) : 한 번에 이루어지는 작업의 단위
		1. 원자성(Atomicity) : 하나의 트랜잭션은 모두 하나의 단위로 처리되어야 함
					  어떤 작업이 잘못되는 경우 모든 것은 다시 원점으로 되돌아가야만 함
		2. 일관성(Consistency) : 데이터베이스의 모든 데이터는 일관성을 유지해야함, 
		3. 격리(Isolation) : 트랜잭션으로 처리되는 중간에 외부에서의 간섭은 없어야만 함
		4. 영속성(Durability) : 트랜잭션이 성공적으로 처리되면 그 결과는 영속적으로 보관되어야 함


# 정규화 : 데이터베이스의 저장 구조를 효율적으로 관리하기 위한 작업
	     '중복된 데이터를 제거'해서 데이터의 저장의 효율을 올리는것
	     1. 테이블은 늘어나고
	     2. 각 테이블의 데이터 야은 줄어듦

	     - 시간이 흐르면 변경되는 데이터를 칼럼으로 기록하지 않는다
		ex) 생년월일 : 칼럼으로 기록
		     나이 : 칼럼으로 유지 x (특별한 경우엔 가능)
	     - 계산이 가능한 데이터를 칼럼으로 기록하지 않음
		ex) 주문과 주문 상세가 별도의 테이블로 분리되어 있다면 사용자가 한 번에 몇 개의 상품을 주문했는지는 기록x
	     - 누구에게나 정해진 값을 이용하는 경우 데이터베이스에서 취급하지 않음

	- 정규화가 진행될수록 테이블은 점점 더 순수한 형태가 되어가고 순수한 형태가 될수록 트랜잭션 처리의 대상에서 멀어짐
				--> 쿼리 등을 이용해서 필요한 데이터를 가져오는 거는 불편해질 수 있음
				--> 조인이나 서브쿼리를 이용해야함
	- 반정규화(역정규화) : 정규화의 반대, 중복이나 계산되는 값을 데이터베이스 상에 보관하고 대신에 조인이나 서브쿼리의 사용을 줄이는 방식
			ex) 게시글의 댓글
			-> 좀 더 빠른 결과를 얻기 위해
			ex) 댓글이 추가될 때 댓글 테이블에 댓글을 insert하고 댓글의 숫자는 보드 테이블에 update해주는 작업이 필요
				-> 하나의 트랜잭션으로 관리되어야 하는 작업

#  @Transactional	// 트랜잭션 처리가 될 수 있도록
		몇가지 중요한 속성이 있으니 경우에 따라 속성들을 조정해서 사용해야 함
	1. 전파(Propagation) 속성
		- PROPACATION_MADATORY
			작업은 반드시 특정한 트랜잭션이 존재한 상태에서만 가능
		- PROPAGATION_NESTED
			기존에 트랜잭션이 있는 경우, 포함되어서 실행
		- PROPAGATION_NAVER
			트랜잭션 상황하에 실행되면 예외 발생
		- PROPAGATION_NOT_SUPPORTED
			트랜잭션이 있는 경우 끝날때까지 보류된 후 실행
		- PROPAGATION_REQUIRED (기본 설정)
			트랜잭션이 있으면 그 상황에서 실행, 없으면 새로운 트랜잭션 실행
		- PROPAGATION_REQUIRED_NEW
			대상은 자신만의 고유한 트랜잭션으로 실행
		- PROPAGATION_SUPPORTS
			트랜잭션을 필요로 하지 않으나, 트랜잭션 상황하에 있다면 포함되어서 실행

	2. 격리(Isolation) 레벨
		- DEFAULT 
			DB 설정, 기본 격리 수준(기본설정)
		- SERIALIZABLE
			가장 높은 격리, 성능 저하의 우려 ㅇ   
		- READ_UNCOMMITED    
			커밋되지 않은 데이터에 대한 읽기를 허용  
		- READ_COMMITED    
			커밋된 데이터에 대해 일기 허용
		- REPEATEABLE_READ   
			동일 필드에 대해 다중 접근 시 모두 동일한 결과를 보장    
			
	3. Read-only 속성  
	4. Rollback-for-예외  
	5. No-rollback-for-예외    
