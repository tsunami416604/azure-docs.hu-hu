---
title: Az Azure AD-séma és az egyéni kifejezések ismertetése
description: Ez a cikk ismerteti az Azure AD-séma, az attribútumok, amelyek a létesítő ügynök folyamatok, és az egyéni kifejezések.
services: active-directory
documentationcenter: ''
author: billmath
manager: daveba
editor: ''
ms.service: active-directory
ms.devlang: na
ms.topic: conceptual
ms.tgt_pltfrm: na
ms.workload: identity
ms.date: 02/18/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: 4ac09fb3faf55be6c07a1e0a88b6e2032c9ab8ce
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78299329"
---
# <a name="understand-the-azure-ad-schema"></a>Az Azure AD-séma ismertetése
Az Azure Active Directoryban (Azure AD) egy objektum, mint minden könyvtárban, egy programozott magas szintű adatkonstrukció, amely például a felhasználókat, csoportokat és kapcsolattartókat jelöl. Amikor új felhasználót vagy kapcsolattartót hoz létre az Azure AD-ben, az objektum új példányát hozza létre. Ezek a példányok tulajdonságaik alapján differenciálhatók.

Az Azure AD-ben lévő tulajdonságok azok az elemek, amelyek az Azure AD-ben egy objektum egy példányának adatainak tárolásáért felelősek.

Az Azure AD-séma határozza meg a szabályokat, amelyek tulajdonságokat lehet használni egy bejegyzésben, milyen típusú értékeket, amelyek ezek a tulajdonságok, és hogyan felhasználók kölcsönhatásba léphetnek ezekkel az értékekkel. 

Az Azure AD kétféle tulajdonsággal rendelkezik:
- **Beépített tulajdonságok:** Az Azure AD-séma által előre definiált tulajdonságok. Ezek a tulajdonságok különböző felhasználási módokat biztosítanak, és előfordulhat, hogy nem érhetők el.
- **Címtárbővítmények:** Olyan tulajdonságok, amelyek levannak biztosítva, hogy az Azure AD saját használatra testreszabható legyen. Ha például egy adott attribútummal bővítette a helyszíni Active Directoryt, és szeretné továbbítani ezt az attribútumot, használhatja a megadott egyéni tulajdonságok egyikét. 

## <a name="attributes-and-expressions"></a>Attribútumok és kifejezések
Ha egy objektum, például egy felhasználó ki van építve az Azure AD-be, a felhasználói objektum új példánya jön létre. Ez a létrehozás tartalmazza az objektum tulajdonságait, amelyeket attribútumokként is ismerünk. Kezdetben az újonnan létrehozott objektum attribútumait a szinkronizálási szabályok által meghatározott értékekre állították be. Ezeket az attribútumokat ezután naprakészen a felhőkiépítési ügynök.

![Objektum kiépítés](media/concept-attributes/attribute1.png)

Előfordulhat például, hogy egy felhasználó egy marketingosztály tagja. Az Azure AD-részleg attribútum a kiépítéskor jön létre, és az érték értéke Marketing. Hat hónappal később, ha értékesítésre váltanak, a helyszíni Active Directory részlegattribútumuk Értékesítés re változik. Ez a módosítás szinkronizálja az Azure AD-t, és megjelenik az Azure AD felhasználói objektumban.

Attribútum-szinkronizálás lehet közvetlen, ahol az érték az Azure AD-ben közvetlenül a helyszíni attribútum értéke. Vagy egy programozott kifejezés is kezelni a szinkronizálást. Programozott kifejezésre van szükség azokban az esetekben, amikor valamilyen logikát vagy meghatározást kell tenni az érték feltöltéséhez.

Ha például a " "john.smith@contoso.com"mail attribútummal" volt, és a " "@contoso.comrészt ki kellett tépni, és csak a "john.smith" értéket kellett kifolynia, akkor a következőhöz hasonlót használ:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Minta bemenete/kimenete:** <br>

* **BEMENET** (mail):john.smith@contoso.com" "
* **KIMENET**: "john.smith"

