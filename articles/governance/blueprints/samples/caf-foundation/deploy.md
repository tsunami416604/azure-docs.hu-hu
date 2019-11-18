---
title: A CAF Foundation tervezetének mintája – üzembe helyezési lépések
description: A CAF Foundation Blueprint-minta üzembe helyezésének lépései, beleértve a Blueprint-összetevők paraméterének részleteit.
ms.date: 08/20/2019
ms.topic: sample
ms.openlocfilehash: afb7b19a2e5ad1f1b115526ac85d63a724101f9b
ms.sourcegitcommit: 2d3740e2670ff193f3e031c1e22dcd9e072d3ad9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/16/2019
ms.locfileid: "74128704"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-foundation-blueprint-sample"></a>Az Azure Foundation Blueprint minta Microsoft Cloud bevezetési keretrendszerének üzembe helyezése

Az Azure (CAF) Foundation tervhez készült Microsoft Cloud bevezetési keretrendszer üzembe helyezéséhez a következő lépéseket kell elvégeznie:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **közzétettként**
> - A terv másolatának kiosztása meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először is implementálja a terv mintáját úgy, hogy létrehoz egy új tervet a környezetben a minta kezdőként való használatával.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán. Keresse meg és válassza ki a **tervrajzokat**.

1. A bal oldali **első lépések** lapon kattintson a **Létrehozás** gombra a _terv létrehozása_területen.

1. Keresse meg a **CAF Foundation** tervezet mintát _más minták_ alatt, és válassza a **minta használata**lehetőséget.

1. Adja meg a tervezet mintájának _alapjait_ :

   - **Terv neve**: adjon meg egy nevet a CAF Foundation tervezet mintájának másolatához.
   - **Definíció helye**: használja a három pontot, és válassza ki a felügyeleti csoportot a minta másolatának mentéséhez.

1. Válassza ki az oldal tetején található _összetevők fület_ , vagy a **következőt:** összetevők az oldal alján.

1. Tekintse át a terv mintáját alkotó összetevők listáját. Számos összetevőhöz vannak olyan paraméterek, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor befejezte a tervezet mintájának áttekintését.

## <a name="publish-the-sample-copy"></a>A minta másolatának közzététele

A terv mintájának másolata már létre lett hozva a környezetében. A rendszer **Piszkozat** módban jön létre, és **közzé** kell tenni ahhoz, hogy hozzá lehessen rendelni és telepíteni lehessen. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás a CAF Foundation tervből is áthelyezhető.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán. Keresse meg és válassza ki a **tervrajzokat**.

1. Válassza a bal oldali **terv-definíciók** lapot. A szűrők használatával megkeresheti a tervezet mintájának másolatát, majd kiválaszthatja.

1. Válassza a **terv közzététele** lehetőséget az oldal tetején. A jobb oldalon található új lapon adjon meg egy **verziót** a tervezet mintájának másolatához. Ez a tulajdonság akkor hasznos, ha később módosítja a módosítást. Adjon meg olyan **módosítási megjegyzéseket** , mint például az "első verzió, amelyet a CAF Foundation Blueprint-minta közzétett." Ezután válassza a **Közzététel** elemet az oldal alján.

## <a name="assign-the-sample-copy"></a>A minta másolatának kiosztása

Miután a tervezet mintájának **közzététele**sikeresen megtörtént, hozzárendelhető egy előfizetéshez, amely a felügyeleti csoporton belül lett mentve. Ezzel a lépéssel megadhatja, hogy az egyes központi telepítések egyediek legyenek-e.

1. Válassza a **minden szolgáltatás** lehetőséget a bal oldali ablaktáblán. Keresse meg és válassza ki a **tervrajzokat**.

1. Válassza a bal oldali **terv-definíciók** lapot. A szűrők használatával megkeresheti a tervezet mintájának másolatát, majd kiválaszthatja.

1. Válassza a terv **kiosztása** elemet a terv definíciója oldal tetején.

