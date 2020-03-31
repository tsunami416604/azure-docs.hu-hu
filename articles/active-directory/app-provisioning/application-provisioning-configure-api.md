---
title: A kiépítés konfigurálásához használja a Microsoft Graph API-kat - Azure Active Directory | Microsoft dokumentumok
description: Be kell állítania egy alkalmazás több példányának kiépítését? Megtudhatja, hogy miként takaríthat meg időt a Microsoft Graph API-k használatával az automatikus kiépítés konfigurációjának automatizálásához.
services: active-directory
documentationcenter: ''
author: msmimart
manager: CelesteDG
ms.assetid: ''
ms.service: active-directory
ms.subservice: app-provisioning
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 11/15/2019
ms.author: mimart
ms.reviewer: arvinh
ms.collection: M365-identity-device-management
ms.openlocfilehash: c72217a565071f9531281af1862ba3681e353a4d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79481466"
---
# <a name="configure-provisioning-using-microsoft-graph-apis"></a>Kiépítés konfigurálása a Microsoft Graph API-k használatával

Az Azure Portalon egy kényelmes módja annak, hogy konfigurálja az egyes alkalmazások kiépítése egyes ével. Ha azonban egy alkalmazás több– vagy akár több száz – példányát hozza létre, könnyebben automatizálhatja az alkalmazások létrehozását és konfigurálását a Microsoft Graph API-kkal. Ez a cikk ismerteti, hogyan automatizálhatja a kiépítési konfiguráció API-kon keresztül. Ez a módszer gyakran használják az alkalmazások, mint az [Amazon Web Services](../saas-apps/amazon-web-service-tutorial.md#configure-azure-ad-sso).

**A Microsoft Graph API-k üzembe lépéseinek áttekintése a kiépítési konfiguráció automatizálásához**


|Lépés  |Részletek  |
|---------|---------|
|[1. lépés. A katalógusalkalmazás létrehozása](#step-1-create-the-gallery-application)     |Bejelentkezés az API-ügyfélbe <br> A katalógus alkalmazássablonjának lekérése <br> A katalógusalkalmazás létrehozása         |
|[2. lépés. Kiépítési feladat létrehozása sablon alapján](#step-2-create-the-provisioning-job-based-on-the-template)     |A kiépítési összekötő sablonjának beolvasása <br> A létesítési feladat létrehozása         |
|[3. lépés. Hozzáférés engedélyezése](#step-3-authorize-access)     |Az alkalmazáshoz való csatlakozás tesztelése <br> A hitelesítő adatok mentése         |
|[4. lépés. Kiépítési feladat indítása](#step-4-start-the-provisioning-job)     |A feladat indítása         |
|[5. lépés. Kiépítés figyelése](#step-5-monitor-provisioning)     |A létesítési feladat állapotának ellenőrzése <br> A létesítési naplók beolvasása         |

> [!NOTE]
> A cikkben látható válaszobjektumok olvashatóság érdekében lerövidíthetők. Az összes tulajdonság egy tényleges hívásból lesz visszaadva.

## <a name="step-1-create-the-gallery-application"></a>1. lépés: A galéria alkalmazás létrehozása

### <a name="sign-in-to-microsoft-graph-explorer-recommended-postman-or-any-other-api-client-you-use"></a>Bejelentkezés a Microsoft Graph Explorerbe (ajánlott), a Postman be vagy bármely más, használt API-ügyfélbe

1. A [Microsoft Graph Explorer](https://developer.microsoft.com/graph/graph-explorer) indítása
1. Válassza a "Bejelentkezés a Microsofttal" gombot, és jelentkezzen be az Azure AD globális rendszergazdájával vagy az alkalmazásrendszergazdai hitelesítő adatokkal.

    ![Graph bejelentkezés](./media/application-provisioning-configure-api/wd_export_02.png)

1. Sikeres bejelentkezés után a felhasználói fiók részletei a bal oldali ablaktáblában jelennek meg.

### <a name="retrieve-the-gallery-application-template-identifier"></a>A katalógus alkalmazássablon-azonosítójának beolvasása
Az Azure AD-alkalmazásgyűjteményben lévő alkalmazások mindegyike rendelkezik egy [alkalmazássablonnal,](https://docs.microsoft.com/graph/api/applicationtemplate-list?view=graph-rest-beta&tabs=http) amely leírja az adott alkalmazás metaadatait. Ezzel a sablonnal létrehozhatja az alkalmazás és a szolgáltatásnév egy példányát a bérlőben a felügyelethez.

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

### <a name="create-the-gallery-application"></a>A katalógusalkalmazás létrehozása

Használja az alkalmazáshoz az utolsó lépésben beolvasott sablonazonosítót az alkalmazás és a szolgáltatásnév [példányának létrehozásához](https://docs.microsoft.com/graph/api/applicationtemplate-instantiate?view=graph-rest-beta&tabs=http) a bérlőben.

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

## <a name="step-2-create-the-provisioning-job-based-on-the-template"></a>2. lépés: A kiépítési feladat létrehozása a sablon alapján

### <a name="retrieve-the-template-for-the-provisioning-connector"></a>A kiépítési összekötő sablonjának beolvasása

A katalógusban lévő, a kiépítéshez engedélyezett alkalmazások sablonokat a konfiguráció egyszerűsítésére. Az alábbi kérelem segítségével [olvassa be a sablont a létesítési konfigurációhoz.](https://docs.microsoft.com/graph/api/synchronization-synchronizationtemplate-list?view=graph-rest-beta&tabs=http) Vegye figyelembe, hogy meg kell adnia az azonosítót. Az azonosító az előző erőforrásra hivatkozik, amely ebben az esetben a ServicePrincipal. 

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

### <a name="create-the-provisioning-job"></a>A létesítési feladat létrehozása
A kiépítés engedélyezéséhez először létre kell [hoznia egy feladatot.](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http) Az alábbi kérelem használatával hozzon létre egy kiépítési feladatot. Használja az előző lépés sablonazonosítóját a feladathoz használandó sablon megadásakor.

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

## <a name="step-3-authorize-access"></a>3. lépés: Hozzáférés engedélyezése

### <a name="test-the-connection-to-the-application"></a>Az alkalmazáshoz való csatlakozás tesztelése

Tesztelje a kapcsolatot a külső alkalmazással. Az alábbi példa egy olyan alkalmazásra, amely ügyféltitkos és secretToken igényel. Minden alkalmazás nak megvan a követelmények. Az alkalmazások gyakran a BaseAddress címet használják az Ügyféltitkos cím helyett. Annak megállapításához, hogy milyen hitelesítő adatokra van szüksége az alkalmazásnak, keresse meg az alkalmazás létesítési konfigurációs lapját, és fejlesztői módban kattintson a tesztkapcsolatra. A hálózati forgalom a hitelesítő adatokhoz használt paramétereket jeleníti meg. A hitelesítő adatok teljes listája [itt](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http)található. 

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

A kiépítés konfigurálásához az Azure AD és az alkalmazás közötti megbízhatósági kapcsolat létrehozásához. Hozzáférés engedélyezése a külső alkalmazáshoz. Az alábbi példa egy olyan alkalmazásra, amely ügyféltitkos és secretToken igényel. Minden alkalmazás nak megvan a követelmények. Tekintse át az [API dokumentációját](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-validatecredentials?view=graph-rest-beta&tabs=http) a rendelkezésre álló lehetőségek megtekintéséhez. 

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

## <a name="step-4-start-the-provisioning-job"></a>4. lépés: A kiépítési feladat indítása
Most, hogy a létesítési feladat konfigurálva van, a következő paranccsal [indítsa el a feladatot.](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http) 


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


## <a name="step-5-monitor-provisioning"></a>5. lépés: A kiépítés figyelése

### <a name="monitor-the-provisioning-job-status"></a>A létesítési feladat állapotának figyelése

Most, hogy a létesítési feladat fut, használja a következő parancsot az aktuális kiépítési ciklus előrehaladásának nyomon követéséhez, valamint az eddigi statisztikákat, például a célrendszerben létrehozott felhasználók és csoportok számát. 

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


### <a name="monitor-provisioning-events-using-the-provisioning-logs"></a>Kiépítési események figyelése a létesítési naplók használatával
A kiépítési feladat állapotának figyelése mellett a [létesítési naplók](https://docs.microsoft.com/graph/api/provisioningobjectsummary-list?view=graph-rest-beta&tabs=http) segítségével lekérdezheti az összes előforduló eseményt (például egy adott felhasználó lekérdezését, és meghatározhatja, hogy sikeresen kilett-e).

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

- [A Microsoft Graph szinkronizálási dokumentációjának áttekintése](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
- [Egyéni SCIM-alkalmazások integrálása az Azure AD-vel](use-scim-to-provision-users-and-groups.md)
