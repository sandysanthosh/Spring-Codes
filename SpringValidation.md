### JSR 303:

	* @Size
	* @NotEmpty
	* @DateTimeFormat(pattern="MM/dd/yyyy")
	* @Phone


### Controller:

	* @Valid 
	* @BindingResult 
	* InitBinder
	* springForm:errors


### POJO:

```

public class Customer {

	@Size(min=2, max=30) 
    private String name;
     
    @NotEmpty @Email
    private String email;
     
    @NotNull @Min(18) @Max(100)
    private Integer age;
     
    @NotNull
    private Gender gender;
     
    @DateTimeFormat(pattern="MM/dd/yyyy")
    @NotNull @Past
    private Date birthday;
    
    @Phone
    private String phone;
    
    Gettes and Setters
    
```
 
 
### Employee.java:

```
  private int id;
	private String name;
	private String role;
  
```
    
### Web.xml:

```
<?xml version="1.0" encoding="UTF-8"?>
<web-app version="2.5" xmlns="https://java.sun.com/xml/ns/javaee"
	xmlns:xsi="https://www.w3.org/2001/XMLSchema-instance"
	xsi:schemaLocation="https://java.sun.com/xml/ns/javaee https://java.sun.com/xml/ns/javaee/web-app_2_5.xsd">
	
	<!-- Processes application requests -->
	<servlet>
		<servlet-name>appServlet</servlet-name>
		<servlet-class>org.springframework.web.servlet.DispatcherServlet</servlet-class>
		<init-param>
			<param-name>contextConfigLocation</param-name>
			<param-value>/WEB-INF/spring/spring.xml</param-value>
		</init-param>
		<load-on-startup>1</load-on-startup>
	</servlet>
		
	<servlet-mapping>
		<servlet-name>appServlet</servlet-name>
		<url-pattern>/</url-pattern>
	</servlet-mapping>

</web-app>

```

### Spring.xml:

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
	
	<beans:bean id="employeeValidator" class="com.journaldev.spring.form.validator.EmployeeFormValidator" />
	
	<beans:bean id="messageSource"
		class="org.springframework.context.support.ReloadableResourceBundleMessageSource">
		<beans:property name="basename" value="classpath:message" />
		<beans:property name="defaultEncoding" value="UTF-8" />
	</beans:bean>
	
	<context:component-scan base-package="com.journaldev.spring" />
	
</beans:beans>


```
    
### Phone Interface:

```

@Documented
@Constraint(validatedBy = PhoneValidator.class)
@Target( { ElementType.METHOD, ElementType.FIELD })
@Retention(RetentionPolicy.RUNTIME)
public @interface Phone {
 
     
    String message() default "{Phone}";
     
    Class<?>[] groups() default {};
     
    Class<? extends Payload>[] payload() default {};
      
}

```


### PhoneValidator.java:

```

public class PhoneValidator implements ConstraintValidator<Phone, String> {

	@Override
	public void initialize(Phone paramA) {
	}

	@Override
	public boolean isValid(String phoneNo, ConstraintValidatorContext ctx) {
		if(phoneNo == null){
			return false;
		}
		//validate phone numbers of format "1234567890"
        if (phoneNo.matches("\\d{10}")) return true;
        //validating phone number with -, . or spaces
        else if(phoneNo.matches("\\d{3}[-\\.\\s]\\d{3}[-\\.\\s]\\d{4}")) return true;
        //validating phone number with extension length from 3 to 5
        else if(phoneNo.matches("\\d{3}-\\d{3}-\\d{4}\\s(x|(ext))\\d{3,5}")) return true;
        //validating phone number where area code is in braces ()
        else if(phoneNo.matches("\\(\\d{3}\\)-\\d{3}-\\d{4}")) return true;
        //return false if nothing matches the input
        else return false;
	}
  
```

### CustomerController .java:

```


package com.journaldev.spring.form.controllers;

import java.util.HashMap;
import java.util.Map;

import javax.validation.Valid;

import org.slf4j.Logger;
import org.slf4j.LoggerFactory;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.validation.BindingResult;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;

import com.journaldev.spring.form.model.Customer;

@Controller
public class CustomerController {

	private static final Logger logger = LoggerFactory
			.getLogger(CustomerController.class);
	
	private Map<String, Customer> customers = null;
	
	public CustomerController(){
		customers = new HashMap<String, Customer>();
	}

	@RequestMapping(value = "/cust/save", method = RequestMethod.GET)
	public String saveCustomerPage(Model model) {
		logger.info("Returning custSave.jsp page");
		model.addAttribute("customer", new Customer());
		return "custSave";
	}

