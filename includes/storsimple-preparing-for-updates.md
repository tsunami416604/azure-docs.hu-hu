---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 8c60e0275853f3c879db22f5414f0fbbbdb47b85
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86050438"
---
## <a name="preparing-for-updates"></a>Frissítések előkészítése
A frissítés vizsgálata és alkalmazása előtt végre kell hajtania a következő lépéseket:

1. Készítsen Felhőbeli pillanatképet az eszközről.
2. Győződjön meg arról, hogy a vezérlő rögzített IP-címei irányíthatók, és csatlakozhatnak az internethez. Ezeket a rögzített IP-címeket az eszköz frissítéseinek kiszolgálására fogjuk használni. Ezt úgy tesztelheti, ha a következő parancsmagot az eszköz Windows PowerShell-felületén lévő összes vezérlőn futtatja:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
    **Minta kimenete teszteléshez – kapcsolat, ha a fix IP-címek csatlakozni tudnak az internethez**

    ```output
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
    ```

Miután sikeresen elvégezte a manuális előzetes ellenőrzéseket, folytathatja a frissítések vizsgálatát és telepítését.

