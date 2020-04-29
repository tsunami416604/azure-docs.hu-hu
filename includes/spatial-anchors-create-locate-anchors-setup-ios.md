---
ms.openlocfilehash: 26eff7b10542843be561264c316bdcf1a63271f7
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67178820"
---
## <a name="setting-up-the-library"></a>A könyvtár beállítása

A Start () parancs meghívásával lehetővé teheti a munkamenet számára a környezeti adatfeldolgozást.

A munkamenet által kiváltott események kezeléséhez állítsa `delegate` a munkamenet tulajdonságát egy objektumra, például a nézetre. Ennek az objektumnak meg kell valósítania a SSCCloudSpatialAnchorSessionDelegate protokollt.
