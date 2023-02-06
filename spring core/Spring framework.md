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
When a component requires another component as dependency. While the second component requires first component as dependency. it is a deadlock.

## How Spring selects a bean ?
1. check if Identifier of parameter matches name of the beans in the context. And wire it.
2. If it does not it match then bean which is marked primary( **Using @Primary**) will be injected 
3. Use the bean that has been specified in @Qualifier annotation.
4. If none of these matches then it fails with Exception.

