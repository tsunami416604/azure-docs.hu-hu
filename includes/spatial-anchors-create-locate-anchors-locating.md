---
ms.openlocfilehash: 430302d8b1b2a01febbbe2f11057bb331ec80c28
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907742"
---
## <a name="locate-a-cloud-spatial-anchor"></a>Keresse meg a felhő térbeli forráshorgony

Térbeli felhőbeli horgonyok megkereséséhez kell ismernie a azonosítókról. Jegyzetobjektum azonosítók a háttérszolgáltatásokat az alkalmazás által tárolt és elérhető-e az összes eszközre, hogy megfelelően hitelesíteni tudják magukat, lehet. Lásd a példát [oktatóanyag: Térbeli horgonyok megosztása eszközök](/azure/spatial-anchors/tutorials/tutorial-share-anchors-across-devices/).

Egy AnchorLocateCriteria objektumpéldányt, és állítsa az azonosítók keres, és a CreateWatcher metódus meghívása a munkamenetben a AnchorLocateCriteria megadásával.
