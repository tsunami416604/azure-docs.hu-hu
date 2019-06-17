---
ms.openlocfilehash: 8ebb10f955be8f3004fdbdc595ea0fefc0d2b7ea
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110525"
---
## <a name="update-properties"></a>Tulajdonságainak frissítése

A horgony tulajdonságainak frissítéséhez használja a `UpdateAnchorProperties()` metódust. Ha két vagy több eszközt próbálja frissíteni ugyanazt a forráshorgony tulajdonságait egyszerre, egy optimista párhuzamossági modellt használjuk. Ami azt jelenti, hogy az első írási legyőzi-e.  Minden más írást egy "Egyidejűségi" hiba jelenik: tulajdonság frissítését lenne szükséges, mielőtt újra próbálkozna.
