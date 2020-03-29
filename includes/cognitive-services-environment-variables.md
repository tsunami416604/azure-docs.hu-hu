---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 367ae27a6afec803d2e3f98f54bdcf852330ddc6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "70274687"
---
## <a name="configure-an-environment-variable-for-authentication"></a>Környezeti változó konfigurálása hitelesítéshez

Az alkalmazásoknak hitelesíteniük kell az általuk használt Cognitive Services-hez való hozzáférést. A hitelesítéshez azt javasoljuk, hogy hozzon létre egy környezeti változót az Azure-erőforrások kulcsainak tárolására. 

Miután rendelkezik a kulccsal, írja be azt egy új környezeti változóba az alkalmazást futtató helyi számítógépen. A környezeti változó megadásához nyisson meg egy konzolablakot, és kövesse az operációs rendszerének megfelelő utasításokat. Cserélje `your-key` le az erőforrás egyik kulcsára.

#### <a name="windows"></a>[Windows](#tab/windows)

```console
setx COGNITIVE_SERVICE_KEY "your-key"
```

Miután hozzáadta a környezeti változót, előfordulhat, hogy újra kell indítania minden futó programot, amelynek szüksége lehet a környezeti változó beolvasására, beleértve a konzolablakot is. Ha például a Visual Studio-t használja szerkesztőként, indítsa újra a Visual Studio alkalmazást a példa futtatása előtt.

#### <a name="linux"></a>[Linux](#tab/linux)

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

#### <a name="macos"></a>[Macos](#tab/unix)

Módosítsa a .bash_profile fájlt, és adja hozzá a környezeti változót:

```bash
export COGNITIVE_SERVICE_KEY=your-key
```

A környezeti változó hozzáadását követően futtassa a `source .bash_profile` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.

***