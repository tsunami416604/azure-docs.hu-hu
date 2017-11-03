---
title: "Kivételkezelés & hiba naplózási forgatókönyv – Azure Logic Apps |} Microsoft Docs"
description: "Speciális kivétel- és az Azure Logic Apps hibanaplózás valódi használati eset ismerteti"
keywords: 
services: logic-apps
author: hedidin
manager: anneta
editor: 
documentationcenter: 
ms.assetid: 63b0b843-f6b0-4d9a-98d0-17500be17385
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/29/2016
ms.author: LADocs; b-hoedid
ms.openlocfilehash: 044de27c75da93c95609110d2b73336c42f746fe
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Forgatókönyv: Kivételkezelést és a hibanaplózás a logic Apps alkalmazások

Ez a forgatókönyv leírja, hogyan bővítheti a logikai alkalmazás kivételkezelést hatékonyabb támogatásához. A kérdés megválaszolásához jelenleg használt valós használati eset: "Az Azure Logic Apps támogatja kivétel és hibakezelés?"

> [!NOTE]
> A jelenlegi Azure Logic Apps séma művelet válaszokat biztosít a szabványos sablon. Ez a sablon belső érvényesítési és API-alkalmazás által visszaadott hiba válaszai egyaránt tartalmazza.

## <a name="scenario-and-use-case-overview"></a>A forgatókönyv és -felhasználási eset áttekintése

A szövegegység itt van, mint az ebben a forgatókönyvben használata esetében: 

Egy jól ismert egészségügyi szervezet részt vevő számunkra, hogy egy rekordot kell létrehoznia egy beteg portál a Microsoft Dynamics CRM Online Azure megoldást fejleszthet. Azok a Dynamics CRM Online beteg portál és a Salesforce időpontot egyeztessen rekordok elküldéséhez szükséges. Azt is kéri, hogy használja a [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) összes beteg rekordok szabványos.

A projekt két fő követelmény rendelkezett:  

* A Dynamics CRM Online portálon küldött rekordok naplózására metódus
* A munkafolyamaton belül történt hibákról megtekintése

> [!TIP]
> A projekt egy magas szintű videót: [integrációs felhasználói csoport](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "integrációs felhasználói csoport").

## <a name="how-we-solved-the-problem"></a>Hogyan azt megoldotta a problémát

