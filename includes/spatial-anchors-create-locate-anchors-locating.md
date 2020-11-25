---
ms.openlocfilehash: c2b1e1d87600e83733eb8bdbf5bc6e2e63376cf4
ms.sourcegitcommit: a43a59e44c14d349d597c3d2fd2bc779989c71d7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/25/2020
ms.locfileid: "95993109"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Felhőbeli térbeli horgony keresése

A korábban mentett Felhőbeli térbeli horgonyok egyike az Azure térbeli horgonyok használatának egyik fő oka. Több különböző módon is megtalálhatja a Felhőbeli térbeli horgonyt. Egyszerre egyetlen stratégiát is használhat a figyelőkhöz.
- Horgonyok megkeresése azonosító alapján.
- Egy korábban található horgonyhoz csatlakoztatott horgonyok megkeresése. [Itt](../articles/spatial-anchors/concepts/anchor-relationships-way-finding.md)megtudhatja, hogyan lehet kapcsolatot kimutatni.
- Keresse meg a horgonyt [durva újrahonosítással](../articles/spatial-anchors/concepts/coarse-reloc.md).

> [!NOTE]
> Minden alkalommal, amikor megtalál egy horgonyt, az Azure térbeli horgonyok megkísérlik használni a gyűjtött környezeti adatokat, hogy kibővítsék a vizualizáció információit a horgonyban. Ha problémába ütközött egy horgony megkeresése során, hasznos lehet egy horgony létrehozása, majd a különböző nézőpontokból és a megvilágítási feltételekből többször is megtalálhatja őket.

Ha a Felhőbeli térbeli horgonyokat azonosító alapján keresi, a Felhőbeli térbeli horgony azonosítóját az alkalmazás háttér-szolgáltatásában kell tárolnia, és elérhetővé kell tennie az összes olyan eszköz számára, amely képes a megfelelő hitelesítésre. Erre példa [: oktatóanyag: térbeli horgonyok megosztása az eszközök között](../articles/spatial-anchors/tutorials/tutorial-share-anchors-across-devices.md).

`AnchorLocateCriteria`Hozzon létre egy objektumot, állítsa be a keresett azonosítókat, és hívja meg a `CreateWatcher` metódust a munkamenetben a következő megadásával: `AnchorLocateCriteria` .