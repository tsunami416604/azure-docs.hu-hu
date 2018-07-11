---
title: Kivételkezelés & hiba naplózási forgatókönyv – Azure Logic Apps |} A Microsoft Docs
description: Valós használati eset ismerteti a speciális kivételkezelést, és a hibanaplózás az Azure Logic Apps
keywords: ''
services: logic-apps
author: hedidin
manager: jeconnoc
editor: ''
documentationcenter: ''
ms.assetid: 63b0b843-f6b0-4d9a-98d0-17500be17385
ms.service: logic-apps
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.custom: H1Hack27Feb2017
ms.date: 07/29/2016
ms.author: LADocs; b-hoedid
ms.openlocfilehash: c4114e32053410689c0482816a46376947023972
ms.sourcegitcommit: aa988666476c05787afc84db94cfa50bc6852520
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2018
ms.locfileid: "37930273"
---
# <a name="scenario-exception-handling-and-error-logging-for-logic-apps"></a>Forgatókönyv: Kivételkezelést, és a hibanaplózás logikai alkalmazásokhoz

Ez a forgatókönyv leírja, hogyan terjesztheti ki egy logikai alkalmazást, amelyek hatékonyabban támogatják a kivételek kezelése. Valós használati eset a választ a kérdésre használja: "Az Azure Logic Apps támogatja a kivételek és hibakezelés?"

> [!NOTE]
> Az aktuális Azure Logic Apps-séma művelet válaszokat biztosít a szabványos sablon. Ez a sablon belső érvényesítése és a egy API-alkalmazás által visszaadott hiba válaszokat tartalmazza.

## <a name="scenario-and-use-case-overview"></a>A forgatókönyv és a használati eset – áttekintés

Itt van a történetet, mint a használati eset ehhez a forgatókönyvhöz: 