1. Adja meg a tervrajz-hozzárendelés paramétereinek értékét:

   - Alapvető beállítások
       - **Előfizetések**: válasszon ki egy vagy több olyan előfizetést, amely a felügyeleti csoportban található, és a terv mintájának másolatát mentette. Ha egynél több előfizetést választ ki, a rendszer minden megadott paraméterrel létrehoz egy hozzárendelést.
     - **Hozzárendelés neve**: a név előre ki van töltve a terv neve alapján.
       Szükség szerint módosítsa a változást, vagy hagyja a következőt:.
     - **Hely**: válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást.
     - Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja.
       További tudnivalók: [Azure-erőforrások felügyelt identitásai](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Terv definíciójának verziója**: válasszon egy **közzétett** verziót a terv mintájának másolatáról.

   - Hozzárendelés zárolása

     Válassza ki a környezethez tartozó terv zárolási beállítását. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Válassza ki az alapértelmezett _rendszerhez rendelt_ felügyelt identitás vagy a _felhasználó által hozzárendelt_ identitás lehetőséget.

   - Tervparaméterek

     Az ebben a szakaszban meghatározott paramétereket a terv definíciójában található számos összetevő használja a konzisztencia biztosításához.

     - **Szervezet**: adja meg a szervezet nevét (például contoso) egyedinek kell lennie.
     - **Azure-régió**: válassza ki az Azure-régiót a központi telepítéshez.
     - **Engedélyezett helyszínek**: mely Azure-régiók engedélyezik az erőforrások beépítését?
     
   - Összetevő paramétereinek

     Az ebben a szakaszban meghatározott paraméterek a definiált összetevőre vonatkoznak. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza a lap alján található **hozzárendelés** elemet. A terv-hozzárendelés létrejött, és az összetevő üzembe helyezése megkezdődik. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának megtekintéséhez nyissa meg a terv-hozzárendelést.

> [!WARNING]
> Az Azure BluePrints szolgáltatás és a beépített tervrajzi minták **díjmentesek**. Az Azure-erőforrások [díjszabása termékenként](https://azure.microsoft.com/pricing/)történik. A [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/) használatával megbecsülheti a tervrajzi minta által üzembe helyezett erőforrások futtatásának költségeit.

## <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a tervrajz-összetevő paramétereinek listáját tartalmazza:

|Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|Engedélyezett tárfiók-termékváltozatok|Szabályzat-hozzárendelés|Policy_Allowed – StorageAccount – SKU|A diagnosztikai napló tárolási fiókjaiban használt SKU|
|Engedélyezett virtuális gépek SKU-i|Szabályzat-hozzárendelés|Policy_Allowed – VM-SKU|Engedélyezett virtuális gépek SKU-i|
|CostCenter-címke hozzáfűzése erőforráscsoporthoz|Szabályzat-hozzárendelés|Policy_CostCenter_Tag|CostCenter címke és a hozzá tartozó érték hozzáfűzése az erőforráscsoporthoz|
|A környezetében engedélyezni nem kívánt erőforrástípusok|Szabályzat-hozzárendelés|Házirend _Allowed – erőforrás-típusok|Mely Azure-erőforrásokat szeretné engedélyezni a környezetben|
|Key Vault üzembe helyezése|Resource Manager-sablon|KV – AccessPolicy|**Zárolt** – Azure ad-csoport vagy felhasználói <Object ID> engedélyek megadására Key Vault|
|Log Analytics üzembe helyezése|Resource Manager-sablon|LogAnalytics_DataRetention|**Zárolt** – a napok számát a rendszer megőrzi log Analytics|
|Log Analytics üzembe helyezése|Resource Manager-sablon|LogAnalytics_Location|**Zárolt** – a munkaterület létrehozásakor használt régió|

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a CAF Foundation Blueprint-minta üzembe helyezésének lépéseit, tekintse meg a következő cikket az architektúra megismeréséhez:

> [!div class="nextstepaction"]
> [A CAF Foundation tervezete – áttekintés](./index.md)

További cikkek a tervekről és a használatukról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.