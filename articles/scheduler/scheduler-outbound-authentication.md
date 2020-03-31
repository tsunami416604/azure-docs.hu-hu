---
title: Kimenő hitelesítés
description: Ismerje meg, hogyan állíthatja be vagy távolíthatja el az Azure Scheduler kimenő hitelesítését
services: scheduler
ms.service: scheduler
author: derek1ee
ms.author: deli
ms.reviewer: klam, estfan
ms.topic: article
ms.date: 08/15/2016
ms.openlocfilehash: bcd14e618323aec1c7ce47fcebb25099fa96be81
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78898513"
---
# <a name="outbound-authentication-for-azure-scheduler"></a>Kimenő hitelesítés az Azure Scheduler számára

> [!IMPORTANT]
> [Az Azure Logic Apps](../logic-apps/logic-apps-overview.md) felváltja az Azure Scheduler programot, [amelyet megszüntetnek.](../scheduler/migrate-from-scheduler-to-logic-apps.md#retire-date) Ha továbbra is szeretne dolgozni az Ütemezőben beállított feladatokkal, a lehető leghamarabb [telepítse át az Azure Logic Apps-alkalmazásokba.](../scheduler/migrate-from-scheduler-to-logic-apps.md) 
>
> Az Ütemező már nem érhető el az Azure Portalon, de a [REST API](/rest/api/scheduler) és az [Azure Scheduler PowerShell-parancsmagok](scheduler-powershell-reference.md) jelenleg elérhetők maradnak, így kezelheti a feladatokat és a feladatgyűjteményeket.

Előfordulhat, hogy az Azure Scheduler-feladatoknak meg kell hívniuk a hitelesítést igénylő szolgáltatásokat, például más Azure-szolgáltatásokat, Salesforce.com, facebookos és biztonságos egyéni webhelyeket. A hívott szolgáltatás meghatározhatja, hogy az Ütemező feladat hozzáférhet-e a kért erőforrásokhoz. 

Az Ütemező a következő hitelesítési modelleket támogatja: 

* *Ügyféltanúsítvány-hitelesítés* SSL/TLS-ügyféltanúsítványok használata esetén
* *Alapfokú* hitelesítés
* *Active Directory OAuth-hitelesítés*

## <a name="add-or-remove-authentication"></a>Hitelesítés hozzáadása vagy eltávolítása

* Ha hitelesítést szeretne hozzáadni egy ütemezőfeladathoz, a `authentication` feladat létrehozásakor vagy frissítésekor adja hozzá `request` a JavaScript objektumjelölés (JSON) gyermekelemet az elemhez. 

  A válaszok soha nem adnak vissza olyan titkos kulcsokat, amelyeket `authentication` az objektumput, PATCH vagy POST kérésen keresztül adnak át az Ütemező szolgáltatásnak. 
  A válaszok a titkos adatokat null értékre állították be, vagy a hitelesített entitást jelölő nyilvános jogkivonatot használhatnak. 

* Ha el szeretné távolítani a hitelesítést egy ütemezőfeladatból, kifejezetten `authentication` futtasson put vagy PATCH kérelmet a feladaton, és állítsa az objektumot null értékre. A válasz nem tartalmaz hitelesítési tulajdonságokat.

## <a name="client-certificate"></a>Ügyféltanúsítvány

### <a name="request-body---client-certificate"></a>Kérelem törzse - Ügyféltanúsítvány

Hitelesítés hozzáadásakor `ClientCertificate` a modell használatával, adja meg ezeket a további elemeket a kérelem törzsében.  

| Elem | Kötelező | Leírás |
|---------|----------|-------------|
| **hitelesítés** (szülőelem) | SSL-ügyféltanúsítvány használatára szolgáló hitelesítési objektum |
| **Típus** | Igen | A hitelesítés típusa. SSL-ügyféltanúsítványok esetén az `ClientCertificate`érték . |
| **Pfx** | Igen | A PFX-fájl base64 kódolású tartalma |
| **alaphelyzetbe állítása** | Igen | A PFX-fájl elérésének jelszava |
||| 

### <a name="response-body---client-certificate"></a>Választörzs - Ügyféltanúsítvány 

Amikor egy kérelmet hitelesítési adatokkal küld, a válasz ezeket a hitelesítési elemeket tartalmazza.

| Elem | Leírás | 
|---------|-------------| 
| **hitelesítés** (szülőelem) | SSL-ügyféltanúsítvány használatára szolgáló hitelesítési objektum |
| **Típus** | A hitelesítés típusa. SSL-ügyféltanúsítványok esetén az `ClientCertificate`érték . |
| **certificateThumbprint** |A tanúsítvány ujjlenyomata |
| **certificateSubjectName (tanúsítványSubjectName)** |A tanúsítvány tulajdonosának megkülönböztető neve |
| **certificateExpiration** | A tanúsítvány lejárati dátuma |
||| 

### <a name="sample-rest-request---client-certificate"></a>MINTA REST-kérelem – Ügyféltanúsítvány

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

### <a name="sample-rest-response---client-certificate"></a>MINTA REST-válasz – Ügyféltanúsítvány

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

## <a name="basic"></a>Basic

### <a name="request-body---basic"></a>Kérelem törzse - Alapszintű

Hitelesítés hozzáadásakor `Basic` a modell használatával, adja meg ezeket a további elemeket a kérelem törzsében.

| Elem | Kötelező | Leírás |
|---------|----------|-------------|
| **hitelesítés** (szülőelem) | Az alapfokú hitelesítést használó hitelesítési objektum | 
| **Típus** | Igen | A hitelesítés típusa. Az egyszerű hitelesítésnél `Basic`az érték . | 
| **Felhasználónév** | Igen | A hitelesítésre keresztelendő felhasználónév | 
| **alaphelyzetbe állítása** | Igen | A hitelesítéshez szükséges jelszó |
|||| 

### <a name="response-body---basic"></a>Válasz törzs - Alap

Amikor egy kérelmet hitelesítési adatokkal küld, a válasz ezeket a hitelesítési elemeket tartalmazza.

| Elem | Leírás | 
|---------|-------------|
| **hitelesítés** (szülőelem) | Az alapfokú hitelesítést használó hitelesítési objektum |
| **Típus** | A hitelesítés típusa. Az alapfokú hitelesítésnél `Basic`az érték . |
| **Felhasználónév** | A hitelesített felhasználónév |
||| 

### <a name="sample-rest-request---basic"></a>REST-mintakérelem – Alapszintű

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

### <a name="sample-rest-response---basic"></a>MINTA REST válasz - Alapszintű

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

### <a name="request-body---active-directory-oauth"></a>Kérelem törzse - Active Directory OAuth 

Hitelesítés hozzáadásakor `ActiveDirectoryOAuth` a modell használatával, adja meg ezeket a további elemeket a kérelem törzsében.

| Elem | Kötelező | Leírás |
|---------|----------|-------------|
| **hitelesítés** (szülőelem) | Igen | Az ActiveDirectoryOAuth-hitelesítést használó hitelesítési objektum |
| **Típus** | Igen | A hitelesítés típusa. ActiveDirectoryOAuth hitelesítés esetén az `ActiveDirectoryOAuth`érték . |
| **Bérlő** | Igen | Az Azure AD-bérlő bérlői azonosítója. Az Azure AD-bérlő bérlői azonosítójának megkereséséhez futtassa `Get-AzureAccount` az Azure PowerShellben. |
| **Közönség** | Igen | Ez az érték `https://management.core.windows.net/`a érték . | 
| **ügyfélazonosító** | Igen | Az Azure AD-alkalmazás ügyfélazonosítója | 
| **titkos kód** | Igen | A jogkivonatot kérő ügyfél titkos kulcsa | 
|||| 

### <a name="response-body---active-directory-oauth"></a>Választörzs - Active Directory OAuth

Amikor egy kérelmet hitelesítési adatokkal küld, a válasz ezeket a hitelesítési elemeket tartalmazza.

| Elem | Leírás |
|---------|-------------|
| **hitelesítés** (szülőelem) | Az ActiveDirectoryOAuth-hitelesítést használó hitelesítési objektum |
| **Típus** | A hitelesítés típusa. ActiveDirectoryOAuth hitelesítés esetén az `ActiveDirectoryOAuth`érték . | 
| **Bérlő** | Az Azure AD-bérlő bérlőazonosítója |
| **Közönség** | Ez az érték `https://management.core.windows.net/`a érték . |
| **ügyfélazonosító** | Az Azure AD-alkalmazás ügyfélazonosítója |
||| 

### <a name="sample-rest-request---active-directory-oauth"></a>REST-mintakérelem – Active Directory OAuth

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

### <a name="sample-rest-response---active-directory-oauth"></a>REST-mintaválasz – Active Directory OAuth

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

## <a name="next-steps"></a>További lépések

* [Az Azure Scheduler alapfogalmai, entitáshierarchiája és terminológiája](scheduler-concepts-terms.md)
* [Azure Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)
* [Az Azure Scheduler REST API-jának leírása](/rest/api/scheduler)
* [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)