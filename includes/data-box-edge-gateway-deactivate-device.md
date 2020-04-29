---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79128828"
---
Az eszköz alaphelyzetbe állításához biztonságosan ki kell törölni az adatlemez és az eszköz rendszerindító lemezének összes adatát. 

A `Reset-HcsAppliance` parancsmag használatával törölje az adatlemezeket és a rendszerindító lemezt, vagy csak az adatlemezeket. A `ClearData` és `BootDisk` kapcsolók lehetővé teszik az adatlemezek és a rendszerindító lemez törlését.

A `BootDisk` kapcsoló törli a rendszerindító lemezt, és használhatatlanná teszi az eszközt. Csak akkor használható, ha az eszközt vissza kell juttatni a Microsoftnak. További információ: [az eszköz visszaküldése a Microsoftnak](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device).

Ha a helyi webes felhasználói felületen az eszköz alaphelyzetbe állítását használja, csak az adatlemezek törlődnek, a rendszerindító lemez pedig sértetlen marad. A rendszerindító lemez tartalmazza az eszköz konfigurációját.

1. [Kapcsolódjon a PowerShell felületéhez](#connect-to-the-powershell-interface).
2. A parancssorba írja be a következőt:

    `Reset-HcsAppliance -ClearData -BootDisk`

    Az alábbi példa bemutatja, hogyan használhatja ezt a parancsmagot:

    ```powershell
    [10.128.24.33]: PS>Reset-HcsAppliance -ClearData -BootDisk

    Confirm
    Are you sure you want to perform this action?
    Performing the operation "Reset-HcsAppliance" on target "ShouldProcess appliance".
    [Y] Yes  [A] Yes to All  [N] No  [L] No to All  [?] Help (default is "Y"): N
    ```
