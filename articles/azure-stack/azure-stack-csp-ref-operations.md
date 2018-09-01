---
title: Regisztrálja a bérlők a használat nyomon követése az Azure Stackben |} A Microsoft Docs
description: Bérlői regisztráció, és hogyan Azure Stack bérlő használat nyomon követése kezelésére szolgáló műveletek részleteit.
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.service: azure-stack
ms.workload: na
pms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 06/08/2018
ms.author: brenduns
ms.reviewer: alfredo
ms.openlocfilehash: ce226bb34c5ff8a7ea7dc44d8428da2bb09e25e5
ms.sourcegitcommit: a3a0f42a166e2e71fa2ffe081f38a8bd8b1aeb7b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/01/2018
ms.locfileid: "43382382"
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Az Azure Stack bérlő regisztrációs kezelése

*A következőkre vonatkozik: Azure Stackkel integrált rendszerek*

Ez a cikk segítségével kezelheti a bérlő regisztrációit, és hogyan zajlik a bérlői használat műveletek részleteit tartalmazza. Adja hozzá a listában, vagy távolítsa el a bérlő leképezések részleteit is megtalálhatja. Használhatja PowerShell vagy a számlázási API-végpontokat kezelheti a használat nyomon követése.

## <a name="add-tenant-to-registration"></a>A regisztrációhoz bérlő hozzáadása

Használja ezt a műveletet, ha meg szeretné hozzáadása egy új bérlőt a regisztrációt, így a használatuk jelentett csatlakozik az Azure Active Directory (Azure AD) bérlő az Azure-előfizetés alatt.

Használhatja ezt a műveletet is, ha meg szeretné változtatni az előfizetés a bérlőhöz tartozó, PUT és New-AzureRMResource meghívhatja újra. A rendszer felülírja a régi leképezést.

Vegye figyelembe, hogy csak egy Azure-előfizetéssel is társítható egy bérlőt. Ha előfizetést szeretne hozzáadni egy második meglévő bérlő, az első előfizetés túlterhelt írásos. 

### <a name="use-api-profiles"></a>API-profilok használatához

A parancsmagok, ez a cikk a szükséges, hogy egy API-profilt, adja meg, amikor futtatja a Powershellt. API-profilok az Azure erőforrás-szolgáltatók és az API-verziók csoportját képviselik. Ezek segítenek az API-t a megfelelő verzióját használja, ha használata több Azure-felhők, például az Azure és az Azure Stack globális használatakor. Profilok olyan nevet, amely megfelel a kiadási dátum szerint vannak megadva. Az ebben a cikkben azt kell használnia a **2017-09-03** profilt.

Az Azure Stacket és API-profilokkal kapcsolatos további információkért lásd: [kezelése API-verzióprofilok az Azure Stackben](user/azure-stack-version-profiles.md). És a PowerShell API-profillal kapcsolatos utasításokért lásd: [használata API-verzióprofilok az Azure Stack PowerShell](user/azure-stack-version-profiles-powershell.md).

### <a name="parameters"></a>Paraméterek

| Paraméter                  | Leírás |
|---                         | --- |
| registrationSubscriptionID | Az Azure-előfizetés, amely a kezdeti regisztráció lett megadva. |
| customerSubscriptionID     | Az Azure-előfizetést (nem az Azure Stack) regisztrálni kell az ügyfélhez tartozó. Létre kell hozni a Felhőszolgáltató (CSP) szerződéssel. A gyakorlatban ez azt jelenti, hogy Partner Centeren keresztül. Ha egy ügyfél több bérlő rendelkezik, a bérlő Azure Stack-ba való bejelentkezéshez használt ebben az előfizetésben kell létrehozni. |
| Erőforráscsoport              | Az erőforráscsoport az Azure-ban, amely tárolja a regisztrációt. |
| registrationName           | Az Azure Stack-regisztrációk neve. Az Azure-ban tárolt objektum. A név általában az űrlap azurestack-CloudID, ahol CloudID-e az Azure Stack üzembe helyezés a Felhőbeli azonosító van. |

