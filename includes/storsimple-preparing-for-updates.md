---
author: alkohli
ms.service: storsimple
ms.topic: include
ms.date: 10/26/2018
ms.author: alkohli
ms.openlocfilehash: 4e262c9e5bb88e77bc9c09853c06f4cdb41eedaa
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "67179312"
---
## <a name="preparing-for-updates"></a>Felkészülés a frissítésekre
A frissítés bevizsgálata és alkalmazása előtt végre kell hajtania a következő lépéseket:

1. Az eszközadatok felhőbeli pillanatképe.
2. Győződjön meg arról, hogy a vezérlő rögzített IP-címeirányíthető, és képes csatlakozni az internethez. Ezeket a rögzített IP-ket az eszköz frissítéseinek szervizelésére használjuk. Ezt úgy tesztelheti, hogy az eszköz Windows PowerShell-felületéről a következő parancsmast futtatja az egyes vezérlőkön:
   
     `Test-Connection -Source <Fixed IP of your device controller> -Destination <Any IP or computer name outside of datacenter network>`
   
    **Mintakimenet a tesztkapcsolathoz, ha a rögzített IP-k csatlakozhatnak az internethez**

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

Miután sikeresen befejezte ezeket a manuális előzetes ellenőrzéseket, folytathatja a frissítések vizsgálatát és telepítését.

