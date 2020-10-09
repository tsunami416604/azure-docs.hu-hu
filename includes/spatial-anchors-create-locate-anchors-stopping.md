---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "83006499"
---
## <a name="pause-reset-or-stop-the-session"></a>A munkamenet szüneteltetése, alaphelyzetbe állítása vagy leállítása

Ha átmenetileg szeretné leállítani a munkamenetet, meghívhatja a következőt: `Stop()` . Ezzel a művelettel a figyelők és a környezet feldolgozása is leáll, még akkor is, ha meghívja `ProcessFrame()` . Ezután meghívhatja `Start()` a feldolgozás folytatását. A folytatáskor a rendszer karbantartja a munkamenetben már rögzített környezeti adatait.