> [!Note]  
> A bérlőknek kell regisztrálni kell az egyes Azure Stack használata. Ha egy bérlőt használ több Azure Stack, frissítenie a kezdeti regisztrációkat az egyes telepítések a bérlői előfizetéssel.

### <a name="powershell"></a>PowerShell

A New-AzureRmResource-parancsmag használatával a regisztrációs erőforrás frissítése. Jelentkezzen be az Azure-ba (`Add-AzureRmAccount`) a kezdeti regisztráció során használt fiókkal. A következő példa bemutatja, hogyan adja hozzá a bérlőt:

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>API-hívás

**A művelet**: PUT  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Válasz**: 201-es létrehozása  
**Választörzs**: üres  

## <a name="list-all-registered-tenants"></a>Az összes regisztrált bérlők a listában

Egyetlen bérlő számára, amely egy regisztrációs lettek hozzáadva listájának beolvasása.

 > [!Note]  
 > Ha nem-bérlő regisztrálva, akkor nem kap választ.

### <a name="parameters"></a>Paraméterek

| Paraméter                  | Leírás          |
|---                         | ---                  |
| registrationSubscriptionId | Az Azure-előfizetés, amely a kezdeti regisztráció lett megadva.   |
| Erőforráscsoport              | Az erőforráscsoport az Azure-ban, amely tárolja a regisztrációt.    |
| registrationName           | Az Azure Stack-regisztrációk neve. Az Azure-ban tárolt objektum. A név általában formájában **azurestack**-***CloudID***, ahol ***CloudID*** Cloud az Azure Stack üzemelő példány azonosítója.   |

### <a name="powershell"></a>PowerShell

A Get-AzureRmResource parancsmaggal listázhatja az összes regisztrált bérlők. Jelentkezzen be az Azure-ba (`Add-AzureRmAccount`) a kezdeti regisztráció során használt fiókkal. A következő példa bemutatja, hogyan adja hozzá a bérlőt:

```powershell
  Get-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API-hívás

A GET művelettel az összes bérlő leképezés listát kap

**A művelet**: beolvasása  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions?  
api-version=2017-06-01 HTTP/1.1`  
**Válasz**: 200  
**Választörzs**: 

```JSON  
{
    "value": [{
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 1}”,
            "name": " cspSubscriptionId 1",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId1" }
        },
        {
            "id": " subscriptions/{subscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{ cspSubscriptionId 2}”,
            "name": " cspSubscriptionId2 ",
            "type": “Microsoft.AzureStack\customerSubscriptions”,
            "properties": { "tenantId": "tId2" }
        }
    ],
    "nextLink": "{originalRequestUrl}?$skipToken={opaqueString}"
}
```

## <a name="remove-a-tenant-mapping"></a>Egy bérlő leképezés eltávolítása

Egy bérlő, amely hozzá van adva egy regisztrációs távolíthatja el. A bérlői továbbra is az erőforrások felhasználását az Azure Stacken, ha azok használatáért a kezdeti Azure Stack-regisztráció használt előfizetés.

### <a name="parameters"></a>Paraméterek

| Paraméter                  | Leírás          |
|---                         | ---                  |
| registrationSubscriptionId | A regisztráció előfizetés-azonosítója.   |
| Erőforráscsoport              | Az erőforráscsoport, a regisztráció.   |
| registrationName           | A regisztráció neve.  |
| customerSubscriptionId     | Az ügyfél-előfizetés azonosítóját.  |

### <a name="powershell"></a>PowerShell

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>API-hívás

Bérlő leképezések a törlési művelet használatával távolíthatja el.

**A művelet**: törlése  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Válasz**: 204 Nincs tartalom  
**Választörzs**: üres

## <a name="next-steps"></a>További lépések

 - Erőforrás-használati adatok lekérése az Azure Stack kapcsolatos további tudnivalókért lásd: [használat és számlázás az Azure Stackben](azure-stack-billing-and-chargeback.md).