Választottuk [Azure Cosmos DB](https://azure.microsoft.com/services/documentdb/ "Azure Cosmos DB") , a napló és a hiba (Cosmos DB-dokumentumokként rekordok jelenti) rekordok tára. Mivel Azure Logic Apps összes szabványos sablonját, azt nem kell létrehozni egy egyéni séma. API-alkalmazás sikerült létrehozhatunk **beszúrása** és **lekérdezés** hiba és a naplófájlokon is rekordok. Azt is meghatározhatja a séma minden, az API-alkalmazásban.  

Egy további követelmény a rekordok kiüríteni egy bizonyos dátum után volt. Cosmos DB nevű tulajdonsággal rendelkezik [élettartama](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "élettartama") (TTL), amely engedélyezett, hogy állítsa be a **élettartama** rekordok vagy gyűjtemény értékét. Ez a funkció Cosmos DB rekord manuálisan törölnie kell számolni.

> [!IMPORTANT]
> Az oktatóanyag teljesítéséhez szüksége egy Cosmos DB adatbázisban és két gyűjtemények (naplózás és -hibák) létrehozásához.

## <a name="create-the-logic-app"></a>A logikai alkalmazás létrehozása

Az első lépés a logikai alkalmazás létrehozása, és nyissa meg az alkalmazás a Logic App tervezőben. A jelen példában használjuk a logic apps szülő-gyermek. Tegyük fel, hogy azt a szülő már létrehozott és tervezi, hogy egy gyermek logikai alkalmazás létrehozása.

A Dynamics CRM Online kívül hamarosan naplórekord fogjuk, kezdjük, míg a lap tetején. Igazolnia kell használnia egy **kérelem** eseményindítót, mert a szülő logikai alkalmazás elindítja a gyermek.

### <a name="logic-app-trigger"></a>Logic app eseményindító

Használjuk a **kérelem** indítható el, mert a következő példában látható módon:

```` json
"triggers": {
        "request": {
          "type": "request",
          "kind": "http",
          "inputs": {
            "schema": {
              "properties": {
                "CRMid": {
                  "type": "string"
                },
                "recordType": {
                  "type": "string"
                },
                "salesforceID": {
                  "type": "string"
                },
                "update": {
                  "type": "boolean"
                }
              },
              "required": [
                "CRMid",
                "recordType",
                "salesforceID",
                "update"
              ],
              "type": "object"
            }
          }
        }
      },

````


## <a name="steps"></a>Lépések

A forrás (kérelem) a beteg rekord azt kell jelentkeznie a Dynamics CRM Online portálon.

1. A Dynamics CRM Online azt kell szereznie egy új időpontot egyeztessen rekordot.

   Az eseményindító CRM érkező nyújt nekünk a a **CRM PatentId**, **rekordtípus**, **új vagy frissített rekord** (új vagy frissíteni a logikai értéket), és **SalesforceId**. A **SalesforceId** null is lehet, mert csak a frissítéshez használja.
   A CRM-bejegyzés azt lekérése a CRM használatával **PatientID** és a **rekordtípus**.

2. A következő igazolnia kell a DocumentDB API-alkalmazás hozzáadása **InsertLogEntry** művelet Logic App Designer itt látható módon.

   **Naplóbejegyzés beszúrása**

   ![Naplóbejegyzés beszúrása](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Hiba bejegyzés beillesztése**

   ![Naplóbejegyzés beszúrása](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Ellenőrizze a rekord hiba létrehozása**

   ![Az állapot](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Logic app forráskód

> [!NOTE]
> A következő példák csak minták. Mivel ebben az oktatóanyagban alapul most az üzemi környezetben, értékének megvalósítását egy **forráscsomópont** nem jelenik meg az ütemezés egy időpontot egyeztessen. kapcsolódó Tulajdonságok > 

### <a name="logging"></a>Naplózás

A következő logic app példakód bemutatja, hogyan naplózási kezelésére.

#### <a name="log-entry"></a>Naplóbejegyzés

Ez a logic app forráskódja naplóbejegyzést beszúrni.

``` json
"InsertLogEntry": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "date": "@{outputs('Gets_NewPatientRecord')['headers']['Date']}",
            "operation": "New Patient",
            "patientId": "@{triggerBody()['CRMid']}",
            "providerId": "@{triggerBody()['providerID']}",
            "source": "@{outputs('Gets_NewPatientRecord')['headers']}"
        },
        "method": "post",
        "uri": "https://.../api/Log"
        },
        "runAfter":    {
            "Gets_NewPatientecord": ["Succeeded"]
        }
}
```

#### <a name="log-request"></a>Napló kérelem

Ez a napló kérelemüzenet közzé az API-alkalmazásba.

``` json
    {
    "uri": "https://.../api/Log",
    "method": "post",
    "body": {
        "date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "operation": "New Patient",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "providerId": "",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}"
        }
    }

```


#### <a name="log-response"></a>Válasz naplózása

Ez a napló válaszüzenetet az API-alkalmazás.

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:32:17 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "964",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "ttl": 2592000,
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0_1465597937",
        "_rid": "XngRAOT6IQEHAAAAAAAAAA==",
        "_self": "dbs/XngRAA==/colls/XngRAOT6IQE=/docs/XngRAOT6IQEHAAAAAAAAAA==/",
        "_ts": 1465597936,
        "_etag": "/"0400fc2f-0000-0000-0000-575b3ff00000/"",
        "patientID": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:56Z",
        "source": "{/"Pragma/":/"no-cache/",/"x-ms-request-id/":/"e750c9a9-bd48-44c4-bbba-1688b6f8a132/",/"OData-Version/":/"4.0/",/"Cache-Control/":/"no-cache/",/"Date/":/"Fri, 10 Jun 2016 22:31:56 GMT/",/"Set-Cookie/":/"ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1/",/"Server/":/"Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0/",/"X-AspNet-Version/":/"4.0.30319/",/"X-Powered-By/":/"ASP.NET/",/"Content-Length/":/"1935/",/"Content-Type/":/"application/json; odata.metadata=minimal; odata.streaming=true/",/"Expires/":/"-1/"}",
        "operation": "New Patient",
        "salesforceId": "",
        "expired": false
    }
}

```

Most már a hibakezelési lépéseket vizsgáljuk meg.

### <a name="error-handling"></a>Hibakezelés

A következő logic app példakód bemutatja, hogyan implementálható hibakezelés.

#### <a name="create-error-record"></a>Hiba rekord létrehozása

Ez a logic app forráskód egy hibarekordot létrehozásához.

``` json
"actions": {
    "CreateErrorRecord": {
        "metadata": {
        "apiDefinitionUrl": "https://.../swagger/docs/v1",
        "swaggerSource": "website"
        },
        "type": "Http",
        "inputs": {
        "body": {
            "action": "New_Patient",
            "isError": true,
            "crmId": "@{triggerBody()['CRMid']}",
            "patientID": "@{triggerBody()['CRMid']}",
            "message": "@{body('Create_NewPatientRecord')['message']}",
            "providerId": "@{triggerBody()['providerId']}",
            "severity": 4,
            "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
            "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
            "salesforceId": "",
            "update": false
        },
        "method": "post",
        "uri": "https://.../api/CrMtoSfError"
        },
        "runAfter":
        {
            "Create_NewPatientRecord": ["Failed" ]
        }
    }
}             
```

#### <a name="insert-error-into-cosmos-db--request"></a>Helyezze be a hiba a Cosmos DB--kérése

``` json

{
    "uri": "https://.../api/CrMtoSfError",
    "method": "post",
    "body": {
        "action": "New_Patient",
        "isError": true,
        "crmId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "patientId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "providerId": "",
        "severity": 4,
        "salesforceId": "",
        "update": false,
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MasterID_mp__c/":/"/",/"C_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"ONY_ID__c/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "statusCode": "400"
    }
}
```

#### <a name="insert-error-into-cosmos-db--response"></a>Hiba beszúrása Cosmos DB--válasz

``` json
{
    "statusCode": 200,
    "headers": {
        "Pragma": "no-cache",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:57 GMT",
        "Server": "Microsoft-IIS/8.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "1561",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "id": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0-1465597917",
        "_rid": "sQx2APhVzAA8AAAAAAAAAA==",
        "_self": "dbs/sQx2AA==/colls/sQx2APhVzAA=/docs/sQx2APhVzAA8AAAAAAAAAA==/",
        "_ts": 1465597912,
        "_etag": "/"0c00eaac-0000-0000-0000-575b3fdc0000/"",
        "prescriberId": "6b115f6d-a7ee-e511-80f5-3863bb2eb2d0",
        "timestamp": "2016-06-10T22:31:57.3651027Z",
        "action": "New_Patient",
        "salesforceId": "",
        "update": false,
        "body": "CRM failed to complete task: Message: duplicate value found: CRM_HUB_ID__c duplicates value on record with id: 001U000001c83gK",
        "source": "{/"Account_Class_vod__c/":/"PRAC/",/"Account_Status_MED__c/":/"I/",/"CRM_HUB_ID__c/":/"6b115f6d-a7ee-e511-80f5-3863bb2eb2d0/",/"Credentials_vod__c/":/"DO - Degree level is DO/",/"DTC_ID_MED__c/":/"/",/"Fax/":/"/",/"FirstName/":/"A/",/"Gender_vod__c/":/"/",/"IMS_ID__c/":/"/",/"LastName/":/"BAILEY/",/"MterID_mp__c/":/"/",/"Medicis_ID_MED__c/":/"851588/",/"Middle_vod__c/":/"/",/"NPI_vod__c/":/"/",/"PDRP_MED__c/":false,/"PersonDoNotCall/":false,/"PersonEmail/":/"/",/"PersonHasOptedOutOfEmail/":false,/"PersonHasOptedOutOfFax/":false,/"PersonMobilePhone/":/"/",/"Phone/":/"/",/"Practicing_Specialty__c/":/"FM - FAMILY MEDICINE/",/"Primary_City__c/":/"/",/"Primary_State__c/":/"/",/"Primary_Street_Line2__c/":/"/",/"Primary_Street__c/":/"/",/"Primary_Zip__c/":/"/",/"RecordTypeId/":/"012U0000000JaPWIA0/",/"Request_Date__c/":/"2016-06-10T22:31:55.9647467Z/",/"XXXXXXX/":/"/",/"Specialty_1_vod__c/":/"/",/"Suffix_vod__c/":/"/",/"Website/":/"/"}",
        "code": 400,
        "errors": null,
        "isError": true,
        "severity": 4,
        "notes": null,
        "resolved": 0
        }
}
```

#### <a name="salesforce-error-response"></a>Salesforce hibaválaszba

``` json
{
    "statusCode": 400,
    "headers": {
        "Pragma": "no-cache",
        "x-ms-request-id": "3e8e4884-288e-4633-972c-8271b2cc912c",
        "X-Content-Type-Options": "nosniff",
        "Cache-Control": "no-cache",
        "Date": "Fri, 10 Jun 2016 22:31:56 GMT",
        "Set-Cookie": "ARRAffinity=785f4334b5e64d2db0b84edcc1b84f1bf37319679aefce206b51510e56fd9770;Path=/;Domain=127.0.0.1",
        "Server": "Microsoft-IIS/8.0,Microsoft-HTTPAPI/2.0",
        "X-AspNet-Version": "4.0.30319",
        "X-Powered-By": "ASP.NET",
        "Content-Length": "205",
        "Content-Type": "application/json; charset=utf-8",
        "Expires": "-1"
    },
    "body": {
        "status": 400,
        "message": "Salesforce failed to complete task: Message: duplicate value found: Account_ID_MED__c duplicates value on record with id: 001U000001c83gK",
        "source": "Salesforce.Common",
        "errors": []
    }
}

```

### <a name="return-the-response-back-to-parent-logic-app"></a>Térjen vissza a választ szülő logikai alkalmazás

Miután lekérni a választ, adja meg a választ a szülő logikai alkalmazást.

#### <a name="return-success-response-to-parent-logic-app"></a>Szülő logikai alkalmazás sikeres választ küldi vissza

``` json
"SuccessResponse": {
    "runAfter":
        {
            "UpdateNew_CRMPatientResponse": ["Succeeded"]
        },
    "inputs": {
        "body": {
            "status": "Success"
    },
    "headers": {
    "    Content-type": "application/json",
        "x-ms-date": "@utcnow()"
    },
    "statusCode": 200
    },
    "type": "Response"
}
```

#### <a name="return-error-response-to-parent-logic-app"></a>Szülő logikai alkalmazás hiba választ küldi vissza

``` json
"ErrorResponse": {
    "runAfter":
        {
            "Create_NewPatientRecord": ["Failed"]
        },
    "inputs": {
        "body": {
            "status": "BadRequest"
        },
        "headers": {
            "Content-type": "application/json",
            "x-ms-date": "@utcnow()"
        },
        "statusCode": 400
    },
    "type": "Response"
}

```


## <a name="cosmos-db-repository-and-portal"></a>Cosmos DB tárház és -portál

A megoldás hozzáadott képességei a [Cosmos DB](https://azure.microsoft.com/services/documentdb).

### <a name="error-management-portal"></a>Hiba történt a felügyeleti portálon

Meg a hibákat, létrehozhat egy MVC webalkalmazás Cosmos DB hiba rekordját megjelenítéséhez. A **lista**, **részletek**, **szerkesztése**, és **törlése** műveletek szerepelnek a jelenlegi verziójával.

> [!NOTE]
> Művelet szerkesztése: Cosmos DB a felváltja a teljes dokumentumot. A bejegyzések a **lista** és **részletes** nézetek olyan csak minták. Nincsenek tényleges beteg időpontot egyeztessen rögzíti.

Az alábbiakban az MVC-alkalmazás adatait, a korábban ismertetett módszert létrehozott példát.

#### <a name="error-management-list"></a>Hiba a felügyeleti lista
![Hiba](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Hiba felügyeleti részletes nézet
![A hiba adatai](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Napló felügyeleti portálon

A naplók megtekintéséhez is létrehoztunk egy MVC webalkalmazás. Az alábbiakban az MVC-alkalmazás adatait, a korábban ismertetett módszert létrehozott példát.

#### <a name="sample-log-detail-view"></a>A minta napló részletes nézet
![Napló részletes nézet](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>API-alkalmazás adatait

#### <a name="logic-apps-exception-management-api"></a>Logic Apps kivételek kezelése API

A nyílt forráskódú Azure Logic Apps kivétel felügyeleti API app funkciókat biztosítja a itt leírtak – két vezérlők:

* **ErrorController** egy hibarekordot (dokumentumok) szúr be egy DocumentDB-gyűjteményt.
* **LogController** egy naplóbejegyzést képviselnek (dokumentumok) szúr be egy DocumentDB-gyűjteményt.

> [!TIP]
> Mindkét vezérlők használata `async Task<dynamic>` műveletek, így a műveletek megoldásához futásidőben, így a DocumentDB séma létrehozhatjuk törzsében. a művelet. 
> 

Minden dokumentumot a documentdb egy egyedi azonosítóval kell rendelkeznie. Használjuk `PatientId` és egy Unix timestamp értéket (kétirányú) konvertált időbélyeg hozzáadása. Az érték, a tört érték eltávolítása csonkolja azt.

Megtekintheti a hiba vezérlőhöz API forráskódját [a Githubról](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/Logic App Exception Management API/Controllers/ErrorController.cs).

Az API-t a logikai alkalmazás, a következő szintaxissal nevezzük:

``` json
 "actions": {
        "CreateErrorRecord": {
          "metadata": {
            "apiDefinitionUrl": "https://.../swagger/docs/v1",
            "swaggerSource": "website"
          },
          "type": "Http",
          "inputs": {
            "body": {
              "action": "New_Patient",
              "isError": true,
              "crmId": "@{triggerBody()['CRMid']}",
              "prescriberId": "@{triggerBody()['CRMid']}",
              "message": "@{body('Create_NewPatientRecord')['message']}",
              "salesforceId": "@{triggerBody()['salesforceID']}",
              "severity": 4,
              "source": "@{actions('Create_NewPatientRecord')['inputs']['body']}",
              "statusCode": "@{int(outputs('Create_NewPatientRecord')['statusCode'])}",
              "update": false
            },
            "method": "post",
            "uri": "https://.../api/CrMtoSfError"
          },
          "runAfter": {
              "Create_NewPatientRecord": ["Failed"]
            }
        }
 }
```

Az előző példakódban kifejezést keresi a *Create_NewPatientRecord* állapotának **sikertelen**.

## <a name="summary"></a>Összefoglalás

* Naplózás és a logikai alkalmazás hibakezelési könnyedén alkalmazni.
* A DocumentDB a tárház védelemként is alkalmazhatják a napló és a hiba a rekordok (dokumentumok).
* MVC hozhat létre a napló és a hiba a rekordok megjelenítéséhez a portálon.

### <a name="source-code"></a>Forráskód

A Logic Apps kivétel felügyeleti API-alkalmazás forráskódja érhető el ezen [GitHub-tárházban](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "Logic App kivétel felügyeleti API").

## <a name="next-steps"></a>Következő lépések

* [További logic app példák és forgatókönyvek megtekintése](../logic-apps/logic-apps-examples-and-scenarios.md)
* [További információk a logic Apps alkalmazások figyelése](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [A logic Apps alkalmazások automatikus központi telepítési sablonok létrehozása](../logic-apps/logic-apps-create-deploy-template.md)
