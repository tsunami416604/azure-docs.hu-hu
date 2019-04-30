---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60232523"
---
## <a name="update-properties"></a>Tulajdonságok frissítése

A horgony tulajdonságainak frissítéséhez használja a `UpdateAnchorProperties()` metódust. Ha két vagy több eszközt próbálja frissíteni ugyanazt a forráshorgony tulajdonságait egyszerre, egy optimista párhuzamossági modellt használjuk. Ami azt jelenti, hogy az első írási legyőzi-e.  Minden más írást egy "Egyidejűségi" hiba jelenik: tulajdonság frissítését lenne szükséges, mielőtt újra próbálkozna.
