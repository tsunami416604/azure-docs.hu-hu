---
ms.openlocfilehash: a165061b2f927d6302504819ef27c846d76d0f30
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "83006499"
---
## <a name="pause-reset-or-stop-the-session"></a>A munkamenet szüneteltetése, alaphelyzetbe állítása vagy leállítása

Ha átmenetileg szeretné leállítani a munkamenetet, meghívhatja a következőt: `Stop()` . Ezzel a művelettel a figyelők és a környezet feldolgozása is leáll, még akkor is, ha meghívja `ProcessFrame()` . Ezután meghívhatja `Start()` a feldolgozás folytatását. A folytatáskor a rendszer karbantartja a munkamenetben már rögzített környezeti adatait.