Az egyéni kifejezések írásáról és a szintaxisról az [Attribútumleképezések írása az Azure Active Directoryban](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data)című témakörben talál további információt.

Az alábbi táblázat felsorolja a gyakori attribútumokat, és hogyan szinkronizálja őket az Azure AD-vel.


|Helyszíni Active Directory|Leképezés típusa|Azure AD|
|-----|-----|-----|
|Cn|Direct|köznapi név
|országKód|Direct|országKód|
|displayName|Direct|displayName|
|givenName|Kifejezés|givenName|
|objectGUID azonosító|Direct|sourceAnchorBinary|  
|userprincipalName (felhasználóneve)|Direct|userPrincipalName|
|ProxyAdress (ProxyAcím)|Direct|Proxycím|

## <a name="view-the-schema"></a>A séma megtekintése
> [!WARNING]
> A felhőkiépítési konfiguráció létrehoz egy egyszerű szolgáltatás. Az egyszerű szolgáltatás az Azure Portalon látható. Ne módosítsa az attribútum-leképezések használatával az egyszerű szolgáltatás az Azure Portalon.  Ez nem támogatott.

A séma megtekintéséhez és ellenőrzéséhez kövesse az alábbi lépéseket.

1.  Nyissa meg a [Graph Explorer](https://developer.microsoft.com/graph/graph-explorer)t.
1.  Jelentkezzen be globális rendszergazdai fiókjával.
1.  A bal oldalon válassza ki a **módosítási engedélyeket,** és győződjön meg arról, hogy **a Directory.ReadWrite.All** *jóváhagyással rendelkezik.*
1.  Futtassa `https://graph.microsoft.com/beta/serviceprincipals/?$filter=startswith(Displayname,'Active')`a lekérdezést . Ez a lekérdezés a szolgáltatásnévi tagok szűrt listáját adja vissza.
1.  Keresse `"appDisplayName": "Active Directory to Azure Active Directory Provisioning"` meg és `"id"`jegyezze fel a értékét.
    ```
    "value": [
            {
                "id": "00d41b14-7958-45ad-9d75-d52fa29e02a1",
                "deletedDateTime": null,
                "accountEnabled": true,
                "appDisplayName": "Active Directory to Azure Active Directory Provisioning",
                "appId": "1a4721b3-e57f-4451-ae87-ef078703ec94",
                "applicationTemplateId": null,
                "appOwnerOrganizationId": "47df5bb7-e6bc-4256-afb0-dd8c8e3c1ce8",
                "appRoleAssignmentRequired": false,
                "displayName": "Active Directory to Azure Active Directory Provisioning",
                "errorUrl": null,
                "homepage": "https://account.activedirectory.windowsazure.com:444/applications/default.aspx?metadata=AD2AADProvisioning|ISV9.1|primary|z",
                "loginUrl": null,
                "logoutUrl": null,
                "notificationEmailAddresses": [],
                "preferredSingleSignOnMode": null,
                "preferredTokenSigningKeyEndDateTime": null,
                "preferredTokenSigningKeyThumbprint": null,
                "publisherName": "Active Directory Application Registry",
                "replyUrls": [],
                "samlMetadataUrl": null,
                "samlSingleSignOnSettings": null,
                "servicePrincipalNames": [
                    "http://adapplicationregistry.onmicrosoft.com/adprovisioningtoaad/primary",
                    "1a4721b3-e57f-4451-ae87-ef078703ec94"
                ],
                "signInAudience": "AzureADMultipleOrgs",
                "tags": [
                    "WindowsAzureActiveDirectoryIntegratedApp"
                ],
                "addIns": [],
                "api": {
                    "resourceSpecificApplicationPermissions": []
                },
                "appRoles": [
                    {
                        "allowedMemberTypes": [
                            "User"
                        ],
                        "description": "msiam_access",
                        "displayName": "msiam_access",
                        "id": "a0326856-1f51-4311-8ae7-a034d168eedf",
                        "isEnabled": true,
                        "origin": "Application",
                        "value": null
                    }
                ],
                "info": {
                    "termsOfServiceUrl": null,
                    "supportUrl": null,
                    "privacyStatementUrl": null,
                    "marketingUrl": null,
                    "logoUrl": null
                },
                "keyCredentials": [],
                "publishedPermissionScopes": [
                    {
                        "adminConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on behalf of the signed-in user.",
                        "adminConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "id": "d40ed463-646c-4efe-bb3e-3fa7d0006688",
                        "isEnabled": true,
                        "type": "User",
                        "userConsentDescription": "Allow the application to access Active Directory to Azure Active Directory Provisioning on your behalf.",
                        "userConsentDisplayName": "Access Active Directory to Azure Active Directory Provisioning",
                        "value": "user_impersonation"
                    }
                ],
                "passwordCredentials": []
            },
    ```
1. Cserélje `{Service Principal id}` le az értéket, `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`és futtassa a lekérdezést .
1. Keresse `"id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976"` meg és `"id"`jegyezze fel a értékét.
    ```
    {
                "id": "AD2AADProvisioning.fd1c9b9e8077402c8bc03a7186c8f976",
                "templateId": "AD2AADProvisioning",
                "schedule": {
                    "expiration": null,
                    "interval": "PT2M",
                    "state": "Active"
                },
                "status": {
                    "countSuccessiveCompleteFailures": 0,
                    "escrowsPruned": false,
                    "code": "Active",
                    "lastSuccessfulExecutionWithExports": null,
                    "quarantine": null,
                    "steadyStateFirstAchievedTime": "2019-11-08T15:48:05.7360238Z",
                    "steadyStateLastAchievedTime": "2019-11-20T16:17:24.7957721Z",
                    "troubleshootingUrl": "",
                    "lastExecution": {
                        "activityIdentifier": "2dea06a7-2960-420d-931e-f6c807ebda24",
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 15,
                        "countEscrowedRaw": 15,
                        "countExported": 0,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "2019-11-20T16:15:21.116098Z",
                        "timeEnded": "2019-11-20T16:17:24.7488681Z"
                    },
                    "lastSuccessfulExecution": {
                        "activityIdentifier": null,
                        "countEntitled": 0,
                        "countEntitledForProvisioning": 0,
                        "countEscrowed": 0,
                        "countEscrowedRaw": 0,
                        "countExported": 5,
                        "countExports": 0,
                        "countImported": 0,
                        "countImportedDeltas": 0,
                        "countImportedReferenceDeltas": 0,
                        "state": "Succeeded",
                        "error": null,
                        "timeBegan": "0001-01-01T00:00:00Z",
                        "timeEnded": "2019-11-20T14:09:46.8855027Z"
                    },
                    "progress": [],
                    "synchronizedEntryCountByType": [
                        {
                            "key": "group to Group",
                            "value": 33
                        },
                        {
                            "key": "user to User",
                            "value": 3
                        }
                    ]
                },
                "synchronizationJobSettings": [
                    {
                        "name": "Domain",
                        "value": "{\"DomainFQDN\":\"contoso.com\",\"DomainNetBios\":\"CONTOSO\",\"ForestFQDN\":\"contoso.com\",\"ForestNetBios\":\"CONTOSO\"}"
                    },
                    {
                        "name": "DomainFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "DomainNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "ForestFQDN",
                        "value": "contoso.com"
                    },
                    {
                        "name": "ForestNetBios",
                        "value": "CONTOSO"
                    },
                    {
                        "name": "QuarantineTooManyDeletesThreshold",
                        "value": "500"
                    }
                ]
            }
    ```
1. Most futtassa a lekérdezést `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`.
 
    Például: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

   Cserélje `{Service Principal Id}` `{AD2ADD Provisioning Id}` ki, és az értékeket.

1. Ez a lekérdezés a sémát adja vissza.

   ![Visszaadott séma](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>További lépések

- [Mi az az üzembe helyezés?](what-is-provisioning.md)
- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)
