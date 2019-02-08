---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 0c89ed34b21ca0c41d4f7765d99d8fe8bf7c647d
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55889284"
---
## <a name="preparing-for-updates"></a>Frissítések előkészítése
A következő lépésekkel vizsgálata és a frissítés telepítése előtt kell:

1. Az eszköz adatok felhőbeli pillanatkép készítése.
2. Győződjön meg arról, hogy a vezérlő rögzített IP-címek irányítható, és képes csatlakozni az internethez. A fix IP-címek az eszköz frissítéseinek karbantartásához használható. A következő parancsmag futtatásával minden vezérlő az eszköz a Windows PowerShell interfészről tesztelheti:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network> `
   
    **Példa a kimenetre a Test-Connection, amikor a fix IP-címek képes csatlakozni az internethez**

        Controller0>Test-Connection -Source 10.126.173.91 -Destination bing.com

        Source      Destination     IPV4Address      IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200
        HCSNODE0  bing.com        204.79.197.200

        Controller0>Test-Connection -Source 10.126.173.91 -Destination  204.79.197.200

        Source      Destination       IPV4Address    IPV6Address
        ----------------- -----------  -----------
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200
        HCSNODE0  204.79.197.200  204.79.197.200

Miután ezek manuális előzetes ellenőrzése sikeresen befejeződött, folytassa vizsgálata, és telepítse a frissítéseket.

