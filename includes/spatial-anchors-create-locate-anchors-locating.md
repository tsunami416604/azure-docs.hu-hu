---
ms.openlocfilehash: 52dfbfca5f79a7f92848ea39eddc00aa10f05ff1
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110655"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Keresse meg a felhő térbeli forráshorgony

Keresse meg a térbeli korábban feltöltött felhőbeli horgony magától egyik az elsődleges oka az Azure térbeli horgonyok kódtár használatával. Térbeli felhőbeli horgonyok megkereséséhez kell ismernie a azonosítókról. Jegyzetobjektum azonosítók is tárolhatók, az alkalmazás háttér-szolgáltatásban, és elérhető-e az összes eszközre, hogy megfelelően hitelesíteni tudják magukat. Lásd a példát [oktatóanyag: Térbeli horgonyok megosztása eszközök](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Hozza létre egy `AnchorLocateCriteria` objektumazonosító, állítsa be az azonosítók keres, és hívja a `CreateWatcher` metódust a munkamenet megadásával a `AnchorLocateCriteria`.
