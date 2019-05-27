---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66110607"
---
## <a name="pause-reset-or-stop-the-session"></a>Szüneteltetés, visszaállítása vagy állítsa le a munkamenet

A munkamenet leállításához átmenetileg, hívhat `Stop()`. Így leáll minden olyan figyelők és a környezet feldolgozó, akkor is, ha ProcessFrame() indít el. Ezután meghívja `Start()` folytatására. Amikor folytatása, a munkamenet már rögzített környezeti adatok változatlan marad.
