---
title: Minta – Egyesült Királyság hivatalos és egyesült királysági NHS-terve – lépések üzembe helyezése
description: Üzembe helyezheti az Egyesült Királyság hivatalos és az Egyesült Királysági NHS-tervezetének lépéseit.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: d720f220b8903d960e7bba2a75aa5a86bd0391ce
ms.sourcegitcommit: 920ad23613a9504212aac2bfbd24a7c3de15d549
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/15/2019
ms.locfileid: "68226001"
---
# <a name="deploy-the-uk-official-and-uk-nhs-blueprint-samples"></a>Az Egyesült Királyság hivatalos és egyesült királysági NHS-mintáinak üzembe helyezése

Az Egyesült királyságbeli hivatalos és az Egyesült királyságbeli NHS-tervezet mintáinak üzembe helyezéséhez a következő lépéseket kell elvégeznie:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **közzétettként**
> - A terv másolatának kiosztása meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először is implementálja a terv mintáját úgy, hogy létrehoz egy új tervet a környezetben a minta kezdőként való használatával.

1. Válassza a **minden szolgáltatás** lehetőséget, és keresse meg és válassza ki a **házirend** elemet a bal oldali ablaktáblán. A **szabályzat** lapon válassza a **tervrajzok**lehetőséget.

1. A bal oldali **első lépések** lapon kattintson a **Létrehozás** gombra a _terv létrehozása_területen.

1. Keresse meg az **Egyesült Királyság hivatalos** vagy **Egyesült Királysági NHS** -tervezetét _más minták_ alatt, és válassza **a minta használata**lehetőséget.

1. Adja meg  a tervezet mintájának alapjait:

   - **Terv neve**: Adjon meg egy nevet a terv mintájának másolatához.
   - **Definíció helye**: Használja a három pontot, és válassza ki a felügyeleti csoportot, és mentse a minta másolatát a következőre:.

1. Válassza ki  az összetevők fület a lap tetején, vagy **a következőt:**  Az oldal alján található összetevők.

1. Tekintse át a terv mintáját alkotó összetevők listáját. Számos összetevőhöz vannak olyan paraméterek, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor befejezte a tervezet mintájának áttekintését.

## <a name="publish-the-sample-copy"></a>A minta másolatának közzététele

A terv mintájának másolata már létre lett hozva a környezetében. A rendszer **Piszkozat** módban jön létre, és **közzé** kell tenni ahhoz, hogy hozzá lehessen rendelni és telepíteni lehessen. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás a standardtól akár el is helyezhető.

1. Válassza a **minden szolgáltatás** lehetőséget, és keresse meg és válassza ki a **házirend** elemet a bal oldali ablaktáblán. A **szabályzat** lapon válassza a **tervrajzok**lehetőséget.

1. Válassza a bal oldali **terv** -definíciók lapot. A szűrők használatával megkeresheti a tervezet mintájának másolatát, majd kiválaszthatja.

1. Válassza a **terv közzététele** lehetőséget az oldal tetején. A jobb oldalon található új lapon adjon meg egy **verziót** a tervezet mintájának másolatához. Ez a tulajdonság akkor hasznos, ha később módosítja a módosítást. Adjon meg olyan **módosítási megjegyzéseket** , mint az "első verzió az Egyesült Királyság hivatalos vagy az Egyesült KIRÁLYSÁGbeli NHS-tervezetből". Ezután válassza a **Közzététel** elemet az oldal alján.

## <a name="assign-the-sample-copy"></a>A minta másolatának kiosztása

Miután a tervezet mintájának **közzététele**sikeresen megtörtént, hozzárendelhető egy előfizetéshez, amely a felügyeleti csoporton belül lett mentve. Ezzel a lépéssel megadhatja, hogy az egyes központi telepítések egyediek legyenek-e.

1. Válassza a **minden szolgáltatás** lehetőséget, és keresse meg és válassza ki a **házirend** elemet a bal oldali ablaktáblán. A **szabályzat** lapon válassza a **tervrajzok**lehetőséget.

1. Válassza a bal oldali **terv** -definíciók lapot. A szűrők használatával megkeresheti a tervezet mintájának másolatát, majd kiválaszthatja.

1. Válassza  a terv kiosztása elemet a terv definíciója oldal tetején.

