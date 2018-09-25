---
title: Kimenő hitelesítés – az Azure Scheduler szolgáltatásával
description: Ismerje meg, hogyan állíthatja be, vagy távolítsa el az Azure Scheduler Kimenő hitelesítés
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam
ms.assetid: 6707f82b-7e32-401b-a960-02aae7bb59cc
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: 88f2fe0781bad4b652826b6a8d1961dd39b063e1
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46993333"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Kimenő hitelesítés az Azure Scheduler

> [!IMPORTANT]
> [Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) kivezetjük, az Azure Scheduler lecseréli. Feladatok ütemezése [helyette próbálkozzon az Azure Logic Apps](../scheduler/migrate-from-scheduler-to-logic-apps.md). 

Az Azure Scheduler-feladatok, például a más Azure-szolgáltatások, a Salesforce.com, a Facebook és a biztonságos egyéni webhelyek hitelesítést igénylő szolgáltatásokat lehet. A hívott szolgáltatás megállapíthatja, hogy a Scheduler-feladat hozzáférhet-e a kért erőforrások. 

A Scheduler támogatja a hitelesítés modellek: 

* *Ügyféltanúsítvány* hitelesítési ügyfél SSL/TLS-tanúsítványok használata esetén
* *Alapszintű* hitelesítés
* *Az Active Directory OAuth* hitelesítés

## <a name="add-or-remove-authentication"></a>Adja hozzá, vagy távolítsa el a hitelesítést

* Hitelesítés hozzáadása egy Scheduler-feladat létrehozásakor vagy a feladat frissítése, adja hozzá a `authentication` gyermekelemet JavaScript Object Notation (JSON), a `request` elemet. 

  Válaszok soha nem adja vissza a titkos kulcsokat, a PUT, PATCH és POST-kérés a Scheduler szolgáltatás átadott a `authentication` objektum. 
  Válaszok titkos adatok null vagy előfordulhat, hogy használhatja egy nyilvános a hitelesített entitás jelképező jogkivonatot. 

* Hitelesítési eltávolítani egy Scheduler-feladat, explicit módon futtassa a PUT vagy PATCH kérés a feladat, és állítsa be a `authentication` objektum null értékű. A válasz nem tartalmazza a kívánt hitelesítési tulajdonságokat.

## <a name="client-certificate"></a>Ügyféltanúsítvány

### <a name="request-body---client-certificate"></a>Kérelem törzse - ügyféltanúsítvány

Hitelesítés hozzáadása során a `ClientCertificate` modellezheti, adja meg, ezek a kiegészítő elemek a kérelem törzsében.  

| Elem | Szükséges | Leírás |
|---------|----------|-------------|
| **hitelesítési** (elem) | A hitelesítési objektumot az ügyfél SSL-tanúsítvány használata |
| **type** | Igen | A hitelesítési típus. Az ügyfél SSL-tanúsítványok, tulajdonság értéke `ClientCertificate`. |
| **PFX** | Igen | A PFX-fájlt base64-kódolású tartalma |
| **jelszó** | Igen | A jelszót a PFX-fájl eléréséhez |
||| 

### <a name="response-body---client-certificate"></a>Választörzs - ügyféltanúsítvány 

Kérelmet küldött a hitelesítési adatokat, akkor a válasz ezek hitelesítési elemeket tartalmazza.

| Elem | Leírás | 
|---------|-------------| 
| **hitelesítési** (elem) | A hitelesítési objektumot az ügyfél SSL-tanúsítvány használata |
| **type** | A hitelesítési típus. Az ügyfél SSL-tanúsítványok, tulajdonság értéke `ClientCertificate`. |
| **certificateThumbprint** |A tanúsítvány ujjlenyomata |
| **certificateSubjectName** |A tanúsítvány tulajdonos megkülönböztető neve |
| **certificateExpiration** | A tanúsítvány lejárati dátuma |
||| 

### <a name="sample-rest-request---client-certificate"></a>Példa REST kérelem - ügyféltanúsítvány

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "clientcertificate",
          "password": "password",
          "pfx": "pfx key"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---client-certificate"></a>Példa REST-válasz – ügyféltanúsítvány

