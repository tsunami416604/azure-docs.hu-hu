---
title: A telepítés lépéseit – Egyesült Királyság hivatalos és Egyesült Királyság számos tervezetet – minta
description: Helyezze üzembe az Egyesült Királyság hivatalos és Egyesült Királyság számos tervezet minták lépéseket.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/26/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: 43aae882f27031d3e51ac8a4f5a68d243a973d6d
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67453202"
---
# <a name="deploy-the-uk-official-and-uk-nhs-blueprint-samples"></a>Az Egyesült Királyság hivatalos és Egyesült Királyság számos tervezet minták üzembe helyezése

Az Egyesült Királyság hivatalos és Egyesült Királyság számos tervezet minták üzembe helyezéséhez az alábbi lépéseket kell tenni:

> [!div class="checklist"]
> - Hozzon létre egy új tervezet arról a minta
> - Jelölje meg a mintát, másolatának **közzétett**
> - A másolatát a tervezet hozzárendelése egy meglévő előfizetés

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-blueprint-from-sample"></a>Tervrajz létrehozása mintából

Először meg a tervezet minta létrehoz egy új tervezet alapszintű, a minta az környezet.

1. Válassza ki **minden szolgáltatás** , és keresse meg és válassza **házirend** a bal oldali panelen. Az a **házirend** lapon jelölje be **tervezetek**.

1. Az a **bevezetés** oldal bal oldalán válassza a **létrehozás** gomb alatt _tervrajz létrehozása_.

1. Keresse meg a **UK hivatalos** vagy **Egyesült Királyság számos** tervezet minta alapján _egyéb minták_ válassza **a minta használata**.

1. Adja meg a _alapjai_ a tervezet-minta:

   - **Tervrajz neve**: Adja meg a tervezet-minta a példányának nevét.
   - **Definíció helye**: Használja a három pontra, és válassza ki a felügyeleti csoport a minta másolatának mentése.

1. Válassza ki a _összetevők_ fülre az oldal tetején lévő vagy **tovább: Összetevők** az oldal alján.

1. Tekintse át a tervezet minta alkotó összetevők listáját. Az összetevők számos rendelkezik, paraméterek, amelyeket később fogunk meghatározni. Válassza ki **Piszkozat mentése** amikor befejezte a tervezet-minta áttekintése.

## <a name="publish-the-sample-copy"></a>A minta másolási közzététele

A tervezet minta másolatának létrehozása megtörtént a környezetben. A létrehozást **Draft** módban kell lennie, és **közzétett** előtt az hozzárendelve, és telepítve. A tervezet minta másolatát is beállítható, hogy a környezet és az igényeinek, azonban, hogy a módosítás lehet, hogy esniük, a standard.

1. Válassza ki **minden szolgáltatás** , és keresse meg és válassza **házirend** a bal oldali panelen. Az a **házirend** lapon jelölje be **tervezetek**.

1. Válassza ki a **definíciók tervezetet** a bal oldalon. A szűrők segítségével keresse meg a tervezet minta példányát, és válassza ki azt.

1. Válassza ki **közzététel tervezet** az oldal tetején. A jobb oldalon az új lap, adja meg egy **verzió** a tervezet-minta a másolatát. Ez a tulajdonság hasznos Ha később egy módosítása. Adja meg **megjegyzések módosítása** például az "UK-OFFICIAL vagy az Egyesült Királyság számos tervezet mintából származó közzétett első verziója." Válassza ki **közzététel** az oldal alján.

## <a name="assign-the-sample-copy"></a>A minta másolási hozzárendelése

Miután a tervezet minta másolata sikeresen megtörtént **közzétett**, előfizetésre történő mentése felügyeleti csoporton belül is hozzárendelhető. Ez a lépés nem, melyekben a paraméterek vannak-e adva az, hogy az egyes telepítések a példány a tervezet minta egyedi.

1. Válassza ki **minden szolgáltatás** , és keresse meg és válassza **házirend** a bal oldali panelen. Az a **házirend** lapon jelölje be **tervezetek**.

1. Válassza ki a **definíciók tervezetet** a bal oldalon. A szűrők segítségével keresse meg a tervezet minta példányát, és válassza ki azt.

1. Válassza ki **tervezet hozzárendelése** a tervrajz-definíció lap tetején.

