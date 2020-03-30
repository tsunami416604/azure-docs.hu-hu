---
title: Kivételkezelés & hibanaplózási forgatókönyv
description: Valós használati eset és forgatókönyv a speciális kivételkezeléshez és hibanaplózáshoz az Azure Logic Apps alkalmazásban
services: logic-apps
ms.suite: integration
author: hedidin
ms.reviewer: klam, estfan, logicappspm
ms.topic: article
ms.date: 07/29/2016
ms.openlocfilehash: 1bb6e28c9dcae01f3233178706d2a24156fa509a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76902699"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Eset: A logikai alkalmazások kivételkezelése és hibanaplózása

Ebben a forgatókönyvben ismerteti, hogyan bővítheti a logikai alkalmazást a kivételkezelés jobb támogatása érdekében. Egy valós használati esetet használtunk a következő kérdés megválaszolására: "Támogatja-e az Azure Logic Apps a kivételt és a hibakezelést?"

> [!NOTE]
> A jelenlegi Azure Logic Apps-séma egy szabványos sablont biztosít a műveletválaszokhoz. Ez a sablon tartalmazza a belső érvényesítési és hibaválaszok egy API-alkalmazásból visszaadott.

## <a name="scenario-and-use-case-overview"></a>Eset- és használati eset – áttekintés

Itt van a történet, mint a használati eset ebben a forgatókönyvben: 

