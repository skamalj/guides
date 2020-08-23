# Courses and Documentation
Link to Course - https://www.coursera.org/specializations/apigee-api-gcp   
This is 3 course specialisation available on coursera,by no means it is comprehensive but it gets you started with APIGEE in a comfortable way.    
      
In addition to the course, I went through official documentation available here - https://docs.apigee.com/api-platform/get-started/learn-edge    
There are 3 sections which are important from exam point of view. 
* Develop
* Publish
* Reference    
You see these links in top bar. I am covering the links from Develop and Publish section in wiki below.    
     
"Reference" section is absolutely important and in my view one cannot hope to pass the exam without  going through this.  All the policies which I refer below in the wiki, one should go through its reference documentation in this section and understand all the parameters and configurations involved. I give you example below for one of the policy "PopulateCache".     
    
Now for this policy, if you look at the structure below,  you should know 
* what is "CacheKey"?
* How "cacheKey" is created?
* What are different scopes?
* What is source?
* See that your can specify timeout.
```
<PopulateCache async="false" continueOnError="false" enabled="true" name="Populate-Cache-1">    
    <DisplayName>Populate Cache 1</DisplayName>     
    <Properties/>     

<!-- CacheKey is the lookup key for item stored in the cache.  Prefix , if specified, overrides scope element -->     
    <CacheKey>    
        <Prefix/>    
        <KeyFragment ref=""/>     
    </CacheKey>     
    <!-- Omit this element if you're using the included shared cache. -->     
    <CacheResource/>     

<!-- Scope can be Global, ApplicationName(ApiProxyname), Proxy , Target (Proxy or Target name), Exclusive (Same as proxy or target , but depends on where policy is attahced -->    
    <Scope>Exclusive</Scope>     
    <ExpirySettings>     
        <TimeoutInSeconds>300</TimeoutInSeconds>     
    </ExpirySettings>     

<!-- Source of value , which needs to be stored. Source and CacheKey form key-value pair -->     
    <Source>flowVar</Source>     
</PopulateCache>     
``` 
  


# API Design 

