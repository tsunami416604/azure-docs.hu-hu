---
title: A Microsoft Azure webhely-helyreállítási szolgáltató frissítésének – problémamegoldás
description: A Microsoft Azure Site Recovery-szolgáltató frissítésekor felmerülő gyakori problémák megoldása.
author: rayne-wiselman
manager: carmonm
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 11/10/2019
ms.author: raynew
ms.openlocfilehash: b59f933fedd5f1d3ed3f7972b1a1fe653df31be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75893901"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>A Microsoft Azure Site Recovery Provider frissítésével kapcsolatos hibák elhárítása

Ez a cikk segítséget nyújt a Microsoft Azure webhely-helyreállítási szolgáltató frissítése során hibákat okozó problémák megoldásában.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>A frissítés nem jelenti, hogy a legújabb hely-helyreállítási szolgáltató már telepítve van

A Microsoft Azure Site Recovery Provider (DRA) frissítésekor az egyesített telepítés frissítése sikertelen lesz, és kiadja a hibaüzenetet:

A frissítés nem támogatott, mivel a szoftver egy magasabb verziója már telepítve van.

A frissítéshez kövesse az alábbi lépéseket:

1. Töltse le a Microsoft Azure Site Recovery egyesített telepítését:
   1. Az Azure Site Recovery cikk [szolgáltatásfrissítéseinek](service-updates-how-to.md#links-to-currently-supported-update-rollups) "Hivatkozások a jelenleg támogatott összegző frissítőcsomagokra" című szakaszában válassza ki azt a szolgáltatót, amelyre frissít.
   2. Az összegző lapon keresse meg a **Frissítési információk szakaszt,** és töltse le a Microsoft Azure Site Recovery egyesített telepítésének összesítő frissítőcsomagját.

2. Nyisson meg egy parancssort, és keresse meg azt a mappát, amelybe az Egyesített telepítő fájlt töltötte le. Bontsa ki a telepítőfájlokat a letöltésből a következő paranccsal: MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:&lt;a kibontott fájlok&gt;mappaelérési útja .
    
    Példa parancs:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

3. A parancssorban keresse meg azt a mappát, amelybe kibontotta a fájlokat, és futtassa a következő telepítési parancsokat:
   
    CX_THIRDPARTY_SETUP. EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART UCX_SERVER_SETUP. EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART /UPGRADE

1. Térjen vissza arra a mappára, amelybe letöltötte az egyesített telepítőt, és futtassa a MicrosoftAzureSiteRecoveryUnifiedSetup.exe programot a frissítés befejezéséhez. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Frissítési hiba a külső fél től származó mappa átnevezése miatt

A frissítés sikeressedéséhez a harmadik féltől származó mappát nem szabad átnevezni.

A probléma megoldásához.

1. Indítsa el a Rendszerleíróadatbázis-szerkesztőt (regedit.exe), és nyissa meg a HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10 ágat.
1. Vizsgálja `Build_Version` meg a kulcs értékét. Ha a legújabb verzióra van beállítva, csökkentse a verziószámot. Ha például a legújabb verzió 9,22. \* és `Build_Version` a kulcs beállítása erre az értékre, majd csökkentse azt 9,21-re. \*.
1. Töltse le a Microsoft Azure webhely-helyreállítási egységes telepítését:
   1. Az Azure Site Recovery cikk [szolgáltatásfrissítéseinek](service-updates-how-to.md#links-to-currently-supported-update-rollups) "Hivatkozások a jelenleg támogatott összegző frissítőcsomagokra" című szakaszában válassza ki azt a szolgáltatót, amelyre frissít.
   2. Az összegző lapon keresse meg a **Frissítési információk szakaszt,** és töltse le a Microsoft Azure Site Recovery egyesített telepítésének összesítő frissítőcsomagját.
1. Nyisson meg egy parancssort, és keresse meg azt a mappát, amelybe az Egyesített telepítőfájlt töltötte be, és bontsa&lt;ki a telepítőfájlokat&gt;a letöltésből a következő paranccsal: MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x: a kibontott fájlok mappaelérési útja .

    Példa parancs:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

1. A parancssorban keresse meg azt a mappát, amelybe kibontotta a fájlokat, és futtassa a következő telepítési parancsokat:
   
    CX_THIRDPARTY_SETUP. EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART

1. A feladatkezelő vel figyelheti a telepítés előrehaladását. Amikor a folyamat CX_THIRDPARTY_SETUP. EXE már nem látható a feladatkezelőben, folytassa a következő lépéssel.
1. Ellenőrizze, hogy létezik-e C:\harmadik fél, és hogy a mappa tartalmazza-e az RRD-tárakat.
1. Térjen vissza arra a mappára, amelybe letöltötte az egyesített telepítőt, és futtassa a MicrosoftAzureSiteRecoveryUnifiedSetup.exe programot a frissítés befejezéséhez. 