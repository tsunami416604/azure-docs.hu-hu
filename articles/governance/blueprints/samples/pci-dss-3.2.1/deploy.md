---
title: Minta - PCI-DSS v3.2.1 tervezet - telepítés lépéseit
description: Helyezze üzembe a fizetési Card Industry Data Security Standard v3.2.1 tervezet minta lépéseket.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: b4e9435e5f569a076fc5beb6441b9da935b87f3a
ms.sourcegitcommit: d3b1f89edceb9bff1870f562bc2c2fd52636fc21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67561370"
---
# <a name="deploy-the-pci-dss-v321-blueprint-sample"></a>A PCI-DSS v3.2.1 tervezet minta üzembe helyezése

Az Azure tervezetek PCI-DSS v3.2.1 tervezet minta üzembe helyezéséhez az alábbi lépéseket kell tenni:

> [!div class="checklist"]
> - Hozzon létre egy új tervezet arról a minta
> - Jelölje meg a mintát, másolatának **közzétett**
> - A másolatát a tervezet hozzárendelése egy meglévő előfizetés

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-blueprint-from-sample"></a>Tervrajz létrehozása mintából

Először meg a tervezet minta létrehoz egy új tervezet alapszintű, a minta az környezet.

1. Válassza ki **minden szolgáltatás** , és keresse meg és válassza **házirend** a bal oldali panelen. Az a **házirend** lapon jelölje be **tervezetek**.

1. Az a **bevezetés** oldal bal oldalán válassza a **létrehozás** gomb alatt _tervrajz létrehozása_.

1. Keresse meg a **PCI-DSS v3.2.1** tervezet minta alapján _egyéb minták_ válassza **a minta használata**.

1. Adja meg a _alapjai_ a tervezet-minta:

   - **Tervrajz neve**: Adja meg a PCI-DSS v3.2.1 tervezet minta a példányának nevét.
   - **Definíció helye**: Használja a három pontra, és válassza ki a felügyeleti csoport a minta másolatának mentése.

1. Válassza ki a _összetevők_ fülre az oldal tetején lévő vagy **tovább: Összetevők** az oldal alján.

1. Tekintse át a tervezet minta alkotó összetevők listáját. Az összetevők számos rendelkezik, paraméterek, amelyeket később fogunk meghatározni. Válassza ki **Piszkozat mentése** amikor befejezte a tervezet-minta áttekintése.

## <a name="publish-the-sample-copy"></a>A minta másolási közzététele

A tervezet minta másolatának létrehozása megtörtént a környezetben. A létrehozást **Draft** módban kell lennie, és **közzétett** előtt az hozzárendelve, és telepítve. A tervezet minta másolatát is beállítható, hogy a környezet és az igényeinek, azonban, hogy a módosítás lehet, hogy esniük, a standard szintű PCI-DSS v3.2.1.

1. Válassza ki **minden szolgáltatás** , és keresse meg és válassza **házirend** a bal oldali panelen. Az a **házirend** lapon jelölje be **tervezetek**.

1. Válassza ki a **definíciók tervezetet** a bal oldalon. A szűrők segítségével keresse meg a tervezet minta példányát, és válassza ki azt.

1. Válassza ki **közzététel tervezet** az oldal tetején. A jobb oldalon az új lap, adja meg egy **verzió** a tervezet-minta a másolatát. Ez a tulajdonság hasznos Ha később egy módosítása. Adja meg **megjegyzések módosítása** például az "első verziója eltér a PCI-DSS v3.2.1 tervezet közzétett." Válassza ki **közzététel** az oldal alján.

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

|Összetevő neve|Összetevő típusa|Paraméter neve|Leírás|
|-|-|-|-|
|[Előzetes verzió]: PCI v3.2.1:2018 vezérlők naplózása és naplózási követelmények támogatására adott Virtuálisgép-bővítmények telepítése|Szabályzat-hozzárendelés|Erőforrástípusok listája | A kiválasztott erőforrástípusok diagnosztikai beállításának naplózása. Alapértelmezett érték az összes erőforrást jelölt ki:| 
|Engedélyezett helyek|Szabályzat-hozzárendelés|Engedélyezett helyek listája|Minden erőforrás üzembe helyezve az engedélyezett data center helyek listája. Ez a lista globálisan testre szabható, a kívánt Azure-helyen. Válassza ki az engedélyezni kívánt helyeket.| 
|Engedélyezett helyek erőforráscsoportok|Szabályzat-hozzárendelés |Engedélyezett helye |Ez a szabályzat lehetővé teszi, hogy korlátozni azon helyeket, a szervezete létrehozhat az erőforráscsoportokat. Használja a földrajzi megfelelőségi követelmények kényszerítésére.| 
|Az SQL Server-kiszolgálók üzembe helyezése a naplózás|Szabályzat-hozzárendelés|Adatmegőrzési napok|Adatok rentention hány napon belül. Alapértelmezett érték: 180, de PCI 365 igényel.| 
|Az SQL Server-kiszolgálók üzembe helyezése a naplózás|Szabályzat-hozzárendelés|Storage-fiók erőforráscsoportjának neve|Naplózási adatbázis felvezeti ezeket egy naplófájlba, jelentkezzen be az Azure Storage-fiók (storage-fiók létrejön az egyes régiókban, ahol jön létre egy SQL Server által az adott régióban található összes kiszolgáló megosztott).| 

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a PCI-DSS v3.2.1 tervezet minta üzembe helyezése a lépéseket, keresse fel a áttekintése és a vezérlő leképezés a következő cikkeket:

> [!div class="nextstepaction"]
> [PCI-DSS v3.2.1 tervezet - áttekintés](./index.md)
> [PCI-DSS v3.2.1 tervezet - vezérlő leképezés](./control-mapping.md)

További cikkek a tervekről és azok használatáról:

- Tudnivalók a [tervek életciklusáról](../../concepts/lifecycle.md).
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