Egy jól ismert egészségügyi szervezet végző velünk egy Azure-megoldás, amely a betegek portál hozna létre a Microsoft Dynamics CRM Online használatával. Találkozó rekordok a Dynamics CRM Online betegek portál és a Salesforce között küldése szükség rájuk. Azt is kéri, hogy használja a [HL7 FHIR](http://www.hl7.org/implement/standards/fhir/) minden a betegek kartonjai szabvány.

A projekt kellett két fő követelmény:  

* A Dynamics CRM Online portál által küldött rekordok naplózására metódus
* Bármely a munkafolyamaton belüli fellépő hibák megtekintése

> [!TIP]
> Egy magas szintű videót a projekttel kapcsolatos [Integration User Group](http://www.integrationusergroup.com/logic-apps-support-error-handling/ "Integration User Group").

## <a name="how-we-solved-the-problem"></a>Hogyan tudjuk megoldani a problémát

Választottuk [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db/ "Azure Cosmos DB") vagy a (Cosmos DB-dokumentumok formájában rekordok jelenti) a napló- és rekordokat. Mivel az Azure Logic Apps minden válasz standard sablont, hogy nem kellene hozzon létre egy egyéni sémát. Létrehozhatunk egy API-alkalmazás **beszúrása** és **lekérdezés** hiba és a naplózási bejegyzések. Azt is megadására sémát minden, az API-alkalmazásban.  

Egy további követelmény a rekordok kiürítése után egy adott dátumon volt. A cosmos DB nevű tulajdonsággal rendelkezik [élettartama](https://azure.microsoft.com/blog/documentdb-now-supports-time-to-live-ttl/ "élettartama") élettartam (TTL), ami engedélyezett számunkra, hogy állítsa be a **élettartama** érték minden egyes rekord vagy egy egész gyűjteményre vonatkozóan. Ez a funkció kiküszöbölhetők azok a rekordok Cosmos DB-ben manuálisan törölni kell.

> [!IMPORTANT]
> Az oktatóanyag elvégzéséhez szüksége hozhat létre egy Cosmos DB-adatbázis és a két gyűjteménnyel (naplózás és a hibák).

## <a name="create-the-logic-app"></a>A logikai alkalmazás létrehozása

Az első lépés, hogy a logikai alkalmazás létrehozásához, és nyissa meg az alkalmazást a Logikaialkalmazás-tervezőben. Ebben a példában használjuk a szülő-gyermek logic apps. Tegyük fel, hogy már létrehozta a szülő, és hozzon létre egy gyermek logikai alkalmazást fog.

Bejelentkezés a Dynamics CRM Online típusúaké rekord fogjuk, mert kezdjük az elején. Kell használnunk egy **kérelem** eseményindítót, mert a szülő logikai alkalmazás aktiválása a gyermek.

### <a name="logic-app-trigger"></a>Logikaialkalmazás-trigger

Használjuk a **kérelem** aktiválása az alábbi példában látható módon:

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

A betegek rekord forrása (kérelem) azt kell jelentkeznie a Dynamics CRM Online portálon.

1. Azt be kell szereznie egy új találkozó rekordot a Dynamics CRM Online-hoz.

   Az eseményindító CRM-ből érkező velünk a kapcsolatot biztosít a **CRM PatentId**, **rekordtípus**, **új és frissített rekord** (új vagy nem frissíthető, logikai értéket), és  **SalesforceId**. A **SalesforceId** null is lehet, mert csak egy frissítés használja.
   A CRM használatával lekérjük a CRM-rekord **PatientID** és a **rekordtípus**.

2. Következő lépésként adja hozzá az Azure Cosmos DB SQL API-alkalmazás **InsertLogEntry** művelet Logic App Designerben itt látható módon.

   **Naplóbejegyzés beszúrása**

   ![Naplóbejegyzés beszúrása](media/logic-apps-scenario-error-and-exception-handling/lognewpatient.png)

   **Hiba történt a bejegyzés beszúrása**

   ![Naplóbejegyzés beszúrása](media/logic-apps-scenario-error-and-exception-handling/insertlogentry.png)

   **Ellenőrzés létrehozása rekord sikertelen**

   ![Állapot](media/logic-apps-scenario-error-and-exception-handling/condition.png)

## <a name="logic-app-source-code"></a>Logikai alkalmazás forráskódja

> [!NOTE]
> A következő példák olyan csak példák. Mivel ebben az oktatóanyagban egy implementáció, most már az éles környezetben, az értékét alapul egy **forráscsomópont** nem jelenítik meg az ütemezés egy találkozót. kapcsolódó Tulajdonságok > 

### <a name="logging"></a>Naplózás

A következő logic app-minta bemutatja, hogyan kezelje a naplózás.

#### <a name="log-entry"></a>Naplóbejegyzés

Itt van a logikai alkalmazás forráskódja szúr be egy naplóbejegyzést.

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

Ez a napló kérelemüzenet tesznek közzé az API-alkalmazásba.

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

Ez a napló válaszüzenet az API-alkalmazásból.

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

Most nézzük meg a hibakezelési lépések.

### <a name="error-handling"></a>Hibakezelés

A következő logic app-minta bemutatja, hogyan implementálható a hibakezelést.

#### <a name="create-error-record"></a>Hiba-rekord létrehozása

Íme egy hiba rekord létrehozása a logikai alkalmazás forráskódja.

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

#### <a name="insert-error-into-cosmos-db--request"></a>Hiba beszúrása az, Cosmos DB – kérelem

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

#### <a name="insert-error-into-cosmos-db--response"></a>Cosmos DB – válasz hiba beszúrása

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

#### <a name="salesforce-error-response"></a>A Salesforce hibaválaszba

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

### <a name="return-the-response-back-to-parent-logic-app"></a>Vissza a szülő logikai alkalmazás a választ adja vissza

Miután a választ kap, a választ a szülő logikai alkalmazás adhat át.

#### <a name="return-success-response-to-parent-logic-app"></a>A szülő logikai alkalmazás sikeres választ adja vissza

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

#### <a name="return-error-response-to-parent-logic-app"></a>A szülő logikai alkalmazás hiba választ adja vissza

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


## <a name="cosmos-db-repository-and-portal"></a>A cosmos DB-tárházat és a portálon

A megoldás képességeit a hozzáadott [Azure Cosmos DB](https://azure.microsoft.com/services/cosmos-db).

### <a name="error-management-portal"></a>Hiba történt a felügyeleti portálon

A hibák megtekintéséhez, létrehozhat egy MVC-webalkalmazást, a Cosmos DB-ből a hiba a rekordok jelenjenek meg. A **lista**, **részletek**, **szerkesztése**, és **törlése** műveletek szerepelnek a jelenlegi verziót.

> [!NOTE]
> Művelet szerkesztése: Cosmos DB váltja fel a teljes dokumentumot. A rekordok látható a **lista** és **részletes** nézetek mintát csak olyan. Még nincsenek tényleges betegek találkozó rögzíti.

Az alábbiakban példát az MVC-alkalmazás adatait, a korábban ismertetett megközelítés hoztak létre.

#### <a name="error-management-list"></a>Hiba a felügyeleti lista
![Hibalista](media/logic-apps-scenario-error-and-exception-handling/errorlist.png)

#### <a name="error-management-detail-view"></a>Hiba felügyeleti részletes nézet
![A hiba adatai](media/logic-apps-scenario-error-and-exception-handling/errordetails.png)

### <a name="log-management-portal"></a>Napló felügyeleti portálján

A naplók megtekintéséhez is létrehoztunk egy MVC-webalkalmazást. Az alábbiakban példát az MVC-alkalmazás adatait, a korábban ismertetett megközelítés hoztak létre.

#### <a name="sample-log-detail-view"></a>Minta napló részletes nézet
![Napló részletes nézet](media/logic-apps-scenario-error-and-exception-handling/samplelogdetail.png)

### <a name="api-app-details"></a>API-alkalmazás részletei

#### <a name="logic-apps-exception-management-api"></a>Logic Apps kivétel felügyeleti API

A nyílt forráskódú Azure Logic Apps kivétel felügyeleti API-alkalmazást a funkcionalitást biztosít az itt leírt módon – a két vezérlőn:

* **ErrorController** szúr be egy hibarekord (dokumentum) egy Azure Cosmos DB-gyűjteményben.
* **LogController** szúr be egy naplórekord (dokumentum) egy Azure Cosmos DB-gyűjteményben.

> [!TIP]
> Mindkét vezérlő használata `async Task<dynamic>` művelet, így műveletlánc futásidőben, így az Azure Cosmos DB-séma létrehozhatjuk a művelet törzsében. 
> 

Az Azure Cosmos DB minden dokumentumnak rendelkeznie kell egy egyedi azonosítót. Használjuk `PatientId` , és vegye fel a következő időbélyeget Unix timestamp értéket (kétirányú) alakítja át. Az értéket a törtrész csonkolja azt.

Megtekintheti a forráskódot a hiba vezérlő API [GitHub](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi/blob/master/LogicAppsExceptionManagementApi/Controllers/LogController.cs).

Az API-hoz logikai alkalmazásból származó, a következő szintaxis használatával nevezzük:

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

A kifejezés a fenti kódmintában keres a *Create_NewPatientRecord* állapotát **sikertelen**.

## <a name="summary"></a>Összegzés

* Könnyen valósítható meg naplózás és hibakezelés egy logikai alkalmazást.
* Azure Cosmos DB napló- és -rekordok (dokumentumok) használhatja a tárházban.
* MVC segítségével hozzon létre egy napló és a hiba a rekordok jelenjenek meg a portálon.

### <a name="source-code"></a>Forráskód

A Logic Apps kivétel Management API-alkalmazás forráskódja elérhető a jelen [GitHub-adattár](https://github.com/HEDIDIN/LogicAppsExceptionManagementApi "Logic App kivétel felügyeleti API").

## <a name="next-steps"></a>További lépések

* [További logic app példák és forgatókönyvek megtekintése](../logic-apps/logic-apps-examples-and-scenarios.md)
* [További információ a logic apps figyelése](../logic-apps/logic-apps-monitor-your-logic-apps.md)
* [A logic apps automatizált üzembehelyezési sablonok létrehozása](../logic-apps/logic-apps-create-deploy-template.md)
