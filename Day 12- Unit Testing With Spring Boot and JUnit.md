# Day 12- Unit Testing With Spring Boot, JUnit

#### <u>Unit Testing:</u>

Unit tests are carried on a small piece of code that performs a specific functionality. The code could be any method, or a class. Performing unit testing helps us catch any errors in the code at an early stage.

In Java, it can be done in many ways using many frameworks like Selenium, TestNG, JUnit, Mockito(for Java Spring Boot applications). An overview of  how unit testing is done using Mockito and JUnit have been described below.

Before we proceed any further, we need to understand what is Spring Boot.

#### <u>Spring Boot Application:</u>

Spring Boot is an open-source framework that is used to create stand-alone applications in Java. Those application are ready to run and can be deployed directly to the docker containers. It provides auto-configuration, which makes it easier for the developers to start working on the application quickly instead of wasting time in the preparing for the configurations for the application. Spring Boot does it itself.

It provides the following advantages:

* Provides an easy way to run the application.
* Supports both XML and annotations.
* Provides variety of non-functional features which are common in larger projects.
* Can run separately or independently in small modules, loosely coupled.

A Java spring boot code has been used and tested with Mockito framework for unit testing.

#### Mockito Testing:

Similar to JUnit, test cases are written using Mockito framework. Below is the snapshot of the test code.

![](images/Screenshot%20(995).png)

The code passes all the test cases, and gives a report on how the test cases passed or failed in the console output.

![](images/Screenshot%20(997).png)

#### JUnit :

JUnit is a testing framework that makes use of annotations to identify methods, the order of execution of methods such as @Test, @Before, @After, and many more. Annotations are preceded with an '@' symbol. 

A sample Java program is taken to carry out a JUnit testing on that piece of code.

```java
package org.testgroup.org.testartifact;
import java.util.ArrayList;
import java.util.List;
 
public class App {
 
    private List<String> lstFruits = new ArrayList<String>();
 
    public void add(String fruit) {
        lstFruits.add(fruit);
    }
 
    public void remove(String fruit) {
        lstFruits.remove(fruit);
    }
 
    public int size() {
        return lstFruits.size();
    }
     
    public void removeAll(){
        lstFruits.clear();
    }
}
```

 The test code is as follows:

```java
package org.testgroup.org.testartifact;
import junit.framework.Test;
import junit.framework.TestCase;
import junit.framework.TestSuite;

public class AppTest extends TestCase
{
	 protected App a = new App();
	 
	    protected void setUp() {
	        a.add("Mango");
	        a.add("Strawberry");
	        a.add("Cherry");
	    }
	 
	    public void testSize() {
	        assertEquals("Checking size of List", 3, a.size());
	    }
	 
	    public void testAdd() {
	        a.add("Banana");
	        assertEquals("Adding 1 more fruit to list", 4, a.size());
	    }
	 
	    public void testRemove() {
	        a.remove("Orange");
	        assertEquals("Removing 1 fruit from list", 2, a.size());
	    }
	 
	    protected void tearDown() {
	        a.removeAll();
	    }
	}
```

![](images/Screenshot%20(999).png)

The above test code first imports the Testcase class from the junit package and makes use of assertEquals annotation to check whether the expected output is coming or not.

#### <u>Links Referred:</u>

1. "https://docs.spring.io/spring-boot/docs/current/reference/htmlsingle/"
2. "https://stackify.com/what-is-spring-boot/"
3. "https://www.vogella.com/tutorials/Mockito/article.html"
4. "https://www.vogella.com/tutorials/JUnit/article.html"
5. "https://javacodehouse.com/blog/junit-tutorial/"
6. "https://examples.javacodegeeks.com/core-java/junit/junit-testcase-example/"