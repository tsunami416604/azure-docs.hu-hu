---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006407"
---
## <a name="setting-up-the-library"></a>A könyvtár beállítása

Meghívja a `Start()` -munkamenetet a környezeti adatfeldolgozás engedélyezéséhez.

A munkamenet által kiváltott események kezeléséhez állítsa a `delegate` munkamenet tulajdonságát egy objektumra, például a nézetre. Ennek az objektumnak meg kell valósítania a `SSCCloudSpatialAnchorSessionDelegate` protokollt.
