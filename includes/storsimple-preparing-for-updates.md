---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4e262c9e5bb88e77bc9c09853c06f4cdb41eedaa
ms.sourcegitcommit: a60a55278f645f5d6cda95bcf9895441ade04629
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2019
ms.locfileid: "58890975"
---
## <a name="preparing-for-updates"></a>Frissítések előkészítése
A következő lépésekkel vizsgálata és a frissítés telepítése előtt kell:

1. Az eszköz adatok felhőbeli pillanatkép készítése.
2. Győződjön meg arról, hogy a vezérlő rögzített IP-címek irányítható, és képes csatlakozni az internethez. A fix IP-címek az eszköz frissítéseinek karbantartásához használható. A következő parancsmag futtatásával minden vezérlő az eszköz a Windows PowerShell interfészről tesztelheti:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
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

