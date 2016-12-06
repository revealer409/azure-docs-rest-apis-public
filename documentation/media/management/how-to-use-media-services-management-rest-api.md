---
title: "How to: Use Media Services Management REST API"
ms.custom: ""
ms.date: "2016-03-08"
ms.reviewer: ""
ms.service: "media-services"
ms.suite: ""
ms.tgt_pltfrm: ""
ms.topic: "article"
ms.assetid: 1b2943bc-a9cb-4373-abbc-ff83ff60c69c
caps.latest.revision: 14
author: "Juliako"
ms.author: "juliako"
manager: "erikre"
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
# How to: Use Media Services Management REST API

> [!NOTE]
>  It is now recommended to use  the Azure Resource Manager (ARM) REST API endpoints, as described in [Media Services management REST API reference](../../../api-ref/media/MediaService.json).
  
  
 The code in this topic defines the `ManagementRESTAPIHelper` class that demonstrates how to use Media Services Management REST APIs. The constructor of the class takes three arguments: the endpoint of your service, the associated management certificate thumbprint, and the subscription id. For more information about management certificate thumbprint, see [Authenticating Service Management Requests](http://msdn.microsoft.com/en-us/1becb7dc-1cdc-4db4-8ae8-7e351c96c251).  
  
 The following example instantiates the `ManagementRESTAPIHelper` class and calls one of its members.  
  
```  
ManagementRESTAPIHelper helper =  
          new ManagementRESTAPIHelper("https://management.core.windows.net",   
                         "certificate-thumbprint", "subscription-id");  
  
// Initialize the AccountInfo class.  
MediaServicesAccount accountInfo = new MediaServicesAccount();  
accountInfo.AccountName = "media-services-account-name";  
accountInfo.Region = "region";  
accountInfo.StorageAccountName = "storage-account-name";  
accountInfo.StorageAccountKey = "storage-account-key";  
accountInfo.BlobStorageEndpointUri = "blob-storage-endpoint";  
  
AttachStorageAccountRequest storageAccountToAttach = new AttachStorageAccountRequest();  
storageAccountToAttach.StorageAccountName = "storage-account-name";  
storageAccountToAttach.StorageAccountKey = "storage-account-key";  
storageAccountToAttach.BlobStorageEndpointUri = "blob-storage-endpoint";  
  
// Call CreateMediaServiceAccountUsingXmlContentType to create a new \  
// Media Services account.   
helper.CreateMediaServiceAccountUsingXmlContentType(accountInfo);  
  
// Call AttachStorageAccountToMediaServiceAccount to   
// attach an existing storage account to the Media Services account.  
helper.AttachStorageAccountToMediaServiceAccount(accountInfo,   
                                    storageAccountToAttach);  
  
// Call the following methods to get details about   
// Media Services account.  
helper.GetAccountDetails(accountInfo);  
helper.ListAvailableRegions(accountInfo);  
helper.ListSubscriptionAccounts(accountInfo);  
helper.ListSubscriptionAccounts(accountInfo);  
```  
  
## To define the ManagementRESTAPIHelper class  
 Add the following using statements:  
  
```  
using System;  
using System.Collections.Generic;  
using System.IO;  
using System.Linq;  
using System.Net;  
using System.Security.Cryptography.X509Certificates;  
using System.Threading;  
using System.Xml;  
using System.Xml.Linq;  
  
```  
  
 Define the following class:  
  
```  
class ManagementRESTAPIHelper  
{  
    public string Endpoint { get; set; }  
    public string CertThumbprint { get; set; }  
    public string SubscriptionId { get; set; }  
  
    public ManagementRESTAPIHelper(string endpoint, string thumbprint, string subscriptionID)  
    {  
        Endpoint = endpoint;  
        CertThumbprint = thumbprint;  
        SubscriptionId = subscriptionID;  
    }  
  
    private X509Certificate2 GetClientCertificate()  
    {  
        List<StoreLocation> locations = new List<StoreLocation>   
        {   
            StoreLocation.CurrentUser,   
            StoreLocation.LocalMachine   
        };  
  
        foreach (var location in locations)  
        {  
            X509Store store = new X509Store("My", location);  
            try  
            {  
                store.Open(OpenFlags.ReadOnly | OpenFlags.OpenExistingOnly);  
                X509Certificate2Collection certificates = store.Certificates.Find(  
                    X509FindType.FindByThumbprint, CertThumbprint, false);  
                if (certificates.Count == 1)  
                {  
                    return certificates[0];  
                }  
            }  
            finally  
            {  
                store.Close();  
            }  
        }  
  
        throw new ArgumentException(string.Format(  
            "A Certificate with thumbprint '{0}' could not be located.",  
            CertThumbprint));  
    }  
  
    public string CreateMediaServiceAccountUsingXmlContentType(MediaServicesAccount accountInfo)  
    {  
        var clientCert = GetClientCertificate();  
  
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts",  
            Endpoint, SubscriptionId));  
  
        // Create the request XML document  
        XNamespace ns = "http://schemas.datacontract.org/2004/07/Microsoft.Cloud.Media.Management.ResourceProvider.Models";  
        XDocument requestBody = new XDocument(  
            new XElement(  
                ns + "AccountCreationRequest",  
                new XElement(ns + "AccountName", accountInfo.AccountName),  
                new XElement(ns + "BlobStorageEndpointUri", accountInfo.BlobStorageEndpointUri),  
                new XElement(ns + "Region", accountInfo.Region),  
                new XElement(ns + "StorageAccountKey", accountInfo.StorageAccountKey),  
                new XElement(ns + "StorageAccountName", accountInfo.StorageAccountName)  
                ));  
  
        XDocument responseBody;  
        responseBody = null;  
        string requestId = String.Empty;  
  
        request.Method = "POST";  
        request.Headers.Add("x-ms-version", "2011-10-01");  
        request.ClientCertificates.Add(clientCert);  
        request.ContentType = "application/xml";  
        request.Accept = "application/atom+xml";  
  
        if (requestBody != null)  
        {  
            using (Stream requestStream = request.GetRequestStream())  
            {  
                using (StreamWriter streamWriter = new StreamWriter(  
                    requestStream, System.Text.UTF8Encoding.UTF8))  
                {  
                    requestBody.Save(streamWriter, SaveOptions.DisableFormatting);  
                }  
            }  
        }  
  
        HttpWebResponse response;  
        HttpStatusCode statusCode = HttpStatusCode.Unused;  
        try  
        {  
            response = (HttpWebResponse)request.GetResponse();  
        }  
        catch (WebException ex)  
        {  
            // GetResponse throws a WebException for 4XX and 5XX status codes  
            response = (HttpWebResponse)ex.Response;  
        }  
  
        try  
        {  
            statusCode = response.StatusCode;  
            if (response.ContentLength > 0)  
            {  
                if (response.ContentType.Contains("application/xml") || response.ContentType.Contains("application/atom+xml"))  
                {  
                    using (XmlReader reader = XmlReader.Create(response.GetResponseStream()))  
                    {  
                        responseBody = XDocument.Load(reader);  
                        Console.WriteLine(responseBody.ToString());  
  
                    }  
                }  
                else  
                {  
                    using (StreamReader reader = new StreamReader(response.GetResponseStream()))  
                    {  
                        Console.WriteLine(reader.ReadToEnd());  
                    }  
                }  
  
            }  
  
            if (response.Headers != null)  
            {  
                requestId = response.Headers["x-ms-request-id"];  
            }  
        }  
        finally  
        {  
            response.Close();  
        }  
  
        if (!statusCode.Equals(HttpStatusCode.Created))  
        {  
            throw new ApplicationException(string.Format(  
                "Call to CreateAccount returned an error. Status Code: {0}",  
                statusCode  
                ));  
        }  
  
        return requestId;  
    }  
  
    public void AttachStorageAccountToMediaServiceAccount(MediaServicesAccount accountInfo, AttachStorageAccountRequest storageaccount)  
    {  
        var clientCert = GetClientCertificate();  
  
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts",  
            Endpoint, SubscriptionId, accountInfo.AccountName));  
        request.Method = "POST";  
        request.ContentType = "application/json; charset=utf-8";  
        request.Headers.Add("x-ms-version", "2011-10-01");  
        request.Headers.Add("Accept-Encoding: gzip, deflate");  
        request.ClientCertificates.Add(clientCert);  
  
        string jsonString;  
  
        using (MemoryStream ms = new MemoryStream())  
        {  
            DataContractJsonSerializer serializer  
                    = new DataContractJsonSerializer(typeof(AttachStorageAccountRequest));  
  
            serializer.WriteObject(ms, storageaccount);  
  
            jsonString = Encoding.Default.GetString(ms.ToArray());  
  
        }  
  
        using (Stream requestStream = request.GetRequestStream())  
        {  
            var requestBytes = System.Text.Encoding.ASCII.GetBytes(jsonString);  
            requestStream.Write(requestBytes, 0, requestBytes.Length);  
            requestStream.Close();  
        }  
  
        using (var response = (HttpWebResponse)request.GetResponse())  
        {  
            if (response.StatusCode == HttpStatusCode.NoContent)  
                Console.WriteLine("The primary key was regenerated.");  
        }  
    }  
  
    public void DeleteAccount(MediaServicesAccount accountInfo)  
    {  
        var clientCert = GetClientCertificate();  
  
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}",  
            Endpoint, SubscriptionId, accountInfo.AccountName));  
        request.Method = "DELETE";  
        request.Headers.Add("x-ms-version", "2011-10-01");  
        request.Accept = "application/json";  
        request.ContentType = "application/json";  
        request.ClientCertificates.Add(clientCert);  
  
        Console.WriteLine(string.Format("Try to delete account \"{0}\".", accountInfo.AccountName));  
        using (var response = (HttpWebResponse)request.GetResponse())  
        {  
            // "using" used just to dispose the response received.  
        }  
  
        try  
        {  
            AccountDetails detail = GetAccountDetails(accountInfo);  
        }  
        catch (Exception ex)  
        {  
            if (ex.Message.Contains("(404) Not Found"))  
            {  
                Console.WriteLine("Account[{0}] has been deleted successfully", accountInfo.AccountName);  
            }  
            else  
            {  
                Console.WriteLine("Deleted Account[{0}] Failed", accountInfo.AccountName);  
            }  
        }  
    }  
  
    public List<SupportedRegion> ListAvailableRegions(MediaServicesAccount accountInfo)  
    {  
        var clientCert = GetClientCertificate();  
  
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/SupportedRegions",  
            Endpoint, SubscriptionId));  
  
        request.Method = "GET";  
        request.Headers.Add("x-ms-version", "2011-10-01");  
        request.Accept = "application/json";  
        request.ContentType = "application/json";  
        request.ClientCertificates.Add(clientCert);  
  
        List<SupportedRegion> regions;  
  
        using (var response = (HttpWebResponse)request.GetResponse())  
        {  
            var stream1 = response.GetResponseStream();  
            DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(List<SupportedRegion>));  
            regions = (List<SupportedRegion>)ser.ReadObject(stream1);  
  
            foreach (var r in regions)  
            {  
                Console.WriteLine(r.RegionName);  
            }  
        }  
  
        return regions;  
    }  
  
    public AccountDetails GetAccountDetails(MediaServicesAccount accountInfo)  
    {  
        var clientCert = GetClientCertificate();  
  
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}",  
            Endpoint, SubscriptionId, accountInfo.AccountName));  
        request.Method = "GET";  
        request.Headers.Add("x-ms-version", "2011-10-01");  
        request.Accept = "application/json";  
        request.ContentType = "application/json";  
        request.ClientCertificates.Add(clientCert);  
  
        AccountDetails accountDetails = null;  
  
        Console.WriteLine(string.Format("Try to get the details of accountName[{0}].", accountInfo.AccountName));  
        using (var response = (HttpWebResponse)request.GetResponse())  
        {  
            var stream1 = response.GetResponseStream();  
            DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(AccountDetails));  
            accountDetails = (AccountDetails)ser.ReadObject(stream1);  
            Console.WriteLine("Deserialized back:");  
            Console.WriteLine(accountDetails.AccountName);  
            Console.WriteLine(accountDetails.StorageAccountName);  
        }  
  
        Console.WriteLine("Got account detail successfully");  
        return accountDetails;  
    }  
  
    public void SynchronizeStorageAccountKey(MediaServicesAccount accountInfo)  
    {  
        var clientCert = GetClientCertificate();  
  
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts/{3}/Key",  
            Endpoint, SubscriptionId, accountInfo.AccountName, accountInfo.StorageAccountName));  
        request.Method = "PUT";  
        request.ContentType = "application/json; charset=utf-8";  
        request.Headers.Add("x-ms-version", "2011-10-01");  
        request.Headers.Add("Accept-Encoding: gzip, deflate");  
        request.ClientCertificates.Add(clientCert);  
  
        using (var streamWriter = new StreamWriter(request.GetRequestStream()))  
        {  
            streamWriter.Write("\"");  
            streamWriter.Write(accountInfo.StorageAccountKey);  
            streamWriter.Write("\"");  
            streamWriter.Flush();  
        }  
  
        AccountDetails details = GetAccountDetails(accountInfo);  
  
        using (var response = (HttpWebResponse)request.GetResponse())  
        {  
            string jsonResponse;  
            Stream receiveStream = response.GetResponseStream();  
            Encoding encode = Encoding.GetEncoding("utf-8");  
            if (receiveStream != null)  
            {  
                var readStream = new StreamReader(receiveStream, encode);  
                jsonResponse = readStream.ReadToEnd();  
            }  
        }  
    }  
  
    public List<StorageAccountDetails> ListStorageAccountDetails(MediaServicesAccount accountInfo)  
    {  
        var clientCert = GetClientCertificate();  
  
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/StorageAccounts",  
            Endpoint, SubscriptionId, accountInfo.AccountName));  
        request.Method = "GET";  
        request.Headers.Add("x-ms-version", "2011-10-01");  
        request.Accept = "application/json";  
        request.ContentType = "application/json";  
        request.ClientCertificates.Add(clientCert);  
  
        List<StorageAccountDetails> storageAccounts;  
  
        using (var response = (HttpWebResponse)request.GetResponse())  
        {  
            var stream1 = response.GetResponseStream();  
            DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(List<StorageAccountDetails>));  
            storageAccounts = (List<StorageAccountDetails>)ser.ReadObject(stream1);  
  
            foreach (var r in storageAccounts)  
            {  
                Console.WriteLine("Account name: {0}", r.StorageAccountName);  
                Console.WriteLine("IsDefault: {0}", r.IsDefault);  
            }  
        }  
  
        return storageAccounts;  
    }  
  
    public List<AzureMediaServicesResource> ListSubscriptionAccounts(MediaServicesAccount accountInfo)  
    {  
        var clientCert = GetClientCertificate();  
  
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts",  
            Endpoint, SubscriptionId));  
        request.Method = "GET";  
        request.Headers.Add("x-ms-version", "2011-10-01");  
        request.Accept = "application/json";  
        request.ContentType = "application/json";  
        request.ClientCertificates.Add(clientCert);  
  
        List<AzureMediaServicesResource> accounts;  
  
        using (var response = (HttpWebResponse)request.GetResponse())  
        {  
            var stream1 = response.GetResponseStream();  
            DataContractJsonSerializer ser = new DataContractJsonSerializer(typeof(List<AzureMediaServicesResource>));  
            accounts = (List<AzureMediaServicesResource>)ser.ReadObject(stream1);  
  
            foreach (var r in accounts)  
            {  
                Console.WriteLine("Name: {0}", r.Name);  
            }  
        }  
  
        return accounts;  
    }  
  
    public void RegeneratePrimaryAccountKey(MediaServicesAccount accountInfo)  
    {  
        var clientCert = GetClientCertificate();  
  
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/AccountKeys/Primary/Regenerate",  
                Endpoint, SubscriptionId, accountInfo.AccountName));  
        request.Method = "Post";  
        request.ContentType = "application/json; charset=utf-8";  
        request.Headers.Add("x-ms-version", "2011-10-01");  
        request.Headers.Add("Accept-Encoding: gzip, deflate");  
        request.Accept = "application/json";  
        request.ClientCertificates.Add(clientCert);  
  
        using (var streamWriter = new StreamWriter(request.GetRequestStream()))  
        {  
            streamWriter.Write("\"");  
            streamWriter.Write(accountInfo.AccountName);  
            streamWriter.Write("\"");  
            streamWriter.Flush();  
        }  
  
        using (var response = (HttpWebResponse)request.GetResponse())  
        {  
            if (response.StatusCode == HttpStatusCode.NoContent)  
                Console.WriteLine("The primary key was regenerated.");  
        }  
    }  
  
    public void RegenerateSecondaryAccountKey(MediaServicesAccount accountInfo)  
    {  
        var clientCert = GetClientCertificate();  
  
        HttpWebRequest request = (HttpWebRequest)WebRequest.Create(string.Format("{0}/{1}/services/mediaservices/Accounts/{2}/AccountKeys/Secondary/Regenerate",  
            Endpoint, SubscriptionId, accountInfo.AccountName));  
        request.Method = "Post";  
        request.ContentType = "application/json; charset=utf-8";  
        request.Headers.Add("x-ms-version", "2011-10-01");  
        request.Headers.Add("Accept-Encoding: gzip, deflate");  
        request.Accept = "application/json";  
        request.ClientCertificates.Add(clientCert);  
  
        using (var streamWriter = new StreamWriter(request.GetRequestStream()))  
        {  
            streamWriter.Write("\"");  
            streamWriter.Write(accountInfo.AccountName);  
            streamWriter.Write("\"");  
            streamWriter.Flush();  
        }  
  
        using (var response = (HttpWebResponse)request.GetResponse())  
        {  
            if (response.StatusCode == HttpStatusCode.NoContent)  
                Console.WriteLine("The secondary key was regenerated.");  
        }  
    }  
}  
#region SerializationClasses  
  
public class AccountCreationResult  
{  
    public Guid AccountId { get; set; }  
    public string AccountName { get; set; }  
    public HttpStatusCode StatusCode { get; set; }  
    public string Subscription { get; set; }  
}  
  
public class AttachStorageAccountRequest  
{  
    public string StorageAccountName { get; set; }  
    public string StorageAccountKey { get; set; }  
    public string BlobStorageEndpointUri { get; set; }  
}  
  
public class AzureMediaServicesResource  
{  
    public string Name { get; set; }  
    public string Type { get; set; }  
    public string State { get; set; }  
    public Uri SelfLink { get; set; }  
    public Uri ParentLink { get; set; }  
    public Guid AccountId { get; set; }  
}  
  
public class SupportedRegion  
{  
    public string RegionName { get; set; }  
}  
  
public class StorageAccountDetails  
{  
    public string StorageAccountName { get; set; }  
    public string BlobStorageEndPoint { get; set; }  
    public bool IsDefault { get; set; }  
}  
  
public class AccountDetails  
{  
    public string AccountName { get; set; }  
    public string AccountKey { get; set; }  
    public AccountKeys AccountKeys { get; set; }  
    public string StorageAccountName { get; set; }  
    public string AccountRegion { get; set; }  
}  
  
public class AccountKeys  
{  
    public string Primary { get; set; }  
    public string Secondary { get; set; }  
}  
  
public class MediaServicesAccount  
{  
    public string AccountName { get; set; }  
    public string Region { get; set; }  
    public string StorageAccountName { get; set; }  
    public string StorageAccountKey { get; set; }  
    public string BlobStorageEndpointUri { get; set; }  
}  
  
#endregion  
  
```