---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 367ae27a6afec803d2e3f98f54bdcf852330ddc6
ms.sourcegitcommit: 32242bf7144c98a7d357712e75b1aefcf93a40cc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/04/2019
ms.locfileid: "70274687"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Környezeti változó konfigurálása hitelesítéshez

Az alkalmazásoknak hitelesíteniük kell az általuk használt Cognitive Services hozzáférését. A hitelesítéshez javasoljuk, hogy hozzon létre egy környezeti változót az Azure-erőforrások kulcsainak tárolásához. 

Miután elvégezte a kulcsot, írja azt egy új környezeti változóba az alkalmazást futtató helyi gépen. A környezeti változó megadásához nyisson meg egy konzolablakot, és kövesse az operációs rendszerének megfelelő utasításokat. Cserélje `your-key` le az értékét az erőforrás egyik kulcsára.

#### <a name="windowstabwindows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

Miután hozzáadta a környezeti változót, előfordulhat, hogy újra kell indítania minden futó programot, amelynek szüksége lehet a környezeti változó beolvasására, beleértve a konzolablakot is. Ha például a Visual studiót használja szerkesztőként, indítsa újra a Visual studiót a példa futtatása előtt.

#### <a name="linuxtablinux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

#### <a name="macostabunix"></a>[macOS](#tab/unix)

Módosítsa a .bash_profile fájlt, és adja hozzá a környezeti változót:

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

A környezeti változó hozzáadását követően futtassa a `source .bash_profile` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

***