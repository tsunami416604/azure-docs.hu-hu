---
title: Az erőforrás-zárolás megismerése
description: Ismerje meg, hogy milyen zárolási lehetőségek védik az erőforrásokat a tervrajzok kiosztása során.
author: DCtheGeek
ms.author: dacoulte
ms.date: 04/24/2019
ms.topic: conceptual
ms.service: blueprints
ms.openlocfilehash: 5c62fdb698dddf293d339904fd0c854052d636eb
ms.sourcegitcommit: d7689ff43ef1395e61101b718501bab181aca1fa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2019
ms.locfileid: "71981050"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Az erőforrások zárolásának megismerése az Azure-tervekben

A konzisztens környezetek nagy méretekben történő létrehozása csak abban az esetben hasznos, ha van olyan mechanizmus, amely a konzisztencia fenntartására szolgál. Ez a cikk azt ismerteti, hogyan működik az erőforrás-zárolás az Azure-tervekben. Az erőforrás-zárolásra és a _megtagadási hozzárendelések_alkalmazására vonatkozó példa az [új erőforrások védelme](../tutorials/protect-new-resources.md) oktatóanyagban található.

## <a name="locking-modes-and-states"></a>Zárolási módok és állapotok

A zárolási mód a terv-hozzárendelésre vonatkozik, és három lehetőség közül választhat: **Ne zárja**be, **csak olvasás**vagy ne **törölje**. A zárolási módot a rendszer a terv hozzárendelése során konfigurálja az összetevő üzembe helyezése során. A terv hozzárendelésének frissítésével egy másik zárolási mód is beállítható.
A zárolási módok azonban nem változtathatók meg a tervrajzokon kívül.

A tervrajz-hozzárendelésekben az összetevők által létrehozott erőforrások négy állapottal rendelkeznek: **Nincs zárolva**, **csak olvasható**, **nem szerkeszthető/törölhető**, vagy **nem törölhető**. Az egyes összetevők típusa **nem zárolt** állapotban lehet. Az alábbi táblázat egy erőforrás állapotának meghatározására használható:

|Mód|Összetevő típusú erőforrástípus|State|Leírás|
|-|-|-|-|
|Ne legyen zárolás|*|Nincs zárolva|Az erőforrásokat nem a tervrajzok védik. Ezt az állapotot az **írásvédett** erőforráshoz hozzáadott erőforrások, vagy az erőforráscsoport-összetevő **nem törölhető** a terv-hozzárendelésen kívül is használják.|
|Csak olvasható|Resource group|Nem lehet szerkeszteni/törölni|Az erőforráscsoport írásvédett, és az erőforráscsoport címkéi nem módosíthatók. A **nem zárolt** erőforrások hozzáadhatók, áthelyezhetők, módosíthatók vagy törölhetők ebből az erőforráscsoporthoz.|
|Csak olvasható|Nem erőforráscsoport|Csak olvasható|Az erőforrás semmilyen módon nem módosítható – nem módosítható, és nem törölhető.|
|Nem törölhető|*|Nem lehet törölni|Az erőforrások módosíthatók, de nem törölhetők. A **nem zárolt** erőforrások hozzáadhatók, áthelyezhetők, módosíthatók vagy törölhetők ebből az erőforráscsoporthoz.|

## <a name="overriding-locking-states"></a>Zárolási állapotok felülbírálása

Általában előfordulhat, hogy valaki megfelelő [szerepköralapú hozzáférés-vezérléssel](../../../role-based-access-control/overview.md) (RBAC) rendelkezik az előfizetésben, például a "tulajdonos" szerepkört, amely lehetővé teszi az erőforrások módosítását vagy törlését. Ez a hozzáférés nem vonatkozik arra az esetre, amikor a tervrajzok egy üzembe helyezett hozzárendelés részeként a zárolást alkalmazza. Ha a hozzárendelés **csak olvasási** vagy nem **törlési** beállítással lett beállítva, akkor még az előfizetés tulajdonosa is elvégezheti a letiltott műveletet a védett erőforráson.

Ez a biztonsági mérték védi a definiált terv és az olyan környezet egységességét, amelyet véletlen vagy programozott törlés vagy módosítás alapján hoztak létre.

## <a name="removing-locking-states"></a>Zárolási állapotok eltávolítása

