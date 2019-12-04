---
title: Az Azure AD-séma és az egyéni kifejezések ismertetése
description: Ez a témakör ismerteti az Azure AD-sémát, a kiépítési ügynök által beáramló attribútumokat és az egyéni kifejezéseket.
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
ms.date: 12/02/2019
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: eae594bcc20e3c4ed1c6fbd0333699de8c9f4452
ms.sourcegitcommit: 76b48a22257a2244024f05eb9fe8aa6182daf7e2
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74794496"
---
# <a name="understanding-the-azure-ad-schema"></a>Az Azure AD-séma ismertetése
Az Azure AD-ban, például bármely címtárban található objektum egy programozott, magas szintű adatszerkezet, amely felhasználók, csoportok és névjegyek számára is felhasználható.  Amikor új felhasználót vagy kapcsolatot hoz létre az Azure AD-ben, az adott objektum új példányát hozza létre.  Ezek a példányok a tulajdonságaik alapján különböztethetők meg.

Az Azure AD-ben a tulajdonságok az Azure AD-beli objektumok egy példányával kapcsolatos információk tárolására felelős elemek.  

Az Azure AD-séma határozza meg azokat a szabályokat, amelyekkel a tulajdonságok felhasználhatók egy adott bejegyzésben, a tulajdonságokat tartalmazó értékek fajtáit, valamint azt, hogy a felhasználók hogyan használhatják ezeket az értékeket. 

Az Azure AD két típusú tulajdonsággal rendelkezik.  A tulajdonságok a következők:
- **Beépített tulajdonságok** – az Azure ad-séma által előre definiált tulajdonságok.  Ezek a tulajdonságok eltérő felhasználást biztosítanak, és lehetséges, hogy nem érhetők el.
- **Directory-bővítmények** – a megadott tulajdonságok, amelyek segítségével testre szabhatja az Azure ad-t saját használatra.  Ha például kiterjesztette a helyszíni Active Directory egy bizonyos attribútummal, és az attribútumot szeretné átdolgozni, a megadott egyéni tulajdonságok egyikét használhatja. 

## <a name="attributes-and-expressions"></a>Attribútumok és kifejezések
Ha egy objektum, például egy felhasználó az Azure AD-be van kiépítve, a rendszer létrehozza a felhasználói objektum egy új példányát.  Ez a létrehozás magában foglalja az objektum tulajdonságait is, amelyek más néven attribútumok.  Kezdetben az újonnan létrehozott objektum attribútumai a szinkronizálási szabályok által meghatározott értékekre lesznek beállítva.  Ezeket az attribútumokat a rendszer a felhőalapú kiépítési ügynökön keresztül naprakészen tartja.

![](media/concept-attributes/attribute1.png)

Ha például egy felhasználó a marketing részleg részét képezi, az Azure AD-részleg attribútuma kezdetben a kiépítés után lesz létrehozva, majd a marketing értékre lesz állítva.  Hat hónappal később pedig értékesítésre változnak.  A helyszíni AD-részleg attribútuma értékesítésre változik.  Ezt követően a módosítás szinkronizálva lesz az Azure AD-vel, és az Azure AD felhasználói objektumán fog szerepelni.

Az attribútumok szinkronizálása lehet közvetlen, ahol az Azure AD-ban lévő érték közvetlenül a helyszíni attribútum értékére van beállítva.  Vagy lehet, hogy van egy programozott kifejezés, amely kezeli ezt a szinkronizálást.  Egy programozott kifejezésre olyan esetekben van szükség, amikor valamilyen logikát vagy meghatározást kell elvégezni az érték feltöltéséhez.

Ha például rendelkezem a mail-attribútummal ("john.smith@contoso.com"), és szükségem volt a "@contoso.com" rész kiépítésére, és csak a "János. Smith" értéket szeretném használni:

`Replace([mail], "@contoso.com", , ,"", ,)`  

**Minta bemenet/kimenet:** <br>

* **Bemenet** (e-mail): "john.smith@contoso.com"
* **Kimenet**: "John. Smith"

További információ az egyéni kifejezések írásakor és a szintaxisban: [kifejezések írása az attribútumok leképezéséhez Azure Active Directory](https://docs.microsoft.com/azure/active-directory/manage-apps/functions-for-customizing-application-data).

Az alábbi lista általános attribútumok, valamint az Azure AD-vel való szinkronizálásuk módja.


|Helyszíni Active Directory|Leképezés típusa|Azure AD|
|-----|-----|-----|
|CN|Direct|Köznapinév
|Országhívószám|Direct|Országhívószám|
|displayName|Direct|displayName|
|givenName|Kifejezés|givenName|
|objectGUID|Direct|sourceAnchorBinary|  
|userprincipalName|Direct|userPrincipalName|
|ProxyAdress|Direct|ProxyAddress|

## <a name="viewing-the-schema"></a>A séma megtekintése
A séma megtekintéséhez és ellenőrzéséhez hajtsa végre a következő lépéseket:

1.  Navigáljon a [Graph Explorerben](https://developer.microsoft.com/graph/graph-explorer).
2.  Jelentkezzen be a globális rendszergazdai fiókjával
3.  A bal oldalon kattintson az **engedélyek módosítása** elemre, és győződjön meg arról, hogy a **Directory. ReadWrite. All** is beleegyezett.
4.  Futtassa a következő lekérdezést: https://graph.microsoft.com/beta/serviceprincipals/.  Ez a lekérdezés az egyszerű szolgáltatások listáját fogja visszaadni.
5.  Keresse meg a "appDisplayName": "Active Directory Azure Active Directory kiépítés" lehetőséget, és jegyezze fel az "id:" értéket.
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
6. Cserélje le a {szolgáltatás egyszerű azonosítóját} értékre, és futtassa a következő lekérdezést: `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal id}/synchronization/jobs/`
7. Keresse meg az "id": "AD2AADProvisioning. fd1c9b9e8077402c8bc03a7186c8f976" szakaszt, és jegyezze fel az "id:" azonosítót.
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
8. Most futtassa a következő lekérdezést: `https://graph.microsoft.com/beta/serviceprincipals/{Service Principal Id}/synchronization/jobs/{AD2AAD Provisioning id}/schema`
 
    Példa: https://graph.microsoft.com/beta/serviceprincipals/653c0018-51f4-4736-a3a3-94da5dcb6862/synchronization/jobs/AD2AADProvisioning.e9287a7367e444c88dc67a531c36d8ec/schema

 Cserélje le a {Service Principal ID} és {AD2ADD kiépítési azonosító} értéket az értékekre.

9. Ez a lekérdezés a sémát fogja visszaadni.
  ![](media/concept-attributes/schema1.png)
 
## <a name="next-steps"></a>Következő lépések 

- [Mi a kiépítés?](what-is-provisioning.md)
- [Mi az Azure AD Connect Cloud kiépítés?](what-is-cloud-provisioning.md)
