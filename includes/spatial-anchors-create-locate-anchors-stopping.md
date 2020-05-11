---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: ac4a365a6c6ffa6b6a5fbca1b8f17fde87b4c05e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/10/2020
ms.locfileid: "83006499"
---
## <a name="pause-reset-or-stop-the-session"></a>A munkamenet szüneteltetése, alaphelyzetbe állítása vagy leállítása

Ha átmenetileg szeretné leállítani a munkamenetet, `Stop()`meghívhatja a következőt:. Ezzel a művelettel a figyelők és a környezet feldolgozása is leáll, még `ProcessFrame()`akkor is, ha meghívja. Ezután meghívhatja `Start()` a feldolgozás folytatását. A folytatáskor a rendszer karbantartja a munkamenetben már rögzített környezeti adatait.