Ha szükség lesz egy hozzárendelés által védett erőforrás módosítására vagy törlésére, kétféleképpen teheti meg.

- A terv-hozzárendelés frissítése a **nem zárolt** zárolási módba
- A terv hozzárendelésének törlése

A hozzárendelés eltávolításakor a tervrajzok által létrehozott zárolások törlődnek. Az erőforrás azonban marad, és a szokásos módon törölni kell.

## <a name="how-blueprint-locks-work"></a>A terv zárolásának működése

Egy RBAC [megtagadási hozzárendelések](../../../role-based-access-control/deny-assignments.md) megtagadási művelete a terv hozzárendelése során az összetevő-erőforrásokra vonatkozik, ha a hozzárendelés a **csak olvasható** vagy a **nem törlés** lehetőséget választotta. A megtagadási műveletet a terv-hozzárendelés felügyelt identitása adja hozzá, és csak azonos felügyelt identitással lehet eltávolítani az összetevő-erőforrásokból. Ez a biztonsági mérték kikényszeríti a zárolási mechanizmust, és megakadályozza a terveken kívüli tervezetek zárolásának eltávolítását.

![Az erőforráscsoport hozzárendelésének megtagadása](../media/resource-locking/blueprint-deny-assignment.png)

Az egyes üzemmódok [megtagadási hozzárendeléseinek tulajdonságai](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) a következők:

|Mód |Engedélyek. műveletek |Engedélyek. Tapintatok |Principals[i].Type |ExcludePrincipals [i]. ID | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Csak olvasható |**\*** |**@no__t – 1/olvasás** |SystemDefined (mindenki) |tervezet-hozzárendelés és felhasználó által definiált **excludedPrincipals** |Erőforráscsoport – _igaz_; Erőforrás – _hamis_ |
|Nem törölhető |**@no__t – 1/delete** | |SystemDefined (mindenki) |tervezet-hozzárendelés és felhasználó által definiált **excludedPrincipals** |Erőforráscsoport – _igaz_; Erőforrás – _hamis_ |

> [!IMPORTANT]
> A Azure Resource Manager legfeljebb 30 percig gyorsítótárazza a szerepkör-hozzárendelés részleteit. Ennek eredményeképpen a hozzárendelések megtagadására vonatkozó művelet megtagadása a terv erőforrásaiban nem lehet azonnal teljes mértékben érvényben. Ebben az időszakban lehetséges lehet egy olyan erőforrás törlése, amely a terv zárolásával védhető.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Tag kizárása egy megtagadási hozzárendelésből

Bizonyos tervezési vagy biztonsági helyzetekben szükség lehet egy rendszerbiztonsági tag kizárására a terv-hozzárendelés által létrehozott [megtagadási hozzárendelésből](../../../role-based-access-control/deny-assignments.md) . Ezt REST API úgy végezheti el, hogy a [hozzárendelés létrehozásakor](/rest/api/blueprints/assignments/createorupdate)legfeljebb öt értéket ad hozzá a **excludedPrincipals** tömbhöz a **zárolások** tulajdonságban.
Ez egy példa egy kérelem törzsére, amely tartalmazza a **excludedPrincipals**:

```json
{
  "identity": {
    "type": "SystemAssigned"
  },
  "location": "eastus",
  "properties": {
    "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
    "blueprintId": "/providers/Microsoft.Management/managementGroups/{mgId}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
    "locks": {
        "mode": "AllResourcesDoNotDelete",
        "excludedPrincipals": [
            "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
            "38833b56-194d-420b-90ce-cff578296714"
        ]
    },
    "parameters": {
      "storageAccountType": {
        "value": "Standard_LRS"
      },
      "costCenter": {
        "value": "Contoso/Online/Shopping/Production"
      },
      "owners": {
        "value": [
          "johnDoe@contoso.com",
          "johnsteam@contoso.com"
        ]
      }
    },
    "resourceGroups": {
      "storageRG": {
        "name": "defaultRG",
        "location": "eastus"
      }
    }
  }
}
```

## <a name="next-steps"></a>További lépések

- Kövesse az [új erőforrások védelemmel](../tutorials/protect-new-resources.md) foglalkozó oktatóanyagot.
- Tudnivalók a [tervek életciklusáról](lifecycle.md).
- A [statikus és dinamikus paraméterek](parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](sequencing-order.md) testreszabásának elsajátítása.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).