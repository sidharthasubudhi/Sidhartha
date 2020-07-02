# CV-ConsumerDrivenTests

## Why consumer driven testing ?

In micro services architecture most of the services play role of both consumer and provider. They consume data/response from some other services as well as provide the independent functionality for other services/application to consume.

In testing of micro services, it is extremely important to make sure than any change happening in the provider service does not break any of its consuming services or application. In short, ever provider should adhere to the contract between itself and the consumer it serves

This may not be true in case of public APIs where provider of APIs publish the API and then hundreds of consumers consume it. Consumer driver testing won't be an appropriate methodology to test public APIs

These consumer driver or contract driven tests are written in a tool called **PACT**  
Please refer to https://docs.pact.io/ for more details on PACT 

Ideally, its good to write consumer tests in technologies used by consumer service and provider tests in technologies used by provider service. But as PACT currently lacks good bindings support in Python, All the tests have been written in Java

## Running Tests

This project contains tests for both **consumers** and **providers**  
Make sure of below things are up & running before executing tests:  
+ [PactBroker](https://github.com/bethesque/pact_broker)
+ Required services under test


### Starting PactBroker locally
We can start PactBroker using a Docker image. Refer file **docker-compose-pactbroker.yml** under the project folder  
run _**docker-compose --file docker-compose-pactbroker.yml  up --build**_  

If started successfully, you should be able to see PactBroker UI at http://localhost:9006/ui/relationships
Once the PactBroker is started you are ready to run the tests

### Consumer Tests
To execute the tests run **_gradle clean pactPublish -DtestFilter=com.sap.icn.mlp.consumer.*_**
pactPublish will run the Consumer tests as well as publish the generated pact files to the PactBroker  

Once the Pacts are published to PactBroker, Tests for required provider/s can be executed

 Model api consumer tests for integration landscape : 
 
 
 ```gradle clean pactPublish -DtestFilter="com.sap.icn.mlp.consumer.model.*" -Denv=integration```

 Model api security consumer tests for integration landscape : 
 
 
 ```gradle clean pactPublish -DtestFilter=com.sap.icn.mlp.consumer.security.model.* -Denv=integration```

 Foundation service broker consumer tests for integration landscape : 
 
 
 ```gradle clean pactPublish -DtestFilter="com.sap.icn.mlp.consumer.mlfservicebroker.*" -Denv=integration```

### Provider Tests
To execute provider tests for a specific service pass the required filter to gradle  
for example run : **_gradle clean test -DtestFilter=com.sap.icn.mlp.provider.*_**

 Model api provider tests for integration landscape : 
 
 
 ```gradle clean test -DtestFilter="com.sap.icn.mlp.consumer.model.*" -Denv="integration" -DclientId="sb-b12c2bc2-7834-4dd5-b828-b3ae6f6b7766!b53|foundation_standard-integration_space!b53" -DclientSecret="u9C6eGn0g0zkI5TAyrRpHa6pZkg="```
 
  Model api security provider tests for integration landscape : 
  
  
  ```gradle clean test -DtestFilter="com.sap.icn.mlp.provider.security.model.*" -Denv="integration" -DclientId_int="sb-990279cb-9a78-43be-80e5-b6df7371bb4f!b53|foundation_internal-integration_space!b53" -DclientSecret_int="sOmYFzMq1ZlNmBFxMoAPLyk1R6I=" -DclientId="sb-b12c2bc2-7834-4dd5-b828-b3ae6f6b7766!b53|foundation_standard-integration_space!b53" -DclientSecret="u9C6eGn0g0zkI5TAyrRpHa6pZkg="```
  
  Foundation service broker test for integration landscape : 
  
  
  ```gradle clean test -DtestFilter="com.sap.icn.mlp.provider.mlfservicebroker.*" -Denv="integration" -Denv=integration -DclientId=admin -DclientSecret=admin```

### Advanced Configuration Parameter
The following optional environment variable can be used when required

   ```-DproxyHost=proxy.sin.sap.corp - Proxy host is required when connecting from VPN```  
   ```-DproxyPort=8080 - Proxy port is required when connecting from VPN```  
   ```-DbrokerHost=server.com - If your PactBroker is not running on default localhost```  
   ```-DbrokerPort=9007 - If your PactBroker is not running on default port 9006```  


### Consumer and Provider Naming Conventions

Provider/Consumer naming convention = ```<Team Name>_<type of test>_<component name>```

Examples
 
 1) ***MLP team***, ***security tests*** for ***model-api component*** - ```mlp_security_model```
 2) ***Ticket Intelligence team***, ***integration tests*** for ***some component*** - ```tixIntelli_integration_somecomponent```