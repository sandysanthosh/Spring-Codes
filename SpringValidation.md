

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
    
### WEb.xml:

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


