---
author: aahill
ms.service: cognitive-services
ms.topic: include
ms.date: 06/24/2019
ms.author: aahi
ms.openlocfilehash: 734fce2c01614d5dca73f171fb59f25f39d13705
ms.sourcegitcommit: aa66898338a8f8c2eb7c952a8629e6d5c99d1468
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67461494"
---
## <a name="configure-an-environment-variable-for-authentication"></a>A hitelesítési környezeti változó konfigurálása

Alkalmazások hozzáférésének engedélyezése a Cognitive Services használata kell. Hitelesítést végezni, azt javasoljuk egy környezeti változót, egy kulcs tárolása az Azure-erőforrások létrehozásához. 

Miután a kulcs, kiírhatja azokat egy új környezeti változóba az alkalmazást futtató helyi gépen. A környezeti változó megadásához nyisson meg egy konzolablakot, és kövesse az operációs rendszerének megfelelő utasításokat. Cserélje le `your-key` a Anomáliadetektálási detector használatával hívóbetűre:

* Windows

    ```console
    setx COGNITIVE_SERVICE_KEY "your-key"
    ```

    Miután hozzáadta a környezeti változót, előfordulhat, hogy újra kell indítania minden futó programot, amelynek szüksége lehet a környezeti változó beolvasására, beleértve a konzolablakot is. Például ha szerkesztőként a Visual Studio használ, indítsa újra a Visual Studio a példa futtatása előtt.

* Linux
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    A környezeti változó hozzáadását követően futtassa a `source ~/.bashrc` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.
    
* macOS
    
    Módosítsa a .bash_profile fájlt, és adja hozzá a környezeti változót:
    
    ```bash
    export COGNITIVE_SERVICE_KEY=your-key
    ```
    
    A környezeti változó hozzáadását követően futtassa a `source .bash_profile` parancsot a konzolablakban a módosítások érvénybe léptetéséhez.
