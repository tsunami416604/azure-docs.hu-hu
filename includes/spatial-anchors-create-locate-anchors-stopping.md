---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67178840"
---
## <a name="pause-reset-or-stop-the-session"></a>Szüneteltetés, visszaállítása vagy állítsa le a munkamenet

A munkamenet leállításához átmenetileg, hívhat `Stop()`. Így leáll minden olyan figyelők és a környezet feldolgozó, akkor is, ha ProcessFrame() indít el. Ezután meghívja `Start()` folytatására. Amikor folytatása, a munkamenet már rögzített környezeti adatok változatlan marad.
