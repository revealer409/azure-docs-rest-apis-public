---
title: "Get the health of an application"
ms.custom: ""
ms.date: "2016-03-30"
ms.prod: "azure"
ms.reviewer: ""
ms.service: "service-fabric"
ms.suite: ""
ms.tgt_pltfrm: ""
ms.topic: "reference"
applies_to: 
  - "Azure"
  - "Windows 10"
  - "Windows 8"
  - "Windows 8.1"
  - "Windows Server 2012 R2"
dev_langs: 
  - "CSharp"
helpviewer_keywords: 
  - "Service Fabric REST API Reference"
ms.assetid: c35fe5c8-ed7f-41e1-9902-1c6f36a10aac
caps.latest.revision: 17
author: "rwike77"
ms.author: "ryanwi"
manager: "timlt"
translation.priority.mt: 
  - "de-de"
  - "es-es"
  - "fr-fr"
  - "it-it"
  - "ja-jp"
  - "ko-kr"
  - "pt-br"
  - "ru-ru"
  - "zh-cn"
  - "zh-tw"
---
# Get the health of an application
Gets the health of a Service Fabric application.  
  
## Request  
 See [Application](application2.md) for headers and parameters that are used by all requests related to applications.  
  
|Method|Request URI|  
|------------|-----------------|  
|GET|This URI can be used to get the health of an application:<br /><br /> `<URI>/Applications/{application-name}/$/GetHealth?api-version={api-version}`|  
|GET|This URI can be used to get the health of an application with events, services and deployed applications filters:<br /><br /> `<URI>/Applications/{application-name}/$/GetHealth?api-version={api-version}& EventsHealthStateFilter={ EventsHealthStateFilter}& DeployedApplicationsHealthStateFilter={ DeployedApplicationsHealthStateFilter}& ServicesHealthStateFilter={ServicesHealthStateFilter}`|  
  
