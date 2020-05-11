---
ms.openlocfilehash: e7e3a31663e6c166b912ba20166ba0f0f769008a
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006407"
---
## <a name="setting-up-the-library"></a>A könyvtár beállítása

Meghívja `Start()` a-munkamenetet a környezeti adatfeldolgozás engedélyezéséhez.

A munkamenet által kiváltott események kezeléséhez állítsa `delegate` a munkamenet tulajdonságát egy objektumra, például a nézetre. Ennek az objektumnak meg `SSCCloudSpatialAnchorSessionDelegate` kell valósítania a protokollt.
