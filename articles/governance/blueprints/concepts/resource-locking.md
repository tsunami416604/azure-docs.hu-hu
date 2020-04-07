---
title: Az erőforrások zárolásának ismertetése
description: Ismerje meg az Azure Blueprints zárolási lehetőségeit az erőforrások védelme érdekében a tervezet hozzárendelésekén.
ms.date: 03/25/2020
ms.topic: conceptual
ms.openlocfilehash: 9c4e2f4c6fd8f5fb574002217ca71d1e7d130ff7
ms.sourcegitcommit: 642a297b1c279454df792ca21fdaa9513b5c2f8b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80676754"
---
# <a name="understand-resource-locking-in-azure-blueprints"></a>Az erőforrások zárolásának megismerése az Azure Blueprints-ben

A konzisztens környezetek létrehozása csak akkor igazán értékes, ha van egy mechanizmus, amely fenntartja ezt a konzisztenciát. Ez a cikk bemutatja, hogyan működik az erőforrás-zárolás az Azure Blueprints. Az erőforrás-zárolás és a _megtagadási hozzárendelések_alkalmazása példáját az [új erőforrások védelme](../tutorials/protect-new-resources.md) oktatóanyag című témakörben láthatja.

> [!NOTE]
> Az Azure Blueprints által üzembe helyezett erőforrás-zárolások csak a blueprint-hozzárendelés által üzembe helyezett erőforrásokra vonatkoznak. A meglévő erőforrások, például a már létező erőforráscsoportokban lévő erőforrásokhoz nem tartozik zárolások hozzá.

## <a name="locking-modes-and-states"></a>Zárolási módok és állapotok

A zárolási mód a tervezethozzárendelésre vonatkozik, és három lehetőség közül választhat: **Ne zároljon,** **csak olvasható**vagy ne törölje a **programot.** A zárolási mód a műtermék üzembe helyezése során van konfigurálva egy tervrajz-hozzárendelés során. A tervezethozzárendelés frissítésével egy másik zárolási mód állítható be.
A zárolási módok azonban nem módosíthatók az Azure Blueprints-en kívül.

A tervezethozzárendelésben összetevők által létrehozott erőforrások négy állapottal rendelkeznek: **Nincs zárolva,** **írásvédett**, **nem szerkeszthető / törölhető,** vagy nem **törölhető.** Minden műterméktípus lehet **nem zárolt** állapotban. Az alábbi táblázat az erőforrások állapotának meghatározására használható:

|Mód|Műtermék erőforrástípusa|Állapot|Leírás|
|-|-|-|-|
|Ne zárd be|*|Nincs zárolva|Az erőforrásokat az Azure Blueprints nem védi. Ez az állapot **írásvédett** vagy ne **törölje** az erőforráscsoport-összetevőt egy tervezet-hozzárendelésen kívülről hozzáadott erőforrásokhoz is.|
|Csak olvasási engedély|Erőforráscsoport|Nem lehet edinni / törölni|Az erőforráscsoport írásvédett, és az erőforráscsoport címkéi nem módosíthatók. **A nem zárolt** erőforrások hozzáadhatók, áthelyezhetők, módosíthatók vagy törölhetők ebből az erőforráscsoportból.|
|Csak olvasási engedély|Nem erőforráscsoport|Csak olvasási engedély|Az erőforrás semmilyen módon nem módosítható – nincs enek, és nem törölhetők.|
|Nincs törlés|*|Nem lehet törölni|Az erőforrások módosíthatók, de nem törölhetők. **A nem zárolt** erőforrások hozzáadhatók, áthelyezhetők, módosíthatók vagy törölhetők ebből az erőforráscsoportból.|

## <a name="overriding-locking-states"></a>A zárolási állapotok felülbírálása

Általában lehetséges, hogy az előfizetésben megfelelő [szerepköralapú hozzáférés-vezérléssel](../../../role-based-access-control/overview.md) (RBAC) rendelkező személy, például a "Tulajdonos" szerepkör, bármely erőforrás tanuskodhat, hogy módosítson vagy töröljön bármely erőforrást. Ez a hozzáférés nem így van, ha az Azure Blueprints egy üzembe helyezett hozzárendelés részeként zárolást alkalmaz. Ha a hozzárendelés az **Írásvédett vagy** a **Ne törlése** beállítással lett beállítva, még az előfizetés tulajdonosa sem tudja végrehajtani a letiltott műveletet a védett erőforráson.

