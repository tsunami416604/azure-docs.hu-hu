---
title: Minta-PCI-DSS v 3.2.1 terv – lépések üzembe helyezése
description: Üzembe helyezheti a Payment Card Industry adatbiztonsági standard v 3.2.1 terv mintájának lépéseit.
services: blueprints
author: DCtheGeek
ms.author: dacoulte
ms.date: 06/24/2019
ms.topic: conceptual
ms.service: blueprints
manager: carmonm
ms.openlocfilehash: f6ae466d10d585b46c9c71614cfeff6b688e6e48
ms.sourcegitcommit: 2aefdf92db8950ff02c94d8b0535bf4096021b11
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/03/2019
ms.locfileid: "70231649"
---
# <a name="deploy-the-pci-dss-v321-blueprint-sample"></a>A PCI-DSS v 3.2.1 terv üzembe helyezése minta

Az Azure-tervrajzok PCI-DSS v 3.2.1 Blueprint-minta üzembe helyezéséhez a következő lépéseket kell végrehajtani:

> [!div class="checklist"]
> - Új terv létrehozása a mintából
> - A minta másolatának megjelölése **közzétettként**
> - A terv másolatának kiosztása meglévő előfizetéshez

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-blueprint-from-sample"></a>Terv létrehozása mintából

Először is implementálja a terv mintáját úgy, hogy létrehoz egy új tervet a környezetben a minta kezdőként való használatával.

1. Válassza a **minden szolgáltatás** lehetőséget, és keresse meg és válassza ki a **házirend** elemet a bal oldali ablaktáblán. A **szabályzat** lapon válassza a **tervrajzok**lehetőséget.

1. A bal oldali **első lépések** lapon kattintson a **Létrehozás** gombra a _terv létrehozása_területen.

1. Keresse meg a **PCI-DSS v 3.2.1** terv mintát _más minták_ alatt, és válassza **a minta használata**lehetőséget.

1. Adja meg a tervezet mintájának alapjait:

   - **Terv neve**: Adja meg a PCI-DSS v 3.2.1 Blueprint minta példányának nevét.
   - **Definíció helye**: Használja a három pontot, és válassza ki a felügyeleti csoportot, és mentse a minta másolatát a következőre:.

1. Válassza ki az összetevők fület a lap tetején, vagy **a következőt:**  Az oldal alján található összetevők.

1. Tekintse át a terv mintáját alkotó összetevők listáját. Számos összetevőhöz vannak olyan paraméterek, amelyeket később definiálunk. Válassza a **Piszkozat mentése** lehetőséget, amikor befejezte a tervezet mintájának áttekintését.

## <a name="publish-the-sample-copy"></a>A minta másolatának közzététele

A terv mintájának másolata már létre lett hozva a környezetében. A rendszer **Piszkozat** módban jön létre, és **közzé** kell tenni ahhoz, hogy hozzá lehessen rendelni és telepíteni lehessen. A terv mintájának másolata testreszabható a környezet és a szükséges igények alapján, de ez a módosítás a PCI-DSS v 3.2.1 standard csomagból is áthelyezhető.

1. Válassza a **minden szolgáltatás** lehetőséget, és keresse meg és válassza ki a **házirend** elemet a bal oldali ablaktáblán. A **szabályzat** lapon válassza a **tervrajzok**lehetőséget.

1. Válassza a bal oldali **terv** -definíciók lapot. A szűrők használatával megkeresheti a tervezet mintájának másolatát, majd kiválaszthatja.