1. Adja meg a tervrajz-hozzárendelés paramétereinek értékét:

   - Alapvető beállítások

     - **Előfizetések**: Válasszon ki egy vagy több olyan előfizetést, amely a felügyeleti csoportban található, a terv mintájának másolatát a következőre mentette:. Ha egynél több előfizetést választ ki, a rendszer minden megadott paraméterrel létrehoz egy hozzárendelést.
     - **Hozzárendelés neve**: A név előre ki van töltve a terv neve alapján.
       Szükség szerint módosítsa a változást, vagy hagyja a következőt:.
     - **Hely**: Válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További tudnivalók: [Azure-erőforrások felügyelt identitásai](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Terv definíciójának verziója**: Válasszon egy **közzétett** verziót a tervezet mintájának másolatáról.

   - Hozzárendelés zárolása

     Válassza ki a környezethez tartozó terv zárolási beállítását. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszerhez rendelt_ felügyelt identitás beállítást.

   - Összetevő paramétereinek

     Az ebben a szakaszban meghatározott paraméterek a definiált összetevőre vonatkoznak. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza a lap alján található **hozzárendelés** elemet. A terv-hozzárendelés létrejött, és az összetevő üzembe helyezése megkezdődik. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának megtekintéséhez nyissa meg a terv-hozzárendelést.

> [!WARNING]
> Az Azure BluePrints szolgáltatás és a beépített tervrajzi minták díjmentesek . Az Azure-erőforrások [díjszabása termékenként](https://azure.microsoft.com/pricing/)történik. A [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/) használatával megbecsülheti a tervrajzi minta által üzembe helyezett erőforrások futtatásának költségeit.

## <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a tervrajz-összetevő paramétereinek listáját tartalmazza:

Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|Tervezet kezdeményezés az Egyesült Királyság hivatalos vagy Egyesült Királysági NHS-hez|Szabályzat-hozzárendelés |A diagnosztikai naplók naplózására szolgáló erőforrástípusok (házirend: Tervezet kezdeményezés az Egyesült Királyság hivatalos vagy Egyesült Királysági NHS-hez) |A naplózni kívánt erőforrástípusok listája, ha a diagnosztikai napló beállítása Megjegyzés engedélyezve van.  Elfogadható értékek: [támogatott szolgáltatások, sémák és kategóriák az Azure diagnosztikai naplóihoz](../../../../azure-monitor/platform/diagnostic-logs-schema.md). |
|\[Előzetes\]verzió: A Linux rendszerű virtuális gépek Log Analytics ügynökének üzembe helyezése |Szabályzat-hozzárendelés |Nem kötelező: Azon virtuálisgép-lemezképek listája, amelyek támogatottak a hatókörbe felvenni kívánt linuxos operációs rendszer (házirend: \[Előzetes\]verzió: Log Analytics-ügynök üzembe helyezése Linux rendszerű virtuális gépeken) |Választható Az alapértelmezett érték _none_. További információ: [log Analytics munkaterület létrehozása a Azure Portalban](../../../../azure-monitor/learn/quick-create-workspace.md). |
|\[Előzetes\]verzió: Windows rendszerű virtuális gépek Log Analytics ügynökének üzembe helyezése |Szabályzat-hozzárendelés |Nem kötelező: Azon virtuálisgép-rendszerképek listája, amelyek a hatókörhöz való hozzáadáshoz támogatott Windows operációs rendszert (házirend: \[Előzetes\]verzió: Log Analytics-ügynök üzembe helyezése Windows rendszerű virtuális gépeken) |Választható Az alapértelmezett érték _none_. További információ: [log Analytics munkaterület létrehozása a Azure Portalban](../../../../azure-monitor/learn/quick-create-workspace.md). |

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a brit hivatalos és az Egyesült Királysági NHS-tervezetek üzembe helyezésének lépéseit, tekintse meg az alábbi cikkeket az Áttekintés és a vezérlés leképezésének megismeréséhez:

> [!div class="nextstepaction"]
> [Egyesült Királyság hivatalos és egyesült királysági NHS-tervezetei – áttekintés](./index.md)
> [az Egyesült Királyság hivatalos és az Egyesült királyságbeli NHS-tervezetekről – vezérlés-hozzárendelés](./control-mapping.md)

További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
