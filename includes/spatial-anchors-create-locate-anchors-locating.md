---
ms.openlocfilehash: b5fec8bbc0db78454b080a411702014bd96f7db9
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "76887683"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Felhőbeli térbeli horgony keresése

A korábban mentett Felhőbeli térbeli horgonyok egyike az Azure térbeli horgonyok használatának egyik fő oka. Több különböző módon is megtalálhatja a Felhőbeli térbeli horgonyt. Egyszerre egyetlen stratégiát is használhat a figyelőkhöz.
- Horgonyok megkeresése azonosító alapján.
- Egy korábban található horgonyhoz csatlakoztatott horgonyok megkeresése. [Itt](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/)megtudhatja, hogyan lehet kapcsolatot kimutatni.
- Keresse meg a horgonyt [durva újrahonosítással](/azure/spatial-anchors/concepts/coarse-reloc/).

Ha a Felhőbeli térbeli horgonyokat azonosító alapján keresi, a Felhőbeli térbeli horgony azonosítóját az alkalmazás háttér-szolgáltatásában kell tárolnia, és elérhetővé kell tennie az összes olyan eszköz számára, amely képes a megfelelő hitelesítésre. Erre példa [: oktatóanyag: térbeli horgonyok megosztása az eszközök között](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Hozzon létre `AnchorLocateCriteria` egy objektumot, állítsa be a keresett azonosítókat, és hívja meg a `CreateWatcher` metódust a munkamenetben a következő `AnchorLocateCriteria`megadásával:.