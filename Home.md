### Web.xml:

```

<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5" xmlns="https://java.sun.com/xml/ns/javaee"
	xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="https://java.sun.com/xml/ns/javaee https://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">

	<!-- Spring Security Configuration File -->
	<context-param>
		<param-name>contextConfigLocation</param-name>
		<param-value>/WEB-INF/spring/appServlet/spring-security.xml</param-value>
	</context-param>

	<!-- Creates the Spring Container shared by all Servlet and Filters -->
	<listener>
		<listener-class>org.springframework.web.context.ContextLoaderListener</listener-class>
	</listener>
	<listener>
		<listener-class>org.springframework.security.web.session.HttpSessionEventPublisher</listener-class>
	</listener>
	
	<session-config>
		<session-timeout>15</session-timeout>
	</session-config>

	<!-- Spring Security Filter -->
	<filter>
		<filter-name>springSecurityFilterChain</filter-name>
		<filter-class>org.springframework.web.filter.DelegatingFilterProxy</filter-class>
	</filter>

	<filter-mapping>
		<filter-name>springSecurityFilterChain</filter-name>
		<url-pattern>/*</url-pattern>
	</filter-mapping>
	
	<!-- Spring MVC - START -->
	<servlet>
		<servlet-name>appServlet</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>
			/WEB-INF/spring/appServlet/servlet-context.xml
			</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>

	<servlet-mapping>
		<servlet-name>appServlet</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>
	<!-- Spring MVC - END -->

</web-app>

```

### UserDetailsService.java:

```

package com.journaldev.spring.security.dao;

import java.util.Collection;
import java.util.List;

import org.apache.commons.logging.Log;
import org.apache.commons.logging.LogFactory;
import org.springframework.security.core.GrantedAuthority;
import org.springframework.security.core.authority.SimpleGrantedAuthority;
import org.springframework.security.core.userdetails.UserDetails;
import org.springframework.security.core.userdetails.UserDetailsService;
import org.springframework.security.core.userdetails.UsernameNotFoundException;

public class AppUserDetailsServiceDAO implements UserDetailsService {

	protected final Log logger = LogFactory.getLog(getClass());
	
	@Override
	public UserDetails loadUserByUsername(final String username)
			throws UsernameNotFoundException {
		
		logger.info("loadUserByUsername username="+username);
		
		if(!username.equals("pankaj")){
			throw new UsernameNotFoundException(username + " not found");
		}
		
		//creating dummy user details, should do JDBC operations
		return new UserDetails() {
			
			private static final long serialVersionUID = 2059202961588104658L;

			@Override
			public boolean isEnabled() {
				return true;
			}
			
			@Override
			public boolean isCredentialsNonExpired() {
				return true;
			}
			
			@Override
			public boolean isAccountNonLocked() {
				return true;
			}
			
			@Override
			public boolean isAccountNonExpired() {
				return true;
			}
			
			@Override
			public String getUsername() {
				return username;
			}
			
			@Override
			public String getPassword() {
				return "pankaj123";
			}
			
			@Override
			public Collection<? extends GrantedAuthority> getAuthorities() {
				List<SimpleGrantedAuthority> auths = new java.util.ArrayList<SimpleGrantedAuthority>();
				auths.add(new SimpleGrantedAuthority("Admin"));
				return auths;
			}
		};
	}

}


```
### HomeController.java:

```

package com.journaldev.spring.controller;

import java.text.DateFormat;
import java.util.Date;
import java.util.Locale;

import javax.servlet.http.HttpServletRequest;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.PathVariable;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

@Controller
public class HomeController {
	
	private static final Logger logger = LoggerFactory.getLogger(HomeController.class);
	
	@RequestMapping(value = "/home", method = RequestMethod.GET)
	public String home(Locale locale, Model model) {
		logger.info("Welcome home! The client locale is {}.", locale);
		Date date = new Date();
		DateFormat dateFormat = DateFormat.getDateTimeInstance(DateFormat.LONG, DateFormat.LONG, locale);
		
		String formattedDate = dateFormat.format(date);
		
		model.addAttribute("serverTime", formattedDate );
		
		return "home";
	}
	
	@RequestMapping(value = "/emp/get/{id}", method = RequestMethod.GET)
	public String getEmployee(Locale locale, Model model,@PathVariable("id") int id) {
		logger.info("Welcome user! Requested Emp ID is: "+id);
		Date date = new Date();
		DateFormat dateFormat = DateFormat.getDateTimeInstance(DateFormat.LONG, DateFormat.LONG, locale);
		
		String formattedDate = dateFormat.format(date);
		
		model.addAttribute("serverTime", formattedDate );
		model.addAttribute("id", id);
		model.addAttribute("name", "Pankaj");
		
		return "employee";
	}
	
	@RequestMapping(value="/login")
	public String login(HttpServletRequest request, Model model){
		return "login";
	}
	
	@RequestMapping(value="/logout")
	public String logout(){
		return "logout";
	}
	
	@RequestMapping(value="/denied")
	public String denied(){
		return "denied";
	}
}

```