1. Válassza a **terv közzététele** lehetőséget az oldal tetején. A jobb oldalon található új lapon adjon meg egy **verziót** a tervezet mintájának másolatához. Ez a tulajdonság akkor hasznos, ha később módosítja a módosítást. Adjon meg olyan **módosítási megjegyzéseket** , mint például az "első verzió a PCI-DSS v 3.2.1 tervből" című példa. Ezután válassza a **Közzététel** elemet az oldal alján.

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
     - **Hely**: Válassza ki azt a régiót, amelyben létre kívánja hozni a felügyelt identitást. Az Azure Blueprint a hozzárendelt tervben lévő összes összetevő üzembe helyezéséhez ezt a felügyelt identitást használja. További tudnivalók: [Azure-erőforrások felügyelt identitásai](../../../../active-directory/managed-identities-azure-resources/overview.md).
     - **Terv definíciójának verziója**: Válasszon egy **közzétett** verziót a tervezet mintájának másolatáról.

   - Hozzárendelés zárolása

     Válassza ki a környezethez tartozó terv zárolási beállítását. További információkat talál a [terv-erőforrások zárolásáról](../../concepts/resource-locking.md) szóló cikkben.

   - Felügyelt identitás

     Hagyja meg az alapértelmezett _rendszerhez rendelt_ felügyelt identitás beállítást.

   - Összetevő paraméterei

     Az ebben a szakaszban meghatározott paraméterek a definiált összetevőre vonatkoznak. Ezek a paraméterek [dinamikus paraméterek](../../concepts/parameters.md#dynamic-parameters) , mert a terv hozzárendelése során vannak meghatározva. A teljes listát vagy az összetevő paramétereit és azok leírását lásd: összetevő- [Paraméterek táblázata](#artifact-parameters-table).

1. Az összes paraméter megadása után válassza a lap alján található **hozzárendelés** elemet. A terv-hozzárendelés létrejött, és az összetevő üzembe helyezése megkezdődik. Az üzembe helyezés nagyjából egy órát vesz igénybe. Az üzembe helyezés állapotának megtekintéséhez nyissa meg a terv-hozzárendelést.

> [!WARNING]
> Az Azure BluePrints szolgáltatás és a beépített tervrajzi minták díjmentesek. Az Azure-erőforrások [díjszabása termékenként](https://azure.microsoft.com/pricing/)történik. A [díjszabási számológép](https://azure.microsoft.com/pricing/calculator/) használatával megbecsülheti a tervrajzi minta által üzembe helyezett erőforrások futtatásának költségeit.

## <a name="artifact-parameters-table"></a>Összetevő-paraméterek táblázata

A következő táblázat a tervrajz-összetevő paramétereinek listáját tartalmazza:

|Elemnév|Összetevő típusa|Paraméternév|Leírás|
|-|-|-|-|
|\[Előzetes\] naplózási PCI v 3.2.1:2018 – a naplózási követelmények támogatásához szükséges virtuálisgép-bővítmények ellenőrzése és üzembe helyezése|Szabályzat-hozzárendelés|Erőforrástípusok listája | A kiválasztott erőforrástípusok diagnosztikai beállításainak naplózása. Az alapértelmezett érték minden erőforrás van kiválasztva| 
|Engedélyezett helyek|Szabályzat-hozzárendelés|Engedélyezett helyszínek listája|A alkalmazásba telepítendő összes erőforráshoz engedélyezett adatközpont-helyszínek listája. Ez a lista globálisan testreszabható a kívánt Azure-helyen. Válassza ki az engedélyezni kívánt helyet.| 
|Erőforráscsoportok engedélyezett helyei|Szabályzat-hozzárendelés |Engedélyezett hely |Ez a szabályzat lehetővé teszi, hogy korlátozza a szervezete által az erőforráscsoportok létrehozásához szükséges helyet. A Geo-megfelelőségi követelmények betartatására használható.| 
|Naplózás üzembe helyezése SQL-kiszolgálókon|Szabályzat-hozzárendelés|Megőrzési napok|Az adatmennyiség napok száma megőrzése. Az alapértelmezett érték 180, de a PCI esetében 365.| 
|Naplózás üzembe helyezése SQL-kiszolgálókon|Szabályzat-hozzárendelés|A Storage-fiók erőforráscsoport-neve|A naplózás az adatbázis-eseményeket egy naplóba írja az Azure Storage-fiókban (a Storage-fiók minden régióban létrejön, ahol létrejön egy SQL Server, amelyet az adott régióban lévő összes kiszolgáló megoszt majd).| 

## <a name="next-steps"></a>További lépések

Most, hogy áttekintette a PCI-DSS v 3.2.1 Blueprint minta üzembe helyezésének lépéseit, tekintse meg az alábbi cikkeket az Áttekintés és a vezérlés leképezésének megismeréséhez:

> [!div class="nextstepaction"]
> [PCI-DSS v 3.2.1 terv – áttekintés](./index.md)
> a[PCI-DSS v 3.2.1 tervről – vezérlés leképezése](./control-mapping.md)

További cikkek a tervekről és azok használatáról:

- A [terv életciklusának](../../concepts/lifecycle.md)megismerése.
- A [statikus és dinamikus paraméterek](../../concepts/parameters.md) használatának elsajátítása.
- A [tervekkel kapcsolatos műveleti sorrend](../../concepts/sequencing-order.md) testreszabásának elsajátítása.
- A [tervek erőforrás-zárolásának](../../concepts/resource-locking.md) alkalmazásával kapcsolatos részletek.
- A [meglévő hozzárendelések frissítésének](../../how-to/update-existing-assignments.md) elsajátítása.
