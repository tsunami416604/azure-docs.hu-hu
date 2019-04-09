---
title: Erőforrás zárolása ismertetése
description: Ismerje meg az erőforrások védelme a tervezet hozzárendelésekor zárolási beállítások.
author: DCtheGeek
ms.author: dacoulte
ms.date: 03/28/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.custom: seodec18
ms.openlocfilehash: 232d823f364f9f98d1da1bade50ba369b898a57d
ms.sourcegitcommit: 62d3a040280e83946d1a9548f352da83ef852085
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/08/2019
ms.locfileid: "59275929"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Erőforrás zárolása az Azure-tervek ismertetése

A nagy mennyiségű egységes környezetet létrehozása csak akkor valóban a legértékesebb, ha olyan mechanizmus, amellyel a konzisztencia fenntartása. Ez a cikk ismerteti, hogyan erőforrás zárolása az Azure-tervek. Lásd például erőforrás zárolása és az alkalmazás _hozzárendelések megtagadása_, tekintse meg a [új erőforrások védelme](../tutorials/protect-new-resources.md) oktatóanyag.

## <a name="locking-modes-and-states"></a>Zárolási mód és állapotok

Zárolási mód vonatkozik a tervezet-hozzárendelést, és azt a három pontot tartalmaz: **Ne zárolja**, **csak olvasható**, vagy **ne törölje a**. A zárolási mód összetevő üzembe helyezése során a tervezet-hozzárendelés során van konfigurálva. A tervezet-hozzárendelés frissítése egy másik zárolási mód is beállítható.
Zárolás módok, azonban nem módosítható tervezetek kívül.

A tervezet-hozzárendelést az összetevők által létrehozott erőforrások négy állapota van: **Sok mindent megváltoztathat**, **csak olvasható**, **nem Szerkesztés / Törlés**, vagy **nem lehet törölni**. Minden egyes összetevő típusa lehet a **nincs zárolva** állapota. Erőforrás állapotának megállapítása a következő táblázat használható:

|Mód|Összetevő erőforrás típusa|Állapot|Leírás|
|-|-|-|-|
|Nincs zárolás|*|Nincs zárolva|Tervezetek nem védett erőforrásokat. Ebben az állapotban is szolgál az erőforrások hozzáadott egy **csak olvasható** vagy **ne törölje a** erőforrás csoport összetevő a tervezet-hozzárendelést kívül.|
|Csak olvasási engedély|Erőforráscsoport|Cannot Edit / Delete|Az erőforráscsoport csak olvasható, és címkéket az erőforráscsoport nem módosítható. **Sok mindent megváltoztathat** erőforrások hozzáadva, áthelyezték, módosítható, vagy ez az erőforráscsoport törölve.|
|Csak olvasási engedély|Nem-erőforráscsoport|Csak olvasási engedély|Az erőforrás nem módosítható bármilyen módon – nem végez módosítást, és nem törölhető.|
|Do Not Delete|*|Cannot Delete|Az erőforrások is módosítható, de nem lehet törölni. **Sok mindent megváltoztathat** erőforrások hozzáadva, áthelyezték, módosítható, vagy ez az erőforráscsoport törölve.|

## <a name="overriding-locking-states"></a>Zárolási állapotok felülbírálása

Általában lehetséges, ha valaki megfelelő [szerepköralapú hozzáférés-vezérlés](../../../role-based-access-control/overview.md) (RBAC) az előfizetésben, például a "Tulajdonos" szerepkört kell lehetővé tenni, hogy megváltoztatni vagy bármely erőforrás törlése. Ez a hozzáférés nem ez a helyzet, ha tervezetek érvényes részeként üzembe helyezett hozzárendelés zárolását. Ha a hozzárendelés állították be az a **csak olvasható** vagy **ne törölje** lehetőséget, még az előfizetés tulajdonosa a letiltott művelet hajtható végre, a védett erőforrás.

Ez a biztonsági intézkedés védi a konzisztencia, a megadott tervezet és a környezet létrehozása a véletlen vagy programozott törlés vagy az megváltoztatására úgy lett kialakítva.

## <a name="removing-locking-states"></a>Zárolási állapotok eltávolítása

Ha módosítja vagy törli a hozzárendelés által védett erőforrás szükségessé válik, két módon ennek a végrehajtására.

- A tervezet-hozzárendelés frissítése a zárolási mód **nem zárolása**
- A tervezet-hozzárendelés törlése

A hozzárendelés eltávolítása után a rendszer eltávolítja a tervek szerint létrehozott zárolása. Azonban az erőforrás marad, és normál módon törölni kell.

## <a name="how-blueprint-locks-work"></a>Hogyan tervezet zárolja a munka

Az RBAC [hozzárendelések megtagadása](../../../role-based-access-control/deny-assignments.md) megtagadási művelet során mikor lesz alkalmazva összetevő erőforrások a tervezet-hozzárendelést a hozzárendelés választásakor a **csak olvasható** vagy **ne törölje a** a beállítás. A megtagadási művelet által felügyelt identitását a tervezet-hozzárendelést kerülnek, és csak eltávolíthatók az összetevő-erőforrások által a azonos felügyelt identitás. Ez biztonsági okokból a zárolási mechanizmus kikényszeríti, és megakadályozza, hogy a tervek kívül a tervezet zárolás eltávolítása.

![Tervezet erőforráscsoport-hozzárendelés elutasítása](../media/resource-locking/blueprint-deny-assignment.png)

> [!IMPORTANT]
> Az Azure Resource Manager akár 30 percig gyorsítótárazza a szerepkör-hozzárendelés részletei. Ennek eredményeképpen a megtagadási hozzárendelések megtagadási művelet a tervezet-erőforrások nem azonnal el teljes körű érvénybe. Ez idő alatt, előfordulhat, hogy lehet törölni a tervezet zárolások fogja védeni kívánt erőforrás.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Egy egyszerű kizárása Megtagadás hozzárendelésekor

Néhány tervezési vagy a biztonsági forgatókönyvek lehet egy egyszerű, ki kell zárni a [hozzárendelés megtagadása](../../../role-based-access-control/deny-assignments.md) a tervezet-hozzárendelést hoz létre. Ez történik REST API-ban, akár öt érték hozzáadásával a **excludedPrincipals** Pole a **zárolások** tulajdonság amikor [a hozzárendelés létrehozása](/rest/api/blueprints/assignments/createorupdate).
Ez a példa, amely tartalmazza a kérés törzse **excludedPrincipals**:

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

- Kövesse a [új erőforrások védelmét](../tutorials/protect-new-resources.md) oktatóanyag.
- Tudnivalók a [tervek életciklusáról](lifecycle.md).
- A [statikus és dinamikus paraméterek](parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](sequencing-order.md) testreszabásának elsajátítása.
- A [meglévő hozzárendelések frissítésének](../how-to/update-existing-assignments.md) elsajátítása.
- A tervek hozzárendelése során felmerülő problémák megoldása [általános hibaelhárítással](../troubleshoot/general.md).