### home.jsp

```

<%@ taglib uri="https://java.sun.com/jsp/jstl/core" prefix="c"%>
<%@ page session="false"%>
<html>
<head>
<title>Home</title>
</head>
<body>
	<h1>Hello world!</h1>

	<P>The time on the server is ${serverTime}.</P>
</body>
</html>

```

### Employee.jsp:

```

<%@ taglib uri="https://java.sun.com/jsp/jstl/core" prefix="c"%>
<%@ page session="false"%>
<html>
<head>
<title>Get Employee Page</title>
</head>
<body>
	<h1>Employee Information</h1>
	<p>
		Employee ID:${id}<br> Employee Name:${name}<br>
	</p>
	<c:if test="${pageContext.request.userPrincipal.name != null}">
	Hi ${pageContext.request.userPrincipal.name}<br>
	
	<c:url var="logoutAction" value="/j_spring_security_logout"></c:url>
	
	<form action="${logoutAction}" method="post">
		<input type="submit" value="Logout" />
	</form>
	</c:if>
</body>
</html>


```

### Login.jsp:

```

<%@ taglib uri="https://java.sun.com/jsp/jstl/core" prefix="c"%>

<html>

<head>
<title>Login Page</title>
</head>
<body>
	<h3>Login with Username and Password</h3>
	<c:url var="loginUrl" value="/j_spring_security_check"></c:url>
	<form action="${loginUrl}" method="POST">
		<table>
			<tr>
				<td>User ID:</td>
				<td><input type='text' name='username' /></td>
			</tr>
			<tr>
				<td>Password:</td>
				<td><input type='password' name='password' /></td>
			</tr>
			<tr>
				<td colspan='2'><input name="submit" type="submit"
					value="Login" /></td>
			</tr>
		</table>
	</form>
</body>
</html>


```

### Logou.jsp:

```

<html>
<head>
	<title>Logout Page</title>
</head>
<body>
<h2>
	Logout Successful!  
</h2>

</body>
</html>

```
### Denied.jsp:

```
<html>
<head>
	<title>Access Denied</title>
</head>
<body>
<h1>
	Access Denied!  
</h1>

</body>
</html>

```



### Spring Configuration:

```
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns="https://www.springframework.org/schema/mvc"
	xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
	xmlns:beans="https://www.springframework.org/schema/beans"
	xmlns:context="https://www.springframework.org/schema/context"
	xsi:schemaLocation="https://www.springframework.org/schema/mvc https://www.springframework.org/schema/mvc/spring-mvc.xsd
		https://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd
		https://www.springframework.org/schema/context https://www.springframework.org/schema/context/spring-context.xsd">

	<!-- DispatcherServlet Context: defines this servlet's request-processing infrastructure -->
	
	 <!-- Enables the Spring MVC @Controller programming model -->
	<annotation-driven />

        <!-- Handles HTTP GET requests for /resources/** by efficiently serving up static resources in the ${webappRoot}/resources directory -->
	<resources mapping="/resources/**" location="/resources/" />

	 <!-- Resolves views selected for rendering by @Controllers to .jsp resources in the /WEB-INF/views directory -->
	<beans:bean class="org.springframework.web.servlet.view.InternalResourceViewResolver">
		<beans:property name="prefix" value="/WEB-INF/views/" />
		<beans:property name="suffix" value=".jsp" />
	</beans:bean>
	
	<context:component-scan base-package="com.journaldev.spring.controller" />
	
</beans:beans>
```

