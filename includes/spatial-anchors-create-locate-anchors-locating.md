---
ms.openlocfilehash: b5fec8bbc0db78454b080a411702014bd96f7db9
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76887683"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Felhőbeli térbeli horgony megkeresése

Az Azure Spatial Anchors használatának egyik fő oka egy korábban mentett felhőbeli térbeli horgony megkeresése. A felhőbeli térbeli horgonyok többféleképpen is megkereshetőek. Használhatja egy stratégia a figyelő egy időben.
- A horgonyok megkeresése azonosító alapján.
- Keresse meg a korábban egy korábban található horgonyhoz csatlakoztatott horgonyokat. A horgonykapcsolatokról [itt](/azure/spatial-anchors/concepts/anchor-relationships-way-finding/)olvashat.
- Keresse horgony segítségével [durva áthelyezése](/azure/spatial-anchors/concepts/coarse-reloc/).

Ha a felhőbeli térbeli horgonyok azonosító szerint található, érdemes tárolni a felhőtérbeli horgonyazonosítót az alkalmazás háttérszolgáltatásában, és elérhetővé kell tenni minden olyan eszköz számára, amely megfelelően hitelesíthető. Erre példa: [Oktatóanyag: Térbeli horgonyok megosztása az eszközök között.](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/)

Objektum példányosítása, `AnchorLocateCriteria` a keresett azonosítók beállítása, valamint `CreateWatcher` a munkamenet metódusának meghívása a `AnchorLocateCriteria`.