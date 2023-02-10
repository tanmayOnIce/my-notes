## What is Spring data ?

A family of projects belonnging to spring framework whose purpose is to simplify the access to both relational and noSQL databases.

It contains -
1. Spring data Commons - Provides metadata model for persisting Java Classes and technology neutral repository interfaces.
2. Spring data JPA - deals with JPA based repositories. It provide supports by reducing boilerplate and creating implementation for the repository interfaces.
3. Spring data JDBC - Deals with JDBC-based repositories. **Does no suppot cacheing or lazy loading**
4. Spring Data REST - exports repository data as RESTFul resources.
5. Spring Data MongoDB - deals with mongoDB document database.
6. Spring Data Redis - deals with access to the Redis key-value database.

## What are the dependencies needed for Hibernate based JPA project ?
1. hibernate-entitymanager (Have transitive dependency on JPA)
2. mysql-connector-java

## What is a persistence unit ?
A persistenece unit is a pairing between domain class model and database connection, plus some other configuration settings.

## How do we configure persistence unit ?

<Persistence xmlns="http://java.sun.com/xml/ns/persistence" xmlns:xsi="http://www.w3.org/2001/
XMLSchema-instance"
xsi:schemaLocation="http://
java.sun.com/xml/ns/persistence http://java.sun.com/
xml/ns/persistence/persistence_2_0.xsd"
version="2.0">

<persistence-unit name="ch02">
    <provider>org.hibernate.jpa.HibernatePersistenceProvider<provider>
    <properties>
<property name="javax.persistence.jdbc.driver" #C
value="com.mysql.cj.jdbc.Driver"/
> #C
<property name="javax.persistence.jdbc.url" #D
value="jdbc:mysql://localhost:3306/
CH02?serverTimezone=UTC "/> #D
<property name="javax.persistence.jdbc.user" value="root"/
> #E
<property name="javax.persistence.jdbc.password" value=""/
> #F
<property name="hibernate.dialect" #G
value="org.hibernate.dialect.MySQL8Dialect"/
> #G
<property name="hibernate.show_sql" value="true"/
> #H
<property name="hibernate.format_sql" value="true"/
> #I
<property name="hibernate.hbm2ddl.auto" value="create"/
> #J
</properties>
</persistence-unit>
</persistence>

This file is in META-INF folder with name persistence.xml. And it defines one persistence unit.

JPA is only a specification we need to provide a vendor specific PersistenceProvider implementation of the API

We need to define the JDBC properties like the driver,url of the database,the username,password,dialect, If we want to show sql,If we want to format said sql,

## How to write a persistent class ?

@Entity
public class Message {
    @Id
    @GeneratedValue(strategy = GenerationType.IDENTITY)
    private Long id;

    private String text;

    public String getText() {
        return text;
    }

    public void setText(String text) {
        this.text = text;
    }
}

## if two instances of message have the same identifier value, are they same ?

Yes in the sense that they represent the same row in table.

## what can be identifier type ?
virtually anything.

## how to save using hibernate /

public class HelloWorldJPATest {
    @Test
    public void storeLoadMessage() {
        EntityManagerFactory emf = Persistence.createEntityManagerFactory("ch02");  <---- Represent persistence unit

        try {
            EntityManager em = emf.createEntityManager();  <---- begins a new session 
            em.getTransaction().begin(); <-- starts transaction

            Message message = new Message();
            message.setText("Hello world");
            em.persist(message); <----- transient to persistence
            em.getTransaction().commit(); <-- check with persistence context and executes necessary SQL

            List<Message> messages = em.createQuery("select m from Message m",Message.class).getResultList();

            messages.get(messages.size() - 1).setText("Hello world from JPA");
            em.getTransaction().commit();

            assertEquals(1,message.size());

            assertEquals("Hello World from JPA!",messages.get(0).getText()));

            em.close();
        } finally {
            emf.close();
        }
    }
}

## what is dirty checking in JPA ?

When we update a persistent object in a transaction. Hibernate will check with the db and update the data.

## Do we need hibernate.cfg.xml for using hibernate ? Or persistnece.xml is enough ?

Persistance.xml is enough. But if we want to add hibernate related features then we use hibernate.cfg.xml.

## what is hibernate equivalent of EntityManagerFactory ?
SessionFactory.

## give an example of hibernate.cfg.xml ?

<?xml version='1.0' encoding='utf-8'?>
<!DOCTYPE hibernate-configuration PUBLIC
"-//Hibernate/Hibernate Configuration DTD//
EN" "http://www.hibernate.org/dtd/hibernate-
configuration-3.0.dtd">
<hibernate-
configuration> #A
<session-
factory> #B
<property name="hibernate.connection.driver_class"> #C
com.mysql.cj.jdbc.Driver #C
</
property> #C
<property name="hibernate.connection.url"> #D
jdbc:mysql://localhost:3306/CH02?
serverTimezone=UTC #D
</
property> #D
<property name="hibernate.connection.username">root</
property> #E
<property name="hibernate.connection.password"></
property> #F
<property name="hibernate.connection.pool_size">50</
property> #G
<property name="show_sql">true</
property> #H
<property name="hibernate.hbm2ddl.auto">create</
property> #I
</session-factory>
</hibernate-configuration>


## Write a hibernate specific save query ?

public class HelloWorldHibernateTest {
    private static SessionFactory createSessionFactory(){
        Configuration configuration = new Configuration(); <--needed for creating SessionFactory.
        configuration.configure().addAnnotatedClass(Message.class); <-- need to add the entity model. 

        ServiceRegistry serviceRegistry = new StandardServiceRegistryBuilder().applySettings(configuration.getProperties()).build(); <-- host and manages the services that needs access to session factory.

        return configuration.buildSessionFactory(serviceRegistry); <-- finally build a session factory.
    }

    @Test
    public void storeLoadMessage() {
        try(SessionFactory sessionFactory = createSessionFactory();Session session = sessionFactory.openSession()) {
            session.beginTransaction();
            Message message = new Message();
            message.setText("Hello world form Hibernate!");
            session.persist(message);
            session.getTransaction().commit();

            session.beginTransaction();
            CriteriaQuery<Message> criteriaQuery = session.getCriteriaBuilder().createQuery(Message.class);
            criteriaQuery.from(Message.class);
            List<Message> messages = session.createQuery(criteriaQuery).getResultList();
            session.getTransaction().commit();

            assertAll(
                assertEquals(1,message.size()),
                assertEquals("Hello world from Hibernate!",messages.get(0).getText())
            );
        }
    }
}