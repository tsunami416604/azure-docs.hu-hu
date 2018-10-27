---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: e743128932f6a136820819c21e0386eb678c4203
ms.sourcegitcommit: 48592dd2827c6f6f05455c56e8f600882adb80dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2018
ms.locfileid: "50165901"
---
<!--author=jgerend last changed: 03/16/16-->

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

