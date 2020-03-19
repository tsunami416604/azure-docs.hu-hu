---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/05/2019
ms.author: alkohli
ms.openlocfilehash: 21c19027d21a87e199d74644cfc5c8f3cd52ba4c
ms.sourcegitcommit: f97d3d1faf56fb80e5f901cd82c02189f95b3486
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/11/2020
ms.locfileid: "79128828"
---
Az eszköz visszaállításához biztonságosan törölnie kell az eszköz adatlemezén és rendszerindító lemezén található összes adatot. 

Használja a `Reset-HcsAppliance` parancsmagot az adatlemezek és a rendszerindító lemez, vagy csak az adatlemezek adatainak törléséhez. A `ClearData` és `BootDisk` kapcsolók lehetővé teszik az adatlemezek, illetve a rendszerindító lemez törlését.

A `BootDisk` kapcsoló törli a rendszerindító lemezt, és használhatatlanná teszi az eszközt. Csak akkor érdemes használni, ha az eszközt vissza kell küldeni a Microsoftnak. További információért lásd az [eszköz Microsoftnak való visszaküldését ismertető](https://docs.microsoft.com/azure/databox-online/data-box-edge-return-device) szakaszt.

Ha az eszköz visszaállításához a helyi webes felhasználói felületet használja, csak az adatlemezek biztonságos törlése megy végbe, a rendszerindító lemez pedig érintetlen marad. A rendszerindító lemez tartalmazza az eszköz konfigurációját.

1. [Csatlakozás a PowerShell-felülethez](#connect-to-the-powershell-interface).
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
