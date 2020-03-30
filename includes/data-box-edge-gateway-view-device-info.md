---
author: alkohli
ms.service: databox
ms.topic: include
ms.date: 03/04/2019
ms.author: alkohli
ms.openlocfilehash: d5af557a62f4bd35c242d334c28a38c3d632f7cf
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179579"
---
1. [Csatlakozzon a PowerShell-kapcsolathoz.](#connect-to-the-powershell-interface)
2. Használja `Get-HcsApplianceInfo` az információt az eszközhöz.

    A következő példa a parancsmag használatát mutatja be:

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

    Az alábbiakban egy táblázat ban összefoglalva néhány fontos eszközinformációkat:
    
    | Paraméter                             | Leírás                                                                                                                                                  |   |
    |--------------------------------|--------------------------------------------------------------------------------------------------------------------------------------------------------------|---|
    | Rövid név                   | Az eszköz rövid neve, ahogy a helyi webes felhasználói felületen keresztül konfigurálva van az eszköz telepítése során. Az alapértelmezett rövid név az eszköz sorozatszáma.  |   |
    | Sorozatszám                   | Az eszköz sorozatszáma a gyárban hozzárendelt egyedi szám.                                                                             |   |
    | Modell                          | A Data Box Edge vagy a Data Box Gateway eszköz modellje. A modell virtuális a Data Box Gateway és a fizikai Data Box Edge.                   |   |
    | FriendlySoftwareVersion        | Az eszköz szoftververziójának megfelelő barátságos karakterlánc. A rendszer futó előnézet, a barátságos szoftver verzió lenne Data Box Edge 1902. |   |
    | HcsVersion                     | A HCS szoftver verziója fut a készüléken. Például a Data Box Edge 1902-nek megfelelő HCS szoftververzió 1.4.771.324.            |   |
    | LocalcapacityInMb              | A készülék teljes helyi kapacitása Megabits-ben.                                                                                                        |   |
    | Regisztrált                   | Ez az érték azt jelzi, hogy az eszköz aktiválva van-e a szolgáltatással.                                                                                         |   |