1. Adja meg a paraméter értékét a tervezet-hozzárendelést:

   - Alapvető beállítások

     - **Előfizetések**: Válasszon ki egy vagy több az előfizetéseket, amelyek a felügyeleti csoportban, a tervezet minta másolatának mentése. Ha egynél több előfizetéssel, hozzárendelést a megadott paraméterek használatával hozható létre.
     - **Hozzárendelés neve**: Az Ön a tervrajz neve alapján előre megadott név.
       Szükség szerint változtassa meg, vagy hagyja üresen, mivel.
     - **Hely**: Válassza ki a régiót, a felügyelt identitás kell létrehozni. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További tudnivalók: [Azure-erőforrások felügyelt identitásai](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Tervrajz-definíció verziója**: Válasszon ki egy **közzétett** a tervezet minta másolatának verzióját.

   - Zárolási hozzárendelés

     Válassza ki a tervezet zárolás, a környezet beállítása. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszerhez rendelt_ felügyelt identitás lehetőséget.

   - Összetevő-paraméterek

     A lehívandó összetevő definiálva van ebben a szakaszban definiált paraméterek érvényesek. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) mivel azok van megadva a a tervezet-hozzárendelés során. A teljes listát vagy összetevő paraméterek és ezek leírását: [összetevő paramétereket tartalmazó](#artifact-parameters-table).

1. Után minden paraméter van megadva, válassza a **hozzárendelése** az oldal alján. A tervezet-hozzárendelést jön létre, és összetevő telepítési kezdődik. Üzembe helyezés eltarthat nagyjából egy óra. Üzembe helyezés állapotának ellenőrzéséhez nyissa meg a tervezet-hozzárendelést.

> [!WARNING]
> Az Azure-tervek szolgáltatás és a beépített tervezet minták **díjmentes**. Az Azure-erőforrások [termék díjszabása](https://azure.microsoft.com/pricing/). Használja a [díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) a tervezet minta által üzembe helyezett erőforrások futtatásával járó költségeket megbecsülheti.

## <a name="artifact-parameters-table"></a>Összetevő paraméterek táblában

A következő táblázat felsorolja a tervezet összetevő paraméterek:

Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|Egyesült Királyság hivatalos vagy az Egyesült Királyság számos kezdeményezés tervezetet|Szabályzat-hozzárendelés |Erőforrástípusok diagnosztikai naplók (szabályzat: Kezdeményezés tervezetet az Egyesült Királyság hivatalos vagy az Egyesült Királyság számos) |Ha a beállítás diagnosztikai naplója engedélyezve Megjegyzés naplózása erőforrástípusok listája.  Elfogadható értékek, lásd: [támogatott szolgáltatások, sémákat és kategóriák az Azure diagnosztikai naplók](../../../../azure-monitor/platform/diagnostic-logs-schema.md). |
|[Előzetes verzió]: Log Analytics-ügynök Linux rendszerű virtuális gépek üzembe helyezése |Szabályzat-hozzárendelés |Nem kötelező: Hogy támogatott Linux operációs rendszer hatókör hozzáadása Virtuálisgép-rendszerképek listája (szabályzat: [előzetes verzió]: Log Analytics-ügynök Linux rendszerű virtuális gépek üzembe helyezése) |(Nem kötelező) Alapértelmezett érték _none_. További információkért lásd: [Log Analytics-munkaterület létrehozása az Azure Portalon](../../../../azure-monitor/learn/quick-create-workspace.md). |
|[Előzetes verzió]: Log Analytics-ügynököket Windows virtuális gépek üzembe helyezése |Szabályzat-hozzárendelés |Nem kötelező: Hatókör hozzáadása a Windows operációs rendszer rendelkezik támogatott Virtuálisgép-rendszerképek listája (szabályzat: [előzetes verzió]: Log Analytics-ügynököket Windows virtuális gépek üzembe helyezése) |(Nem kötelező) Alapértelmezett érték _none_. További információkért lásd: [Log Analytics-munkaterület létrehozása az Azure Portalon](../../../../azure-monitor/learn/quick-create-workspace.md). |

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a lépéseket, az Egyesült Királyság hivatalos és Egyesült Királyság számos tervezet minták, látogasson el a további információ a áttekintése és a vezérlő leképezés a következő cikkeket:

> [!div class="nextstepaction"]
> [Egyesült Királyság hivatalos és az Egyesült Királyság számos tervezetek – áttekintés](./index.md)
> [UK hivatalos és az Egyesült Királyság számos tervezetek - leképezés szabályozza](./control-mapping.md)

További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