### Spring-security.xml:

```
<?xml version="1.0" encoding="UTF-8"?>
<beans:beans xmlns="https://www.springframework.org/schema/security"
	xmlns:beans="https://www.springframework.org/schema/beans" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="https://www.springframework.org/schema/security https://www.springframework.org/schema/security/spring-security.xsd
			https://www.springframework.org/schema/beans https://www.springframework.org/schema/beans/spring-beans.xsd">

	<!-- Configuring RoleVoter bean to use custom access roles, by default roles 
		should be in the form ROLE_{XXX} -->
	<beans:bean id="roleVoter"
		class="org.springframework.security.access.vote.RoleVoter">
		<beans:property name="rolePrefix" value=""></beans:property>
	</beans:bean>

	<beans:bean id="accessDecisionManager"
		class="org.springframework.security.access.vote.AffirmativeBased">
		<beans:constructor-arg name="decisionVoters"
			ref="roleVoter" />
	</beans:bean>

	<http authentication-manager-ref="jdbc-auth"
		access-decision-manager-ref="accessDecisionManager">	
		<intercept-url pattern="/emp/**" access="Admin" />
		<form-login login-page="/login" authentication-failure-url="/denied"
			username-parameter="username" password-parameter="password"
			default-target-url="/home" />
		<logout invalidate-session="true" logout-success-url="/login"
			logout-url="/j_spring_security_logout" />
		<access-denied-handler error-page="/denied"/>
		<session-management invalid-session-url="/login">
			<concurrency-control max-sessions="1"
				expired-url="/login" />
		</session-management>
	</http>

	<authentication-manager id="in-memory-auth">
		<authentication-provider>
			<user-service>
				<user name="pankaj" password="pankaj123" authorities="Admin" />
			</user-service>
		</authentication-provider>
	</authentication-manager>

	<authentication-manager id="dao-auth">
		<authentication-provider user-service-ref="userDetailsService">
		</authentication-provider>
	</authentication-manager>

	<beans:bean id="userDetailsService"
		class="com.journaldev.spring.security.dao.AppUserDetailsServiceDAO" />

	<authentication-manager id="jdbc-auth">
		<authentication-provider>
			<jdbc-user-service data-source-ref="dataSource"
				users-by-username-query="select username,password,enabled from Employees where username = ?"
				authorities-by-username-query="select username,role from Roles where username = ?" />
		</authentication-provider>
	</authentication-manager>

	<!-- MySQL DB DataSource -->
	<beans:bean id="dataSource"
		class="org.springframework.jdbc.datasource.DriverManagerDataSource">

		<beans:property name="driverClassName" value="com.mysql.jdbc.Driver" />
		<beans:property name="url"
			value="jdbc:mysql://localhost:3306/TestDB" />
		<beans:property name="username" value="pankaj" />
		<beans:property name="password" value="pankaj123" />
	</beans:bean>

	<!-- If DataSource is configured in Tomcat Servlet Container -->
	<beans:bean id="dbDataSource"
		class="org.springframework.jndi.JndiObjectFactoryBean">
		<beans:property name="jndiName" value="java:comp/env/jdbc/MyLocalDB" />
	</beans:bean>
</beans:beans>

```

### Pom.xml:

