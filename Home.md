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

