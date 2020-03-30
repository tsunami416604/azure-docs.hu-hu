---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79128828"
---
Az eszköz alaphelyzetbe állításához biztonságosan ki kell törölnie az összes adatot az adatlemezen és az eszköz rendszerindító lemezén. 

A `Reset-HcsAppliance` parancsmag segítségével törölje ki az adatlemezeket és a rendszerindító lemezt, vagy csak az adatlemezeket. A `ClearData` `BootDisk` kapcsolók lehetővé teszik az adatlemezek és a rendszerindító lemez törlését.

A `BootDisk` kapcsoló törli a rendszerindító lemezt, és használhatatlanná teszi a készüléket. Csak akkor használható, ha az eszközt vissza kell küldeni a Microsoftnak. További információt [az Eszköz visszaküldése a Microsoftnak](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device)című témakörben talál.

Ha az eszköz alaphelyzetbe állítását a helyi webes felhasználói felületen használja, csak az adatlemezek et törli a rendszer biztonságosan, de a rendszerindító lemez érintetlen marad. A rendszerindító lemez tartalmazza az eszköz konfigurációját.

1. [Csatlakozzon a PowerShell-kapcsolathoz.](#connect-to-the-powershell-interface)
2. A parancssorba írja be a következőt:

    `Reset-HcsAppliance -ClearData -BootDisk`

    A következő példa bemutatja, hogyan kell használni ezt a parancsmamot:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
