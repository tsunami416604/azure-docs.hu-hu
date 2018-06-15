---
title: Regisztrálja a bérlők a felhasználás-nyomkövetési Azure verem |} Microsoft Docs
description: Bérlői regisztráció és a bérlők van nyomon követésének módja az Azure-verem kezelésére szolgáló műveletek részleteit.
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
ms.date: 02/22/2018
ms.author: mabrigg
ms.reviewer: alfredo
ms.openlocfilehash: ef7ca59647a1f8c15d85c809609060a5945bedde
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32159111"
---
# <a name="manage-tenant-registration-in-azure-stack"></a>Bérlői regisztrációs Azure verem kezelése

*A következőkre vonatkozik: Azure verem integrált rendszerek*

A cikkben segítségével kezelhetők a bérlői regisztrációk és bérlői használat nyomon követésének módja műveletek részleteit. Adja hozzá a listában, vagy távolítsa el a bérlői leképezését részleteit is megtalálhatja. PowerShell vagy a számlázási API-végpontok segítségével nyomon követése kezelése.

## <a name="add-tenant-to-registration"></a>Adja hozzá a bérlő a regisztrációhoz

Ha hozzá szeretne adni egy új bérlőt a regisztrációban, így használatát készüljön jelentés, az Azure-előfizetés kapcsolódnak az Azure Active Directory (Azure AD) bérlői használja ezt a műveletet.

Használhatja ezt a műveletet, ha meg szeretné változtatni az előfizetése társítva van egy bérlő, hívása PUT/New-AzureRMResource újra. A régi leképezés a rendszer felülírja.

Vegye figyelembe, hogy csak egy Azure-előfizetés társítható egy bérlői. Ha egy második előfizetés hozzáadása egy meglévő bérlői kísérli meg, az első előfizetés, túlzott írásbeli. 


| Paraméter                  | Leírás |
|---                         | --- |
| registrationSubscriptionID | Az Azure-előfizetés, amely a kezdeti regisztráció lett megadva. |
| customerSubscriptionID     | Az Azure-előfizetés (nem Azure-verem) tartozó regisztrálni kell az ügyfél. Létre kell hozni a Felhőszolgáltató (CSP) lehetőség. A gyakorlatban ez azt jelenti, hogy Partner központon keresztül történik. Ha egy ügyfél több bérlői, ehhez az előfizetéshez kell létrehozni a bérlőt, jelentkezzen be Azure verem használható. |
| Erőforráscsoport              | Az erőforráscsoport, amely a regisztrációs tárolja az Azure-ban. |
| registrationName           | A regisztráció az Azure-készlet neve. Az Azure-ban tárolt objektum. A név általában a képernyő azurestack-cloudid megadása, cloudid megadása esetén a felhő azonosítója az Azure Alkalmazásveremben üzembe van. |

> [!Note]  
> Bérlők regisztrálva kell lennie az egyes Azure veremnek megfelelő használnak. Ha a bérlő több Azure-verem használ, módosítania a kezdeti regisztráció az adott környezet a bérlői előfizetéssel.

### <a name="powershell"></a>PowerShell

A New-AzureRmResource parancsmag használatával frissítse a regisztrációs erőforrás. Jelentkezzen be az Azure (`Add-AzureRmAccount`) használata a kezdeti regisztráció használt fiókkal. Itt látható egy példa a bérlő hozzáadása:

```powershell
  New-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01 -Properties
```

### <a name="api-call"></a>Az API-hívás

**A művelet**: PUT  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  /providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Válasz**: 201-es létrehozása  
**Választörzs**: üres  

## <a name="list-all-registered-tenants"></a>Minden regisztrált bérlők felsorolása

A regisztráció hozzáadott összes bérlők listájának lekérése.

 > [!Note]  
 > Ha nem a bérlők regisztrálva, nem kapott választ.

### <a name="parameters"></a>Paraméterek

| Paraméter                  | Leírás          |
|---                         | ---                  |
| registrationSubscriptionId | Az Azure-előfizetés, amely a kezdeti regisztráció lett megadva.   |
| Erőforráscsoport              | Az erőforráscsoport, amely a regisztrációs tárolja az Azure-ban.    |
| registrationName           | A regisztráció az Azure-készlet neve. Az Azure-ban tárolt objektum. A név általában formájában van **azurestack**-***cloudid megadása***, ahol ***cloudid megadása*** az Azure Alkalmazásveremben üzembe felhő azonosítója.   |

### <a name="powershell"></a>PowerShell

A Get-AzureRmResovurce parancsmag segítségével regisztrált összes bérlők. Jelentkezzen be az Azure (`Add-AzureRmAccount`) használata a kezdeti regisztráció használt fiókkal. Itt látható egy példa a bérlő hozzáadása:

```powershell
  Get-AzureRmResovurce -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Az API-hívás

A GET művelettel az összes bérlői hozzárendelések listáját kaphat

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

## <a name="remove-a-tenant-mapping"></a>Távolítsa el a bérlő leképezése

Eltávolíthatja a bérlő számára, a regisztráció hozzá lett adva. Ha bérlőre erőforrások Azure veremben továbbra is használja, a kezdeti Azure verem regisztrációhoz használt előfizetés díjfizetéssel használatát.

### <a name="parameters"></a>Paraméterek

| Paraméter                  | Leírás          |
|---                         | ---                  |
| registrationSubscriptionId | A regisztráció előfizetés-azonosító.   |
| Erőforráscsoport              | Az erőforráscsoport, a regisztráció.   |
| registrationName           | A regisztrációs neve.  |
| customerSubscriptionId     | A felhasználói előfizetés-azonosító.  |

### <a name="powershell"></a>PowerShell

```powershell
  Remove-AzureRmResource -ResourceId "subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/{customerSubscriptionId}" -ApiVersion 2017-06-01
```

### <a name="api-call"></a>Az API-hívás

Eltávolíthatja a bérlő leképezéseket a törlési művelet használatával.

**A művelet**: törlése  
**RequestURI**: `subscriptions/{registrationSubscriptionId}/resourceGroups/{resourceGroup}  
/providers/Microsoft.AzureStack/registrations/{registrationName}/customerSubscriptions/  
{customerSubscriptionId}?api-version=2017-06-01 HTTP/1.1`  
**Válasz**: 204 Nincs tartalma  
**Választörzs**: üres

## <a name="next-steps"></a>További lépések

 - Hogyan lehet lekérni az erőforrás-használati adatait az Azure oszlopból kapcsolatos további információkért lásd: [használati és számlázási Azure verem](/azure-stack-billing-and-chargeback.md).
