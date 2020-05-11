---
ms.openlocfilehash: 4df49b4e68769a907423c4edef9f8820df4d20b7
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2020
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

Hozzon létre `AnchorLocateCriteria` egy objektumot, állítsa be a keresett azonosítókat, és hívja meg a `CreateWatcher` metódust a munkamenetben a következő `AnchorLocateCriteria`megadásával:.