---
title: A Feladatütemező kimenő hitelesítésre
description: A Feladatütemező kimenő hitelesítésre
services: scheduler
documentationcenter: .NET
author: derek1ee
manager: kevinlam1
editor: ''
ms.assetid: 6707f82b-7e32-401b-a960-02aae7bb59cc
ms.service: scheduler
ms.workload: infrastructure-services
ms.tgt_pltfrm: na
ms.devlang: dotnet
ms.topic: article
ms.date: 08/15/2016
ms.author: deli
ms.openlocfilehash: e345b2e22daae5b24c23645f7d2636f66df630ff
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23926507"
---
# <a name="scheduler-outbound-authentication"></a>A Feladatütemező kimenő hitelesítésre
A Feladatütemező szolgáltatás kell hitelesítést igénylő szolgáltatások hívásához. Ezzel a módszerrel a hívott szolgáltatás is meghatározhatja, ha az ütemezési feladat az erőforrások eléréséhez. Ezek a szolgáltatások többek között más Azure-szolgáltatások, a Salesforce.com, a Facebook-on és a biztonságos egyéni webhelyeket.

## <a name="adding-and-removing-authentication"></a>Hozzáadása és eltávolítása a hitelesítés
A Feladatütemező feladathoz a hitelesítés az egyszerű – vegyen fel egy JSON gyermek hozzáadása `authentication` való a `request` elem létrehozásakor vagy frissít egy feladatot. A PUT, a javítás vagy a POST kérelemben – a Feladatütemező szolgáltatás részeként átadott titkokat a `authentication` objektum – soha ne vissza válaszokat. A válaszokat, titkos információk értéke null, vagy nem rendelkezik nyilvános a hitelesített entitás jelképező jogkivonatot.

Távolítsa el a hitelesítést, PUT, vagy a feladat explicit módon, javítás beállítása a `authentication` objektum null értékű. Nem jelenik meg a hitelesítési tulajdonságai vissza válaszként.