Ez a biztonsági intézkedés védi a megadott tervterv konzisztenciáját és azt a környezetet, amelyet a véletlen vagy programozott törlésvagy módosítás létrehozásához terveztek.

### <a name="assign-at-management-group"></a>Hozzárendelés a felügyeleti csoportban

Egy további lehetőség, hogy megakadályozzák előfizetés-tulajdonosok eltávolítása egy tervezet-hozzárendelés, hogy rendelje hozzá a tervezetet egy felügyeleti csoporthoz. Ebben a forgatókönyvben csak a felügyeleti csoport **tulajdonosai** rendelkeznek a tervezet hozzárendelés eltávolításához szükséges engedélyekkel.

Ha a tervezetet egy felügyeleti csoporthoz szeretné hozzárendelni egy előfizetés helyett, a REST API-hívás a következőkhöz fog kinézni:

```http
PUT https://management.azure.com/providers/Microsoft.Management/managementGroups/{assignmentMG}/providers/Microsoft.Blueprint/blueprintAssignments/{assignmentName}?api-version=2018-11-01-preview
```

A felügyeleti csoport `{assignmentMG}` által meghatározott kell lennie, vagy a felügyeleti csoport hierarchiájában, vagy ugyanaz a felügyeleti csoport, ahol a tervezet definíciója menti.

A tervezet-hozzárendelés kérelemtörzse így néz ki:

