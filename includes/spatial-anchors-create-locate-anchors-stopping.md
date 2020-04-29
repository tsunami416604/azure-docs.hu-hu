---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "67178840"
---
## <a name="pause-reset-or-stop-the-session"></a>A munkamenet szüneteltetése, alaphelyzetbe állítása vagy leállítása

Ha átmenetileg szeretné leállítani a munkamenetet, `Stop()`meghívhatja a következőt:. Ezzel a művelettel a figyelők és a környezetek feldolgozása is leáll, még akkor is, ha meghívja a ProcessFrame ()-t. Ezután meghívhatja `Start()` a feldolgozás folytatását. A folytatáskor a rendszer karbantartja a munkamenetben már rögzített környezeti adatait.