```json
HTTP/1.1 200 OKCache-Control: no-cache
Pragma: no-cache
Content-Length: 858
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 56c7b40e-721a-437e-88e6-f68562a73aa8
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 1075219e-e879-4030-bc81-094e54fbabce
x-ms-routing-request-id: WESTUS:20160316T190424Z:1075219e-e879-4030-bc81-094e54fbabce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:04:23 GMT

{
  "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
  "type": "Microsoft.Scheduler/jobCollections/jobs",
  "name": "southeastasiajc/httpjob",
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "certificateThumbprint": "88105CG9DF9ADE75B835711D899296CB217D7055",
          "certificateExpiration": "2021-01-01T07:00:00Z",
          "certificateSubjectName": "CN=Scheduler Mgmt",
          "type": "ClientCertificate"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
    "status": {
      "nextExecutionTime": "2016-03-16T19:05:00Z",
      "executionCount": 0,
      "failureCount": 0,
      "faultedCount": 0
    }
  }
}
```

## <a name="basic"></a>Alapszintű

### <a name="request-body---basic"></a>Kérelem törzse – alapszintű

Hitelesítés hozzáadása során a `Basic` modellezheti, adja meg, ezek a kiegészítő elemek a kérelem törzsében.

| Elem | Szükséges | Leírás |
|---------|----------|-------------|
| **hitelesítési** (elem) | A hitelesítési objektumot az alapszintű hitelesítés használata | 
| **type** | Igen | A hitelesítési típus. Az alapszintű hitelesítés, az értéke `Basic`. | 
| **felhasználónév** | Igen | Való hitelesítéséhez használt felhasználónév | 
| **jelszó** | Igen | A jelszót a hitelesítéshez |
|||| 

### <a name="response-body---basic"></a>Választörzs – alapszintű

Kérelmet küldött a hitelesítési adatokat, akkor a válasz ezek hitelesítési elemeket tartalmazza.

| Elem | Leírás | 
|---------|-------------|
| **hitelesítési** (elem) | A hitelesítési objektumot az alapszintű hitelesítés használata |
| **type** | A hitelesítési típus. Az alapszintű hitelesítés, az értéke `Basic`. |
| **felhasználónév** | A hitelesített felhasználónevet |
||| 

### <a name="sample-rest-request---basic"></a>Példa REST-kérés – alapszintű

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 562
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "type": "basic",
          "username": "user",
          "password": "password"
        }
      },
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled"
  }
}
```

### <a name="sample-rest-response---basic"></a>Példa REST-válasz – alapszintű

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 701
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: a2dcb9cd-1aea-4887-8893-d81273a8cf04
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 7816f222-6ea7-468d-b919-e6ddebbd7e95
x-ms-routing-request-id: WESTUS:20160316T190506Z:7816f222-6ea7-468d-b919-e6ddebbd7e95
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:05:06 GMT

{  
   "id":"/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type":"Microsoft.Scheduler/jobCollections/jobs",
   "name":"southeastasiajc/httpjob",
   "properties":{  
      "startTime":"2015-05-14T14:10:00Z",
      "action":{  
         "request":{  
            "uri":"https://mywebserviceendpoint.com",
            "method":"GET",
            "headers":{  
               "x-ms-version":"2013-03-01"
            },
            "authentication":{  
               "username":"user1",
               "type":"Basic"
            }
         },
         "type":"Http"
      },
      "recurrence":{  
         "frequency":"Minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"Enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="active-directory-oauth"></a>Active Directory OAuth

### <a name="request-body---active-directory-oauth"></a>Kérelem törzse – Active Directory OAuth 

Hitelesítés hozzáadása során a `ActiveDirectoryOAuth` modellezheti, adja meg, ezek a kiegészítő elemek a kérelem törzsében.

| Elem | Szükséges | Leírás |
|---------|----------|-------------|
| **hitelesítési** (elem) | Igen | A hitelesítési objektumot ActiveDirectoryOAuth hitelesítés használatával |
| **type** | Igen | A hitelesítési típus. A ActiveDirectoryOAuth hitelesítéshez, az értéke `ActiveDirectoryOAuth`. |
| **bérlő** | Igen | A bérlőazonosító az Azure AD-bérlővel. Az Azure AD-bérlő esetében a bérlőazonosító megkereséséhez futtassa `Get-AzureAccount` az Azure PowerShellben. |
| **Célközönség** | Igen | Ez az érték `https://management.core.windows.net/`. | 
| **ClientId** | Igen | Az Azure AD-alkalmazás az ügyfél-azonosítója | 
| **Titkos kulcs** | Igen | A titkos kulcsot a jogkivonatot kér az ügyfél számára | 
|||| 