	@RequestMapping(value = "/cust/save.do", method = RequestMethod.POST)
	public String saveCustomerAction(
			@Valid Customer customer,
			BindingResult bindingResult, Model model) {
		if (bindingResult.hasErrors()) {
			logger.info("Returning custSave.jsp page");
			return "custSave";
		}
		logger.info("Returning custSaveSuccess.jsp page");
		model.addAttribute("customer", customer);
		customers.put(customer.getEmail(), customer);
		return "custSaveSuccess";
	}

}

```

### message_en.properties file:

```


#application defined error messsages
id.required=Employee ID is required
name.required=Employee Name is required
role.required=Employee Role is required
negativeValue={0} can't be negative or zero

#Spring framework error messages to be used when conversion from form data to bean fails
typeMismatch.int={0} Value must be an integer
typeMismatch.java.lang.Integer={0} must be an integer
typeMismatch={0} is of invalid format

#application messages for annotations, {ValidationClass}.{modelObjectName}.{field}
#the {0} is field name, other fields are in alphabatical order, max and then min  
Size.customer.name=Customer {0} should be between {2} and {1} characters long
NotEmpty.customer.email=Email is a required field
NotNull.customer.age=Customer {0} should be in years

#Generic annotation class messages
Email=Email address is not valid
NotNull=This is a required field
NotEmpty=This is a required field
Past=Date should be Past

#Custom validation annotation
Phone=Invalid format, valid formats are 1234567890, 123-456-7890 x1234

```


### CustSave.Jsp


```

<%@ page language="java" contentType="text/html; charset=UTF-8"
	pageEncoding="UTF-8"%>
<!DOCTYPE html PUBLIC "-//W3C//DTD HTML 4.01 Transitional//EN" "https://www.w3.org/TR/html4/loose.dtd">
<%@ taglib uri="https://www.springframework.org/tags/form"
	prefix="springForm"%>
<html>
<head>
<meta http-equiv="Content-Type" content="text/html; charset=UTF-8">
<title>Customer Save Page</title>
<style>
.error {
	color: #ff0000;
	font-style: italic;
	font-weight: bold;
}
</style>
</head>
<body>

	<springForm:form method="POST" commandName="customer"
		action="save.do">
		<table>
			<tr>
				<td>Name:</td>
				<td><springForm:input path="name" /></td>
				<td><springForm:errors path="name" cssClass="error" /></td>
			</tr>
			<tr>
				<td>Email:</td>
				<td><springForm:input path="email" /></td>
				<td><springForm:errors path="email" cssClass="error" /></td>
			</tr>
			<tr>
				<td>Age:</td>
				<td><springForm:input path="age" /></td>
				<td><springForm:errors path="age" cssClass="error" /></td>
			</tr>
			<tr>
				<td>Gender:</td>
				<td><springForm:select path="gender">
						<springForm:option value="" label="Select Gender" />
						<springForm:option value="MALE" label="Male" />
						<springForm:option value="FEMALE" label="Female" />
					</springForm:select></td>
				<td><springForm:errors path="gender" cssClass="error" /></td>
			</tr>
			<tr>
				<td>Birthday:</td>
				<td><springForm:input path="birthday" placeholder="MM/dd/yyyy"/></td>
				<td><springForm:errors path="birthday" cssClass="error" /></td>
			</tr>
			<tr>
				<td>Phone:</td>
				<td><springForm:input path="phone" /></td>
				<td><springForm:errors path="phone" cssClass="error" /></td>
			</tr>
			<tr>
				<td colspan="3"><input type="submit" value="Save Customer"></td>
			</tr>
		</table>

	</springForm:form>

</body>
</html>

```

### custSaveSuccess

```

<%@ taglib uri="https://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ taglib prefix="fmt" uri="https://java.sun.com/jsp/jstl/fmt" %>

<%@ page session="false" %>
<html>
<head>
	<title>Customer Saved Successfully</title>
</head>
<body>
<h3>
	Customer Saved Successfully.
</h3>

<strong>Customer Name:${customer.name}</strong><br>
<strong>Customer Email:${customer.email}</strong><br>
<strong>Customer Age:${customer.age}</strong><br>
<strong>Customer Gender:${customer.gender}</strong><br>
<strong>Customer Birthday:<fmt:formatDate value="${customer.birthday}" type="date" /></strong><br>

</body>
</html>


```


### empSaveSuccess.jsp:

```

<%@ taglib uri="https://java.sun.com/jsp/jstl/core" prefix="c" %>
<%@ page session="false" %>
<html>
<head>
	<title>Employee Saved Successfully</title>
</head>
<body>
<h3>
	Employee Saved Successfully.
</h3>

<strong>Employee ID:${emp.id}</strong><br>
<strong>Employee Name:${emp.name}</strong><br>
<strong>Employee Role:${emp.role}</strong><br>

</body>
</html>


```







