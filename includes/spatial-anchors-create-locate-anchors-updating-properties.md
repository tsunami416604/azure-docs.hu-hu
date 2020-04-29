---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67178832"
---
## <a name="update-properties"></a>Tulajdonságok frissítése

Egy horgony tulajdonságainak frissítéséhez használja a `UpdateAnchorProperties()` metódust. Ha két vagy több eszköz egyszerre próbálkozik ugyanazon horgony tulajdonságainak frissítésével, optimista párhuzamossági modellt használunk. Ez azt jelenti, hogy az első írás fog nyerni.  Az összes többi írás "Egyidejűség" hibaüzenetet kap: az újbóli próbálkozás előtt szükség lesz a tulajdonságok frissítésére.
