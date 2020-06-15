# TMWebServiceFramework
This framework is created to free team members from writing code of servlet.
This framework only works on server which are based on J2EE specifications.

# Instructions to use framework:

1)Download tmservice.jar add it in lib folder of your webapplication.

2)Download gson-2.6.XX.jar (or any latest veriosn) from a verified source and put it in your webapplication lib folder.

3)After adding jar file you have to create following entries in your web.xml


    <servlet>
    <servlet-name>TMServiceLoader</servlet-name>
    <servlet-class>com.thinking.machines.servlets.TMServiceLoader</servlet-class>
    <load-on-startup>0</load-on-startup>
    </servlet>
    <servlet>
    <servlet-name>TMService</servlet-name>
    <servlet-class>com.thinking.machines.TMService</servlet-class>
    </servlet>
    <servlet-mapping>
    <servlet-name>TMService</servlet-name>
    <url-pattern>/service/*</url-pattern>
    </servlet-mapping>

# Instructions to create your own service

1)To create your own service you have to create a classs and import packages com.thinking.machines.annotations.*; in your class.

2)To make service a part of framework you have to apply @Path annotation on the class as well as method.@Path annotation accept a string in which you have to specify url pattern.For eg. for /employee/add you have to specify @Path("/employee")
on class and @Path("/add") on method.
eg:
    
    import com.thinking.machines.annotations.*;
    @path(/employee)
    class Employee
    {
    @Path("/add")
    public void addEmployee()
    {
      //code to add employee
    }
    }
    
3)To send response you have to specify @ResponseType annotation on the method you can pass 3 values to it :
    a)text/html: to send html in response.(your method should return a string).
    b)json:to send json in response.(return type of your method must be an Object).
    c)none:to return nothing.(Framework will send an empty string in response).
    eg:
    
    import com.thinking.machines.annotations.*;
    @path(/employee)
    class Employee
    {
    @ResponseType("text/html")
    @Path("/add")
    public String addEmployee()
    {
      //code to add employee
      return "employee added";
    }
    }

# To compile the service:
for Linux:

    javac -classpath path_to_your_webserver_base_folder/webapps/name_of_unzipped_folder/WEB-INF/classes:path_to_your_webserver_base_folder/webapps/name_of_unzipped_folder/WEB-INF/lib/*:path_to_your_webserver_base_folder/lib/*:. your_service_file_name.java

for windows:

    javac -classpath path_to_your_webserver_base_folder/webapps/name_of_unzipped_folder/WEB-INF/classes;path_to_your_webserver_base_folder/webapps/name_of_unzipped_folder/WEB-INF/lib/*;path_to_your_webserver_base_folder/lib/*;. your_service_file_name.java

# To test your service:
you have to type following url in browser:

    http://server_ip:server_port/webapp_name/service/path_on_class/path_on_method


# To create a secured service:

1)You have to create a class import package com.thinking.machines.interfaces.* which contains a interface named SecureMethodInterface you have to implement it in your class.And implements its two methods isSecured and ifNotSecured.
isSecured method has 3 parameters HttpServletResponse,HttpSession,ServletContext its return type is boolean.

2)Second method is ifNotSecured which return a string.You have to return a path for which request is forwarded if method is not secured.

3)you have to apply @Secured annotation on the method which you want to secure.You have to specify name of class in which you have implemented the SecureMethodInterface along with package name.

eg:

      //***************Example of class which implements SecureMethodInterface*****************
    import com.thinking.machines.interfaces.*;
    class EmployeeAddMethodSecurity implements SecureMethodInterface
    {
      public boolean isSecured(HttpServletResponse httpServletResponse,HttpSession httpSession,ServletContext servletContext)
      {
        return true/false; //true if everything is ok else false 
      }
      public String ifNotSecured()
      {
        return "/service/employee/error";
      }
    }
    //**********************Service class Example*******************
    import com.thinking.machines.annotations.*;
    @Path("/employee")
    class Employee
    {
      @ResponseType("json")
      @Path("/add")
      public Employee addEmployee(EmployeeInfo employeeInfo)
      {
        //code which return  a object after adding employee it will be converted in json by framework
        return new Emloyee();
      }
      @ResponseType("text/html")
      @Path("/error")
      public String error()
      {
        //whatever you want to do in this method is upto you this is an example only
        return "an error occured"; 
      }
    }

# Some other services:
  a) File upload:
      For file upload you have to apply @RequiredFiles annotation on your service method and the method compulsory have only       one parameter i.e. java.io.File[].
      eg:
      
      import com.thinking.machines.annotations.*;
      @Path("/employee")
      class Employee
      {
        @RequiredFiles
        @path("/fileUpload")
        public void copyFiles(java.io.File[] files)
        {
          //code to copy files
        }
      }
    
  b) Request Forwarding:
        To forward request you have to apply @Forward annotation on your service method to forward the request to another url
        you have to specify url in forward annotation.
        eg:
        
    import com.thinking.machines.annotations.*;
    @Path("/employee")
    class Employee
    {
      @ResponseType("json")
      @Path("/add")
      public Employee addEmployee(EmployeeInfo employeeInfo)
      {
        //code which return  a object after adding employee it will be converted in json by framework
        return new Emloyee();
      }
      @ResponseType("none")
      @Path("/error")
      @Forward("/error.html")
      public String error()
      {
        //whatever you want to do in this method is upto you this is an example only
        return "an error occured"; 
      }
    }
        
  c)You can also create services as a jar file.After creating jar files put the jar file in WEB-INF/lib folder.
   
  d)If you want the framework should scan only some jar files you want you can specify them in the file name                    servletConfiguration.conf which you have to put in WEB-INF/conf folder the example configuration file already exist their.
  eg:
  
      {
        jars:["abc.jar","def.jar",....]
      }
        
# Guidelines for service method creation:

1)You can create a service method with no parameters.

2)If you give a custom class parameter than framework will convert the json data in that class type.

3)you can specify only one custom class parameter.

4)Along with custom class reference you can demand the reference of HttpServletRequest,HttpSession and ServletContext by specifying them in parameter of service method.

  
# To use this framework you have to follow some rules:

1)To send any data from client to server you have to use JSON.

2)you have to follow all the guidelines properly to make it work properly. 

