---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110525"
---
## <a name="update-properties"></a>Tulajdonságok frissítése

A horgony tulajdonságainak frissítéséhez használja a `UpdateAnchorProperties()` metódust. Ha két vagy több eszközt próbálja frissíteni ugyanazt a forráshorgony tulajdonságait egyszerre, egy optimista párhuzamossági modellt használjuk. Ami azt jelenti, hogy az első írási legyőzi-e.  Minden más írást egy "Egyidejűségi" hiba jelenik: tulajdonság frissítését lenne szükséges, mielőtt újra próbálkozna.
