---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "67178832"
---
## <a name="update-properties"></a>Tulajdonságok frissítése

A horgony tulajdonságainak frissítéséhez használja `UpdateAnchorProperties()` a módszert. Ha két vagy több eszköz egyszerre próbálja meg frissíteni a tulajdonságokat ugyanahhoz a horgonyhoz, optimista egyidejűségi modellt használunk. Ami azt jelenti, hogy az első írás fog nyerni.  Minden más írási hiba "Egyidejűség" hibaüzenetet kap: a tulajdonságok frissítésére lenne szükség, mielőtt újra próbálkozna.
