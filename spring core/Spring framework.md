# Spring framework basics

## what is framework ?
Think of framework like a DIY piece. You have all the possible component to build a furniture and a manual on how to assemble them. Now if you want to build a wardrobe, you have to find the right parts and assemble them.

## What is use case ?
A _use case_ defines a reason why a person will use your app.

## What is Spring core ?

Spring works on the priciple of **Inversion Of Control**. Here _inversion_ means the dependencies are controlled by Spring.
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
4. Checking for security vurnerability
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


## what to do if i want to run any instruction after bean creation ?
Use **@PostConstruct** annotation.

## If we want to supply only some bean programatically then we need to use ?

Use **context.registerBean** method.

    context.registerBean("parrot1",
    Parrot.class,
    parrotSupplier,
    bc -> bc.setPrimary(true));