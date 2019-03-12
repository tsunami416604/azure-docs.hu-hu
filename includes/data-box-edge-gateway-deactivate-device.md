---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: aebb82690a7a49aba071ed64349d37d516208cca
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555162"
---
Eszköz alaphelyzetbe állításakor biztonságosan az adatlemezt, és az eszköz a rendszerindító lemez található összes adatot törölni kell. 

Használja a `Reset-HcsAppliance` parancsmag ki az adatlemezeket és a a rendszerindító lemez és az adatlemezek törlése. A `ClearData` és `BootDisk` kapcsolók lehetővé teszik az adatlemezeket és a rendszerindító lemez rendre törlése.

Ha az eszköz alaphelyzetbe állítása a helyi webes felhasználói Felületet használja, csak az adatlemezek biztonságos módon törlik a tartalmát, de a rendszerindító lemez érintetlen marad. A rendszerindító lemez tartalmazza az eszköz konfigurálása.

1. [Kapcsolódás a PowerShell-felületünkön](#connect-to-the-powershell-interface).
2. A parancssorba írja be a következőt:

    `Reset-HcsAppliance -ClearData -BootDisk`

    Az alábbi példa bemutatja, hogyan használható ennek a parancsmagnak:
    ```
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk
    
    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