Egy jól ismert egészségügyi szervezet felbérelt minket egy Azure-megoldás kifejlesztésére, amely a Microsoft Dynamics CRM Online használatával hozna létre egy betegportált. A Dynamics CRM Online betegportálés a Salesforce között kellett elküldeniük a találkozórekordokat. Arra kértek minket, hogy a [HL7 FHIR](https://www.hl7.org/implement/standards/fhir/) szabványt használjuk minden beteg nyilvántartásához.

A projektnek két fő követelménye volt:  

* A Dynamics CRM Online portálról küldött rekordok naplózásának módja
* A munkafolyamaton belül előforduló hibák megtekintésének módja

> [!TIP]
> A projekttel kapcsolatos magas szintű videót az Integrációs felhasználói csoport című témakörben [taszítsa.](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "Integrációs felhasználói csoport")

## <a name="how-we-solved-the-problem"></a>Hogyan oldottuk meg a problémát

Az [Azure Cosmos DB-t](https://azure.microsoft.com/services/cosmos-db/ "Azure Cosmos DB") választottuk a napló- és hibarekordok tárházaként (a Cosmos DB a rekordokat dokumentumként hivatkozik). Mivel az Azure Logic Apps rendelkezik egy szabványos sablont az összes válaszhoz, nem kell egyéni sémát létrehozni. Létrehozhatunk egy API-alkalmazást **a Beszúrás** és a **Lekérdezés** segítségével mind a hiba-, mind a naplórekordokhoz. Az API-alkalmazáson belül is definiálhatunk egy sémát.  

Egy másik követelmény az volt, hogy egy bizonyos dátum után töröljék a rekordokat. A Cosmos DB rendelkezik egy [Time to Live](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "Ideje élni") (TTL) nevű tulajdonsággal, amely lehetővé tette számunkra, hogy minden rekordhoz vagy gyűjteményhez beállítsunk egy **"Time to Live"** értéket. Ez a képesség szükségtelenné tűrte a rekordok manuális törlését a Cosmos DB-ben.

> [!IMPORTANT]
> Az oktatóanyag befejezéséhez létre kell hoznia egy Cosmos DB-adatbázist és két gyűjteményt (Naplózás és hibák).

## <a name="create-the-logic-app"></a>A logikai alkalmazás létrehozása

Az első lépés a logikai alkalmazás létrehozása és az alkalmazás megnyitása a Logic App Designerben. Ebben a példában szülő-gyermek logikai alkalmazásokat használunk. Tegyük fel, hogy már létrehoztuk a szülőt, és egy gyermeklogikai alkalmazást fogunk létrehozni.

Mivel a Dynamics CRM Online-ból kikerülő rekordot fogjuk naplózni, kezdjük a csúcson. Meg kell használni a **kérelem** eseményindító, mert a szülő logikai alkalmazás elindítja ezt a gyermeket.

### <a name="logic-app-trigger"></a>Logikai alkalmazás eseményindítója

**A** következő példában látható kérelemesemény-eseményindítót használunk:

``` json
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

```


## <a name="steps"></a>Lépések

A Dynamics CRM Online portálról kell naplóznunk a betegrekord forrását (kérését).

1. Új találkozórekordot kell beszereznünk a Dynamics CRM Online-tól.

   A CRM-ből érkező eseményindító biztosítja számunkra a **CRM PatentId**, **rekordtípus**, **új vagy frissített rekord** (új vagy frissítési logikai érték) és a **SalesforceId**. A **SalesforceId** null értékű lehet, mert csak frissítésre használják.
   A CRM-rekordot a CRM **PatientID** és a **Record Type**segítségével kapjuk meg.

2. Ezután hozzá kell adnunk az Azure Cosmos DB SQL API app **InsertLogEntry** műveletet, ahogy itt látható a Logic App Designer.

   **Naplóbejegyzés beszúrása**

   ![Naplóbejegyzés beszúrása](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Hibabejegyzés beszúrása**

   ![Naplóbejegyzés beszúrása](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Rekordhiba létrehozásának ellenőrzése**

   ![Állapot](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Logikai alkalmazás forráskódja

> [!NOTE]
> A következő példák csak minták. Mivel ez az oktatóanyag egy éles környezetben lévő implementáción alapul, előfordulhat, hogy a **forráscsomópont** értéke nem jeleníti meg a találkozó ütemezéséhez kapcsolódó tulajdonságokat.> 

### <a name="logging"></a>Naplózás

A következő logikai alkalmazáskód-minta bemutatja, hogyan kell kezelni a naplózást.

#### <a name="log-entry"></a>Naplóbejegyzés

Itt van a logikai alkalmazás forráskódja a naplóbejegyzés beszúrásához.

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

#### <a name="log-request"></a>Naplókérés

Itt van az API-alkalmazásba küldött naplókérési üzenet.

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


#### <a name="log-response"></a>Naplóválasz

Itt van a napló válasz üzenetet az API-alkalmazásból.

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

Most nézzük meg a hibakezelési lépéseket.

### <a name="error-handling"></a>Hibakezelés

A következő logikai alkalmazáskód-minta bemutatja, hogyan valósíthatja meg a hibakezelést.

#### <a name="create-error-record"></a>Hibarekord létrehozása

Itt van a logikai alkalmazás forráskódja a hibarekord létrehozásához.

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

#### <a name="insert-error-into-cosmos-db--request"></a>Hiba beszúrása a Cosmos DB--request behelyezése

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

#### <a name="insert-error-into-cosmos-db--response"></a>Hiba beszúrása a Cosmos DB--response-ba

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

#### <a name="salesforce-error-response"></a>Salesforce-hibaválasz

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

### <a name="return-the-response-back-to-parent-logic-app"></a>A válasz visszaküldése a szülőlogikai alkalmazásnak

Miután megkapja a választ, a választ visszaküldheti a szülő logikai alkalmazásnak.

#### <a name="return-success-response-to-parent-logic-app"></a>Sikeres válasz visszaadása a szülőlogikai alkalmazásnak

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

#### <a name="return-error-response-to-parent-logic-app"></a>Hibaválasz visszaadása a szülőlogikai alkalmazásnak

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


## <a name="cosmos-db-repository-and-portal"></a>Cosmos DB adattár és portál

Megoldásunk az [Azure Cosmos DB-vel](https://azure.microsoft.com/services/cosmos-db)adott lehetőségeket.

### <a name="error-management-portal"></a>Hibakezelési portál

A hibák megtekintéséhez létrehozhat egy MVC webalkalmazást a Cosmos DB hibarekordjainak megjelenítéséhez. A **Lista,** **részletek,** **szerkesztés**és **törlés** műveletek az aktuális verzióban szerepelnek.

> [!NOTE]
> Szerkesztési művelet: Cosmos DB a teljes dokumentum lecseréli. A **Lista** és a **Részletek** nézetben látható rekordok csak minták. Ezek nem tényleges beteg-találkozó nyilvántartást.

Íme néhány példa a korábban leírt megközelítéssel létrehozott MVC alkalmazásunk részleteire.

#### <a name="error-management-list"></a>Hibakezelési lista
![Hibalista](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Hibakezelési részletnézet
![A hiba adatai](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Naplókezelési portál

A naplók megtekintéséhez létrehoztunk egy MVC webalkalmazást is. Íme néhány példa a korábban leírt megközelítéssel létrehozott MVC alkalmazásunk részleteire.

#### <a name="sample-log-detail-view"></a>Mintanapló részletes nézete
![Napló részleteinek nézete](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>AZ API-alkalmazások részletei

#### <a name="logic-apps-exception-management-api"></a>Logic Apps kivételkezelési API

Nyílt forráskódú Azure Logic Apps kivételkezelési API-alkalmazásunk az itt leírtak nak megfelelő funkciókat biztosít – két vezérlő van:

* **ErrorController** beszúr egy hibarekordot (dokumentumot) egy Azure Cosmos DB-gyűjteménybe.
* **LogController** Naplórekordot (dokumentumot) szúr be egy Azure Cosmos DB-gyűjteménybe.

> [!TIP]
> Mindkét vezérlő `async Task<dynamic>` műveleteket használ, így a műveletek futásidőben oldhatók fel, így létrehozhatjuk az Azure Cosmos DB sémát a művelet törzsében. 
> 

Az Azure Cosmos DB minden dokumentumának egyedi azonosítóval kell rendelkeznie. Egy Unix időbélyeg-értékre konvertált időbélyeget használunk, `PatientId` és hozzáadunk egy időbélyeget (dupla). Az érték csonkolása a törtérték eltávolításához.

A hibavezérlő API forráskódját a [GitHubról](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/LogicAppsExceptionManagementApi/Controllers/LogController.cs)tekintheti meg.

Az API-t logikai alkalmazásból hívjuk meg a következő szintaxis használatával:

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

Az előző kódminta kifejezése ellenőrzi a *Create_NewPatientRecord* **állapota Failed**.

## <a name="summary"></a>Összefoglalás

* A naplózás és a hibakezelés egyszerűen implementálható egy logikai alkalmazásban.
* Használhatja az Azure Cosmos DB, mint a napló- és hibarekordok (dokumentumok) tárházaként.
* Az MVC segítségével portált hozhat létre a napló- és hibarekordok megjelenítéséhez.

### <a name="source-code"></a>Forráskód

A Logic Apps kivételkezelési API-alkalmazás forráskódja ebben a [GitHub-tárházban](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "Logikai alkalmazás kivételkezelési API-ja")érhető el.

## <a name="next-steps"></a>További lépések

* [További logikai alkalmazásokra és -forgatókönyvekre való példa](../logic-apps/logic-apps-examples-and-scenarios.md)
* [Logikai alkalmazások figyelése](../logic-apps/monitor-logic-apps.md)
* [A logikai alkalmazás üzemelő példányának automatizálása](../logic-apps/logic-apps-azure-resource-manager-templates-overview.md)
