---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "67178832"
---
## <a name="update-properties"></a>Tulajdonságok frissítése

Egy horgony tulajdonságainak frissítéséhez használja a `UpdateAnchorProperties()` metódust. Ha két vagy több eszköz egyszerre próbálkozik ugyanazon horgony tulajdonságainak frissítésével, optimista párhuzamossági modellt használunk. Ez azt jelenti, hogy az első írás fog nyerni.  Az összes többi írás "Egyidejűség" hibaüzenetet kap: az újbóli próbálkozás előtt szükség lesz a tulajdonságok frissítésére.
