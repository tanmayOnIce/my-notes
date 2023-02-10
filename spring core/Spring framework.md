# Spring framework basics

## what is framework ?
Think of framework like a DIY piece. You have all the possible component to build a furniture and a manual on how to assemble them. Now if you want to build a wardrobe, you have to find the right parts and assemble them.

## What is use case ?
A _use case_ defines a reason why a person will use your app.

## What is Spring core ?

Spring works on the principle of **Inversion Of Control**. Here _inversion_ means the dependencies are controlled by Spring.
> In term of _control_ refers to creation of instance or calling the method.
> Spring create (or manages) objects of the class we defined.
> Ioc (Inversion of control) container here is called **Spring context**

## What can Spring core do for you ?
1. Resource management
2. internationalization (i18n)
3. type conversion
4. spEL

## What is Spring Boot ?
Spring boot provides a default configuration for all the spring managed dependencies ( dependencies that ends with **starter**) HI now you know how

## what is a build tool ?
A build tool is a software we use to build apps more easily.
some of the task that often part of building the app as follows -
1. Downloading dependencies
2. Running Test
3. Validate Syntax rules
4. Checking for security vulnerability
5. Compiling
6. Packaging
Maven Resource - [Introducting Maven: A Build Tool for Today's Java Developers By Balaji Varanasi](https://www.amazon.in/Introducing-Maven-Build-Todays-Developers/dp/1484254090)

## what are a maven project consists Of?
1. **src** for code
2. **pom.xml** for configuring your Maven project

## how to add dependencies in maven ?
     <dependencies>
    <dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-jdbc</artifactId>
    <version>5.2.6.RELEASE</version>
    </dependency>
    </dependencies>

## How can you add bean in the context ?
1. By using **@Component** in class
2. By providing a **@Bean** annotated method in a **@Configuration** annotated class.

| Using the Bean Annotation  | Using stereotype annotation   |
|-------------- | -------------- |
| Have full control over the instance creation         | Only have control after instance created by framework          |
| Can have multiple instance for same class        | Only have one instance per context          |
| Can be instance of any type        | only the class you are managing          |


## what to do if I want to run any instruction after bean creation ?
Use **@PostConstruct** annotation.

## If we want to supply only some bean programmatically then we need to use ?

Use **context.registerBean** method.

    context.registerBean("parrot1",
    Parrot.class,
    parrotSupplier,
    bc -> bc.setPrimary(true));

## what is wiring ? 
Calling the method that creates the bean

## what is autowiring ?
Provide dependencies as a value of method parameter

## when we are wiring calling a @Bean method for DI does it creates another instance ?
**No, It did not create one**. Spring is smart enough to intercept the call and put the bean from the context

## What is dependency Injection ?
DI is a technique that involves seting a value of specific field or parameter. It is an application of IoC principle, And IoC implies that framework controls the execution.

## How many ways you can autowire in spring ?
3 ways.
1. Inject as field of the class (For Proof Of Concepts **DO NOT USE FOR PRODUCTION**)
   1. Why Though ?
      1. Since the fields can be final
2. Injecting through constructor (Preferred way)
3. Injecting through setters (for Optional Dependency)

## what is circular dependencies ?
When a component requires another component as dependency. While the second component requires first component as dependency. it is a deadlock. So spring app does not start.

## How Spring selects a bean ?
1. check if Identifier of parameter matches name of the beans in the context. And wire it.
2. If it does not it match then bean which is marked primary( **Using @Primary**) will be injected 
3. Use the bean that has been specified in @Qualifier annotation.
4. If none of these matches then it fails with Exception.

## what is interface ?
interface is an abstract structure that is used to specify responsibility. Interface says "What needs to happen". Implementation says "how it should happen".

> A service is a object that implements a use case

## What are some naming convention used in a enterprise spring app ?
1. Service names ends with 'Service'
2. Classes that interacts with database are called data access objects they ends with DAO and repository ends with repository.
3. Something that communicates outside of app is called **proxies**

## where should you use stereotype annotation ?
Only in the implementation.

## How to make default implementation for a interface ?
1. Using **@Primary** in the implementation alongside the stereotype annotation.
2. Use **@Qualifier** while defining the beans. And **@Qualifier** when injecting the bean.

## why there is @Service, @Repository when they are same as @Component ?
To signify each implementation its responsibility like - 
1. @Service for use case
2. @Repository implements a repository responsibility
3. @Component is a generic bean

## what is scope in spring ? How many scopes are there
Scope is the way spring manages creation and management of bean lifecycle. There are two scopes -
1. Singleton (default)
2. Prototype

## How singleton scope is different from singleton design pattern ?
Singleton scope is one instance per name. That is it can have multiple instance of same class. Whereas, Singleton design patter create one instance of the class per application.

## what rules should be followed for singleton bean ?
It must be immutable. As synchronization is discouraged.

## what type of initialization is supported by Spring singleton bean ?
By default spring creates bean using eager initialization. But can be configured to be lazily initialized.

## How to do lazy initialization ?
Use @Lazy annotation with stereotype annotation.

## How to use prototype scope ?
1. With @Bean use **@Scope(BeanDefinition.SCOPE_PROTOTYPE)**. Using this each thread gets it's own instance.
2. with stereotype instance using **@Scope(BeanDefinition.SCOPE_PROTOTYPE)**

## What precaution to take in prototype scope ?
**Do not inject prototype directly i.e. Use constructor injection, setter injection, field injection** use context.getBean(CommentProcessor.class);

## What is aspect in programming?
A way the framework intercepts method calls and possibly alters the execution of methods.

## what is different concepts in Spring AOP ?
1. **What** code spring needs to run in aspect is aspect
2. **When** the app should execute the aspect is advice
3. **Which** method the framework needs to intercept is point cut
4. **which Event** triggers the execution is join point. For spring it is always method call.

## how spring aop works ?
When requesting for bean the spring framework will not give reference to the bean rather an object that calls aspect logic. This is called **weaving**.

## what are the steps to create aspects ?
1. Add @EnableAspectJAutoProxy
2. Create a class with @Aspect (It is not a stereo type annotation) and make it a bean
3. Define a method that will implement the aspect logic and tell Spring when and which method to intercept.
4. Implement the aspect logic.
   
## Give an example of aspect ?

    @Aspect
    public class LoggingAspect {
     @Around("execution(* services.*.*(..))")
     public void log(ProceedingJoinPoint joinPoint) {
     joinPoint.proceed();
     }
    }

The expression inside @Around is called AspectJ Point cut Language.
ProceedingJoinPoint parameter represents intercepted method. It is used to delegate to actual method. It must be called.

## what does execution(* services.*.*(..)) mean ?

execution -> says when the method is called
* -> return type can be of any type
services -> package is services
\* -> intercepted method can be of any class
\* -> interceptor method name can be any
(..) -> intercept method can be of any number of arguments 

## what are the different aspects you can use ?
1. @Before
2. @AfterReturning
3. @AfterThrowing
4. @After
5. @Around

## How to use @AfterReturning ?

@Aspect
public class LoggingAspect {
    private Logger logger = Logger.getLogger(LoggingAspect.class.getName());

    @AfterReturning(value = "@annotation(ToLog))",returning = "returnedValue")
    public void log(Object returnedValue) {
        logger.log("Method executed and returned "+returnedValue);
    }
}

## What question to keep in mind while working with multiple aspects ?
* In which order the spring execute these aspects?
* Does the execution order matter?

## In which order Spring runs aspect ?
Spring does not guarantee the same execution sequence. To keep order use @Order. If the value is less then it will run first.