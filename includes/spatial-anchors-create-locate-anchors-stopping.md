---
ms.openlocfilehash: 6d6886996d926ce778600dfa1d69caaa1c7ac004
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "67178840"
---
## <a name="pause-reset-or-stop-the-session"></a>A munkamenet szüneteltetése, alaphelyzetbe állítása vagy leállítása

A munkamenet ideiglenes leállításához meghívhatja a meghívást. `Stop()` Ezzel leállítja a figyelők és a környezeti feldolgozás, akkor is, ha meghívja ProcessFrame(). Ezután meghívhat `Start()` a feldolgozás folytatásához. A folytatáskor a munkamenetben már rögzített környezeti adatok megmaradnak.
