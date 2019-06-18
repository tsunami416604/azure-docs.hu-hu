---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "66110607"
---
## <a name="pause-reset-or-stop-the-session"></a>Szüneteltetés, visszaállítása vagy állítsa le a munkamenet

A munkamenet leállításához átmenetileg, hívhat `Stop()`. Így leáll minden olyan figyelők és a környezet feldolgozó, akkor is, ha ProcessFrame() indít el. Ezután meghívja `Start()` folytatására. Amikor folytatása, a munkamenet már rögzített környezeti adatok változatlan marad.