Read [this page](https://docs.apigee.com/api-platform/fundamentals/best-practices-api-proxy-design-and-development) multiple times if required. It refers a book [The Missing Link](https://pages.apigee.com/web-api-design-register.html?int_source=docs&int_medium=website&int_campaign=ebook&int_content=web-api-design-missing-link). Read this book cover to cover. It covers API design in a really good way.   
The topics covered are :- 
* URL Design 
* Coding standard and Fault handling 
* Persistence vs Caching 
* Callout, monitoring and Security and more.. 

# Basics 

Create first proxy - https://docs.apigee.com/api-platform/get-started/get-started   
What is APIGEE - https://docs.apigee.com/api-platform/get-started/what-apigee-edge   

My Notes
* Each APIGEE account can have multiple “Org”. Free account is limited to one account 
* Each Org can have multiple environments. Free account has “test” and “prod” 
* Virtual hosts are dns aliases (Ex. a.b.com), which form the host part of your API url. These can be multiple but free account limits to just two “default” and “secure”.  In simple terms, if you had your own APIGEE setup then IP address of your router can be mapped to multiple dns names which in turn will map to multiple virtualHosts. 
* As virtual hosts are to accept the proxy, targets are for sending the traffic to target endpoints. More on these later. 
* Policies are rules which the traffic must pass through from virtual host to target and back. These form bulk of the exam. 

# What is API Proxy    

Learn about what is ProxyEndPoint, TargetEndPoint and Policies - https://docs.apigee.com/api-platform/fundamentals/understanding-apis-and-api-proxies    
Understand Routes - https://docs.apigee.com/api-platform/fundamentals/understanding-routes 

# Flows 

What are Flows - https://docs.apigee.com/api-platform/fundamentals/what-are-flows    
Conditional Flows - https://youtu.be/H3dbycHbPpA    
Flow Variables - https://docs.apigee.com/api-platform/fundamentals/introduction-flow-variables    

# Policies 
What is policy - https://docs.apigee.com/api-platform/develop/policy-attachment-and-enforcement     
* Do watch video in above page 
         
Attaching Policy - https://docs.apigee.com/api-platform/develop/attaching-and-configuring-policies-management-ui   
 
# Fault Handling 

Fault Handling - https://docs.apigee.com/api-platform/fundamentals/fault-handling 

There are two types of errors:-  
* Automatic , raised by various policies 
* Custom raised by using "RasieFault" policy 

## RaiseFault Policy   
RaiseFault is a policy which is used in flows to raise errors based on conditions.  This is like raising exception in java program.     
```
<TargetEndpoint name="default">     
...    
  <Response>           
    <Step>       
      <Name>Raise-Fault-1</Name>              
      <Condition>(message.content Like "*unavailable*")</Condition>        
    </Step>     
  </Response>
```  

Use conditions when raising faults using Raisefault 
Can be raised either in proxy endpoint or target endpoint 

## FaultRules 

* Fault rules are like catch blocks in Java Program 
* Default faultrule is executed when no fault rule is defined or they are defined but none is executed. In a case where always senforce is set to true, defaultrule is always executed  
* When fault occurs, further execution of policies is halted and  proxy enters error flow. I.e proxy looks for appropriate faultrules for execution. 


```
<FaultRules>   
       <FaultRule name="invalid_key_rule">   
            <Step>   
                <Name>invalid-key-message</Name>    
            </Step>    
            <Condition>(fault.name = "FailedToResolveAPIKey")</Condition>    
        </FaultRule>    
    </FaultRules>    
    <DefaultFaultRule name="default-fault">    
        <Step>    
            <Name>Default-message</Name>    
        </Step>     
    </DefaultFaultRule> 
```

* Faultrule can have multiple steps and each step can have condition associated.  
* If the error occurs in Proxy, faultrules defined in proxyendpoint are evaluated bottom-up.  For targetendpoint rules are evaluated top-down. 
* Fault raised in proxy is not handled by faultrules in target and vice-versa 
* AssignMessagePolicy and MessageLoggingPolicy are recommended to use in faultrules 
* Only first matching rule is executed and rest are skipped. 
* Multiple or Zero steps within the rule can be executed, but fault rule is considered as executed.  
* Both raisefault and faultrule can set or customize the error response, if both are defined , faultrule wins – because it is the handler 
* ContinueOnError 
* * If set to true, policy will not enter error state and hence no faultrules wil be executed. 
* * In this case next policy will be executed where application needs to handle the error using conditional policies 

# All policies mentioned from this point onward need you to refer "reference" section of the documentation page.

# Mediation 

Shape and Convert Messages - https://docs.apigee.com/api-platform/develop/shaping-and-converting-messages
### Policies
* JSONtoXML
* AssignMessage
* ExtractVariables
* XMLtoJSON

# Caching 
There are some nice videos on this topic and all are consolidated on [this page](https://docs.apigee.com/api-platform/cache/cache-videos), watch these.
### Policies
* PopulateCache   
> Policy sets populatecache.{policyname}.fault = "true"  and fault.name on failure.    
* LookupCache    
> Flowvariable – lookupcache.{policyname}.cachehit  is set to "true/false" based on the status of lookup.     
> This should be first policy in the flow.  PopulateCache should then populate the cache based on lookup status, in addition to any other policy which needs to be executed to get the data (Ex. policy to get usertoken )   
* InvalidateCache
> If prefix is not specified it's value is obtained from "scope".    
> If prefix is specified then it overwrites "scope"     
> There is also cachecontext, which can be used to override appname and targetname, if cache is populated by other other API.   
* ResponseCache
> Concept to understand is that this policy allows request headers to override some of the policy parameters. 
> If Expiry is set and "UseResponseCacheHeaders" is true, then minimum value from these two is used.    
> UseAcceptHeader if set to true, then "Accept" header from request is used (along with other cachekey settings)  to set the cachekey     
> SkipCache is used to specify conditions (ex. request.queryparam.q = nodejs) to skip lookup/population of cache     
> Scope, prefix works exactly as in previous cache policies     
> Used for long-term caching or with backends where data does not change frequently        
> Can only be applied to proxy endpoint preflow request and target endpoint postflow response and only once in request or response.     
> Needs to be added at both places if manual coding , this is done by the UI itself in portal when added to anyone.      
* Key Value Map Operations     
> [KVM Overview](https://docs.apigee.com/api-platform/cache/key-value-maps)   
> [Creating KVM](https://docs.apigee.com/api-platform/cache/creating-and-editing-environment-keyvalue-maps)

# Logging  

# Rate Limiting and Quotas    

### Quota Policy     
* Place quota policy after authentication to ensure quota are calculated for each app/developers.
* This count is over a priod, but it does not say if this quota can be consumed in 1sec or in 1 month. This is the prime difference between this and spikearrest   
* This policy allows assigning different weights to different request ex setting GET to 0 or setting POST to 2. This will mean that each GET request will count as 1 towards quota while POST will count as 2.   
```
<Quota async="false" continueOnError="false" enabled="true" name="Quota-3" type="calendar"> 
   <DisplayName>Quota 3</DisplayName> 

<!-- This count is over a priod, but it does not say if this quota can be consumed in 1sec or in 1 month. This is the prime difference between this and spikearrest --> 
   <Allow count="2000" countRef="verifyapikey.VerifyAPIKey.apiproduct.developer.quota.limit"/> 
   <Allow> 
      <Class ref="request.queryparam.time_variable"> 
        <Allow class="peak_time" count="5000"/> 
        <Allow class="off_peak_time" count="1000"/> 
      </Class> 
   </Allow> 

<!-- count, interval and timeunit can refer attributes in product like verifyapikey.{policy_name}.apiproduct.{attribute} -->  
   <Interval ref="verifyapikey.VerifyAPIKey.apiproduct.developer.quota.interval">1</Interval>  
   <TimeUnit ref="verifyapikey.VerifyAPIKey.apiproduct.developer.quota.timeunit">month</TimeUnit> 
   <StartTime>2017-7-16 12:00:00</StartTime>  
   <Distributed>false</Distributed>  
   <Synchronous>false</Synchronous>  
   <AsynchronousConfiguration>  
      <SyncIntervalInSeconds>20</SyncIntervalInSeconds>  
      <SyncMessageCount>5</SyncMessageCount>  
   </AsynchronousConfiguration>  
   <Identifier/>  

<!-- by default all requests cout as 1 , but using message weight different weights can be assigned to different request ex setting GET to 0 or setting POST to 2. To make it work, first a policy needs to set a weight for request in a flow variable, which then is referred in MessageWeight --> 
   <MessageWeight/>  
</Quota> 
```

### SpikeArrest Policy
* This should be first policy in the proxy to guard against unusual spikes. 
* Important point to note is that, whatever value is provided in rate is smoothed out to 1 request per unit I.e 6pm means 1 request per 10sec, 10ps means 1 request per 100ms. Proxy will then not allow 2nd request within 10secs in first case and 100ms in 2nd case     
* Downside is that no matter what value is provided, concurrent requests are most likely to fail        
```
<SpikeArrest 
  continueOnError="[false|true]" 
  enabled="[true|false]" 
  name="policy_name" 
> 
  <DisplayName>display_name</DisplayName> 
  <Properties/> 
  <Identifier ref="flow_variable"/> 
  <MessageWeight ref="flow_variable"/> 

<!-- Important point to note is that, whatever value is provided in rate is smoothed out to 1 request per unit I.e 6pm means 1 request per 10sec, 10ps means 1 request per 100ms. Proxy will then not allow 2nd request within 10secs in first case and 100ms in 2nd case --> 
  <Rate ref="flow_variable">rate[pm|ps]</Rate> 
  <UseEffectiveCount>[false|true]</UseEffectiveCount> 
</SpikeArrest> 
```

### ConcurrentrateLimit Policy (Will be deprecated) 

* Used in target endpoint preflow to throttle requests to target servers. For example if multiple proxies are sending traffic to same targetendpoint. 

### [Comaparing Rate Limiting Policies](https://docs.apigee.com/api-platform/develop/comparing-quota-spike-arrest-and-concurrent-rate-limit-policies)

# Security 
This is one of the biggest topic by far.   
Start here - https://docs.apigee.com/api-platform/security/api-security . I will recommend that you read every single page under this, but if short on time then go through the one's I have linked below.   

## OAuth Grant Types 

Videos - https://docs.apigee.com/api-platform/security/oauth/oauth2-videos

### [Client Credentials](https://docs.apigee.com/api-platform/security/oauth/oauth-20-client-credentials-grant-type)    
* App is the resource owner 
* Resource owner is not involved 
* Trusted client application Ex. Cron jobs, server-side applications 


### [Auth](https://docs.apigee.com/api-platform/security/oauth/oauth-v2-policy-authorization-code-grant-type)    
* These are 3rd party application I.e untrusted clients 
* Resource owner consent is required  

### [Password](https://docs.apigee.com/api-platform/security/oauth/implementing-password-grant-type) 
* Highly trusted first party client application  
* Client credentials are shared with app  

### Implicit 
* Simplified Auth type, where application is 3rd party but is single page. So auth token is accessible to rest of the application to get accesstokens 
 
### [Accessing Tokens and Codes](https://docs.apigee.com/api-platform/security/oauth/access-tokens)

### Policies
* AccessControl
* BasicAuthentication
* JSONThreatProtection
* OAuthV2
* RegularExpressionProtection
* XMLThreatProtection
* SOAPMessageValidation

# Custom Policy 

# Callouts

# TargetServers   

# Microgateway 

# API Products and Apps 
