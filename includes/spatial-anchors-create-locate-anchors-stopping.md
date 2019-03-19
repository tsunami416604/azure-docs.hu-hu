---
ms.openlocfilehash: 8e02067b32d9404a5bbdf7362b2cc32712b96250
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57907705"
---
## <a name="pause-reset-or-stop-the-session"></a>Szüneteltetés, visszaállítása vagy állítsa le a munkamenet

A munkamenet leállításához átmenetileg, hívhat Stop(). Így leáll minden olyan figyelők és a környezet feldolgozó, akkor is, ha ProcessFrame() indít el. Ezután hívhat Start() folytatására. Amikor folytatása, a munkamenet már rögzített környezeti adatok változatlan marad.
