---
title: "API Management advanced policies"
ms.custom: na
ms.date: 2016-12-21
ms.prod: azure
ms.reviewer: na
ms.service: api-management
ms.suite: na
ms.tgt_pltfrm: na
ms.topic: reference
ms.assetid: 8a13348b-7856-428f-8e35-9e4273d94323
caps.latest.revision: 37
author: steved0x
manager: douge
translation.priority.mt: 
  - de-de
  - es-es
  - fr-fr
  - it-it
  - ja-jp
  - ko-kr
  - pt-br
  - ru-ru
  - zh-cn
  - zh-tw
---
# API Management advanced policies
This topic provides a reference for the following API Management policies. For information on adding and configuring policies, see [Policies in API Management](http://go.microsoft.com/fwlink/?LinkID=398186).  
  
##  <a name="AdvancedPolicies"></a> Advanced policies  
  
-   [Control flow](../APIManagementPolicyRef/API-Management-advanced-policies.md#choose) - Conditionally applies policy statements based on the results of the evaluation of Boolean [expressions](../APIManagementPolicyRef/API-Management-policy-expressions.md).  
  
-   [Forward request](#ForwardRequest) - Forwards the request to the backend service.  
  
-   [Log to Event Hub](#log-to-eventhub) - Sends messages in the specified format to an Event Hub defined by a [Logger](../ApiManagementREST/Azure-API-Management-REST-API-Logger-entity.md) entity.  
  
-   [Retry](#Retry) - Retries execution of the enclosed policy statements, if and until the condition is met. Execution will repeat at the specified time intervals and up to the specified retry count.  
  
-   [Return response](#ReturnResponse) - Aborts pipeline execution and returns the specified response directly to the caller.  
  
-   [Send one way request](#SendOneWayRequest) - Sends a request to the specified URL without waiting for a response.  
  
-   [Send request](#SendRequest) - Sends a request to the specified URL.  
  
-   [Set variable](../APIManagementPolicyRef/API-Management-advanced-policies.md#set-variable) - Persists a value in a named [context](../APIManagementPolicyRef/API-Management-policy-expressions.md#ContextVariables) variable for later access.  
  
-   [Set request method](#SetRequestMethod) - Allows you to change the HTTP method for a request.  
  
-   [Set status code](#SetStatus) - Changes the HTTP status code to the specified value.  
  
-   [Trace](#Trace) - Adds a string into the [API Inspector](https://azure.microsoft.com/en-us/documentation/articles/api-management-howto-api-inspector/) output.  
  
-   [Wait](#Wait) - Waits for enclosed [Send request](../APIManagementPolicyRef/API-Management-advanced-policies.md#SendRequest), [Get value from cache](../APIManagementPolicyRef/API-Management-caching-policies.md#GetFromCacheByKey), or [Control flow](../APIManagementPolicyRef/API-Management-advanced-policies.md#choose) policies to complete before proceeding.  
  
##  <a name="choose"></a> Control flow  
 The `choose` policy applies enclosed policy statements based on the outcome of evaluation of Boolean expressions, similar to an if-then-else or a switch construct in a programming language.  
  
###  <a name="ChoosePolicyStatement"></a> Policy statement  
  
```xml  
<choose>   
    <when condition="Boolean expression | Boolean constant">   
        <!— one or more policy statements to be applied if the above condition is true  -->  
    </when>   
    <when condition="Boolean expression | Boolean constant">   
        <!— one or more policy statements to be applied if the above condition is true  -->  
    </when>   
    <otherwise>   
        <!— one or more policy statements to be applied if none of the above conditions are true  -->  
</otherwise>   
</choose>  
```  
  
 The control flow policy must contain at least one `<when/>` element. The `<otherwise/>` element is optional. Conditions in `<when/>` elements are evaluated in order of their appearance within the policy. Policy statement(s) enclosed within the first `<when/>` element with condition attribute equals `true` will be applied. Policies enclosed within the `<otherwise/>` element, if present, will be applied if all of the `<when/>` element condition attributes are `false`.  
  
### Examples  
  
####  <a name="ChooseExample"></a> Example  
 The following example demonstrates a [set-variable](../APIManagementPolicyRef/API-Management-advanced-policies.md#set-variable) policy and two control flow policies.  
  
 The set variable policy is in the inbound section and creates an `isMobile` Boolean [context](../APIManagementPolicyRef/API-Management-policy-expressions.md#ContextVariables) variable that is set to true if the `User-Agent` request header contains the text `iPad` or `iPhone`.  
  
 The first control flow policy is also in the inbound section, and conditionally applies one of two [Set query string parameter](../APIManagementPolicyRef/API-Management-transformation-policies.md#SetQueryStringParameter) policies depending on the value of the `isMobile` context variable.  
  
 The second control flow policy is in the outbound section and conditionally applies the [Convert XML to JSON](../APIManagementPolicyRef/API-Management-transformation-policies.md#ConvertXMLtoJSON) policy when `isMobile` is set to `true`.  
  
```xml  
<policies>  
    <inbound>  
        <set-variable name="isMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />  
        <base />  
        <choose>  
            <when condition="@(context.Variables.GetValueOrDefault<bool>("isMobile"))">  
                <set-query-parameter name="mobile" exists-action="override">  
                    <value>true</value>  
                </set-query-parameter>  
            </when>  
            <otherwise>  
                <set-query-parameter name="mobile" exists-action="override">  
                    <value>false</value>  
                </set-query-parameter>  
            </otherwise>  
        </choose>  
    </inbound>  
    <outbound>  
        <base />  
        <choose>  
            <when condition="@(context.GetValueOrDefault<bool>("isMobile"))">  
                <xml-to-json kind="direct" apply="always" consider-accept-header="false"/>  
            </when>  
        </choose>  
    </outbound>  
</policies>  
```  
  
#### Example  
 This example shows how to perform content filtering by removing data elements from the response received from the backend service when using the `Starter` product. For a demonstration of configuring and using this policy, see [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/) and fast-forward to 34:30. Start  at 31:50 to see an overview of [The Dark Sky Forecast API](https://developer.forecast.io/) used for this demo.  
  
```xml  
<!-- Copy this snippet into the outbound section to remove a number of data elements from the response received from the backend service based on the name of the api product -->  
<choose>  
  <when condition="@(context.Response.StatusCode == 200 && context.Product.Name.Equals("Starter"))">  
    <set-body>@{  
        var response = context.Response.Body.As<JObject>();  
        foreach (var key in new [] {"minutely", "hourly", "daily", "flags"}) {  
          response.Property (key).Remove ();  
        }  
        return response.ToString();  
      }  
    </set-body>  
  </when>  
</choose>  
```  
  
### Elements  
  
|Element|Description|Required|  
|-------------|-----------------|--------------|  
|choose|Root element.|Yes|  
|when|The condition to use for the `if` or `ifelse` parts of the `choose` policy. If the `choose` policy has multiple `when` sections, they are evaluated sequentially. Once the `condition` of a when element evaluates to `true`, no further `when` conditions are evaluated.|Yes|  
|otherwise|Contains the policy snippet to be used if none of the `when` conditions evaluate to `true`.|No|  
  
### Attributes  
  
|Attribute|Description|Required|  
|---------------|-----------------|--------------|  
|condition="Boolean expression &#124; Boolean constant"|The Boolean expression or constant to evaluated when the containing `when` policy statement is evaluated.|Yes|  
  
###  <a name="ChooseUsage"></a> Usage  
 This policy can be used in the following policy [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) and [scopes](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Policy sections:** inbound, outbound, backend, on-error  
  
-   **Policy scopes:** all scopes  
  
##  <a name="ForwardRequest"></a> Forward request  
 The `forward-request` policy forwards the incoming request to the backend service specified in the request [context](../APIManagementPolicyRef/API-Management-policy-expressions.md#ContextVariables). The backend service URL is specified in the API  [settings](https://azure.microsoft.com/documentation/articles/api-management-howto-create-apis/#configure-api-settings) and can be changed using the [set backend service](../APIManagementPolicyRef/API-Management-transformation-policies.md) policy.  
  
> [!NOTE]
>  Removing this policy results in the request not being forwarded to the backend service and the policies in the outbound section are evaluated immediately upon the successful completion of the policies in the inbound section.  
  
### Policy statement  
  
```xml  
<forward-request timeout="time in seconds" follow-redirects="true | false"/>  
```  
  
### Examples  
  
#### Example  
 The following API level policy forwards all requests to the backend service with a timeout interval of 60 seconds.  
  
```xml  
<!-- api level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <forward-request timeout="60"/>  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### Example  
 This operation level policy uses the `base` element to inherit the backend policy from the parent API level scope.  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <base/>  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### Example  
 This operation level policy explicitly forwards all requests to the backend service with a timeout of 120 and does not inherit the parent API level backend policy.  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <forward-request timeout="120"/>   
        <!-- effective policy. note the absence of <base/> -->  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
#### Example  
 This operation level policy does not forward requests to the backend service.  
  
```xml  
<!-- operation level -->  
<policies>  
    <inbound>  
        <base/>  
    </inbound>  
    <backend>  
        <!-- no forwarding to backend -->  
    </backend>  
    <outbound>  
        <base/>          
    </outbound>  
</policies>  
  
```  
  
### Elements  
  
|Element|Description|Required|  
|-------------|-----------------|--------------|  
|forward-request|Root element.|Yes|  
  
### Attributes  
  
|Attribute|Description|Required|Default|  
|---------------|-----------------|--------------|-------------|  
|timeout="integer"|The timeout interval in seconds before the call to the backend service fails.|No|No timeout|  
|follow-redirects="true &#124; false"|Specifies whether redirects from the backend service are followed by the gateway or returned to the caller.|No|false|  
  
### Usage  
 This policy can be used in the following policy [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) and [scopes](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Policy sections:** backend  
  
-   **Policy scopes:** all scopes  
  
##  <a name="log-to-eventhub"></a> Log to Event Hub  
 The `log-to-eventhub` policy sends messages in the specified format to an Event Hub defined by a [Logger](../ApiManagementREST/Azure-API-Management-REST-API-Logger-entity.md) entity. As its name implies, the policy is used for saving selected request or response context information for online or offline analysis.  
  
> [!NOTE]
>  For a step-by-step guide on configuring an event hub and logging events, see [How to log API Management events with Azure Event Hubs](https://azure.microsoft.com/documentation/articles/api-management-howto-log-event-hubs/).  
  
### Policy statement  
  
```xml  
<log-to-eventhub logger-id="id of the logger entity" partition-id="index of the partition where messages are sent" partition-key="value used for partition assignment">  
  Expression returning a string to be logged  
</log-to-eventhub>  
  
```  
  
### Example  
 Any string can be used as the value to be logged in Event Hubs. In this example the date and time, deployment service name, request id, ip address, and operation name for all inbound calls are logged to the event hub [Logger](../ApiManagementREST/Azure-API-Management-REST-API-Logger-entity.md) registered with the `contoso-logger` id.  
  
```xml  
<policies>  
  <inbound>  
    <log-to-eventhub logger-id ='contoso-logger'>  
      @( string.Join(",", DateTime.UtcNow, context.Deployment.ServiceName, context.RequestId, context.Request.IpAddress, context.Operation.Name) )   
    </log-to-eventhub>  
  </inbound>  
  <outbound>          
  </outbound>  
</policies>  
```  
  
### Elements  
  
|Element|Description|Required|  
|-------------|-----------------|--------------|  
|log-to-eventhub|Root element. The value of this element is the string to log to your event hub.|Yes|  
  
### Attributes  
  
|Attribute|Description|Required|  
|---------------|-----------------|--------------|  
|logger-id|The id of the [Logger](../ApiManagementREST/Azure-API-Management-REST-API-Logger-entity.md) registered with your API Management service.|Yes|  
|partition-id|Specifies the index of the partition where messages are sent.|Optional. This attribute may not be used if `partition-key` is used.|  
|partition-key|Specifies the value used for partition assignment when messages are sent.|Optional. This attribute may not be used if `partition-id` is used.|  
  
### Usage  
 This policy can be used in the following policy [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) and [scopes](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Policy sections:** inbound, outbound, backend, on-error  
  
-   **Policy scopes:** all scopes  
  
##  <a name="Retry"></a> Retry  
 The             `retry` policy executes its child policies once and then retries their execution until the retry `condition` becomes            `false` or retry            `count` is exhausted.  
  
### Policy statement  
  
```xml  
  
<retry  
    condition="boolean expression or literal"  
    count="number of retry attempts"  
    interval="retry interval in seconds"  
    max-interval="maximum retry interval in seconds"  
    delta="retry interval delta in seconds"  
    first-fast-retry="boolean expression or literal">  
        <!-- One or more child policies. No restrictions -->  
</retry>  
  
```  
  
### Example  
 In the following example request forewarding is retried up to ten times using exponential retry algorithm. Since                    `first-fast-retry` is set to false, all retry attempts are subject to the exponsntial retry algorithm.  
  
```xml  
  
<retry  
    condition="@{context.Response.StatusCode == 500}"  
    count="10"  
    interval="10"  
    max-interval="100"  
    delta="10"  
    first-fast-retry="false">  
        <forward-request />  
</retry>  
  
```  
  
### Elements  
  
|Element|Description|Required|  
|-------------|-----------------|--------------|  
|retry|Root element. May contain any other policies as its child elements.|Yes|  
  
### Attributes  
  
|Attribute|Description|Required|Default|  
|---------------|-----------------|--------------|-------------|  
|condition|A boolean literal or [expression](../APIManagementPolicyRef/API-Management-policy-expressions.md) specifying if retries should be stopped (`false`) or continued (`true`).|Yes|N/A|  
|count|A positive number specifying the maximum number of retries to attempt.|Yes|N/A|  
|interval|A positive number in seconds specifying the wait interval between the retry attempts.|Yes|N/A|  
|max-interval|A positive number in seconds specifying the maximum wait interval between the retry attempts. It is used to implement an exponential retry algorithm.|No|N/A|  
|delta|A positive number in seconds specifying the wait interval increment. It is used to implement the linear and exponential retry algorithms.|No|N/A|  
|first-fast-retry|If set to                                    `true` , the first retry attempt is performed immediately.|No|`false`|  
  
> [!NOTE]
>  When only the `interval` is specified, **fixed** interval retries are performed.  
>  When only the `interval` and `delta` are specified, a **linear** interval retry algorithm is used, where wait time between retries is calculated according the following formula - `interval + (count - 1)*delta`.  
>  When the `interval`, `max-interval` and `delta` are specified, **exponential** interval retry algorithm is applied, where the wait time between the retries is growing exponentially from the value of `interval` to the value `max-interval` according to the following forumula - `min(interval + (2^count - 1) * random(delta * 0.8, delta * 1.2), max-interval)`.  
  
### Usage  
 This policy can be used in the following policy                    [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) and                   [scopes](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) . Note that child policy usage restrictions will be inherited by this policy.  
  
-   **Policy sections:** inbound, outbound, backend, on-error  
  
-   **Policy scopes:** all scopes  
  
##  <a name="ReturnResponse"></a> Return response  
 The `return-response` policy aborts pipeline execution and returns either a default or custom response to the caller. Default response is `200 OK` with no body. Custom response can be specified via a context variable or policy statements. When both are provided, the response contained within the context variable is modified by the policy statements before being returned to the caller.  
  
### Policy statement  
  
```xml  
<return-response response-variable-name="existing context variable">  
  <set-header/>  
  <set-body/>  
  <set-status/>  
</return-response>  
  
```  
  
### Example  
  
```xml  
<return-response>  
   <set-status code="401" reason="Unauthorized"/>  
     <set-header name="WWW-Authenticate" exists-action="override">  
        <value>Bearer error="invalid_token"</value>  
     </set-header>  
</return-response>  
  
```  
  
### Elements  
  
|Element|Description|Required|  
|-------------|-----------------|--------------|  
|return-response|Root element.|Yes|  
|set-header|A [set-header](../APIManagementPolicyRef/API-Management-transformation-policies.md#SetHTTPheader) policy statement.|No|  
|set-body|A [set-body](../APIManagementPolicyRef/API-Management-transformation-policies.md#SetBody) policy statement.|No|  
|set-status|A [set-status](../APIManagementPolicyRef/API-Management-advanced-policies.md#SetStatus) policy statement.|No|  
  
### Attributes  
  
|Attribute|Description|Required|  
|---------------|-----------------|--------------|  
|response-variable-name|The name of the context variable referenced from, for example, an upstream [send-request](../APIManagementPolicyRef/API-Management-advanced-policies.md#SendRequest) policy and containing a `Response` object|Optional.|  
  
### Usage  
 This policy can be used in the following policy [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) and [scopes](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Policy sections:** inbound, outbound, backend, on-error  
  
-   **Policy scopes:** all scopes  
  
##  <a name="SendOneWayRequest"></a> Send one way request  
 The `send-one-way-request` policy sends the provided request to the specified URL without waiting for a response.  
  
### Policy statement  
  
```xml  
<send-one-way-request mode="new | copy">  
  <url>...</url>  
  <method>...</method>  
  <header name="" exists-action="override | skip | append | delete">...</header>  
  <body>...</body>  
</send-one-way-request>  
  
```  
  
### Example  
 This sample policy shows an example of using the `send-one-way-request` policy to send a message to a Slack chat room if the HTTP response code is greater than or equal to 500. For more information on this sample, see [Using external services from the Azure API Management service](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
```xml  
<choose>  
    <when condition="@(context.Response.StatusCode >= 500)">  
      <send-one-way-request mode="new">  
        <set-url>https://hooks.slack.com/services/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</set-url>  
        <set-method>POST</set-method>  
        <set-body>@{  
                return new JObject(  
                        new JProperty("username","APIM Alert"),  
                        new JProperty("icon_emoji", ":ghost:"),  
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",  
                                                context.Request.Method,  
                                                context.Request.Url.Path + context.Request.Url.QueryString,  
                                                context.Request.Url.Host,  
                                                context.Response.StatusCode,  
                                                context.Response.StatusReason,  
                                                context.User.Email  
                                                ))  
                        ).ToString();  
            }</set-body>  
      </send-one-way-request>  
    </when>  
</choose>  
  
```  
  
### Elements  
  
|Element|Description|Required|  
|-------------|-----------------|--------------|  
|send-one-way-request|Root element.|Yes|  
|url|The URL of the request.|No if mode=copy; otherwise yes.|  
|method|The HTTP method for the request.|No if mode=copy; otherwise yes.|  
|header|Request header. Use multiple header elements for multiple request headers.|No|  
|body|The request body.|No|  
  
### Attributes  
  
|Attribute|Description|Required|Default|  
|---------------|-----------------|--------------|-------------|  
|mode="string"|Determines whether this is a new request or a copy of the current request. In outbound mode, mode=copy does not initialize the request body.|No|New|  
|name|Specifies the name of the header to be set.|Yes|N/A|  
|exists-action|Specifies what action to take when the header is already specified. This attribute must have one of the following values.<br /><br /> -   override - replaces the value of the existing header.<br />-   skip - does not replace the existing header value.<br />-   append - appends the value to the existing header value.<br />-   delete - removes the header from the request.<br /><br /> When set to `override` enlisting multiple entries with the same name results in the header being set according to all entries (which will be listed multiple times); only listed values will be set in the result.|No|override|  
  
### Usage  
 This policy can be used in the following policy [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) and [scopes](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Policy sections:** inbound, outbound, backend, on-error  
  
-   **Policy scopes:** all scopes  
  
##  <a name="SendRequest"></a> Send request  
 The `send-request` policy sends the provided request to the specified URL, waiting no longer than the set timeout value.  
  
### Policy statement  
  
```xml  
<send-request mode="new|copy" response-variable-name="" timeout="60 sec" ignore-error  
="false|true">  
  <set-url>...</set-url>  
  <set-method>...</set-method>  
  <set-header name="" exists-action="override|skip|append|delete">...</set-header>  
  <set-body>...</set-body>  
</send-request>  
  
```  
  
### Example  
 This example shows one way to verify a reference token with an authorization server. For more information on this sample, see [Using external services from the Azure API Management service](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
```xml  
<inbound>  
  <!-- Extract Token from Authorization header parameter -->  
  <set-variable name="token" value="@(context.Request.Headers.GetValueOrDefault("Authorization","scheme param").Split(' ').Last())" />  
  
  <!-- Send request to Token Server to validate token (see RFC 7662) -->  
  <send-request mode="new" response-variable-name="tokenstate" timeout="20" ignore-error="true">  
    <set-url>https://microsoft-apiappec990ad4c76641c6aea22f566efc5a4e.azurewebsites.net/introspection</set-url>  
    <set-method>POST</set-method>  
    <set-header name="Authorization" exists-action="override">  
      <value>basic dXNlcm5hbWU6cGFzc3dvcmQ=</value>  
    </set-header>  
    <set-header name="Content-Type" exists-action="override">  
      <value>application/x-www-form-urlencoded</value>  
    </set-header>  
    <set-body>@($"token={(string)context.Variables["token"]}")</set-body>  
  </send-request>  
  
  <choose>  
        <!-- Check active property in response -->  
        <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">  
            <!-- Return 401 Unauthorized with http-problem payload -->  
            <return-response response-variable-name="existing response variable">  
                <set-status code="401" reason="Unauthorized" />  
                <set-header name="WWW-Authenticate" exists-action="override">  
                    <value>Bearer error="invalid_token"</value>  
                </set-header>  
            </return-response>  
        </when>  
    </choose>  
  <base />  
</inbound>  
  
```  
  
### Elements  
  
|Element|Description|Required|  
|-------------|-----------------|--------------|  
|send-request|Root element.|Yes|  
|url|The URL of the request.|No if mode=copy; otherwise yes.|  
|method|The HTTP method for the request.|No if mode=copy; otherwise yes.|  
|header|Request header. Use multiple header elements for multiple request headers.|No|  
|body|The request body.|No|  
  
### Attributes  
  
|Attribute|Description|Required|Default|  
|---------------|-----------------|--------------|-------------|  
|mode="string"|Determines whether this is a new request or a copy of the current request. In outbound mode, mode=copy does not initialize the request body.|No|New|  
|response-variable-name="string"|If not present, `context.Response` is used.|No|N/A|  
|timeout="integer"|The timeout interval in seconds before the call to the URL fails.|No|60|  
|ignore-error|If true and the request results in an error:<br /><br /> -   If response-variable-name was specified it will contain a null value.<br />-   If response-variable-name was not specified, context.Request will not be updated.|No|false|  
|name|Specifies the name of the header to be set.|Yes|N/A|  
|exists-action|Specifies what action to take when the header is already specified. This attribute must have one of the following values.<br /><br /> -   override - replaces the value of the existing header.<br />-   skip - does not replace the existing header value.<br />-   append - appends the value to the existing header value.<br />-   delete - removes the header from the request.<br /><br /> When set to `override` enlisting multiple entries with the same name results in the header being set according to all entries (which will be listed multiple times); only listed values will be set in the result.|No|override|  
  
### Usage  
 This policy can be used in the following policy [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) and [scopes](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Policy sections:** inbound, outbound, backend, on-error  
  
-   **Policy scopes:** all scopes  
  
##  <a name="set-variable"></a> Set variable  
 The `set-variable` policy declares a [context](../APIManagementPolicyRef/API-Management-policy-expressions.md#ContextVariables) variable and assigns it a value specified via an [expression](../APIManagementPolicyRef/API-Management-policy-expressions.md) or a string literal. if the expression contains a literal it will be converted to a string and the type of the value will be `System.String`.  
  
###  <a name="set-variablePolicyStatement"></a> Policy statement  
  
```xml  
<set-variable name="variable name" value="Expression | String literal" />  
```  
  
###  <a name="set-variableExample"></a> Example  
 The following example demonstrates a set variable policy in the inbound section. This set variable policy creates an `isMobile` Boolean [context](../APIManagementPolicyRef/API-Management-policy-expressions.md#ContextVariables) variable that is set to true if the `User-Agent` request header contains the text `iPad` or `iPhone`.  
  
```xml  
<set-variable name="IsMobile" value="@(context.Request.Headers["User-Agent"].Contains("iPad") || context.Request.Headers["User-Agent"].Contains("iPhone"))" />  
```  
  
### Elements  
  
|Element|Description|Required|  
|-------------|-----------------|--------------|  
|set-variable|Root element.|Yes|  
  
### Attributes  
  
|Attribute|Description|Required|  
|---------------|-----------------|--------------|  
|name|The name of the variable.|Yes|  
|value|The value of the variable. This can be an expression or a literal value.|Yes|  
  
### Usage  
 This policy can be used in the following policy [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) and [scopes](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Policy sections:** inbound, outbound, backend, on-error  
  
-   **Policy scopes:** all scopes  
  
###  <a name="set-variableAllowedTypes"></a> Allowed types  
 Expressions used in the `set-variable` policy must return one of the following basic types.  
  
-   System.Boolean  
  
-   System.SByte  
  
-   System.Byte  
  
-   System.UInt16  
  
-   System.UInt32  
  
-   System.UInt64  
  
-   System.Int16  
  
-   System.Int32  
  
-   System.Int64  
  
-   System.Decimal  
  
-   System.Single  
  
-   System.Double  
  
-   System.Guid  
  
-   System.String  
  
-   System.Char  
  
-   System.DateTime  
  
-   System.TimeSpan  
  
-   System.Byte?  
  
-   System.UInt16?  
  
-   System.UInt32?  
  
-   System.UInt64?  
  
-   System.Int16?  
  
-   System.Int32?  
  
-   System.Int64?  
  
-   System.Decimal?  
  
-   System.Single?  
  
-   System.Double?  
  
-   System.Guid?  
  
-   System.String?  
  
-   System.Char?  
  
-   System.DateTime?  
  
##  <a name="SetRequestMethod"></a> Set request method  
 The `set-method` policy allows you to change the HTTP request method for a request.  
  
### Policy statement  
  
```xml  
<set-method>METHOD</set-method>  
  
```  
  
### Example  
 This sample policy that uses the `set-method` policy shows an example of sending a message to a Slack chat room if the HTTP response code is greater than or equal to 500. For more information on this sample, see [Using external services from the Azure API Management service](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/).  
  
```xml  
<choose>  
    <when condition="@(context.Response.StatusCode >= 500)">  
      <send-one-way-request mode="new">  
        <set-url>https://hooks.slack.com/services/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</set-url>  
        <set-method>POST</set-method>  
        <set-body>@{  
                return new JObject(  
                        new JProperty("username","APIM Alert"),  
                        new JProperty("icon_emoji", ":ghost:"),  
                        new JProperty("text", String.Format("{0} {1}\nHost: {2}\n{3} {4}\n User: {5}",  
                                                context.Request.Method,  
                                                context.Request.Url.Path + context.Request.Url.QueryString,  
                                                context.Request.Url.Host,  
                                                context.Response.StatusCode,  
                                                context.Response.StatusReason,  
                                                context.User.Email  
                                                ))  
                        ).ToString();  
            }</set-body>  
      </send-one-way-request>  
    </when>  
</choose>  
  
```  
  
### Elements  
  
|Element|Description|Required|  
|-------------|-----------------|--------------|  
|set-method|Root element. The value of the element specifies the HTTP method.|Yes|  
  
### Usage  
 This policy can be used in the following policy [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) and [scopes](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Policy sections:** inbound, on-error  
  
-   **Policy scopes:** all scopes  
  
##  <a name="SetStatus"></a> Set status code  
 The `set-status` policy sets the HTTP status code to the specified value.  
  
### Policy statement  
  
```xml  
<set-status code="" reason=""/>  
  
```  
  
### Example  
 This example shows how to return a 401 response if the authorization token is invalid. For more information, see [Using external services from the Azure API Management service](https://azure.microsoft.com/documentation/articles/api-management-sample-send-request/)  
  
```xml  
<choose>  
  <when condition="@((bool)((IResponse)context.Variables["tokenstate"]).Body.As<JObject>()["active"] == false)">  
    <return-response response-variable-name="existing response variable">  
      <set-status code="401" reason="Unauthorized" />  
      <set-header name="WWW-Authenticate" exists-action="override">  
        <value>Bearer error="invalid_token"</value>  
      </set-header>  
    </return-response>  
  </when>  
</choose>  
  
```  
  
### Elements  
  
|Element|Description|Required|  
|-------------|-----------------|--------------|  
|set-status|Root element.|Yes|  
  
### Attributes  
  
|Attribute|Description|Required|Default|  
|---------------|-----------------|--------------|-------------|  
|code="integer"|The HTTP status code to return.|Yes|N/A|  
|reason="string"|A description of the reason for returning the status code.|Yes|N/A|  
  
### Usage  
 This policy can be used in the following policy [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) and [scopes](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Policy sections:** outbound, backend, on-error  
  
-   **Policy scopes:** all scopes  
  
##  <a name="Trace"></a> Trace  
 The             `trace` policy adds a string into the [API Inspector](https://azure.microsoft.com/en-us/documentation/articles/api-management-howto-api-inspector/) output. The policy will execute only when tracing is triggered, i.e. `Ocp-Apim-Trace` request header is present and set to `true` and `Ocp-Apim-Subscription-Key` request header is present and holds a valid key associated with the admin account.  
  
### Policy statement  
  
```xml  
  
<trace source="arbitrary string literal"/>  
    <!-- string expression or literal -->  
</trace>  
  
```  
  
### Elements  
  
|Element|Description|Required|  
|-------------|-----------------|--------------|  
|trace|Root element.|Yes|  
  
### Attributes  
  
|Attribute|Description|Required|Default|  
|---------------|-----------------|--------------|-------------|  
|source|String literal meaningful to the trace viewer and specifying the source of the message.|Yes|N/A|  
  
### Usage  
 This policy can be used in the following policy                    [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) and                   [scopes](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes) .  
  
-   **Policy sections:** inbound, outbound, backend, on-error  
  
-   **Policy scopes:** all scopes  
  
##  <a name="Wait"></a> Wait  
 The `wait` policy executes its immediate child policies in parallel, and waits for either all or one of its immediate child policies to complete before it completes. The wait policy can have as its immediate child policies [Send request](../APIManagementPolicyRef/API-Management-advanced-policies.md#SendRequest), [Get value from cache](../APIManagementPolicyRef/API-Management-caching-policies.md#GetFromCacheByKey), and [Control flow](../APIManagementPolicyRef/API-Management-advanced-policies.md#choose) policies.  
  
### Policy statement  
  
```xml  
<wait for="all|any">  
  <!--Wait policy can contain send-request, cache-lookup-value,   
        and choose policies as child elements -->  
</wait>  
  
```  
  
### Example  
 In the following example there are two `choose` policies as immediate child policies of the `wait` policy. Each of these `choose` policies executes in parallel. Each `choose` policy attempts to retrieve a cached value. If there is a cache miss, a backend service is called to provide the value. In this example the `wait` policy does not complete until all of its immediate child policies complete, because the `for` attribute is set to `all`.   In this example the context variables (`execute-branch-one`, `value-one`, `execute-branch-two`, and `value-two`) are declared outside of the scope of this example policy.  
  
```xml  
<wait for="all">  
  <choose>  
    <when condition="@((bool)context.Variables["execute-branch-one="])">  
      <cache-lookup-value key="key-one" variable-name="value-one" />  
      <choose>  
        <when condition="@(!context.Variables.ContainsKey("value-one="))">  
          <send-request mode="new" response-variable-name="value-one">  
            <set-url>https://backend-one</set-url>  
            <set-method>GET</set-method>  
          </send-request>  
        </when>  
      </choose>  
    </when>  
  </choose>  
  <choose>  
    <when condition="@((bool)context.Variables["execute-branch-two="])">  
      <cache-lookup-value key="key-two" variable-name="value-two" />  
      <choose>  
        <when condition="@(!context.Variables.ContainsKey("value-two="))">  
          <send-request mode="new" response-variable-name="value-two">  
            <set-url>https://backend-two</set-url>  
            <set-method>GET</set-method>  
          </send-request>  
        </when>  
      </choose>  
    </when>  
  </choose>  
</wait>  
  
```  
  
### Elements  
  
|Element|Description|Required|  
|-------------|-----------------|--------------|  
|wait|Root element. May contain as child elements only `send-request`, `cache-lookup-value`, and `choose` policies.|Yes|  
  
### Attributes  
  
|Attribute|Description|Required|Default|  
|---------------|-----------------|--------------|-------------|  
|for|Determines whether the `wait` policy waits for all immediate child policies to be completed or just one. Allowed values are:<br /><br /> -   `all` - wait for all immediate child policies to complete<br />-   any - wait for any immediate child policy to complete. Once the first immediate child policy has completed, the `wait` policy completes and execution of any other immediate child policies is terminated.|No|all|  
  
### Usage  
 This policy can be used in the following policy [sections](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#sections) and [scopes](http://azure.microsoft.com/documentation/articles/api-management-howto-policies/#scopes).  
  
-   **Policy sections:** inbound, outbound, backend  
  
-   **Policy scopes:**all scopes  
  
## See Also  
 [Policy expressions](../APIManagementPolicyRef/API-Management-policy-expressions.md)