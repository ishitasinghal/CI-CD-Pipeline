# Day 8-  Java Project with Gitlab

A Java program is taken, which is an implementation of the stack data structure to find out whether the brackets are balanced or not.

This project would be used to make a pipeline. The Java program has been provided below:

```java
import java.io.*;
import java.util.*;

public class Balanced_Brackets
{
    static class Stack
    {
    int top=-1;
    char items[]=new char[100];
    void push(char x)
        {
        if(top==99)
            {  
            System.out.println("Stack is full");
            }   
        else
        items[++top]=x;
        } 

char pop(){
if(top==-1){ 
System.out.println("Stack is empty");
return '\0';
} 
else
{ 
char element = items[top];
top--;
return element;
} 
}

boolean isEmpty(){
return (top==-1)?true:false;
} 
}
static boolean isMatchingPair(char c1, char c2){
if(c1=='(' && c2==')'){
return true;}
else if(c1=='{' && c2=='}'){
return true;}
else if(c1=='[' && c2==']'){
return true;}
else
return false;
}

static boolean Balanced(char exp[])
{
Stack st = new Stack();
for(int i=0;i<exp.length;i++){
if(exp[i]=='{' || exp[i]=='(' || exp[i]=='['){
st.push(exp[i]);
}
if(exp[i]=='}' || exp[i]==')' || exp[i]==']'){
if(st.isEmpty()){                   //checking whether the stack is Empty
return false;
}
else if(!isMatchingPair(st.pop(), exp[i])){
return false;
}
}
}
if(st.isEmpty()) 
return true; /*balanced*/
else{   
return false; 
}  
}
public static void main(String[] args)  
    { 
        char exp[] = {'{','(',')','}','[',']', '{'}; 
          if (Balanced(exp)) 
            System.out.println("Balanced "); 
          else
            System.out.println("Not Balanced ");   
    } 
    
}
```

The above code is pushed into the GitLab repository.

For using the GitLab CI/CD, a .gitlab-ci.yml file is written which has all the commands and requirements are written. Below is the code snippet  of the yaml fiel.

```yaml
image: java:latest

stages:
  - build
  - execute

build:
  stage: build
  script: /usr/lib/jvm/java-8-openjdk-amd64/bin/javac ds.java
  artifacts:
    paths:
     - ds.*

execute:
  stage: execute
  script: /usr/lib/jvm/java-8-openjdk-amd64/bin/java ds

```

Initially, the build fails as some error is thrown in the code and a mal is sent to the email id depicting the build fail.

![](images/Screenshot%20(972).png)

As soon as the java file, ds.java  is modified, the yml script starts running, and automatically the pipeline.

![](images/Screenshot%20(974).png)

The pipeline works successfully!

![](images/Screenshot%20(979).png)

Next, another program is written :

```java
import java.util.StringTokenizer;

public class Program
{ 
		  public static void main(String args[]) {
			  
	    int arr[] = {1,3,5,34,65,68,97,35, 100, 800}; 
	        int max=arr[0];  
	        for(int i=1;i<arr.length;i++){  
	            if(max<arr[i])  
	                max=arr[i];  
	        }  
	        System.out.print(max);  
	    }  
	}  


```

Yaml file:

```yaml
image: java:latest

stages:
  - build
  - execute

build:
  stage: build
  script: /usr/lib/jvm/java-8-openjdk-amd64/bin/javac Program.java
  artifacts:
    paths:
     - Program.*

execute:
  stage: execute
  script: /usr/lib/jvm/java-8-openjdk-amd64/bin/java Program

```

![](images/Screenshot%20(979).png)