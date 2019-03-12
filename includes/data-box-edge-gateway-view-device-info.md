---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: 757d6aeecc6c97726e890076b0e27a82a1a45d27
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57555150"
---
1. [Kapcsolódás a PowerShell-felületünkön](#connect-to-the-powershell-interface).
2. Használja a `Get-HcsApplianceInfo` lekérni az adatait az eszköz számára.

    Az alábbi példa bemutatja ennek a parancsmagnak a használatát:

    ```
    [10.100.10.10]: PS>Get-HcsApplianceInfo
    
    Id                            : b2044bdb-56fd-4561-a90b-407b2a67bdfc
    FriendlyName                  : DBE-NBSVFQR94S6
    Name                          : DBE-NBSVFQR94S6
    SerialNumber                  : HCS-NBSVFQR94S6
    DeviceId                      : 40d7288d-cd28-481d-a1ea-87ba9e71ca6b
    Model                         : Virtual
    FriendlySoftwareVersion       : Data Box Gateway 1902
    HcsVersion                    : 1.4.771.324
    IsClustered                   : False
    IsVirtual                     : True
    LocalCapacityInMb             : 1964992
    SystemState                   : Initialized
    SystemStatus                  : Normal
    Type                          : DataBoxGateway
    CloudReadRateBytesPerSec      : 0
    CloudWriteRateBytesPerSec     : 0
    IsInitialPasswordSet          : True
    FriendlySoftwareVersionNumber : 1902
    UploadPolicy                  : All
    DataDiskResiliencySettingName : Simple
    ApplianceTypeFriendlyName     : Data Box Gateway
    IsRegistered                  : False
    ```

    Íme néhány a fontos információkat összefoglalójához tábla:
    
    | Paraméter                             | Leírás                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | Rövid név                   | Az eszköz helyi webes felületén keresztül az eszköz üzembe helyezése során konfigurált rövid neve. Az alapértelmezett rövid név az eszköz sorozatszámát.  |   |
    | a sorozatszám                   | Az eszköz gyári számát az gyári rendelt egyedi számot.                                                                             |   |
    | Modell                          | A Data Box Edge vagy az adatátjáró Box-eszköz modellje. A modell használható a Data Box-átjáró virtuális és fizikai a Data Box Edge-hez.                   |   |
    | FriendlySoftwareVersion        | A rövid karakterlánc, amely megfelel az eszköz szoftververzióján. A rövid szoftververzió preview rendszert, a Data Box Edge 1902 lehet. |   |
    | HcsVersion                     | A HCS szoftver verziója fut az eszközön. Például a Data Box Edge 1902 megfelelő HCS szoftververzió 1.4.771.324.            |   |
    | LocalCapacityInMb              | A teljes helyi kapacitás az eszköz megabit.                                                                                                        |   |
    | IsRegistered                   | Ez az érték azt jelzi, ha az eszköz a szolgáltatás aktiválva van-e.                                                                                         |   |
    ```

