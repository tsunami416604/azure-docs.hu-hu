---
ms.openlocfilehash: ac9fe1d367dba0ebdf4250b3213f191ced758dd3
ms.sourcegitcommit: 87781a4207c25c4831421c7309c03fce5fb5793f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/23/2020
ms.locfileid: "76694514"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Felhőbeli térbeli horgony keresése

A korábban mentett Felhőbeli térbeli horgonyok egyike az Azure térbeli horgonyok használatának egyik fő oka. Több különböző módon is megtalálhatja a Felhőbeli térbeli horgonyt. Egyszerre egyetlen stratégiát is használhat a figyelőkhöz.
- Horgonyok megkeresése azonosító alapján.
- Egy korábban található horgonyhoz csatlakoztatott horgonyok megkeresése. [Itt](/azure/spatial-anchors/concepts/anchor-relationships-way-finding.md)megtudhatja, hogyan lehet kapcsolatot kimutatni.
- Keresse meg a horgonyt [durva újrahonosítással](/azure/spatial-anchors/concepts/coarse-reloc.md).

Ha a Felhőbeli térbeli horgonyokat azonosító alapján keresi, a Felhőbeli térbeli horgony azonosítóját az alkalmazás háttér-szolgáltatásában kell tárolnia, és elérhetővé kell tennie az összes olyan eszköz számára, amely képes a megfelelő hitelesítésre. Erre példa [: oktatóanyag: térbeli horgonyok megosztása az eszközök között](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Hozzon létre egy `AnchorLocateCriteria` objektumot, állítsa be a keresett azonosítókat, majd hívja meg a `CreateWatcher` metódust a munkamenetben a `AnchorLocateCriteria`megadásával.