---
ms.openlocfilehash: eafe71902ee849c310847f4537214722e129ad42
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907681"
---
## <a name="update-properties"></a>Tulajdonságok frissítése

A horgony tulajdonságainak frissítéséhez a UpdateAnchorProperties módszert használja. Ha két vagy több eszközt próbálja frissíteni ugyanazt a forráshorgony tulajdonságait egyszerre, egy optimista párhuzamossági modellt használjuk. Ami azt jelenti, hogy az első írási legyőzi-e.  Minden más írást egy "Egyidejűségi" hiba jelenik: tulajdonság frissítését lenne szükséges, mielőtt újra próbálkozna.
