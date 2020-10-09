---
ms.openlocfilehash: 4df49b4e68769a907423c4edef9f8820df4d20b7
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006496"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Felhőbeli térbeli horgony keresése

A korábban mentett Felhőbeli térbeli horgonyok egyike az Azure térbeli horgonyok használatának egyik fő oka. Több különböző módon is megtalálhatja a Felhőbeli térbeli horgonyt. Egyszerre egyetlen stratégiát is használhat a figyelőkhöz.
- Horgonyok megkeresése azonosító alapján.
- Egy korábban található horgonyhoz csatlakoztatott horgonyok megkeresése. [Itt](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/)megtudhatja, hogyan lehet kapcsolatot kimutatni.
- Keresse meg a horgonyt [durva újrahonosítással](/azure/spatial-anchors/concepts/coarse-reloc/).

> [!NOTE]
> Minden alkalommal, amikor megtalál egy horgonyt, az Azure térbeli horgonyok megkísérlik használni a gyűjtött környezeti adatokat, hogy kibővítsék a vizualizáció információit a horgonyban. Ha problémába ütközött egy horgony megkeresése során, hasznos lehet egy horgony létrehozása, majd a különböző nézőpontokból és a megvilágítási feltételekből többször is megtalálhatja őket.

Ha a Felhőbeli térbeli horgonyokat azonosító alapján keresi, a Felhőbeli térbeli horgony azonosítóját az alkalmazás háttér-szolgáltatásában kell tárolnia, és elérhetővé kell tennie az összes olyan eszköz számára, amely képes a megfelelő hitelesítésre. Erre példa [: oktatóanyag: térbeli horgonyok megosztása az eszközök között](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

`AnchorLocateCriteria`Hozzon létre egy objektumot, állítsa be a keresett azonosítókat, és hívja meg a `CreateWatcher` metódust a munkamenetben a következő megadásával: `AnchorLocateCriteria` .