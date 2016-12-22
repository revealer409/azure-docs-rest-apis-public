---
title: "API Management policy expressions"
ms.custom: na
ms.date: 2016-12-21
ms.prod: azure
ms.reviewer: na
ms.service: api-management
ms.suite: na
ms.tgt_pltfrm: na
ms.topic: reference
ms.assetid: ea160028-fc04-4782-aa26-4b8329df3448
caps.latest.revision: 21
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
# API Management policy expressions
Policy expressions  syntax is C# 6.0. Each expression has access to the implicitly provided [context](../APIManagementPolicyRef/API-Management-policy-expressions.md#ContextVariables) variable and an allowed [subset](../APIManagementPolicyRef/API-Management-policy-expressions.md#CLRTypes) of .NET Framework types.  
  
> [!NOTE]
>  For more information about policy expressions, see the [Policy Expressions](https://azure.microsoft.com/documentation/videos/policy-expressions-in-azure-api-management/) video.  
>   
>  For demonstrations of configuring policies using policy expressions, see [Cloud Cover Episode 177: More API Management Features with Vlad Vinogradsky](https://azure.microsoft.com/documentation/videos/episode-177-more-api-management-features-with-vlad-vinogradsky/). This video contains the following policy expression demonstrations.  
>   
>  -   10:30 - See how to apply policy at the API level to supply context information to the backend service using the [Set query string parameter](../APIManagementPolicyRef/API-Management-transformation-policies.md#SetQueryStringParameter) and [Set HTTP header](../APIManagementPolicyRef/API-Management-transformation-policies.md#SetHTTPheader) policies. At 12:10 there is a demo of calling an operation in the developer portal where you can see these policies at work.  
> -   13:50 - See how to use the [Validate JWT](../APIManagementPolicyRef/API-Management-access-restriction-policies.md#ValidateJWT) policy to pre-authorize access to operations based on token claims. Fast forward to 15:00 to see the policies configured in the policy editor and then to 18:50 for a demonstration of calling an operation from the developer portal both with and without the required authorization token.  
> -   21:00 - See how to use an [API Inspector](https://azure.microsoft.com/documentation/articles/api-management-howto-api-inspector/) trace to see how policies are evaluated and the results of the evaluations.  
> -   25:25 - See how to use policy expressions with the [Get from cache](../APIManagementPolicyRef/API-Management-caching-policies.md#GetFromCache) and [Store to cache](../APIManagementPolicyRef/API-Management-caching-policies.md#StoreToCache) policies to configure API Management response caching duration that matches the response caching of the backend service as specified by the backed service's `Cache-Control` directive.  
> -   34:30 - See how to perform content filtering by removing data elements from the response received from the backend service using the [Control flow](../APIManagementPolicyRef/API-Management-advanced-policies.md#choose) and [Set body](../APIManagementPolicyRef/API-Management-transformation-policies.md#SetBody) policies. Start  at 31:50 to see an overview of [The Dark Sky Forecast API](https://developer.forecast.io/) used for this demo.  
> -   To download the policy statements used in this video, see the [api-management-samples/policies](https://github.com/Azure/api-management-samples/tree/master/policies) github repo.  
  
## In this topic  
  
-   [Syntax](../APIManagementPolicyRef/API-Management-policy-expressions.md#Syntax)  
  
-   [Examples](../APIManagementPolicyRef/API-Management-policy-expressions.md#PolicyExpressionsExamples)  
  
-   [Usage](../APIManagementPolicyRef/API-Management-policy-expressions.md#PolicyExpressionsUsage)  
  
-   [.NET Framework types allowed in policy expressions](../APIManagementPolicyRef/API-Management-policy-expressions.md#CLRTypes)  
  
-   [Context variable](../APIManagementPolicyRef/API-Management-policy-expressions.md#ContextVariables)  
  
##  <a name="Syntax"></a> Syntax  
 Single statement expressions are enclosed in `@(expression)`, where `expression` is a well-formed C# expression statement.  
  
 Multi-statement expressions are enclosed in `@{expression}`. All code paths within multi-statement expressions must end with a `return` statement.  
  
##  <a name="PolicyExpressionsExamples"></a> Examples  
  
```  
@(true)  
  
@((1+1).ToString())  
  
@("Hi There".Length)  
  
@(Regex.Match(context.Response.Headers.GetValueOrDefault("Cache-Control",""), @"max-age=(?<maxAge>\d+)").Groups["maxAge"]?.Value)  
  
@(context.Variables.ContainsKey("maxAge")?3600:int.Parse((string)context.Variables["maxAge"]))  
  
@{   
  string value;   
  if (context.Request.Headers.TryGetValue("Authorization", out value))   
  {   
    return Encoding.UTF8.GetString(Convert.FromBase64String(value));  
  }   
  else   
  {   
    return null;  
  }  
}  
```  
  
##  <a name="PolicyExpressionsUsage"></a> Usage  
 Expressions can be used as attribute values or text values in any of the API Management [policies](../APIManagementPolicyRef/API-Management-policies.md), unless the policy reference specifies otherwise.  
  
> [!IMPORTANT]
>  Note that when you use policy expressions, there is only limited verification of the policy expressions when the policy is defined. Since the expressions are executed at run-time in the inbound or outbound pipeline by the gateway, any run-time exceptions generated by the policy expressions will result in a runtime error in the API call.  
  
##  <a name="CLRTypes"></a> .NET Framework types allowed in policy expressions  
 The following table lists the .NET Framework types and their members that are allowed in policy expressions.  
  
|CLR type|Supported methods|  
|--------------|-----------------------|  
|Newtonsoft.Json.Linq.Extensions|All methods are supported|  
|Newtonsoft.Json.Linq.JArray|All methods are supported|  
|Newtonsoft.Json.Linq.JConstructor|All methods are supported|  
|Newtonsoft.Json.Linq.JContainer|All methods are supported|  
|Newtonsoft.Json.Linq.JObject|All methods are supported|  
|Newtonsoft.Json.Linq.JProperty|All methods are supported|  
|Newtonsoft.Json.Linq.JRaw|All methods are supported|  
|Newtonsoft.Json.Linq.JToken|All methods are supported|  
|Newtonsoft.Json.Linq.JTokenType|All methods are supported|  
|Newtonsoft.Json.Linq.JValue|All methods are supported|  
|System.Collections.Generic.IReadOnlyCollection<T\>|All|  
|System.Collections.Generic.IReadOnlyDictionary<TKey,  TValue>|All|  
|System.Collections.Generic.ISet<TKey, TValue>|All|  
|System.Collections.Generic.KeyValuePair<TKey,  TValue>|Key, Value|  
|System.Collections.Generic.List<TKey, TValue>|All|  
|System.Collections.Generic.Queue<TKey, TValue>|All|  
|System.Collections.Generic.Stack<TKey, TValue>|All|  
|System.Convert|All|  
|System.DateTime|All|  
|System.DateTimeKind|Utc|  
|System.DateTimeOffset|All|  
|System.Decimal|All|  
|System.Double|All|  
|System.Guid|All|  
|System.IEnumerable<T\>|All|  
|System.IEnumerator<T\>|All|  
|System.Int16|All|  
|System.Int32|All|  
|System.Int64|All|  
|System.Linq.Enumerable<T\>|All methods are supported|  
|System.Math|All|  
|System.MidpointRounding|All|  
|System.Nullable<T\>|All|  
|System.Random|All|  
|System.SByte|All|  
|System.Security.Cryptography. HMACSHA384|All|  
|System.Security.Cryptography. HMACSHA512|All|  
|System.Security.Cryptography.HashAlgorithm|All|  
|System.Security.Cryptography.HMAC|All|  
|System.Security.Cryptography.HMACMD5|All|  
|System.Security.Cryptography.HMACSHA1|All|  
|System.Security.Cryptography.HMACSHA256|All|  
|System.Security.Cryptography.KeyedHashAlgorithm|All|  
|System.Security.Cryptography.MD5|All|  
|System.Security.Cryptography.RNGCryptoServiceProvider|All|  
|System.Security.Cryptography.SHA1|All|  
|System.Security.Cryptography.SHA1Managed|All|  
|System.Security.Cryptography.SHA256|All|  
|System.Security.Cryptography.SHA256Managed|All|  
|System.Security.Cryptography.SHA384|All|  
|System.Security.Cryptography.SHA384Managed|All|  
|System.Security.Cryptography.SHA512|All|  
|System.Security.Cryptography.SHA512Managed|All|  
|System.Single|All|  
|System.String|All|  
|System.StringSplitOptions|All|  
|System.Text.Encoding|All|  
|System.Text.RegularExpressions.Capture|Index, Length, Value|  
|System.Text.RegularExpressions.CaptureCollection|Count, Item|  
|System.Text.RegularExpressions.Group|Captures, Success|  
|System.Text.RegularExpressions.GroupCollection|Count, Item|  
|System.Text.RegularExpressions.Match|Empty, Groups, Result|  
|System.Text.RegularExpressions.Regex|.ctor, IsMatch, Match, Matches, Replace|  
|System.Text.RegularExpressions.RegexOptions|Compiled, IgnoreCase, IgnorePatternWhitespace, Multiline, None, RightToLeft, Singleline|  
|System.TimeSpan|All|  
|System.Tuple|All|  
|System.UInt16|All|  
|System.UInt32|All|  
|System.UInt64|All|  
|System.Uri|All|  
|System.Xml.Linq.Extensions|All methods are supported|  
|System.Xml.Linq.XAttribute|All methods are supported|  
|System.Xml.Linq.XCData|All methods are supported|  
|System.Xml.Linq.XComment|All methods are supported|  
|System.Xml.Linq.XContainer|All methods are supported|  
|System.Xml.Linq.XDeclaration|All methods are supported|  
|System.Xml.Linq.XDocument|All methods are supported|  
|System.Xml.Linq.XDocumentType|All methods are supported|  
|System.Xml.Linq.XElement|All methods are supported|  
|System.Xml.Linq.XName|All methods are supported|  
|System.Xml.Linq.XNamespace|All methods are supported|  
|System.Xml.Linq.XNode|All methods are supported|  
|System.Xml.Linq.XNodeDocumentOrderComparer|All methods are supported|  
|System.Xml.Linq.XNodeEqualityComparer|All methods are supported|  
|System.Xml.Linq.XObject|All methods are supported|  
|System.Xml.Linq.XProcessingInstruction|All methods are supported|  
|System.Xml.Linq.XText|All methods are supported|  
|System.Xml.XmlNodeType|All|  
  
##  <a name="ContextVariables"></a> Context variable  
 A variable named `context` is implicitly available in every policy [expression](../APIManagementPolicyRef/API-Management-policy-expressions.md#Syntax). Its members provide information pertinent to the `\request`. All of the `context` members are read-only.  
  
|Context Variable|Allowed methods, properties, and parameter values|  
|----------------------|-------------------------------------------------------|  
|context|Api: IApi<br /><br /> Deployment<br /><br /> LastError<br /><br /> Operation<br /><br /> Product<br /><br /> Request<br /><br /> Response<br /><br /> Subscription<br /><br /> Tracing: bool<br /><br /> User<br /><br /> Variables:IReadOnlyDictionary<string, object><br /><br /> void Trace(message: string)|  
|context.Api|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> ServiceUrl: IUrl|  
|context.Deployment|Region: string<br /><br /> ServiceName: string|  
|context.LastError|Source: string<br /><br /> Reason: string<br /><br /> Message: string<br /><br /> Scope: string<br /><br /> Section: string<br /><br /> Path: string<br /><br /> PolicyId: string<br /><br /> For more information about context.LastError, see [Error handling](../APIManagementPolicyRef/Error-handling-in-API-Management-policies.md).|  
|context.Operation|Id: string<br /><br /> Method: string<br /><br /> Name: string<br /><br /> UrlTemplate: string|  
|context.Product|Apis: IEnumerable<IApi\><br /><br /> ApprovalRequired: bool<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: string<br /><br /> Name: string<br /><br /> State: enum ProductState {NotPublished, Published}<br /><br /> SubscriptionLimit: int?<br /><br /> SubscriptionRequired: bool|  
|context.Request|Body: IMessageBody<br /><br /> Certificate: System.Security.Cryptography.X509Certificates.X509Certificate2<br /><br /> Headers: IReadOnlyDictionary<string, string[]><br /><br /> IpAddress: string<br /><br /> MatchedParameters: IReadOnlyDictionary<string, string><br /><br /> Method: string<br /><br /> OriginalUrl:IUrl<br /><br /> Url: IUrl|  
|string context.Request.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Returns comma separated request header values or `defaultValue` if the header is not found.|  
|context.Response|Body: IMessageBody<br /><br /> Headers: IReadOnlyDictionary<string, string[]><br /><br /> StatusCode: int<br /><br /> StatusReason: string|  
|string context.Response.Headers.GetValueOrDefault(headerName: string, defaultValue: string)|headerName: string<br /><br /> defaultValue: string<br /><br /> Returns comma separated response header values or `defaultValue` if the header is not found.|  
|context.Subscription|CreatedTime: DateTime<br /><br /> EndDate: DateTime?<br /><br /> Id: string<br /><br /> Key: string<br /><br /> Name: string<br /><br /> PrimaryKey: string<br /><br /> SecondaryKey: string<br /><br /> StartDate: DateTime?|  
|context.User|Email: string<br /><br /> FirstName: string<br /><br /> Groups: IEnumerable<IGroup\><br /><br /> Id: string<br /><br /> Identities: IEnumerable<IUserIdentity\><br /><br /> LastName: string<br /><br /> Note: string<br /><br /> RegistrationDate: DateTime|  
|IApi|Id: string<br /><br /> Name: string<br /><br /> Path: string<br /><br /> Protocols: IEnumerable<string\><br /><br /> ServiceUrl: IUrl<br /><br /> SubscriptionKeyParameterNames: ISubscriptionKeyParameterNames|  
|IGroup|Id: string<br /><br /> Name: string|  
|IMessageBody|As<T\>(preserveContent: bool = false): Where T: string, JObject, JToken, JArray, XNode, XElement, XDocument<br /><br /> The `context.Request.Body.As<T>` and `context.Request.Body.As<T>` methods are used to read a request or response message body in one of the specified formats. Note that by default when you access the message body, the original message body is lost and must be set by returning the body back in the expression that reads the body. To preserve the body content, set the `preserveContent` parameter to `true` when accessing the message. The default value of `preserveContent` is `false`. If `preserveContent` is set to `true` and a body is returned by the expression, the returned body is used. For more information, see the [Set body](../APIManagementPolicyRef/API-Management-transformation-policies.md#SetBody) policy.|  
|IUrl|Host: string<br /><br /> Path: string<br /><br /> Port: int<br /><br /> Query: IReadOnlyDictionary<string, string[]><br /><br /> QueryString: string<br /><br /> Scheme: string|  
|IUserIdentity|Id: string<br /><br /> Provider: string|  
|ISubscriptionKeyParameterNames|Header: string<br /><br /> Query: string|  
|string IUrl.Query.GetValueOrDefault(queryParameterName: string, defaultValue: string)|queryParameterName: string<br /><br /> defaultValue: string<br /><br /> Returns comma separated query parameter values or `defaultValue` if the parameter is not found.|  
|T context.Variables.GetValueOrDefault<T\>(variableName: string, defaultValue: T)|variableName: string<br /><br /> defaultValue: T<br /><br /> Returns variable value cast to type `T` or `defaultValue` if the variable is not found.<br /><br /> This method throws an exception if the specified type does not match the actual type of the returned variable.|  
|BasicAuthCredentials AsBasic(input: this string)|input: string<br /><br /> If the input parameter contains a valid HTTP Basic Authentication authorization request header value, the method returns an object of type `BasicAuthCredentials`; otherwise the method returns null.|  
|bool TryParseBasic(input: this string, result: out BasicAuthCredentials)|input: string<br /><br /> result: out BasicAuthCredentials<br /><br /> If the input parameter contains a valid HTTP Basic Authentication authorization request header value, the method returns `true` and the result parameter contains a value of type `BasicAuthCredentials`; otherwise the method returns `false`.|  
|BasicAuthCredentials|Password: string<br /><br /> UserId: string|  
|Jwt AsJwt(input: this string)|input: string<br /><br /> If the input parameter contains a valid JWT token value, the method returns an object of type `Jwt`; otherwise the method returns `null`.|  
|bool TryParseJwt(input: this string, result: out Jwt)|input: string<br /><br /> result: out Jwt<br /><br /> If the input parameter contains a valid JWT token value, the method returns `true` and the result parameter contains a value of type `Jwt`; otherwise the method returns `false`.|  
|Jwt|Algorithm: string<br /><br /> Audience: IEnumerable<string\><br /><br /> Claims: IReadOnlyDictionary<string, string[]><br /><br /> ExpirationTime: DateTime?<br /><br /> Id: string<br /><br /> Issuer: string<br /><br /> NotBefore: DateTime?<br /><br /> Subject: string<br /><br /> Type: string|  
|string Jwt.Claims.GetValueOrDefault(claimName: string, defaultValue: string)|claimName: string<br /><br /> defaultValue: string<br /><br /> Returns comma separated claim values or `defaultValue` if the header is not found.|