### <a name="response-body---active-directory-oauth"></a>Választörzs - Active Directory OAuth

Kérelmet küldött a hitelesítési adatokat, akkor a válasz ezek hitelesítési elemeket tartalmazza.

| Elem | Leírás |
|---------|-------------|
| **hitelesítési** (elem) | A hitelesítési objektumot ActiveDirectoryOAuth hitelesítés használatával |
| **type** | A hitelesítési típus. A ActiveDirectoryOAuth hitelesítéshez, az értéke `ActiveDirectoryOAuth`. | 
| **bérlő** | Az Azure AD-bérlő a bérlő azonosítója |
| **Célközönség** | Ez az érték `https://management.core.windows.net/`. |
| **ClientId** | Az Azure AD-alkalmazás az ügyfél-azonosítója |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>Példa REST-kérelem - Active Directory OAuth

```json
PUT https://management.azure.com/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
User-Agent: Fiddler
Host: management.azure.com
Authorization: Bearer sometoken
Content-Length: 757
Content-Type: application/json; charset=utf-8

{
  "properties": {
    "startTime": "2015-05-14T14:10:00Z",
    "action": {
      "request": {
        "uri": "https://mywebserviceendpoint.com",
        "method": "GET",
        "headers": {
          "x-ms-version": "2013-03-01"
        },
        "authentication": {
          "tenant":"microsoft.onmicrosoft.com",
          "audience":"https://management.core.windows.net/",
          "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
          "secret": "G6u071r8Gjw4V4KSibnb+VK4+tX399hkHaj7LOyHuj5=",
          "type":"ActiveDirectoryOAuth"
        }
      },
      "type": "Http"
    },
    "recurrence": {
      "frequency": "Minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "Enabled"
  }
}
```

### <a name="sample-rest-response---active-directory-oauth"></a>Példa REST-válasz – Active Directory OAuth

```json
HTTP/1.1 200 OK
Cache-Control: no-cache
Pragma: no-cache
Content-Length: 885
Content-Type: application/json; charset=utf-8
Expires: -1
x-ms-request-id: 86d8e9fd-ac0d-4bed-9420-9baba1af3251
Server: Microsoft-IIS/8.5
X-AspNet-Version: 4.0.30319
X-Powered-By: ASP.NET
x-ms-ratelimit-remaining-subscription-resource-requests: 599
x-ms-correlation-request-id: 5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
x-ms-routing-request-id: WESTUS:20160316T191003Z:5183bbf4-9fa1-44bb-98c6-6872e3f2e7ce
Strict-Transport-Security: max-age=31536000; includeSubDomains
Date: Wed, 16 Mar 2016 19:10:02 GMT

{
   "id": "/subscriptions/<Azure-subscription-ID>/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
   "type": "Microsoft.Scheduler/jobCollections/jobs",
   "name": "southeastasiajc/httpjob",
   "properties": {
      "startTime": "2015-05-14T14:10:00Z",
      "action": {  
         "request": {
            "uri": "https://mywebserviceendpoint.com",
            "method": "GET",
            "headers": {  
               "x-ms-version": "2013-03-01"
            },
            "authentication": {  
               "tenant": "microsoft.onmicrosoft.com",
               "audience": "https://management.core.windows.net/",
               "clientId": "dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type": "ActiveDirectoryOAuth"
            }
         },
         "type": "Http"
      },
      "recurrence": {  
         "frequency": "minute",
         "endTime": "2016-04-10T08:00:00Z",
         "interval": 1
      },
      "state": "Enabled",
      "status": {  
         "lastExecutionTime": "2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime": "2016-03-16T19:11:00Z",
         "executionCount": 5,
         "failureCount": 5,
         "faultedCount": 1
      }
   }
}
```

## <a name="see-also"></a>Lásd még

* [Mi az Azure Scheduler?](scheduler-intro.md)
* [Az Azure Scheduler alapfogalmai, terminológiája és entitáshierarchiája](scheduler-concepts-terms.md)
* [Azure Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)
* [Az Azure Scheduler REST API-val](https://msdn.microsoft.com/library/mt629143)
* [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)
