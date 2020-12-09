---
title: Bejövő szinkronizálás a felhőalapú kiépítés MS Graph API használatával
description: Ez a témakör azt ismerteti, hogyan engedélyezheti a bejövő szinkronizálást csak a Graph API használatával
services: active-directory
author: billmath
manager: daveba
ms.service: active-directory
ms.workload: identity
ms.topic: conceptual
ms.date: 12/04/2020
ms.subservice: hybrid
ms.author: billmath
ms.collection: M365-identity-device-management
ms.openlocfilehash: f308f46fc021a1d08f4065d48558a6dd71786c7c
ms.sourcegitcommit: 21c3363797fb4d008fbd54f25ea0d6b24f88af9c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2020
ms.locfileid: "96860355"
---
# <a name="inbound-synchronization-for-cloud-provisioning-using-ms-graph-api"></a>Bejövő szinkronizálás a felhőalapú kiépítés MS Graph API használatával

Az alábbi dokumentum azt ismerteti, hogyan replikálhat egy szinkronizációs profilt a semmiből kizárólag MSGraph API-k használatával.  
Ennek a folyamatnak a szerkezete a következő lépésekből áll.  Ezek a következők:

- [Alapszintű beállítás](#basic-setup)
- [Egyszerű szolgáltatás létrehozása](#create-service-principals)
- [Szinkronizálási feladatok létrehozása](#create-sync-job)
- [Célként megadott tartomány frissítése](#update-targeted-domain)
- [Szinkronizálási feladatok indítása](#start-sync-job)
- [Felülvizsgálat állapota](#review-status)

Ezekkel a [Microsoft Azure Active Directory modullal Windows PowerShell](https://docs.microsoft.com/powershell/module/msonline/) -parancsokkal engedélyezheti a szinkronizálást egy éles bérlő számára, amely az adott bérlő adminisztrációs webszolgáltatásának meghívásához szükséges előfeltételek egyike.

## <a name="basic-setup"></a>Alapszintű beállítás

### <a name="enable-tenant-flags"></a>Bérlői jelzők engedélyezése

 ```PowerShell
 Connect-MsolService ('-AzureEnvironment <AzureEnvironmnet>')
 Set-MsolDirSyncEnabled -EnableDirSync $true
 ```
A két parancs első részében Azure Active Directory hitelesítő adatokat kell megadnia. Ezek a parancsmagok implicit módon azonosítják a bérlőt, és lehetővé teszik a szinkronizálást.

## <a name="create-service-principals"></a>Szolgáltatásnevek létrehozása
Ezután létre kell hoznia a [AD2AAD alkalmazást/szolgáltatásnevet](https://docs.microsoft.com/graph/apiapplicationtemplate-instantiate?view=graph-rest-beta&tabs=http)

Ezt az azonosítót kell használnia a 1a4721b3-e57f-4451-ae87-ef078703ec94. A displayName az AD-tartomány URL-címe, ha a portálon van használatban (például contoso.com), de lehet, hogy más nevet kapott.

 ```
 POST https://graph.microsoft.com/beta/applicationTemplates/1a4721b3-e57f-4451-ae87-ef078703ec94/instantiate
 Content-type: application/json
 {
    displayName: [your app name here]
 }
 ```


## <a name="create-sync-job"></a>Szinkronizálási feladatok létrehozása
A fenti parancs kimenete a létrehozott egyszerű szolgáltatásnév objectId adja vissza. Ebben a példában a objectId a 614ac0e9-a59b-481f-bd8f-79a73d167e1c.  A Microsoft Graph használatával adjon hozzá egy synchronizationJob az adott egyszerű szolgáltatáshoz.  

A szinkronizálási feladatok létrehozásával kapcsolatos dokumentáció [itt](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-post?view=graph-rest-beta&tabs=http)található.

Ha a fenti azonosítót nem rögzíti, az egyszerű szolgáltatásnév a következő MS Graph-hívás futtatásával található. Szüksége lesz a könyvtár. Read. All engedélyekre, hogy a hívást tegye:
 
 `GET https://graph.microsoft.com/beta/servicePrincipals `

Ezután keresse meg az alkalmazás nevét a kimenetben.

Futtassa a következő két parancsot két feladat létrehozásához: egyet a felhasználó/csoport kiépítés számára, egyet pedig a jelszó-kivonatolás szinkronizálásához. Ugyanez a kérés kétszer, de különböző sablon-azonosítókkal van ellátva.


Hívja meg a következő két kérelmet:

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADProvisioning"
 } 
 ```

 ```
 POST https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs
 Content-type: application/json
 {
 "templateId":"AD2AADPasswordHash"
 }
 ```

Ha mindkettőt létre szeretné hozni, két hívásra lesz szüksége.

Példa visszatérési értékre (kiépítés esetén):

 ```
HTTP 201/Created
{
    "@odata.context": "https://graph.microsoft.com/beta/$metadata#servicePrincipals('614ac0e9-a59b-481f-bd8f-79a73d167e1c')/synchronization/jobs/$entity",
    "id": "AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da",
    "templateId": "ADDCInPassthrough",
    "schedule": {
        "expiration": null,
        "interval": "PT40M",
        "state": "Disabled"
    },
    "status": {
        "countSuccessiveCompleteFailures": 0,
        "escrowsPruned": false,
        "code": "Paused",
        "lastExecution": null,
        "lastSuccessfulExecution": null,
        "lastSuccessfulExecutionWithExports": null,
        "quarantine": null,
        "steadyStateFirstAchievedTime": "0001-01-01T00:00:00Z",
        "steadyStateLastAchievedTime": "0001-01-01T00:00:00Z",
        "troubleshootingUrl": null,
        "progress": [],
        "synchronizedEntryCountByType": []
    }
}
```

## <a name="update-targeted-domain"></a>Célként megadott tartomány frissítése
Ehhez a bérlőhöz az objektumazonosító és az egyszerű szolgáltatásnév alkalmazásának azonosítója a következő:

ObjectId: 8895955e-2e6c-4d79-8943-4d72ca36878f AppId: 00000014-0000-0000-C000-000000000000 DisplayName: testApp

Frissíteni kell azt a tartományt, amelyet ez a konfiguráció céloz, ezért frissítse a tartományhoz tartozó titkokat.

Győződjön meg arról, hogy a használt tartománynév megegyezik-e a helyszíni tartományvezérlőhöz beállított URL-címmel

 ```
 PUT https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/secrets
 Content-type: application/json
 {
  "value": [
    {
      "key": "Domain",
       {"value":[{"key":"Domain","value":"{\"domain\":\"[YOUR_DOMAIN_NAME]\"}"}]}
    }
  ]
 }
 ```

A várt válasz:... HTTP 204/nincs tartalom

Itt a Kiemelt "tartomány" érték annak a helyszíni Active Directory tartománynak a neve, amelyből a bejegyzéseket Azure Active Directory kell kiépíteni.

## <a name="start-sync-job"></a>Szinkronizálási feladatok indítása
A feladatot újra lekérheti a következő parancs használatával:

 `GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ ` 

A feladatok beolvasására vonatkozó dokumentáció [itt](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-list?view=graph-rest-beta&tabs=http)található. 
 
A művelet elindításához adja ki ezt a kérelmet az első lépésben létrehozott egyszerű szolgáltatásnév objectId, és a feladatot létrehozó kérelemből visszaadott feladathoz tartozó azonosító.

A feladatok elindításához szükséges dokumentáció [itt](https://docs.microsoft.com/graph/api/synchronization-synchronizationjob-start?view=graph-rest-beta&tabs=http)található. 

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/start
 ```

A várt válasz:... HTTP 204/nincs tartalom.

A feladatok szabályozására szolgáló többi parancsot [itt](https://docs.microsoft.com/graph/api/resources/synchronization-synchronizationjob?view=graph-rest-beta)dokumentáljuk.
 
A feladatok újraindításához az egyik a következő lesz:...

 ```
 POST  https://graph.microsoft.com/beta/servicePrincipals/8895955e-2e6c-4d79-8943-4d72ca36878f/synchronization/jobs/AD2AADProvisioning.fc96887f36da47508c935c28a0c0b6da/restart
 {
   "criteria": {
       "resetScope": "Full"
   }
 }
 ```

## <a name="review-status"></a>Felülvizsgálat állapota
A feladatok állapotának lekérése a-on keresztül...

 ```
 GET https://graph.microsoft.com/beta/servicePrincipals/[SERVICE_PRINCIPAL_ID]/synchronization/jobs/ 
 ```

A megfelelő részletekért tekintse meg a Return objektum "status" szakaszát.

## <a name="next-steps"></a>Következő lépések 

- [Mi az az Azure AD Connect felhőalapú jogosultságkiosztás?](what-is-cloud-provisioning.md)
- [Átalakítások](how-to-transformation.md)
- [Azure AD szinkronizációs API](https://docs.microsoft.com/graph/api/resources/synchronization-overview?view=graph-rest-beta)
