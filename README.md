# <span style="color:green">Health-IO - API</span>

This is an API server for health.io app, That belongs to the Project health.io.  
This is Built JAR file of the server, Orignal code of the same has been kept private due to Plagiarism related reasons in College Submission.
Mail at - ```Sourabhraj311@gmail.com``` for source code.

### Status

``` Pre-Release```  ``` Up and Functional, Handling Minor Edge Cases``` ->
[```Live  version```](https://health-io.herokuapp.com/)

### About

This is a REST server that act as a backend and database for all the frontend health.io apps, including the

- Android Client App
- Web Client App
- Desktop Admin App
- Admin Controller App

Also, this api serves a static registration website for Hospitals Registration and Hospital Feeder Registration

### Structure

The API is build Using Spring Boot.
For DataBase I used MongoDB, the data currently is getting stored at MongoDB Cluster at MongoDB Atlas,

``` The URL is https://health-io.herokuapp.com/```

### Configuring Server

### Before Running Server Locally

Make following Changes to [application.properties](http://application.properties) file in `src/main/resourses` berfore
starting the server.

```json5
#spring.data.mongodb.host=
#spring.data.mongodb.port=
#spring.data.mongodb.username=
#spring.data.mongodb.password=
#spring.data.mongodb.database=

## This is your mongo server address, Replace with your own for testing
spring.data.mongodb.uri=mongodb: //localhost:27017/sample
#debug=true

## This is for configuring your Smtp server for sending OTP mails
spring.mail.host=smtp.gmail.com
spring.mail.port=25
spring.mail.username="REPLACE WITH YOUR EMAIL ID"
spring.mail.password="REPLACE WITH YOUR PASSWORD"

# Other properties
#spring.mail.properties.mail.debug=true
spring.mail.properties.mail.transport.protocol=smtp
spring.mail.properties.mail.smtp.auth=true
spring.mail.properties.mail.smtp.connectiontimeout=5000
spring.mail.properties.mail.smtp.timeout=5000
spring.mail.properties.mail.smtp.writetimeout=5000

# TLS, port 587
spring.mail.properties.mail.smtp.starttls.enable=true

# SSL, post 465
spring.mail.properties.mail.smtp.socketFactory.port = 465
spring.mail.properties.mail.smtp.socketFactory.class = javax.net.ssl.SSLSocketFactory
```

Follow API documentation for getting correct URL for Correct Requests.

## Documentation

# API DOCUMENTATION

For instance let the native "URL" word be the current url where the server is running,

For the Test Builds the "URL" short form will remain as localhost

Example

```cpp
("BASE_URL"/api/user/)
// This is equal to is Testing state
"http://localhost:8080/client/user"
```

# Sign-UP

The Default URL = `BASE_URL/client/users/signup/`

## Adding a new user

URL : `BASE_URL/client/users/signup/`
TYPE : `POST`
ACCEPTANCE :

```json5
{
  "first_name": "Vishal",
  "last_name": "Anand",
  "mobile_num": "",
  // Mobile number feature will be implemented in the future, Currently leave it Empty
  "email": "vishalAnand07@gmail.com",
  "password": "randomPassword2",
  "age": "22",
  "dog_tag": "",
  "known_device": []
}

// Carefull Always send lowercase email - id to POST in all methods.
```

You need to send all necessary values to the POST request, and Email ID is the Unique Identifier in this, The email
should be unique and must be in proper format.

As soon as all values are sent properly, A new user with following details are created, Also a random id field is
created that constitutes a random generated Objct ID such as

`"id": "60b283ab12ec054ecfea01cd"`

Also after creating of new user in DB, a new `dog_tag` is assigned to user, and the password is hashed into 64-Bit
encrypted key.

The user in DB looks something like this,

```json5
{
  "id": "60b28f26e2b5f6671275360a",
  "first_name": "Sourabh",
  "last_name": "Kumar",
  "mobile_num": "9142865908",
  "email": "[sourabhraj311@gmail.com](mailto:sourabhraj311@gmail.com)",
  "password": "d1ftw8jHsds5yupoMczbnSZsQ==",
  "age": "22",
  "dog_tag": "DC8E3079-F282-4B2B-8736-9D1E623F471D",
  "tokens": [],
  "known_device": [],
  "saved_hospitals": []
}
```

This id is responsible for deletion and updating of users in the DB, However this is hidden from the client API, All the
CRUD work will be done using email as unique id and dog_tag.

RETURN VALUE :  
Status Code : `200` Message : `Success`  
Status Code : `400` Error : `Bad Request`  
Status Code : `409` Error : `ALready Exists`  
Status Code : `500` Error : `Internal Server Error`

```json5
// You will get this if Succes
{
  "first_name": "Sourabh",
  "last_name": "Kumar",
  "mobile_num": "9142865908",
  "dog_tag": "DC76D5EC-4647-414D-ADEC-091C58FA5982",
  "email": "sourabhraj311@gmail.com"
}
```

Any other value than this means user is not created.

Validations Like verifying inputs such as password strength, email validation etc is `NOT` done in API and need to be
done at frontend.

## Updating USER DATA

Updating works on same URL as of sign-up works with added /update

URL : `BASE_URL/client/users/signup/update**`
TYPE : `POST`  
ACCEPTANCE :

```json5
{
  "first_name": "Sourabh",
  "last_name": "Kumar",
  "mobile_num": "9142865908",
  "email": "sourabhraj311@gmail.com",
  "password": "changedPassword",
  "age": 22,
  "dog_tag": "",
  "known_device": []
}
```

All the data of a particular user can be updated , but email should remain same, otherwise if email is changed, Then a
new user will be created. This is very critical,

`Do not provide a field to update email when giving options to update data via this URL.`  
It will be available Separately.  
As soon as you perform `POST` operation, a new random dog_tag is generated and sent to you along with email.

RESPONSE :

```json5
//You will get this in 200 Succes
{
  "dog_tag": "DC76D5EC-4647-414D-ADEC-091C58FA5982",
  "email": "sourabhraj311@gmail.com"
}
```

Else you will get an error.

RETURN VALUE :  
Status Code : `200` Message : `Success`  
Status Code : `400` Error : `Bad Request`  
Status Code : `409` Error : `ALready Exists`  
Status Code : `500` Error : `Internal Server Error`  
Store this dog_tag as a token onto your local storage and before start of application, check for token availability.

## Login USER

You will have to `POST` user's email, password, device id. The server will then match it with database and check

URL : `BASE_URL/client/users/login**`
TYPE : `POST`  
ACCEPTANCE :

```json5
{
  "email": "sourabhraj311@gmail.com",
  "password": "abcdefgh",
  "device_id": "Acer-Nitro-5"
}
```

RESPONSE :
RETURN VALUE :  
Status Code : `200` Message : `Logged In Succesfully`  
Status Code : `400` Error : `Bad Request`  
Status Code : `409` Error : `Wrong Credentials`  
Status Code : `500` Error : `Internal Server Error`

```json5
//If everything is correct, it will give you a login 
//token, and you can save it to your localstorage 
//for accessing further data
{
  "dog_tag": "679CE570-6EBC-4813-B40B-A3C7176FA299",
  "email": "sourabhraj311@gmail.com",
  "name": "Sourabh Kumar"
}
```

# Forgot Password, Create OTP

First we will take email from the user, Change it to lowercase and the send it to server . Server will verify whether
the email is valid or not, or any users with that email exists or not.

Also take email address of the user and store, as in next step you will need to send email id also in order to verify
OTP with the server.

URL : `URL/client/users/resetPassword/`
TYPE : `POST`  
ACCEPTANCE :

```json5
{
  "email": "sourabhraj311@gmail.com"
}
// Should be Lowercase
```

RESPONSE :

```json5
{
  "status": "Successfully Send Message"
}
//After this an OTP will be sent to the given email from 
***health.io.console@yahoo.com***
```

ERRORS :

```json5
{
  "error": "Failed to Send OTP"
}
```

```json5
{
  "error": "Invalid Email"
}
// When email sent is "", Empty Body
```

```json5
{
  "error": "Email does not Exists, Or Invalid Email"
}
```

```json5
{
  "error": "Runtime Exception"
}
// This error will not be created easily. Chances are low
```

## Verifying OTP

Take OTP that is entered by user and send it to server to verify that it is correct or not.

Also send email address with otp in order to verify,

URL : `URL/client/users/verifyOtp/`
TYPE : `POST`  
ACCEPTANCE

```json5
{
  "email": "sourabhraj311@gmail.com"
  "otp": "123456"
}
```

RESPONSE :

```json5
{
  "status": "OTP Verified Successfully"
}
```

ERROR:

```json5
{
  "error": "Incorrect OTP, Retry"
}
```

```json5
{
  "error": "OTP Expired"
}
```

## Changing Password After Verification

Remember to keep OTP and EMAIL of the user sending OTP still to finally change the OTP.

URL : `URL/client/users/updatePw/`

TYPE : `POST`

ACCEPTANCE :

```json
{
  "email": "sourabhraj311@gmail.com",
  "otp": "123456",
  "password": "Sourabh123@"
  // **Password Strength Must be verified at Frontend Only
}**
```

RESPONSE :

```json
{
  "status": "Password Changed SuccesFully"
}
```

ERROR:

```json
{
  "error": "Not Verified, or You took too long, Retry Sending OTP"
}
```

```json
{
  "error": "No user Found with email sourabhraj311@gmail.com"
}
```

```json
{
  "error": "Error Matching OTP from User"
}
```

# Hospital DataSet

Hospital API is divided into two sections,

### 1. **Client**

Client URL looks like `URL/clients/hospitals/` , and only support fetching of data.

It has two methods , one that return a particular hospital info and another returns all hospital info.

### 2. Admin

Admin have access to Adding and Changing Hospital data in DB

They have URL at `URL/admin/hospitals`, Supporting two operations one is add and other is update.

## Adding A Hospital - (ADMIN ONLY)

Hospital Management will be done on

URL : `URL/admin/hospitals/add/`

TYPE : `POST`

ACCEPTANCE :

The Last Updated field in the hospital will be in format - `SimpleDateFormat("HH:mm:ss dd-MM-yyyy");` for
example, `16:59:24 14-06-2021`

```json5
{
  "licence_id": "LB019348231",
  "name": "Bokaro General Hospital",
  "address": "4th Avenue, Sector 4, Bokaro, Jharkhand",
  "city_name": "Bokaro",
  "state_name": "Jharkhand",
  "geolocation": "23.6743099,86.1434682",
  "description": "Description of the Hospital",
  "contact": "065432109",
  "type": "government",
  "grade": "A",
  "last_updated": "16:59:24 14-06-2021",
  "no_of_bed": "290",
  "vacant_bed": "100",
  "icu": "10",
  "vacant_icu": "3",
  "ccu": "5",
  "vacant_ccu": "2",
  "ventilators": "100",
  "vacant_ventilators": "29",
  "oxygen_cylinders": "420",
  "vacant_oxygen_cylinders": "120",
  "blood_bank": {
    "a_positive": "25",
    "a_negative": "24",
    "b_negative": "50",
    "b_positive": "10",
    "o_positive": "30",
    "o_negative": "23",
    "ab_positive": "12",
    "ab_negative": "09"
  },
  "x_ray": "2",
  "mri": "1",
  "ecg": "1",
  "ultra_sound": "1",
  "opening_time": "00:00",
  "closing_time": "00:00",
  "is_24_hr_service": "true",
  "general_fees": "250",
  "ambulance": "20",
  "vacant_ambulance": "12"
}
```

The above Request will create a hospital is the DB, if the hospital is successfully created, then Respose will be 200.

```json5
{
  "200": "Successfully added new hospital"
}
// This means the Hospital is succesfull created in server
// Rest all are Error Codes
```

## Updating a Hospital

URL : URL/admin/hospitals/update

TYPE : `POST`

ACCEPTANCE : It needs a entire `hospita`l object, same as creating one hospital object

```json
{
  "licence_id": "LB019348231",
  "name": "Bokaro General Hospital",
  "address": "4th Avenue, Sector 4, Bokaro, Jharkhand",
  "city_name": "Bokaro",
  "state_name": "Jharkhand",
  "geolocation": "23.6743099,86.1434682",
  "type": "government",
  "description": "Description of the Hospital",
  "contact": "065432109",
  "grade": "A",
  "last_updated": "16:59:24 14-06-2021",
  "no_of_bed": "290",
  "vacant_bed": "100",
  "icu": "10",
  "vacant_icu": "3",
  "ccu": "5",
  "vacant_ccu": "2",
  "ventilators": "100",
  "vacant_ventilators": "29",
  "oxygen_cylinders": "420",
  "vacant_oxygen_cylinders": "120",
  "blood_bank": {
    "a_positive": "25",
    "a_negative": "24",
    "b_negative": "50",
    "b_positive": "10",
    "o_positive": "30",
    "o_negative": "23",
    "ab_positive": "12",
    "ab_negative": "09"
  },
  "x_ray": "2",
  "mri": "1",
  "ecg": "1",
  "ultra_sound": "1",
  "opening_time": "00:00",
  "closing_time": "00:00",
  "is_24_hr_service": "true",
  "general_fees": "250",
  "ambulance": "20",
  "vacant_ambulance": "12"
}
```

RESPONSE : For response , if everything is OK, a 200 success message will be displayed

```json5
{
  "200": "Successfully added new hospital"
}
// This means the Hospital is succesfull updated in server
// Rest all are Error Codes
```

If there is any error in updating hospital, then Error codes will be returned as response status.

## Deleting A Hospital

URL : `URL/admin/hospitals/delete/`

TYPE : `DELETE`

ACCEPTANCE :

```json
{
  "licence_id": "LB019348231"
}
```

RESPONSE :

Only Response code is returned

```json
// If Deletion is Succes
206, No Content is Returned
// Remember this is not in JSON format. Kindly consider it critically
```

## Fetching Hospital Info - By Licence_ID

URL : `URL/client/hospitals/id/`

TYPE : `POST`

ACCEPTANCE : `email,` `dog_tag,` `id`

```json
{
  "email": "sourabhraj311@gmail.com",
  "dog_tag": "7198F4EC-7ADB-422A-AA31-3EB562A4689B",
  "licence_id": "LB019348231"
}
// Everything is case sensative
```

RESPONSE : Entire hospital will be Returned back if ID exists

```json
{
  "licence_id": "LB019348231",
  "name": "Bokaro General Hospital",
  "address": "MGM Road, Sector 4, Bokaro, Jharkhand",
  "city_name": "Bokaro Steel City",
  "state_name": "Jharkhand",
  "geolocation": "23.6743099,86.1434682",
  "type": "government",
  "grade": "A",
  "description": "Description of the Hospital",
  "contact": "065432109",
  "last_updated": "2021-05-30 08:18:41.053",
  "no_of_bed": "290",
  "vacant_bed": "10",
  "icu": "10",
  "vacant_icu": "3",
  "ccu": "5",
  "vacant_ccu": "2",
  "ventilators": "100",
  "vacant_ventilators": "29",
  "oxygen_cylinders": "420",
  "vacant_oxygen_cylinders": "120",
  "blood_bank": {
    "o_negative": "23",
    "ab_negative": "09",
    "a_negative": "24",
    "b_positive": "10",
    "o_positive": "30",
    "b_negative": "50",
    "ab_positive": "12",
    "a_positive": "25"
  },
  "x_ray": "2",
  "mri": "1",
  "ecg": "1",
  "ultra_sound": "1",
  "opening_time": "00:00",
  "closing_time": "00:00",
  "is_24_hr_service": true,
  "general_fees": "250",
  "vacant_ambulance": "12"
}
```

If ID does not Exists in DB the,

```json5
{
  "timestamp": "2021-05-30T03:00:22.256+00:00",
  "status": 500,
  "error": "Internal Server Error",
  "trace": "java.lang.RuntimeException: No Hospital Exists with id LB0193sdf48231...",
  "message": "No Hospital Exists with id LB0193sdf48231",
  "path": "/client/hospitals/id"
}
// This error 500 Will be returned
```

IF Dog_Tag does not matches, You have to request Login again to generate one,

Sample error when does not matched is

```json5
Status 406 , Not Accepted
//This response is not in json format, Kindly consider it carefully
```

If the email provided does not match any in database, then

```json
{
  "timestamp": "2021-05-30T03:36:32.613+00:00",
  "status": 500,
  "error": "Internal Server Error",
  "trace": "java.lang.RuntimeException: User Does not Exist\n\tat org.bcrec.SmartHealthManagement.Service.UserService.lambda$verifyDogTag$3(UserService.java:107)\n\tat java.base/java.util.Optional.orElseThrow(Optional.java:401)\n\tat org.bcrec.SmartHealthManagement.Service.UserService.verifyDogTag(UserService.java:107)\n\tat org.bcrec.SmartHealthManagement.Controller.HospitalControllerClient.getHospitalById(HospitalControllerClient.java:33)\n\tat java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)\n\tat java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:64)\n\tat java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)\n\tat java.base/java.lang.reflect.Method.invoke(Method.java:564)\n\tat org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:197)\n\tat org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:141)\n\tat org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:106)\n\tat org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:894)\n\tat org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:808)\n\tat org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87)\n\tat org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1063)\n\tat org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:963)\n\tat org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1006)\n\tat org.springframework.web.servlet.FrameworkServlet.doPost(FrameworkServlet.java:909)\n\tat javax.servlet.http.HttpServlet.service(HttpServlet.java:652)\n\tat org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:883)\n\tat javax.servlet.http.HttpServlet.service(HttpServlet.java:733)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:227)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:162)\n\tat org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:53)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:189)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:162)\n\tat org.springframework.web.filter.RequestContextFilter.doFilterInternal(RequestContextFilter.java:100)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:189)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:162)\n\tat org.springframework.web.filter.FormContentFilter.doFilterInternal(FormContentFilter.java:93)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:189)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:162)\n\tat org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:201)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:189)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:162)\n\tat org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:202)\n\tat org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:97)\n\tat org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:542)\n\tat org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:143)\n\tat org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:92)\n\tat org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:78)\n\tat org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:357)\n\tat org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:374)\n\tat org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:65)\n\tat org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:893)\n\tat org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1707)\n\tat org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)\n\tat java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1130)\n\tat java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:630)\n\tat org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)\n\tat java.base/java.lang.Thread.run(Thread.java:832)\n",
  "message": "User Does not Exist",
  "path": "/client/hospitals/id"
}
// Means the user is not registered to send requests.  
```

# Fetching ALL Hospitals List

URL : `URL/clients/hospitals/all/`

TYPE : `POST`

ACCEPTANCE :  `email,` `dog_tag`

```json5
{
  "email": "sourabhraj311@gmail.com",
  "dog_tag": "7198F4EC-7ADB-422A-AA31-3EB562A4689B"
}
// Everything is case sensative
```

RESPONSE : All the list of hospitals will be returned in an Array Form, if all info are correct

```json5
[
  {
    "licence_id": "LB019348231",
    "name": "Bokaro General Hospital",
    "address": "MGM Road, Sector 4, Bokaro, Jharkhand",
    "city_name": "Bokaro Steel City",
    "state_name": "Jharkhand",
    "geolocation": "23.6743099,86.1434682",
    "type": "government",
    "description": "Description of the Hospital",
    "contact": "065432109",
    "grade": "A",
    "last_updated": "2021-05-30 08:18:41.053",
    "no_of_bed": "290",
    "vacant_bed": "10",
    "icu": "10",
    "vacant_icu": "3",
    "ccu": "5",
    "vacant_ccu": "2",
    "ventilators": "100",
    "vacant_ventilators": "29",
    "oxygen_cylinders": "420",
    "vacant_oxygen_cylinders": "120",
    "blood_bank": {
      "o_negative": "23",
      "ab_negative": "09",
      "a_negative": "24",
      "b_positive": "10",
      "o_positive": "30",
      "b_negative": "50",
      "ab_positive": "12",
      "a_positive": "25"
    },
    "x_ray": "2",
    "mri": "1",
    "ecg": "1",
    "ultra_sound": "1",
    "opening_time": "00:00",
    "closing_time": "00:00",
    "is_24_hr_service": true,
    "general_fees": "250",
    "vacant_ambulance": "12"
  }
]
```

IF Dog_Tag does not matches, You have to request Login again to generate one,

Sample error when does not matched is

```json5  
Status 406 , Not Accepted
//This response is not in json format, Kindly consider it carefully
```

If the email provided does not match any in database, then

```json5
{
  "timestamp": "2021-05-30T03:36:32.613+00:00",
  "status": 500,
  "error": "Internal Server Error",
  "trace": "java.lang.RuntimeException: User Does not Exist\n\tat org.bcrec.SmartHealthManagement.Service.UserService.lambda$verifyDogTag$3(UserService.java:107)\n\tat java.base/java.util.Optional.orElseThrow(Optional.java:401)\n\tat org.bcrec.SmartHealthManagement.Service.UserService.verifyDogTag(UserService.java:107)\n\tat org.bcrec.SmartHealthManagement.Controller.HospitalControllerClient.getHospitalById(HospitalControllerClient.java:33)\n\tat java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke0(Native Method)\n\tat java.base/jdk.internal.reflect.NativeMethodAccessorImpl.invoke(NativeMethodAccessorImpl.java:64)\n\tat java.base/jdk.internal.reflect.DelegatingMethodAccessorImpl.invoke(DelegatingMethodAccessorImpl.java:43)\n\tat java.base/java.lang.reflect.Method.invoke(Method.java:564)\n\tat org.springframework.web.method.support.InvocableHandlerMethod.doInvoke(InvocableHandlerMethod.java:197)\n\tat org.springframework.web.method.support.InvocableHandlerMethod.invokeForRequest(InvocableHandlerMethod.java:141)\n\tat org.springframework.web.servlet.mvc.method.annotation.ServletInvocableHandlerMethod.invokeAndHandle(ServletInvocableHandlerMethod.java:106)\n\tat org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.invokeHandlerMethod(RequestMappingHandlerAdapter.java:894)\n\tat org.springframework.web.servlet.mvc.method.annotation.RequestMappingHandlerAdapter.handleInternal(RequestMappingHandlerAdapter.java:808)\n\tat org.springframework.web.servlet.mvc.method.AbstractHandlerMethodAdapter.handle(AbstractHandlerMethodAdapter.java:87)\n\tat org.springframework.web.servlet.DispatcherServlet.doDispatch(DispatcherServlet.java:1063)\n\tat org.springframework.web.servlet.DispatcherServlet.doService(DispatcherServlet.java:963)\n\tat org.springframework.web.servlet.FrameworkServlet.processRequest(FrameworkServlet.java:1006)\n\tat org.springframework.web.servlet.FrameworkServlet.doPost(FrameworkServlet.java:909)\n\tat javax.servlet.http.HttpServlet.service(HttpServlet.java:652)\n\tat org.springframework.web.servlet.FrameworkServlet.service(FrameworkServlet.java:883)\n\tat javax.servlet.http.HttpServlet.service(HttpServlet.java:733)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:227)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:162)\n\tat org.apache.tomcat.websocket.server.WsFilter.doFilter(WsFilter.java:53)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:189)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:162)\n\tat org.springframework.web.filter.RequestContextFilter.doFilterInternal(RequestContextFilter.java:100)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:189)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:162)\n\tat org.springframework.web.filter.FormContentFilter.doFilterInternal(FormContentFilter.java:93)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:189)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:162)\n\tat org.springframework.web.filter.CharacterEncodingFilter.doFilterInternal(CharacterEncodingFilter.java:201)\n\tat org.springframework.web.filter.OncePerRequestFilter.doFilter(OncePerRequestFilter.java:119)\n\tat org.apache.catalina.core.ApplicationFilterChain.internalDoFilter(ApplicationFilterChain.java:189)\n\tat org.apache.catalina.core.ApplicationFilterChain.doFilter(ApplicationFilterChain.java:162)\n\tat org.apache.catalina.core.StandardWrapperValve.invoke(StandardWrapperValve.java:202)\n\tat org.apache.catalina.core.StandardContextValve.invoke(StandardContextValve.java:97)\n\tat org.apache.catalina.authenticator.AuthenticatorBase.invoke(AuthenticatorBase.java:542)\n\tat org.apache.catalina.core.StandardHostValve.invoke(StandardHostValve.java:143)\n\tat org.apache.catalina.valves.ErrorReportValve.invoke(ErrorReportValve.java:92)\n\tat org.apache.catalina.core.StandardEngineValve.invoke(StandardEngineValve.java:78)\n\tat org.apache.catalina.connector.CoyoteAdapter.service(CoyoteAdapter.java:357)\n\tat org.apache.coyote.http11.Http11Processor.service(Http11Processor.java:374)\n\tat org.apache.coyote.AbstractProcessorLight.process(AbstractProcessorLight.java:65)\n\tat org.apache.coyote.AbstractProtocol$ConnectionHandler.process(AbstractProtocol.java:893)\n\tat org.apache.tomcat.util.net.NioEndpoint$SocketProcessor.doRun(NioEndpoint.java:1707)\n\tat org.apache.tomcat.util.net.SocketProcessorBase.run(SocketProcessorBase.java:49)\n\tat java.base/java.util.concurrent.ThreadPoolExecutor.runWorker(ThreadPoolExecutor.java:1130)\n\tat java.base/java.util.concurrent.ThreadPoolExecutor$Worker.run(ThreadPoolExecutor.java:630)\n\tat org.apache.tomcat.util.threads.TaskThread$WrappingRunnable.run(TaskThread.java:61)\n\tat java.base/java.lang.Thread.run(Thread.java:832)\n",
  "message": "User Does not Exist",
  "path": "/client/hospitals/id"
}
// Means the user is not registered to send requests.
```

# Emergency Cases

## Adding an Emergency Case

Adding an emergency case for a particular Hospital, The Emergency cases does not belong to Hospitals document instead it
is available in seperate document named emergency, also the data here is not unique by id, an emergency case is uniquely
identified by hospitals licence id that it belongs to or where it has been reported and with the persons name.

URL : `URL/hospitals/emergency`

TYPE : `POST`

ACCEPTANCE :

```json5
{
  "licence_id": "LB019348233",
  "name_of_patient": "Sanjeev Kumar",
  "type_of_emergency": "Car Accident",
  "address": "Coperative Colony",
  "intensitty_of_emergency": "Medium",
  // Must be send via a dropdown or 
  // checkbox for Accuracy.
  "requirements": "Blood Test, Sedative 21B6 5ml",
  "time": "21:20:14",
  "description": "Condition is normal, Normal Wound at Victims Head, Will be easily recoovered in few weeks. No surgeries needed"
}
```

RESPONSE :

```json5
{
  "status": "Emergency case Added"
}
```

ERROR :

Mostly there is very less chance of error, Still if there is any error the format will contain this.

Also if the format of the POST request is not matched then it will Give error probably.

```json5
{
  "error": "Exception in thread ......"
}
```

## View Emergency Cases

URL : `URL/hospitals/emergency/{id}/`

TYPE : `GET`

ACCEPTANCE :

```json5  
Viewing By ID of Hospital will Return List of All Emergency cases in That particular hospital
// URL/hospitals/emergency/LB019348233
```

RESPONSE :

```json5  
[
  {
    "licence_id": "LB019348233",
    "name_of_patient": "Sanjeev Kumar",
    "type_of_emergency": "Car Accident",
    "address": "Coperative Colony",
    "intensity_of_emergency": null,
    "requirements": "Blood Test, Sedative 21B6 5ml",
    "time": "21:20:14",
    "description": "Condition is normal, Normal Wound at Victims Head, Will be easily recoovered in few weeks. No surgeries needed"
  },
  {
    "licence_id": "LB019348233",
    "name_of_patient": "Sanjeev Kumar",
    "type_of_emergency": "Car Accident",
    "address": "Coperative Colony",
    "intensity_of_emergency": null,
    "requirements": "Blood Test, Sedative 21B6 5ml",
    "time": "21:20:14",
    "description": "Condition is normal, Normal Wound at Victims Head, Will be easily recoovered in few weeks.No surgeries needed"
  },
  {},
  {},
  ......
]
```

ERROR

```json5  
RESPONSE CODE 204 WITH Error NO_CONTENT
```

# SAVING A HOSPITAL

Users will have an array of Saved Hospitals in their database. This array will be a Set of Strings that will store
the `licence_id` of hospitals. This will help front-end part to impliment a saving feature in Application.

## Saving a Hospital

URL : `URL/clients/hospitals/saveNew/{id}`

TYPE : `POST`

ACCEPTANCE :

The URL will accept a path variable `id` which will be the `licence_id` of the hospital the user want to save to the
database. Along with that in body, You need to add two fields

`email` `dog_tag`, First server will verify whether the user is existing or not, then verify the Token and then add the
hospital to the database.

Take Care that you only save valid Hospital ID's in the Array.

```json  
Example URL = localhost:8080/client/hospitals/saveNew/LB019348231/
{
"email": "sourabhraj311@gmail.com",
"dog_tag": "2335F57D-325E-4B81-A7B5-57343F852843"
}
```

RESPONSE :

The server will return `true` boolean type in body of response when status_code is 200,

```json
true
```

ERROR :

For every other scenario where status_code is not equal to 200, An error will be provided

```json5
Status Code 400, BAD REQUEST
This means token Didn't matched, You must logout the user if this is so.
```

WORKAROUNDS :

Also remember to save the Set of Hospital licence_id in the cloud to your local-storage, this will be helpful to display
whether a hospital exists in saved list or not while viewing the entire hospital list in dashboard.

While adding a new user into DB you don't need to provide an empty array for saved_hospitals field, It will be
automatically generated in server.

## Getting List of Saved Hospitals

URL : `URL/clients/hospitals/getSaved`

TYPE : `POST`

ACCEPTANCE : The URL will accept only two parameters `email` and `dog_tag` to verify whether user is authorised or not.

```json
{
  "email": "sourabhraj311@gmail.com",
  "dog_tag": "2335F57D-325E-4B81-A7B5-57343F852843"
}
```

RESPONSE :

The request will return a list of `licence_id` that are saved onto the users database with the email that was provided
inrequest.

```json5
[
  "LB019348231",
  "LB0193482312"
]
```

You can use the following to fetch hospitals with these id and display in saved hospitals screen.

ERROR :

```json5
Status Code 400, BAD REQUEST
This means token Didn't matched, You must logout the user if this is so.
```

# Hospial Managemet - Admin

## Updating Details from the Admin's System

The updation is dived two types of request, one that will update entire hospital data at once as mentioned above in
hospitals section, another is dividing the main data into sub parts of data

1. Primary Details - Name , Address , Coordinates, Days, timing etc
2. Availabalities - Beds, ICU, CCU, Ventilators etc.
3. Facilities - Xray, MRI, etc.
4. Blood bank
5. Emergency Cases Encountered.

## Primary Details

URL : `URL/admin/hospitals/updatePrimary`

TYPE : `POST`

ACCEPTANCE : `licence_id` and `email` fields are necessary to complete the request, else error will be thrown, you have
to send all the fields even if it is not updated. because the updates on server are tightly coupled.'

WARNING : Always send request with all fields mentioned below, else not-mentioned fields will get null values.

```json
{
  "licence_id": "LB019348231",
  "email": "sourabhraj311@gmail.com",
  "name": "Bokaro General Hospital, Bokaro Steel City",
  "description": "This is a sample description for a hospital. Best hospital in Bokaro steel city provided by Bokaro Steel Limited Government Hospital",
  "address": "Sector 4 Main Road, Bokaro"
  "city_name": "Bokaro Steel City",
  "state_name": "Jharkhand",
  "geolocation": "23.6743099, 86.1434682",
  "is_24_hr_service": false
  "opening_time": "08:20",
  "closing_time": "22:00",
  "days": "M T W T F S S"
}
```

RESPONSE :
`Success`

```json
on 200 :
Details updated successfully
```

`Error`

```json
code 200:
Invalid Request, Feeder not Verified
```

```json
code 500:
Runtime Exception: Hospital Not Found, Means hospital is eighter not created or there is error in user account.
```

## Availabalities

URL : `URL/admin/hospitals/`updateAvailability

TYPE : `POST`

ACCEPTANCE : `licence_id` and `email` fields are necessary to complete the request, else error will be thrown, you have
to send all the fields even if it is not updated. because the updates on server are tightly coupled.

WARNING : Always send request with all fields mentioned below, else not-mentioned fields will get null values.

```json5
{
  "licence_id": "LB019348231",
  "email": "sourabhraj311@gmail.com",
  "no_of_bed": "120",
  "vacant_bed": "90",
  "icu": "10",
  "vacant_icu": "3",
  "ccu": "5",
  "vacant_ccu": "2",
  "ventilators": "100",
  "vacant_ventilators": "29",
  "oxygen_cylinders": "420",
  "vacant_oxygen_cylinders": "120"
}
```

RESPONSE :

`Success`

```json5
on 200 :
Details updated successfully
```

`Error`

```json5  
code 200:
Invalid Request, Feeder not Verified
```

```json5  
code 500:
Runtime Exception: Hospital Not Found, Means hospital is eighter not created or there is error in user account.
```

# Thank You

## Sourabh