```json
{
    "identity": {
        "type": "SystemAssigned"
    },
    "location": "eastus",
    "properties": {
        "description": "enforce pre-defined simpleBlueprint to this XXXXXXXX subscription.",
        "blueprintId": "/providers/Microsoft.Management/managementGroups/{blueprintMG}/providers/Microsoft.Blueprint/blueprints/simpleBlueprint",
        "scope": "/subscriptions/{targetSubscriptionId}",
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

A legfontosabb különbség ebben a kérelem törzsében, és `properties.scope` egy előfizetéshez rendelt a tulajdonság. Ezt a szükséges tulajdonságot be kell állítani arra az előfizetésre, amelyre a tervezet-hozzárendelés vonatkozik. Az előfizetésnek a felügyeleticsoport-hozzárendelés tárolására található felügyeleticsoport-hierarchia közvetlen gyermekének kell lennie.

> [!NOTE]
> A felügyeleti csoport hatóköréhez rendelt tervezet továbbra is előfizetési szintű tervezet-hozzárendelésként működik. Az egyetlen különbség az, ahol a tervezet hozzárendelés tárolja, hogy megakadályozza az előfizetés-tulajdonosok eltávolítása a hozzárendelés és a kapcsolódó zárak.

## <a name="removing-locking-states"></a>Zárolási állapotok eltávolítása

Ha szükségessé válik egy hozzárendelés által védett erőforrás módosítása vagy törlése, ezt kétféleképpen teheti meg.

- A tervrajz-hozzárendelés frissítése a **Nincs zárolás** zárolása üzemmódba
- A tervhozzárendelés törlése

A hozzárendelés eltávolításakor az Azure Blueprints által létrehozott zárolások törlődnek. Az erőforrás azonban hátramarad, és normál módon törölni kell.

## <a name="how-blueprint-locks-work"></a>A tervrajzok zárolásának munkálatai

Az RBAC [megtagadási hozzárendelések megtagadási](../../../role-based-access-control/deny-assignments.md) művelete a műtermék-erőforrásokra vonatkozik a tervtervezet hozzárendelése során, ha a hozzárendelés az **Írásvédett vagy** a **Ne törölje** beállítást választotta. A megtagadási művelet et a blueprint-hozzárendelés felügyelt identitása adja hozzá, és csak ugyanazzal a felügyelt identitással távolítható el a műtermék-erőforrásokból. Ez a biztonsági intézkedés kényszeríti a zárolási mechanizmust, és megakadályozza a tervezet zárolásának eltávolítását az Azure Blueprints-en kívül.

![Blueprint megtagadása hozzárendelés erőforráscsoport](../media/resource-locking/blueprint-deny-assignment.png)

Az egyes üzemmódok [megtagadási hozzárendelési tulajdonságai](../../../role-based-access-control/deny-assignments.md#deny-assignment-properties) a következők:

|Mód |Engedélyek.Műveletek |Permissions.Notactions |Igazgatók[i]. Típus |ExcludePrincipals[i]. Id | DoNotApplyToChildScopes |
|-|-|-|-|-|-|
|Csak olvasási engedély |**\*** |**\*/olvasás** |SystemDefined (Mindenki) |tervezet hozzárendelés és a felhasználó által meghatározott **kizártMegbízók** |Erőforráscsoport - _igaz_; Erőforrás - _hamis_ |
|Nincs törlés |**\*/törlés** | |SystemDefined (Mindenki) |tervezet hozzárendelés és a felhasználó által meghatározott **kizártMegbízók** |Erőforráscsoport - _igaz_; Erőforrás - _hamis_ |

> [!IMPORTANT]
> Az Azure Resource Manager legfeljebb 30 percig gyorsítótárazza a szerepkör-hozzárendelés részleteit. Ennek eredményeképpen a megtagadási hozzárendelések megtagadják a tervezet erőforrásokon végrehajtott műveleteket, nem feltétlenül lesznek azonnal teljes mértékben érvényben. Ebben az időszakban előfordulhat, hogy törölheti a tervrajzzárolásokkal védett erőforrást.

## <a name="exclude-a-principal-from-a-deny-assignment"></a>Megbízó kizárása megtagadási hozzárendelésből

Bizonyos tervezési vagy biztonsági forgatókönyvekben szükség lehet egy megbízó kizárására a tervezethozzárendelés által létrehozott [megtagadási hozzárendelésből.](../../../role-based-access-control/deny-assignments.md) Ez a lépés a REST API-ban úgy történik, hogy a hozzárendelés létrehozásakor legfeljebb öt értéket ad hozzá a **zárolási** tulajdonság **kizártPrincipals** [tömbjéhez.](/rest/api/blueprints/assignments/createorupdate) A következő hozzárendelés-definíció egy példa egy kérelemtörzsre, amely magában foglalja **a kizárt felhasználókat:**

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

## <a name="exclude-an-action-from-a-deny-assignment"></a>Művelet kizárása megtagadási hozzárendelésből

A [tervezet-hozzárendelés ben](../../../role-based-access-control/deny-assignments.md) a megtagadási hozzárendelés [főtag kizárásához](#exclude-a-principal-from-a-deny-assignment) hasonlóan kizárhat bizonyos [RBAC-műveleteket.](../../../role-based-access-control/resource-provider-operations.md) A **properties.locks** blokkon belül, ugyanazon a helyen, ahol **kizártPrincipals** van, egy **kizártMűveletek** adhatók hozzá:

```json
"locks": {
    "mode": "AllResourcesDoNotDelete",
    "excludedPrincipals": [
        "7be2f100-3af5-4c15-bcb7-27ee43784a1f",
        "38833b56-194d-420b-90ce-cff578296714"
    ],
    "excludedActions": [
        "Microsoft.ContainerRegistry/registries/push/write",
        "Microsoft.Authorization/*/read"
    ]
},
```

Míg **a kizárt felhasználók explicitnek** kell lenniük, a **kizártműveletek** bejegyzések az RBAC-műveletek helyettesítő karakteres egyeztetését is használhatják. `*`

## <a name="next-steps"></a>További lépések

- Kövesse az [új erőforrások védelme](../tutorials/protect-new-resources.md) oktatóanyagot.
- További információ a [tervterv életciklusáról.](lifecycle.md)
- Ismerje meg, hogyan kell statikus [és dinamikus paramétereket](parameters.md)használni.
- Ismerje meg a [tervezet szekvenálási sorrendjének testreszabását.](sequencing-order.md)
- További információ a [meglévő hozzárendelések frissítéséhez.](../how-to/update-existing-assignments.md)
- Az [általános hibaelhárítással](../troubleshoot/general.md)kapcsolatos tervtervezet ekhozzárendelése során fellépő problémák megoldása.