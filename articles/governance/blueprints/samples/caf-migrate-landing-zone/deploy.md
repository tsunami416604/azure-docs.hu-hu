---
title: Minta – a CAF áttelepítése a bevezetési zóna terve – üzembe helyezési lépések
description: Üzembe helyezheti a CAF bevezetési zóna tervének áttelepítési minta lépéseit.
author: DCtheGeek
ms.author: dacoulte
ms.date: 08/20/2019
ms.topic: sample
ms.service: blueprints
manager: carmonm
ms.custom: fasttrack-new
ms.openlocfilehash: 0125fa20e61890317d4df510cb668f68e5049c43
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231857"
---
# <a name="deploy-the-microsoft-cloud-adoption-framework-for-azure-migrate-landing-zone-blueprint-sample"></a>A Microsoft Cloud bevezetési keretrendszer üzembe helyezése az Azure áttelepítési zóna terve minta

Az Azure-tervezetek a CAF-ban való üzembe helyezéséhez a következő lépéseket kell elvégeznie:

> [!div class="checklist1"]
> - Javasolt a [CAF Foundation](../caf-foundation/index.md) Blueprint minta üzembe helyezése

> [!div class="checklist2"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **közzétettként**
> - A terv másolatának kiosztása meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először is implementálja a terv mintáját úgy, hogy létrehoz egy új tervet a környezetben a minta kezdőként való használatával.

1. Válassza a **minden szolgáltatás** lehetőséget, és keresse meg és válassza ki a **házirend** elemet a bal oldali ablaktáblán. A **szabályzat** lapon válassza a **tervrajzok**lehetőséget.

1. A bal oldali **első lépések** lapon kattintson a **Létrehozás** gombra a _terv létrehozása_területen.

1. Keresse meg a **CAF Migration Landing Zone** Blueprint mintát _más minták_ alatt, és válassza **a minta használata**lehetőséget.

1. Adja meg a tervezet mintájának alapjait:
   - **Terv neve** Adjon meg egy nevet a CAF Migration Landing Zone Blueprint minta példányához.
   - **Definíció helye** Használja a három pontot, és válassza ki a felügyeleti csoportot, és mentse a minta másolatát a következőre:.

1. Válassza ki az összetevők fület a lap tetején, vagy **a következőt:**  Az oldal alján található összetevők.

1. Tekintse át a terv mintáját alkotó összetevők listáját. Számos összetevőhöz vannak olyan paraméterek, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor befejezte a tervezet mintájának áttekintését.

## <a name="publish-the-sample-copy"></a>A minta másolatának közzététele

A terv mintájának másolata már létre lett hozva a környezetében. A rendszer **Piszkozat** módban jön létre, és **közzé** kell tenni ahhoz, hogy hozzá lehessen rendelni és telepíteni lehessen. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás áthelyezhető a CAF-beli bevezetési zóna útmutatása szerint.

1. Válassza a **minden szolgáltatás** lehetőséget, és keresse meg és válassza ki a **házirend** elemet a bal oldali ablaktáblán. A **szabályzat** lapon válassza a **tervrajzok**lehetőséget.

1. Válassza a bal oldali **terv** -definíciók lapot. A szűrők használatával megkeresheti a tervezet mintájának másolatát, majd kiválaszthatja.

1. Válassza a **terv közzététele** lehetőséget az oldal tetején. A jobb oldalon található új lapon adjon meg egy **verziót** a tervezet mintájának másolatához. Ez a tulajdonság akkor hasznos, ha később módosítja a módosítást. Adjon meg olyan **módosítási megjegyzéseket** , mint például az "első verzió megjelent a CAF Migration Landing Zone Blueprint minta". Ezután válassza a **Közzététel** elemet az oldal alján.

## <a name="assign-the-sample-copy"></a>A minta másolatának kiosztása

Miután a tervezet mintájának **közzététele**sikeresen megtörtént, hozzárendelhető egy előfizetéshez, amely a felügyeleti csoporton belül lett mentve. Ezzel a lépéssel megadhatja, hogy az egyes központi telepítések egyediek legyenek-e.

1. Válassza a **minden szolgáltatás** lehetőséget, és keresse meg és válassza ki a **házirend** elemet a bal oldali ablaktáblán. A **szabályzat** lapon válassza a **tervrajzok**lehetőséget.

1. Válassza a bal oldali **terv** -definíciók lapot. A szűrők használatával megkeresheti a tervezet mintájának másolatát, majd kiválaszthatja.

1. Válassza a terv kiosztása elemet a terv definíciója oldal tetején.

1. Adja meg a tervrajz-hozzárendelés paramétereinek értékét:

   - Alapvető beállítások
     - **Előfizetések**: Válasszon ki egy vagy több olyan előfizetést, amely a felügyeleti csoportban található, a terv mintájának másolatát a következőre mentette:. Ha egynél több előfizetést választ ki, a rendszer minden megadott paraméterrel létrehoz egy hozzárendelést.
     - **Hozzárendelés neve**: A név előre ki van töltve a terv neve alapján.
       Szükség szerint módosítsa a változást, vagy hagyja a következőt:.
     - **Hely**: Válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást.
     - Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja.
       További tudnivalók: [Azure-erőforrások felügyelt identitásai](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Terv definíciójának verziója**: Válasszon egy **közzétett** verziót a tervezet mintájának másolatáról.
    
   - Hozzárendelés zárolása

     Válassza ki a környezethez tartozó terv zárolási beállítását. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Válassza ki az alapértelmezett _rendszerhez rendelt_ felügyelt identitás vagy a _felhasználó_ által hozzárendelt identitás lehetőséget.

   - Terv paraméterei

     Az ebben a szakaszban meghatározott paramétereket a terv definíciójában található számos összetevő használja a konzisztencia biztosításához.

       - **Szervezet**: Adja meg a szervezet nevét (például contoso vagy Fabrikam) egyedinek kell lennie.
       - **AzureRegion**: Válasszon ki egy Azure-régiót az üzembe helyezéshez.
       
   - Összetevő paraméterei

     Az ebben a szakaszban meghatározott paraméterek a definiált összetevőre vonatkoznak. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza a lap alján található **hozzárendelés** elemet. A terv-hozzárendelés létrejött, és az összetevő üzembe helyezése megkezdődik. Az üzembe helyezés nagyjából öt percet vesz igénybe. Az üzembe helyezés állapotának megtekintéséhez nyissa meg a terv-hozzárendelést.

> [!WARNING]
> Az Azure BluePrints szolgáltatás és a beépített tervrajzi minták díjmentesek. Az Azure-erőforrások [díjszabása termékenként](https://azure.microsoft.com/pricing/)történik. A [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/) használatával megbecsülheti a tervrajzi minta által üzembe helyezett erőforrások futtatásának költségeit.

## <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a tervrajz-összetevő paramétereinek listáját tartalmazza:

|Elemnév|Összetevő típusa|Paraméternév|Leírás|
|-|-|-|-|
|VNET-kirakodási zóna üzembe helyezése|Resource Manager-sablon|IPAddress_Space|**Zárolt** – adja meg az első két oktettet, például 10,0|
|Key Vault üzembe helyezése|Resource Manager-sablon|KV – AccessPolicy|**Zárolt** csoport vagy felhasználói objektumazonosító, amely engedélyt ad a Key Vault|
|Log Analytics üzembe helyezése|Resource Manager-sablon|LogAnalytics_DataRetention|**Zárolt** – a napok számát a rendszer megőrzi log Analytics|
|Log Analytics üzembe helyezése|Resource Manager-sablon|LogAnalytics_Location|**Zárolt** – a munkaterület létrehozásakor használt régió|
|Azure Migrate üzembe helyezése|Resource Manager-sablon|Azure_Migrate_Location|**Zárolt** – válassza ki a telepítendő régiót Azure Migrate|

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a CAF Migrálás bevezetési zóna üzembe helyezésének lépéseit, tekintse meg az alábbi cikkeket az architektúra megismeréséhez:

> [!div class="nextstepaction"]
> [A CAF áttelepíti a behajózási zóna tervezetét – áttekintés](./index.md)

További cikkek a tervekről és azok használatáról:

- A [terv életciklusának](../../concepts/lifecycle.md)megismerése.
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.