---
title: A Microsoft Azure Site Recovery-szolgáltató frissítésével kapcsolatos hibák elhárítása | Microsoft Docs
description: Megismerés és
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 02/05/2019
ms.author: raynew
ms.openlocfilehash: 6bb8263e7291506be6af7d5af7eda5786274d73c
ms.sourcegitcommit: c2e7595a2966e84dc10afb9a22b74400c4b500ed
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2019
ms.locfileid: "71972616"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>A Microsoft Azure Site Recovery Provider frissítésével kapcsolatos hibák elhárítása

Ez a cikk segítséget nyújt a Microsoft Azure Site Recovery-szolgáltató frissítése során fellépő hibák elhárításához.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>A frissítés nem tud jelentést arról, hogy a legújabb Site Recovery-szolgáltató már telepítve van

Microsoft Azure Site Recovery-szolgáltató (DRA) frissítésekor az egyesített telepítés frissítése meghiúsul, és a következő hibaüzenetet jeleníti meg:

A frissítés nem támogatott, mert a szoftver újabb verziója már telepítve van.

A frissítéshez kövesse az alábbi lépéseket:

1. Töltse le a Microsoft Azure Site Recovery egyesített telepítőt:
   1. A Azure Site Recovery cikkben található [szolgáltatási frissítések](service-updates-how-to.md##links-to-currently-supported-update-rollups) jelenleg támogatott kumulatív frissítésekre vonatkozó szakaszában válassza ki azt a szolgáltatót, amelyre frissíteni kíván.
   2. Az Összesítés lapon keresse meg a **frissítési adatok** szakaszt, és töltse le a Microsoft Azure site Recovery egyesített telepítő kumulatív frissítését.

2. Nyisson meg egy parancssort, és navigáljon ahhoz a mappához, amelyhez az egyesített telepítőfájlt letöltötte. Bontsa ki a telepítési fájlokat a letöltésből a következő paranccsal, MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x: &lt;folder elérési út a kibontott fájlokhoz @ no__t-1.
    
    A példában szereplő parancs:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

3. A parancssorban navigáljon ahhoz a mappához, amelyhez kibontotta a fájlokat, és futtassa a következő telepítési parancsokat:
   
    CX_THIRDPARTY_SETUP. EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART UCX_SERVER_SETUP. EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART/UPGRADE

1. Térjen vissza ahhoz a mappához, amelyre az egyesített telepítőt letöltötte, majd futtassa a MicrosoftAzureSiteRecoveryUnifiedSetup. exe fájlt a frissítés befejezéséhez. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>A harmadik féltől származó mappa neve miatt sikertelen volt a frissítés

Ahhoz, hogy a frissítés sikeres legyen, a harmadik féltől származó mappa nem nevezhető át.

A probléma megoldásához.

1. Indítsa el a Beállításszerkesztőt (Regedit. exe), és nyissa meg a HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10 ágat.
1. Vizsgálja meg a `Build_Version` kulcs értékét. Ha a legújabb verzióra van beállítva, csökkentse a verziószámot. Ha például a legújabb verzió a 9,22. \* és a `Build_Version` kulcs értéke erre az értékre van állítva, akkor csökkentse a 9,21. \* értéket.
1. Töltse le a legújabb Microsoft Azure Site Recovery egyesített telepítőt:
   1. A Azure Site Recovery cikkben található [szolgáltatási frissítések](service-updates-how-to.md##links-to-currently-supported-update-rollups) jelenleg támogatott kumulatív frissítésekre vonatkozó szakaszában válassza ki azt a szolgáltatót, amelyre frissíteni kíván.
   2. Az Összesítés lapon keresse meg a **frissítési adatok** szakaszt, és töltse le a Microsoft Azure site Recovery egyesített telepítő kumulatív frissítését.
1. Nyisson meg egy parancssort, és navigáljon ahhoz a mappához, amelyhez az egyesített telepítőfájlt letöltötte, és a telepítési fájlok kibontása a letöltésből a következő paranccsal, MicrosoftAzureSiteRecoveryUnifiedSetup. exe/q/x: &lt;folder elérési út a kinyert fájlok @ no__t-1.

    A példában szereplő parancs:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

1. A parancssorban navigáljon ahhoz a mappához, amelyhez kibontotta a fájlokat, és futtassa a következő telepítési parancsokat:
   
    CX_THIRDPARTY_SETUP. EXE/VERYSILENT/SUPPRESSMSGBOXES/NORESTART

1. A Feladatkezelő segítségével figyelheti a telepítés folyamatát. A CX_THIRDPARTY_SETUP folyamata. Az EXE már nem látható a Feladatkezelő eszközben, folytassa a következő lépéssel.
1. Ellenőrizze, hogy a C:\thirdparty létezik-e, és hogy a mappa tartalmazza-e a RRD-kódtárakat.
1. Térjen vissza ahhoz a mappához, amelyre az egyesített telepítőt letöltötte, majd futtassa a MicrosoftAzureSiteRecoveryUnifiedSetup. exe fájlt a frissítés befejezéséhez. 