```

<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="https://maven.apache.org/POM/4.0.0" xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="https://maven.apache.org/POM/4.0.0 https://maven.apache.org/maven-v4_0_0.xsd">
	<modelVersion>4.0.0</modelVersion>
	<groupId>com.journaldev.spring</groupId>
	<artifactId>SpringMVCSecurity</artifactId>
	<name>SpringMVCSecurity</name>
	<packaging>war</packaging>
	<version>1.0.0-BUILD-SNAPSHOT</version>
	<properties>
		<java-version>1.6</java-version>
		<org.springframework-version>4.0.2.RELEASE</org.springframework-version>
		<org.aspectj-version>1.7.4</org.aspectj-version>
		<org.slf4j-version>1.7.5</org.slf4j-version>
	</properties>
	<dependencies>
		<!-- Spring -->
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-context</artifactId>
			<version>${org.springframework-version}</version>
			<exclusions>
				<!-- Exclude Commons Logging in favor of SLF4j -->
				<exclusion>
					<groupId>commons-logging</groupId>
					<artifactId>commons-logging</artifactId>
				 </exclusion>
			</exclusions>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-webmvc</artifactId>
			<version>${org.springframework-version}</version>
		</dependency>
		<!-- Spring Security -->
		<dependency>
			<groupId>org.springframework.security</groupId>
			<artifactId>spring-security-config</artifactId>
			<version>3.2.3.RELEASE</version>
		</dependency>
		<dependency>
			<groupId>org.springframework.security</groupId>
			<artifactId>spring-security-web</artifactId>
			<version>3.2.3.RELEASE</version>
		</dependency>
	
		<!-- AspectJ -->
		<dependency>
			<groupId>org.aspectj</groupId>
			<artifactId>aspectjrt</artifactId>
			<version>${org.aspectj-version}</version>
		</dependency>	
		
		<!-- Logging -->
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-api</artifactId>
			<version>${org.slf4j-version}</version>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>jcl-over-slf4j</artifactId>
			<version>${org.slf4j-version}</version>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>org.slf4j</groupId>
			<artifactId>slf4j-log4j12</artifactId>
			<version>${org.slf4j-version}</version>
			<scope>runtime</scope>
		</dependency>
		<dependency>
			<groupId>log4j</groupId>
			<artifactId>log4j</artifactId>
			<version>1.2.15</version>
			<exclusions>
				<exclusion>
					<groupId>javax.mail</groupId>
					<artifactId>mail</artifactId>
				</exclusion>
				<exclusion>
					<groupId>javax.jms</groupId>
					<artifactId>jms</artifactId>
				</exclusion>
				<exclusion>
					<groupId>com.sun.jdmk</groupId>
					<artifactId>jmxtools</artifactId>
				</exclusion>
				<exclusion>
					<groupId>com.sun.jmx</groupId>
					<artifactId>jmxri</artifactId>
				</exclusion>
			</exclusions>
			<scope>runtime</scope>
		</dependency>

		<!-- @Inject -->
		<dependency>
			<groupId>javax.inject</groupId>
			<artifactId>javax.inject</artifactId>
			<version>1</version>
		</dependency>
				
		<!-- Servlet -->
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>servlet-api</artifactId>
			<version>2.5</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>javax.servlet.jsp</groupId>
			<artifactId>jsp-api</artifactId>
			<version>2.1</version>
			<scope>provided</scope>
		</dependency>
		<dependency>
			<groupId>javax.servlet</groupId>
			<artifactId>jstl</artifactId>
			<version>1.2</version>
		</dependency>
	
		<!-- Test -->
		<dependency>
			<groupId>junit</groupId>
			<artifactId>junit</artifactId>
			<version>4.7</version>
			<scope>test</scope>
		</dependency>
		<dependency>
			<groupId>org.springframework</groupId>
			<artifactId>spring-jdbc</artifactId>
			<version>4.0.2.RELEASE</version>
		</dependency>
	</dependencies>
    <build>
        <plugins>
            <plugin>
                <artifactId>maven-eclipse-plugin</artifactId>
                <version>2.9</version>
                <configuration>
                    <additionalProjectnatures>
                        <projectnature>org.springframework.ide.eclipse.core.springnature</projectnature>
                    </additionalProjectnatures>
                    <additionalBuildcommands>
                        <buildcommand>org.springframework.ide.eclipse.core.springbuilder</buildcommand>
                    </additionalBuildcommands>
                    <downloadSources>true</downloadSources>
                    <downloadJavadocs>true</downloadJavadocs>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.apache.maven.plugins</groupId>
                <artifactId>maven-compiler-plugin</artifactId>
                <version>2.5.1</version>
                <configuration>
                    <source>1.6</source>
                    <target>1.6</target>
                    <compilerArgument>-Xlint:all</compilerArgument>
                    <showWarnings>true</showWarnings>
                    <showDeprecation>true</showDeprecation>
                </configuration>
            </plugin>
            <plugin>
                <groupId>org.codehaus.mojo</groupId>
                <artifactId>exec-maven-plugin</artifactId>
                <version>1.2.1</version>
                <configuration>
                    <mainClass>org.test.int1.Main</mainClass>
                </configuration>
            </plugin>
        </plugins>
    </build>
</project>


```

