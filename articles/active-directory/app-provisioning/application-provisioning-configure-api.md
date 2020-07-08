---
title: Microsoft Graph API-k használata a kiépítés konfigurálásához – Azure Active Directory | Microsoft Docs
description: Be kell állítania egy alkalmazás több példányának kiépítés beállítását? Megtudhatja, hogyan takaríthat meg időt a Microsoft Graph API-k használatával az automatikus kiépítés konfigurációjának automatizálásához.
services: active-directory
author: kenwith
manager: celestedg
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.topic: how-to
ms.date: 11/15/2019
ms.author: kenwith
ms.reviewer: arvinh
ms.openlocfilehash: 01d4475e73fd436fd0cd2a8aca1e7a946cdd7562
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84782058"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>Kiépítés konfigurálása Microsoft Graph API-k használatával

A Azure Portal egy kényelmes módszer az egyes alkalmazások kiépített konfigurálására egyszerre. Ha azonban több – vagy akár több száz – is létrehoz egy alkalmazást, egyszerűbb lehet az alkalmazások létrehozásának és konfigurálásának automatizálása a Microsoft Graph API-kkal. Ez a cikk azt ismerteti, hogyan automatizálható a kiépítési konfiguráció az API-kon keresztül. Ezt a módszert gyakran használják olyan alkalmazások esetében, mint a [Amazon Web Services](../saas-apps/amazon-web-service-tutorial.md#configure-azure-ad-sso).

**A kiépítési konfiguráció automatizálásához Microsoft Graph API-k használatával kapcsolatos lépések áttekintése**


|Lépés  |Részletek  |
|---------|---------|
|[1. lépés. A Gallery-alkalmazás létrehozása](#step-1-create-the-gallery-application)     |Bejelentkezés az API-ügyfélbe <br> A katalógus alkalmazás sablonjának beolvasása <br> A Gallery-alkalmazás létrehozása         |
|[2. lépés. Kiépítési feladatok létrehozása sablon alapján](#step-2-create-the-provisioning-job-based-on-the-template)     |A létesítési összekötő sablonjának beolvasása <br> A kiépítési feladatok létrehozása         |
|[3. lépés. Hozzáférés engedélyezése](#step-3-authorize-access)     |Az alkalmazáshoz való kapcsolódás tesztelése <br> A hitelesítő adatok mentése         |
|[4. lépés. Kiépítési feladatok elindítása](#step-4-start-the-provisioning-job)     |A feladat indítása         |
|[5. lépés. A kiépítés figyelése](#step-5-monitor-provisioning)     |A kiépítési feladatok állapotának megtekintése <br> A kiépítési naplók beolvasása         |

> [!NOTE]
> Az ebben a cikkben látható válasz-objektumok az olvashatóság érdekében lerövidítve lehetnek. A rendszer az összes tulajdonságot visszaadja egy tényleges hívásból.

## <a name="step-1-create-the-gallery-application"></a>1. lépés: a gyűjtemény alkalmazás létrehozása

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Jelentkezzen be Microsoft Graph Explorer (ajánlott), Poster vagy bármely más API-ügyfél használatával

1. [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) elindítása
1. Válassza a "Bejelentkezés Microsofttal" gombot, és jelentkezzen be az Azure AD globális rendszergazdai vagy alkalmazás-rendszergazdai hitelesítő adataival.

    ![Gráf bejelentkezés](./media/application-provisioning-configure-api/wd_export_02.png)

1. A sikeres bejelentkezés után a bal oldali ablaktáblán megjelenik a felhasználói fiók adatai.

### <a name="retrieve-the-gallery-application-template-identifier"></a>A katalógus alkalmazás sablon-azonosítójának beolvasása
Az Azure AD-alkalmazás-katalógusban található alkalmazások mindegyike rendelkezik egy [alkalmazás-sablonnal](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) , amely leírja az alkalmazás metaadatait. Ennek a sablonnak a használatával létrehozhatja az alkalmazás és az egyszerű szolgáltatásnév egy példányát a bérlőben a felügyelethez.

#### <a name="request"></a>*Kérés*

<!-- {
  "blockType": "request",
  "name": "get_applicationtemplates"
}-->

```msgraph-interactive
GET https://graph.microsoft.com/beta/applicationTemplates
```

#### <a name="response"></a>*Válasz*

<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationTemplate",
  "isCollection": true
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
  "value": [
  {
    "id": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "Amazon Web Services (AWS)",
        "homePageUrl": "http://aws.amazon.com/",
        "supportedSingleSignOnModes": [
             "password",
             "saml",
             "external"
         ],
         "supportedProvisioningTypes": [
             "sync"
         ],
         "logoUrl": "https://az495088.vo.msecnd.net/app-logo/aws_215.png",
         "categories": [
             "developerServices"
         ],
         "publisher": "Amazon",
         "description": null    
  
}
```

### <a name="create-the-gallery-application"></a>A Gallery-alkalmazás létrehozása

Az utolsó lépésben az alkalmazáshoz lekért sablon-azonosítót használva létrehozhatja az alkalmazás és az egyszerű szolgáltatás [egy példányát](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) a bérlőben.

#### <a name="request"></a>*Kérés*

<!-- {
  "blockType": "request",
  "name": "applicationtemplate_instantiate"
}-->

```msgraph-interactive
POST https://graph.microsoft.com/beta/applicationTemplates/{id}/instantiate
Content-type: application/json

{
  "displayName": "AWS Contoso"
}
```

#### <a name="response"></a>*Válasz*


<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.applicationServicePrincipal"
} -->

```http
HTTP/1.1 201 OK
Content-type: application/json


{
    "application": {
        "objectId": "cbc071a6-0fa5-4859-8g55-e983ef63df63",
        "appId": "92653dd4-aa3a-3323-80cf-e8cfefcc8d5d",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "displayName": "AWS Contoso",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "logoutUrl": null,
        "samlMetadataUrl": null,
    },
    "servicePrincipal": {
        "objectId": "f47a6776-bca7-4f2e-bc6c-eec59d058e3e",
        "appDisplayName": "AWS Contoso",
        "applicationTemplateId": "8b1025e4-1dd2-430b-a150-2ef79cd700f5",
        "appRoleAssignmentRequired": true,
        "displayName": "My custom name",
        "homepage": "https://signin.aws.amazon.com/saml?metadata=aws|ISV9.1|primary|z",
        "replyUrls": [
            "https://signin.aws.amazon.com/saml"
        ],
        "servicePrincipalNames": [
            "93653dd4-aa3a-4323-80cf-e8cfefcc8d7d"
        ],
        "tags": [
            "WindowsAzureActiveDirectoryIntegratedApp"
        ],
    }
}
```

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>2. lépés: a kiépítési feladatok létrehozása a sablon alapján

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>A létesítési összekötő sablonjának beolvasása

A katalógusban a kiépítés számára engedélyezett alkalmazások rendelkeznek sablonokkal a konfiguráció egyszerűsítéséhez. A [kiépítési konfiguráció sablonjának beolvasásához](https://docs.microsoft.com/graph/api/synchronization-synchronizationtemplate-list?view=graph-rest-beta&tabs=http)használja az alábbi kérelmet. Vegye figyelembe, hogy meg kell adnia az azonosítót. Az azonosító az előző erőforrásra hivatkozik, amely ebben az esetben a ServicePrincipal. 

#### <a name="request"></a>*Kérés*

<!-- {
  "blockType": "request",
  "name": "get_synchronizationtemplate"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/templates
```


#### <a name="response"></a>*Válasz*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationTemplate",
  "isCollection": true
} -->
```http
HTTP/1.1 200 OK

{
    "value": [
        {
            "id": "aws",
            "factoryTag": "aws",
            "schema": {
                    "directories": [],
                    "synchronizationRules": []
                    }
        }
    ]
}
```

### <a name="create-the-provisioning-job"></a>A kiépítési feladatok létrehozása
A kiépítés engedélyezéséhez először [létre kell hoznia egy feladatot](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http). Az alábbi kérelem használatával hozzon létre egy létesítési feladatot. A feladatokhoz használni kívánt sablon megadásakor használja az előző lépés templateId.

#### <a name="request"></a>*Kérés*
<!-- {
  "blockType": "request",
  "name": "create_synchronizationjob_from_synchronization"
}-->
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs
Content-type: application/json

{ 
    "templateId": "aws"
}
```

#### <a name="response"></a>*Válasz*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "NotConfigured",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    }
}
```

## <a name="step-3-authorize-access"></a>3. lépés: hozzáférés engedélyezése

### <a name="test-the-connection-to-the-application"></a>Az alkalmazáshoz való kapcsolódás tesztelése

A harmadik féltől származó alkalmazással való kapcsolatfelvétel tesztelése. Az alábbi példa egy olyan alkalmazáshoz szükséges, amely clientSecret és secretToken igényel. Minden alkalmazás rendelkezik a követelményeivel. Az alkalmazások gyakran használják a BaseAddress-t a ClientSecret helyett. Az alkalmazás által igényelt hitelesítő adatok meghatározásához navigáljon az alkalmazás létesítési konfiguráció lapjára, és a fejlesztői módban kattintson a kapcsolat tesztelése elemre. A hálózati forgalom a hitelesítő adatokhoz használt paramétereket fogja megjeleníteni. [Itt](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http)megtalálja a hitelesítő adatok teljes listáját. 

#### <a name="request"></a>*Kérés*
```msgraph-interactive
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{id}/validateCredentials
{ 
    credentials: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```
#### <a name="response"></a>*Válasz*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

### <a name="save-your-credentials"></a>Hitelesítő adatok mentése

A kiépítés konfigurálásához megbízhatósági kapcsolatot kell létrehozni az Azure AD és az alkalmazás között. Engedélyezze a hozzáférést a külső féltől származó alkalmazáshoz. Az alábbi példa egy olyan alkalmazáshoz szükséges, amely clientSecret és secretToken igényel. Minden alkalmazás rendelkezik a követelményeivel. Az elérhető lehetőségek megtekintéséhez tekintse át az [API dokumentációját](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http) . 

#### <a name="request"></a>*Kérés*
```msgraph-interactive
PUT https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/secrets 
 
{ 
    value: [ 
        { key: "ClientSecret", value: "xxxxxxxxxxxxxxxxxxxxx" },
        { key: "SecretToken", value: "xxxxxxxxxxxxxxxxxxxxx" }
    ]
}
```

#### <a name="response"></a>*Válasz*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```

## <a name="step-4-start-the-provisioning-job"></a>4. lépés: a kiépítési feladatok elindítása
Most, hogy a kiépítési feladatot konfigurálták, a következő parancs használatával [indítsa el a feladatot](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http). 


#### <a name="request"></a>*Kérés*
<!-- {
  "blockType": "request",
  "name": "synchronizationjob_start"
}-->
```http
POST https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/start
```

#### <a name="response"></a>*Válasz*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.None"
} -->
```http
HTTP/1.1 204 No Content
```


## <a name="step-5-monitor-provisioning"></a>5. lépés: a kiépítés figyelése

### <a name="monitor-the-provisioning-job-status"></a>A kiépítési feladatok állapotának figyelése

Most, hogy a kiépítési feladat fut, az alábbi paranccsal követheti nyomon az aktuális kiépítési ciklus állapotát, valamint a statisztikákat, például a megcélzott rendszerekben létrehozott felhasználók és csoportok számát. 

#### <a name="request"></a>*Kérés*
<!-- {
  "blockType": "request",
  "name": "get_synchronizationjob"
}-->
```msgraph-interactive
GET https://graph.microsoft.com/beta/servicePrincipals/{id}/synchronization/jobs/{jobId}/
```

#### <a name="response"></a>*Válasz*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.synchronizationJob"
} -->
```http
HTTP/1.1 200 OK
Content-type: application/json
Content-length: 2577

{
    "id": "{jobId}",
    "templateId": "aws",
    "schedule": {
        "expiration": null,
        "interval": "P10675199DT2H48M5.4775807S",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "synchronizedEntryCountByType": [],
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "progress": [],
        "lastSuccessfulExecutionWithExports": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "quarantine": null,
        "troubleshootingUrl": null
    },
    "synchronizationJobSettings": [
      {
          "name": "QuarantineTooManyDeletesThreshold",
          "value": "500"
      }
    ]
}
```


### <a name="monitor-provisioning-events-using-the-provisioning-logs"></a>Kiépítési események figyelése a kiépítési naplók használatával
A kiépítési feladatok állapotának figyelése mellett a [kiépítési naplók](https://docs.microsoft.com/graph/api/provisioningobjectsummary-list?view=graph-rest-beta&tabs=http) segítségével lekérdezheti az összes bekövetkezett eseményt (például egy adott felhasználó lekérdezését, és megállapíthatja, hogy sikerült-e kiépíteni).

#### <a name="request"></a>*Kérés*
```msgraph-interactive
GET https://graph.microsoft.com/beta/auditLogs/provisioning
```
#### <a name="response"></a>*Válasz*
<!-- {
  "blockType": "response",
  "truncated": true,
  "@odata.type": "microsoft.graph.provisioningObjectSummary",
  "name": "list_provisioningobjectsummary_error"
} -->

```http
HTTP/1.1 200 OK
Content-type: application/json

{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#auditLogs/provisioning",
    "value": [
        {
            "id": "gc532ff9-r265-ec76-861e-42e2970a8218",
            "activityDateTime": "2019-06-24T20:53:08Z",
            "tenantId": "7928d5b5-7442-4a97-ne2d-66f9j9972ecn",
            "jobId": "BoxOutDelta.7928d5b574424a97ne2d66f9j9972ecn",
            "cycleId": "44576n58-v14b-70fj-8404-3d22tt46ed93",
            "changeId": "eaad2f8b-e6e3-409b-83bd-e4e2e57177d5",
            "action": "Create",
            "durationInMilliseconds": 2785,
            "sourceSystem": {
                "id": "0404601d-a9c0-4ec7-bbcd-02660120d8c9",
                "displayName": "Azure Active Directory",
                "details": {}
            },
            "targetSystem": {
                "id": "cd22f60b-5f2d-1adg-adb4-76ef31db996b",
                "displayName": "Box",
                "details": {
                    "ApplicationId": "f2764360-e0ec-5676-711e-cd6fc0d4dd61",
                    "ServicePrincipalId": "chc46a42-966b-47d7-9774-576b1c8bd0b8",
                    "ServicePrincipalDisplayName": "Box"
                }
            },
            "initiatedBy": {
                "id": "",
                "displayName": "Azure AD Provisioning Service",
                "initiatorType": "system"
            },
            "sourceIdentity": {
                "id": "5e6c9rae-ab4d-5239-8ad0-174391d110eb",
                "displayName": "Self-service Pilot",
                "identityType": "Group",
                "details": {}
            },
            "targetIdentity": {
                "id": "",
                "displayName": "",
                "identityType": "Group",
                "details": {}
            },
            "statusInfo": {
                "@odata.type": "#microsoft.graph.statusDetails",
                "status": "failure",
                "errorCode": "BoxEntryConflict",
                "reason": "Message: Box returned an error response with the HTTP status code 409.  This response indicates that a user or a group already exisits with the same name. This can be avoided by identifying and removing the conflicting user from Box via the Box administrative user interface, or removing the current user from the scope of provisioning either by removing their assignment to the Box application in Azure Active Directory or adding a scoping filter to exclude the user.",
                "additionalDetails": null,
                "errorCategory": "NonServiceFailure",
                "recommendedAction": null
            },
            "provisioningSteps": [
                {
                    "name": "EntryImportAdd",
                    "provisioningStepType": "import",
                    "status": "success",
                    "description": "Received Group 'Self-service Pilot' change of type (Add) from Azure Active Directory",
                    "details": {}
                },
                {
                    "name": "EntrySynchronizationAdd",
                    "provisioningStepType": "matching",
                    "status": "success",
                    "description": "Group 'Self-service Pilot' will be created in Box (Group is active and assigned in Azure Active Directory, but no matching Group was found in Box)",
                    "details": {}
                },
                {
                    "name": "EntryExportAdd",
                    "provisioningStepType": "export",
                    "status": "failure",
                    "description": "Failed to create Group 'Self-service Pilot' in Box",
                    "details": {
                        "ReportableIdentifier": "Self-service Pilot"
                    }
                }
            ],
            "modifiedProperties": [
                {
                    "displayName": "objectId",
                    "oldValue": null,
                    "newValue": "5e0c9eae-ad3d-4139-5ad0-174391d110eb"
                },
                {
                    "displayName": "displayName",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                },
                {
                    "displayName": "mailEnabled",
                    "oldValue": null,
                    "newValue": "False"
                },
                {
                    "displayName": "mailNickname",
                    "oldValue": null,
                    "newValue": "5ce25n9a-4c5f-45c9-8362-ef3da29c66c5"
                },
                {
                    "displayName": "securityEnabled",
                    "oldValue": null,
                    "newValue": "True"
                },
                {
                    "displayName": "Name",
                    "oldValue": null,
                    "newValue": "Self-service Pilot"
                }
            ]
       }
    ]
}

```
## <a name="related-articles"></a>Kapcsolódó cikkek

- [A szinkronizálási Microsoft Graph dokumentációjának áttekintése](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Egyéni SCIM-alkalmazás integrálása az Azure AD-vel](use-scim-to-provision-users-and-groups.md)