-   Use {EventsHealthStateFilter} (optional) to filter the collection of HealthEvent objects reported on the application based on health state. The value can be obtained from members or bitwise operations on members of [HealthStateFilter](https://msdn.microsoft.com/en-us/library/azure/system.fabric.health.healthstatefilter.aspx). Only events that match the filter will be returned. All events will be used to evaluate the aggregated health state. If not specified, all entries will be returned. Possible values are:  
  
    -   Default - Default value. Matches any HealthState. The value is zero.  
  
    -   None - Filter that doesn’t match any HealthState value. Used in order to return no results on a given collection of states. The value is 1.  
  
    -   Ok - Filter that matches input with HealthState value Ok. The value is 2.  
  
    -   Warning - Filter that matches input with HealthState value Warning. The value is 4.  
  
    -   Error - Filter that matches input with HealthState value Error. The value is 8.  
  
    -   All - Filter that matches input with any HealthState value. The value is 65535.  
  
-   Use {DeployedApplicationsHealthStateFilter} (optional) to filter the collection of DeployedApplicationHealthState children. The value can be obtained from members or bitwise operations on members of HealthStateFilter. Only deployed applications that match the filter will be returned. All deployed application will be used to evaluate the aggregated health state. If not specified, all entries will be returned. Possible values are:  
  
    -   Default - Default value. Matches any HealthState. The value is zero.  
  
    -   None - Filter that doesn’t match any HealthState value. Used in order to return no results on a given collection of states. The value is 1.  
  
    -   Ok - Filter that matches input with HealthState value Ok. The value is 2.  
  
    -   Warning - Filter that matches input with HealthState value Warning. The value is 4.  
  
    -   Error - Filter that matches input with HealthState value Error. The value is 8.  
  
    -   All - Filter that matches input with any HealthState value. The value is 65535.  
  
-   Use {ServicesHealthStateFilter} (optional) to filter the collection of ServiceHealthState objects children of the application. The value can be obtained from members or bitwise operations on members of HealthStateFilter. Only services that match the filter will be returned. All services will be used to evaluate the aggregated health state. If not specified, all entries will be returned. Possible values are:  
  
    -   Default - Default value. Matches any HealthState. The value is zero.  
  
    -   None - Filter that doesn’t match any HealthState value. Used in order to return no results on a given collection of states. The value is 1.  
  
    -   Ok - Filter that matches input with HealthState value Ok. The value is 2.  
  
    -   Warning - Filter that matches input with HealthState value Warning. The value is 4.  
  
    -   Error - Filter that matches input with HealthState value Error. The value is 8.  
  
    -   All - Filter that matches input with any HealthState value. The value is 65535.  
  
## Response  
 Status code: 200 OK  
  
```  
{  
    "HealthEvents": [  
        {  
            "SourceId": "System.CM",  
            "Property": "State",  
            "HealthState": 1,  
            "TimeToLiveInMilliSeconds": "922337203685477",  
            "Description": "Application has been created.",  
            "SequenceNumber": "114",  
            "RemoveWhenExpired": false,  
            "SourceUtcTimestamp": "130742924302425954",  
            "LastModifiedUtcTimestamp": "130742924302425954",  
            "IsExpired": false,  
            "LastOkTransitionAt": "130742924302425954",  
            "LastWarningTransitionAt": "0",  
            "LastErrorTransitionAt": "0"  
        }  
    ],  
    "AggregatedHealthState": 1,  
    "UnhealthyEvaluations": [],  
    "Name": "fabric:\\/WordCount",  
    "ServiceHealthStates": [  
        {  
            "ServiceName": "fabric:\\/WordCount\\/WordCount.WebService",  
            "AggregatedHealthState": 1  
        },  
        {  
            "ServiceName": "fabric:\\/WordCount\\/WordCount.Service",  
            "AggregatedHealthState": 1  
        }  
    ],  
    "DeployedApplicationHealthStates": [  
        {  
            "ApplicationName": "fabric:\\/WordCount",  
            "NodeName": "Node.4",  
            "AggregatedHealthState": 1  
        },  
        {  
            "ApplicationName": "fabric:\\/WordCount",  
            "NodeName": "Node.2",  
            "AggregatedHealthState": 1  
        },  
        {  
            "ApplicationName": "fabric:\\/WordCount",  
            "NodeName": "Node.3",  
            "AggregatedHealthState": 1  
        },  
        {  
            "ApplicationName": "fabric:\\/WordCount",  
            "NodeName": "Node.5",  
            "AggregatedHealthState": 1  
        }  
    ]  
}  
  
```  
  
|Element name|Description|  
|------------------|-----------------|  
|HealthEvents|The health events as an array of JSON objects.|  
|SourceId|The source of the health event as a string.|  
|Property|The property as a string.|  
|HealthState|The health state.<br /><br /> Possible values are:<br /><br /> -   Invalid - Indicates an invalid health state. All Service Fabric enumerations have the invalid type. The value is zero.<br />-   Ok - Indicates the health state is okay. The value is 1.<br />-   Warning - Indicates the health status is at a warning level. The value is 2.<br />-   Indicated the health status is at an error level. The value is 3.<br />-   Unknown - Indicates an unknown health status. The value is 65535.|  
|TimeToLiveInMilliSeconds|The time for this health report to be valid in milliseconds. If not specified, time to live defaults to infinite value. To explicitly set Infinite time to live, use value “922337203685477”.|  
|Description|The health state description. The maximum string length for the description is 4096 characters.|  
|SequenceNumber|The sequence number for this health report as a numeric string. If not specified, a sequence number is auto-generated by the health client.|  
|RemoveWhenExpired|`true` to remove the health report after the time to live expires; `false` to keep the health event after the time to live expires. If not specified, it defaults to false.|  
|AggregatedHealthState|The aggregated health state.<br /><br /> Possible values are:<br /><br /> -   Invalid - Indicates an invalid health state. All Service Fabric enumerations have the invalid type. The value is zero.<br />-   Ok - Indicates the health state is okay. The value is 1.<br />-   Warning - Indicates the health status is at a warning level. The value is 2.<br />-   Indicated the health status is at an error level. The value is 3.<br />-   Unknown - Indicates an unknown health status. The value is 65535.|  
|UnhealthyEvaluations|The unhealthy evaluations that show why the current aggregated health state was returned by Health Manager.|  
|Description|The description of the health evaluation, which represents a summary of the evaluation process.|  
|Kind|The health evaluation kind.<br /><br /> Possible value are:<br /><br /> -   Invalid - Indicates that the health evaluation is invalid. The value is zero.<br />-   Event - Indicates that the health evaluation is for a health event. The value is 1.<br />-   Replicas - Indicates that the health evaluation is for the replicas of a partition. The value is 2.<br />-   Partitions - Indicates that the health evaluation is for the partitions of a service. The value is 3.<br />-   DeployedServicePackages - Indicates that the health evaluation is for the deployed service packages of a deployed application. The value is 4.<br />-   DeployedApplications - Indicates that the health evaluation is for the deployed applications of an application. The value is 5.<br />-   Services - Indicates that the health evaluation is for services of an application. The value is 6.<br />-   Nodes - Indicates that the health evaluation is for the cluster nodes. The value is 7.<br />-   Applications - Indicates that the health evaluation is for the cluster applications. The value is 8.<br />-   SystemApplication - Indicates that the health evaluation is for the system application. The value is 9.<br />-   UpgradeDomainDeployedApplications - Indicates that the health evaluation is for the deployed applications of an application in an upgrade domain. The value is 10.<br />-   UpgradeDomainNodes - Indicates that the health evaluation is for the cluster nodes in an upgrade domain. The value is 11.<br />-   Node - Indicates that the health evaluation is for a node. The value is 12.<br />-   Replica - Indicates that the health evaluation is for a replica. The value is 13.<br />-   Partition - Indicates that the health evaluation is for a partition. The value is 14.<br />-   Service - Indicates that the health evaluation is for a service. The value is 15.<br />-   DeployedServicePackage - Indicates that the health evaluation is for a deployed service package. The value is 16.<br />-   DeployedApplication - Indicates that the health evaluation is for a deployed application. The value is 17.<br />-   Application - Indicates that the health evaluation is for an application. The value is 18.<br />-   DeltaNodesCheck - Indicates that the health evaluation is for the delta of unhealthy cluster nodes. The value is 19.<br />-   UpgradeDomainDeltaNodesCheck - Indicates that the health evaluation is for the delta of unhealthy upgrade domain cluster nodes. The value is 20.<br />-   ApplicationTypeApplications – Indicates that the health evaluation is for applications of an application type. The value is 21.|  
|AggregatedHealthState|The aggregated health state of this unhealthy evaluation.<br /><br /> Possible values are:<br /><br /> -   Invalid - Indicates an invalid health state. All Service Fabric enumerations have the invalid type. The value is zero.<br />-   Ok - Indicates the health state is okay. The value is 1.<br />-   Warning - Indicates the health status is at a warning level. The value is 2.<br />-   Indicated the health status is at an error level. The value is 3.<br />-   Unknown - Indicates an unknown health status. The value is 65535.|  
|Name|The application name.|  
|ServiceHealthStates|The default parameter list as an array of key/value pair strings.|  
|ServiceName|The service name.|  
|AggregatedHealthState|The aggregated health state.<br /><br /> Possible values are:<br /><br /> -   Invalid - Indicates an invalid health state. All Service Fabric enumerations have the invalid type. The value is zero.<br />-   Ok - Indicates the health state is okay. The value is 1.<br />-   Warning - Indicates the health status is at a warning level. The value is 2.<br />-   Indicated the health status is at an error level. The value is 3.<br />-   Unknown - Indicates an unknown health status. The value is 65535.|  
|DeployedApplicationHealthStates|The deployed application health states.|  
|ApplicationName|The application name.|  
|NodeName|The node name.|  
|AggregatedHealthState|The aggregated health state of the deployed application.<br /><br /> Possible values are:<br /><br /> -   Invalid - Indicates an invalid health state. All Service Fabric enumerations have the invalid type. The value is zero.<br />-   Ok - Indicates the health state is okay. The value is 1.<br />-   Warning - Indicates the health status is at a warning level. The value is 2.<br />-   Indicated the health status is at an error level. The value is 3.<br />-   Unknown - Indicates an unknown health status. The value is 65535.|  
  
## Examples  
 The following example displays the health of an application.  
  
```  
/// <summary>  
/// Class similar to ApplicationHealth. Designed for use with JavaScriptSerializer.  
/// </summary>  
public class ApplicationHealth2  
{  
    public string Name { get; set; }  
    public HealthState AggregatedHealthState { get; set; }  
    public List<HealthEvent2> HealthEvents { get; set; }  
    public List<HealthEvaluation> UnhealthyEvaluations { get; set; }  
    public List<DeployedApplicationHealthState2> DeployedApplicationHealthStates { get; set; }  
    public List<ServiceHealthState2> ServiceHealthStates { get; set; }  
}  
  
/// <summary>  
/// Class similar to HealthEvent. Designed for use with JavaScriptSerializer.  
/// </summary>  
public class HealthEvent2  
{  
    public string SourceId { get; set; }  
    public string Property { get; set; }  
    public HealthState HealthState { get; set; }  
    public string TimeToLiveInMilliSeconds { get; set; }  
    public string Description { get; set; }  
    public string SequenceNumber { get; set; }  
    public bool RemoveWhenExpired { get; set; }  
    public string SourceUtcTimestamp { get; set; }  
    public string LastModifiedUtcTimestamp { get; set; }  
    public bool IsExpired { get; set; }  
    public string LastOkTransitionAt { get; set; }  
    public string LastWarningTransitionAt { get; set; }  
    public string LastErrorTransitionAt { get; set; }  
}  
  
/// <summary>  
/// Class similar to DeployedApplicationHealthState. Designed for use with JavaScriptSerializer.  
/// </summary>  
public class DeployedApplicationHealthState2  
{  
    public string ApplicationName { get; set; }  
    public string NodeName { get; set; }  
    public HealthState AggregatedHealthState { get; set; }  
}  
  
/// <summary>  
/// Class similar to ServiceHealthState. Designed for use with JavaScriptSerializer.  
/// </summary>  
public class ServiceHealthState2  
{  
    public string ServiceName { get; set; }  
    public HealthState AggregatedHealthState { get; set; }  
}  
  
/// <summary>  
/// Gets the health of an application.  
/// </summary>  
/// <param name="clusterUri">The URI to access the cluster.</param>  
/// <returns>Returns true if successful; otherwise false.</returns>  
public static bool GetApplicationHealth(Uri clusterUri)  
{  
    // String to capture the response stream.  
    string responseString = string.Empty;  
  
    // Create the request and add URL parameters.  
    Uri requestUri = new Uri(clusterUri, string.Format("/Applications/{0}/$/GetHealth?api-version={1}",  
        "WordCount",     // Application Name  
        "1.0"));            // api-version  
  
    HttpWebRequest request = (HttpWebRequest)WebRequest.Create(requestUri);  
    request.Method = "GET";  
  
    // Execute the request and obtain the response.  
    try  
    {  
        using (HttpWebResponse response = (HttpWebResponse)request.GetResponse())  
        {  
            using (StreamReader streamReader = new StreamReader(response.GetResponseStream(), true))  
            {  
                // Capture the response string.  
                responseString = streamReader.ReadToEnd();  
            }  
        }  
    }  
    catch (WebException e)  
    {  
        // If there is a web exception, display the error message.  
        Console.WriteLine("Error getting the application health:");  
        Console.WriteLine(e.Message);  
        if (e.InnerException != null)  
            Console.WriteLine(e.InnerException.Message);  
        return false;  
    }  
    catch (Exception e)  
    {  
        // If there is another kind of exception, throw it.  
        throw (e);  
    }  
  
    // Deserialize the response string.  
    JavaScriptSerializer jss = new JavaScriptSerializer();  
    ApplicationHealth2 applicationHealth = jss.Deserialize<ApplicationHealth2>(responseString);  
  
    Console.WriteLine("Application Health:");  
    Console.WriteLine("  Name: " + applicationHealth.Name);  
    Console.WriteLine("  AggregatedHealthState: " + applicationHealth.AggregatedHealthState);  
  
    Console.WriteLine("  HealthEvents:");  
    foreach (HealthEvent2 healthEvent in applicationHealth.HealthEvents)  
    {  
        Console.WriteLine("    HealthEvent:");  
        Console.WriteLine("      Description: " + healthEvent.Description);  
        Console.WriteLine("      HealthState: " + healthEvent.HealthState);  
        Console.WriteLine("      SourceId: " + healthEvent.SourceId);  
        Console.WriteLine("      Property: " + healthEvent.Property);  
        Console.WriteLine("      IsExpired: " + healthEvent.IsExpired);  
        Console.WriteLine("      RemoveWhenExpired: " + healthEvent.RemoveWhenExpired);  
        Console.WriteLine("      SequenceNumber: " + healthEvent.SequenceNumber);  
        Console.WriteLine("      SourceUtcTimestamp: " + healthEvent.SourceUtcTimestamp);  
        Console.WriteLine("      TimeToLiveInMilliSeconds: " + healthEvent.TimeToLiveInMilliSeconds);  
        Console.WriteLine("      LastModifiedUtcTimestamp: " + healthEvent.LastModifiedUtcTimestamp);  
        Console.WriteLine("      LastOkTransitionAt: " + healthEvent.LastOkTransitionAt);  
        Console.WriteLine("      LastWarningTransitionAt: " + healthEvent.LastWarningTransitionAt);  
        Console.WriteLine("      LastErrorTransitionAt: " + healthEvent.LastErrorTransitionAt);  
    }  
  
    Console.WriteLine("  UnhealthyEvaluations:");  
    foreach (HealthEvaluation healthEvaluation in applicationHealth.UnhealthyEvaluations)  
    {  
        Console.WriteLine("    UnhealthyEvaluation:");  
        Console.WriteLine("      Description: " + healthEvaluation.Description);  
        Console.WriteLine("      Kind: " + healthEvaluation.Kind);  
        Console.WriteLine("      AggregatedHealthState: " + healthEvaluation.AggregatedHealthState);  
    }  
  
    Console.WriteLine("  ApplicationHealthStates:");  
    foreach (DeployedApplicationHealthState2 applicationHealthState in applicationHealth.DeployedApplicationHealthStates)  
    {  
        Console.WriteLine("    ApplicationHealthState:");  
        Console.WriteLine("      ApplicationName: " + applicationHealthState.ApplicationName);  
        Console.WriteLine("      NodeName: " + applicationHealthState.NodeName);  
        Console.WriteLine("      AggregatedHealthState: " + applicationHealthState.AggregatedHealthState);  
    }  
  
    Console.WriteLine("  ServiceHealthStates:");  
    foreach (ServiceHealthState2 serviceHealthState in applicationHealth.ServiceHealthStates)  
    {  
        Console.WriteLine("    ServiceHealthState:");  
        Console.WriteLine("      ServiceName: " + serviceHealthState.ServiceName);  
        Console.WriteLine("      AggregatedHealthState: " + serviceHealthState.AggregatedHealthState);  
    }  
  
    return true;  
}  
  
/* This code produces output similar to the following:  
  
Application Health:  
  Name: fabric:/WordCount  
  AggregatedHealthState: Ok  
  HealthEvents:  
    HealthEvent:  
      Description: Application has been created.  
      HealthState: Ok  
      SourceId: System.CM  
      Property: State  
      IsExpired: False  
      RemoveWhenExpired: False  
      SequenceNumber: 114  
      SourceUtcTimestamp: 130742924302425954  
      TimeToLiveInMilliSeconds: 922337203685477  
      LastModifiedUtcTimestamp: 130742924302425954  
      LastOkTransitionAt: 130742924302425954  
      LastWarningTransitionAt: 0  
      LastErrorTransitionAt: 0  
  UnhealthyEvaluations:  
  ApplicationHealthStates:  
    ApplicationHealthState:  
      ApplicationName: fabric:/WordCount  
      NodeName: Node.4  
      AggregatedHealthState: Ok  
    ApplicationHealthState:  
      ApplicationName: fabric:/WordCount  
      NodeName: Node.2  
      AggregatedHealthState: Ok  
    ApplicationHealthState:  
      ApplicationName: fabric:/WordCount  
      NodeName: Node.3  
      AggregatedHealthState: Ok  
    ApplicationHealthState:  
      ApplicationName: fabric:/WordCount  
      NodeName: Node.5  
      AggregatedHealthState: Ok  
  ServiceHealthStates:  
    ServiceHealthState:  
      ServiceName: fabric:/WordCount/WordCount.WebService  
      AggregatedHealthState: Ok  
    ServiceHealthState:  
      ServiceName: fabric:/WordCount/WordCount.Service  
      AggregatedHealthState: Ok  
*/  
  
```