Az egyetlen támogatott hitelesítési típusok jelenleg a `ClientCertificate` modell (a "az ügyfél SSL/TLS-tanúsítványok használatával), a `Basic` modell (az egyszerű hitelesítés), és a `ActiveDirectoryOAuth` modell (az Active Directory OAuth hitelesítési.)

## <a name="request-body-for-clientcertificate-authentication"></a>Kérelem törzse ClientCertificate hitelesítéshez
Hitelesítés hozzáadása során a `ClientCertificate` modell, adja meg a következő további elemeket a kérés törzsében.  

| Elem | Leírás |
|:--- |:--- |
| *hitelesítési (szülőelem)* |Hitelesítési objektum ügyfél SSL-tanúsítványt használ. |
| *típusa* |Kötelező. Hitelesítés típusa. Az ügyfél SSL-tanúsítványok, az értéknek kell lennie `ClientCertificate`. |
| *PFX* |Kötelező. A PFX-fájl Base64-kódolású tartalmát. |
| *jelszó* |Kötelező. A jelszó megadásával érheti el a PFX-fájlból. |

## <a name="response-body-for-clientcertificate-authentication"></a>Választörzs ClientCertificate hitelesítéshez
Kérelmet küldött hitelesítési információval, akkor a válasz a következő hitelesítési kapcsolódó elemeket tartalmaz.

| Elem | Leírás |
|:--- |:--- |
| *hitelesítési (szülőelem)* |Hitelesítési objektum ügyfél SSL-tanúsítványt használ. |
| *típusa* |Hitelesítés típusa. Az ügyfél SSL-tanúsítványok, a értéke `ClientCertificate`. |
| *certificateThumbprint* |A tanúsítvány ujjlenyomatát. |
| *certificateSubjectName* |Tulajdonosának megkülönböztető neve a tanúsítványt. |
| *certificateExpiration* |A tanúsítvány lejárati dátuma. |

## <a name="sample-rest-request-for-clientcertificate-authentication"></a>Mintakérelem REST ClientCertificate hitelesítéshez
```
PUT https://management.azure.com/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
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
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-clientcertificate-authentication"></a>Mintaválasz REST ClientCertificate hitelesítéshez
```
HTTP/1.1 200 OK
Cache-Control: no-cache
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
  "id": "/subscriptions/1fe0abdf-581e-4dfe-9ec7-e5cb8e7b205e/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
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

## <a name="request-body-for-basic-authentication"></a>Egyszerű hitelesítés esetén a kérelem törzsében
Hitelesítés hozzáadása során a `Basic` modell, adja meg a következő további elemeket a kérés törzsében.

| Elem | Leírás |
|:--- |:--- |
| *hitelesítési (szülőelem)* |Hitelesítési objektumot az alapszintű hitelesítést használ. |
| *típusa* |Kötelező. Hitelesítés típusa. Az alapszintű hitelesítés, az értéknek kell lennie `Basic`. |
| *felhasználónév* |Kötelező. Felhasználónév hitelesítéséhez. |
| *jelszó* |Kötelező. Jelszó a hitelesítéshez. |

## <a name="response-body-for-basic-authentication"></a>Egyszerű hitelesítés esetén adott válasz törzse
Kérelmet küldött hitelesítési információval, akkor a válasz a következő hitelesítési kapcsolódó elemeket tartalmaz.

| Elem | Leírás |
|:--- |:--- |
| *hitelesítési (szülőelem)* |Hitelesítési objektumot az alapszintű hitelesítést használ. |
| *típusa* |Hitelesítés típusa. Az egyszerű hitelesítés értéke `Basic`. |
| *felhasználónév* |A hitelesített felhasználónév. |

## <a name="sample-rest-request-for-basic-authentication"></a>Az egyszerű hitelesítés REST Mintakérelem
```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
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
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-basic-authentication"></a>Az egyszerű hitelesítés REST Mintaválasz
```
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
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
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
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "nextExecutionTime":"2016-03-16T19:06:00Z",
         "executionCount":0,
         "failureCount":0,
         "faultedCount":0
      }
   }
}
```

## <a name="request-body-for-activedirectoryoauth-authentication"></a>Kérelem törzse ActiveDirectoryOAuth hitelesítéshez
Hitelesítés hozzáadása során a `ActiveDirectoryOAuth` modell, adja meg a következő további elemeket a kérés törzsében.

| Elem | Leírás |
|:--- |:--- |
| *hitelesítési (szülőelem)* |Hitelesítési objektum ActiveDirectoryOAuth hitelesítéssel. |
| *típusa* |Kötelező. Hitelesítés típusa. A ActiveDirectoryOAuth hitelesítéshez, az értéknek kell lennie `ActiveDirectoryOAuth`. |
| *Bérlői* |Kötelező. A bérlő azonosítója az Azure AD-bérlő. |
| *Célközönség* |Kötelező. A beállított érték https://management.core.windows.net/. |
| *clientId* |Kötelező. Adja meg az ügyfél-azonosító az Azure AD-alkalmazás. |
| *titkos kulcs* |Kötelező. Az ügyfél a jogkivonatot kérő titkos kulcsot. |

### <a name="determining-your-tenant-identifier"></a>A bérlői azonosító meghatározása
Megtalálhatja a bérlő azonosítóját az Azure AD-bérlő futtatásával `Get-AzureAccount` az Azure PowerShell.

## <a name="response-body-for-activedirectoryoauth-authentication"></a>Választörzs ActiveDirectoryOAuth hitelesítéshez
Kérelmet küldött hitelesítési információval, akkor a válasz a következő hitelesítési kapcsolódó elemeket tartalmaz.

| Elem | Leírás |
|:--- |:--- |
| *hitelesítési (szülőelem)* |Hitelesítési objektum ActiveDirectoryOAuth hitelesítéssel. |
| *típusa* |Hitelesítés típusa. ActiveDirectoryOAuth hitelesítéshez értéke `ActiveDirectoryOAuth`. |
| *Bérlői* |A bérlő azonosítója az Azure AD-bérlő. |
| *Célközönség* |A beállított érték https://management.core.windows.net/. |
| *clientId* |Az Azure AD-alkalmazás ügyfél-azonosítókra. |

## <a name="sample-rest-request-for-activedirectoryoauth-authentication"></a>ActiveDirectoryOAuth hitelesítési REST Mintakérelem
```
PUT https://management.azure.com/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobcollections/southeastasiajc/jobs/httpjob?api-version=2016-01-01 HTTP/1.1
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
      "type": "http"
    },
    "recurrence": {
      "frequency": "minute",
      "endTime": "2016-04-10T08:00:00Z",
      "interval": 1
    },
    "state": "enabled",
  }
}
```

## <a name="sample-rest-response-for-activedirectoryoauth-authentication"></a>ActiveDirectoryOAuth hitelesítési REST Mintaválasz
```
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
   "id":"/subscriptions/1d908808-e491-4fe5-b97e-29886e18efd4/resourceGroups/CS-SoutheastAsia-scheduler/providers/Microsoft.Scheduler/jobCollections/southeastasiajc/jobs/httpjob",
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
               "tenant":"microsoft.onmicrosoft.com",
               "audience":"https://management.core.windows.net/",
               "clientId":"dc23e764-9be6-4a33-9b9a-c46e36f0c137",
               "type":"ActiveDirectoryOAuth"
            }
         },
         "type":"http"
      },
      "recurrence":{  
         "frequency":"minute",
         "endTime":"2016-04-10T08:00:00Z",
         "interval":1
      },
      "state":"enabled",
      "status":{  
         "lastExecutionTime":"2016-03-16T19:10:00.3762123Z",
         "nextExecutionTime":"2016-03-16T19:11:00Z",
         "executionCount":5,
         "failureCount":5,
         "faultedCount":1
      }
   }
}
```

## <a name="see-also"></a>Lásd még:
 [A Scheduler ismertetése](scheduler-intro.md)

 [Az Azure Scheduler alapfogalmai, terminológiája és entitáshierarchiája](scheduler-concepts-terms.md)

 [Ismerkedés a Scheduler szolgáltatás Azure Portalon való használatával](scheduler-get-started-portal.md)

 [Csomagok és számlázás az Azure Schedulerben](scheduler-plans-billing.md)

 [Az Azure Scheduler REST API-jának leírása](https://msdn.microsoft.com/library/mt629143)

 [Az Azure Scheduler PowerShell-parancsmagjainak leírása](scheduler-powershell-reference.md)

 [Azure Scheduler – magas fokú rendelkezésre állás és megbízhatóság](scheduler-high-availability-reliability.md)

 [Azure Scheduler – korlátozások, alapértékek és hibakódok](scheduler-limits-defaults-errors.md)

