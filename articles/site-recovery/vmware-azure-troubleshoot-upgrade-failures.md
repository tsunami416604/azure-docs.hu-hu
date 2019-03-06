---
title: A Microsoft Azure Site Recovery Provider frissítési hibáinak elhárítása |} A Microsoft Docs
description: Megismerheti és
author: vDonGlover
manager: jarrettr
ms.service: site-recovery
ms.topic: troubleshooting
ms.date: 02/05/2019
ms.author: v-doglov
ms.openlocfilehash: 3a3e9da66cf9ca6e08bb25b4f4b9d09aa0c5b6e7
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57431937"
---
# <a name="troubleshoot-microsoft-azure-site-recovery-provider-upgrade-failures"></a>A Microsoft Azure Site Recovery Provider frissítésével kapcsolatos hibák elhárítása

Ez a cikk segít a frissítés során a Microsoft Azure Site Recovery Provider hibákat okozó problémák megoldásához.

## <a name="the-upgrade-fails-reporting-that-the-latest-site-recovery-provider-is-already-installed"></a>A frissítés sikertelen lesz a jelentés a legutóbbi Site Recovery Provider már telepítve van

A Microsoft Azure Site Recovery szolgáltató (DRA) történő frissítés esetén az egyesített telepítő frissítése sikertelen, és a hibaüzenet problémák:

Frissítés nem támogatott, mivel a szoftver egy újabb verziója már telepítve van.

Szeretné frissíteni, használja az alábbi lépéseket:

1. Töltse le a Microsoft Azure Site Recovery egyesített telepítővel:
   1. "A jelenleg támogatott kumulatív hivatkozások" szakaszában a [szolgáltatási hírek az Azure Site Recoveryben](service-updates-how-to.md##links-to-currently-supported-update-rollups) cikkre, válassza ki a szolgáltatót, amelyre frissít.
   2. Az összesítő lapon keresse meg a **adatainak frissítése** szakaszt, és töltse le a kumulatív frissítés a Microsoft Azure Site Recovery egyesített telepítőjének.

2. Nyisson meg egy parancssort, és lépjen abba a mappába, amelyhez az egyesített telepítő fájlt letöltötte. A telepítési fájlok kinyerése a letöltés a következő parancsot, és MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q x:&lt;mappa elérési útja a kibontott fájlok&gt;.
    
    A példában szereplő parancs:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

3. A parancssorban lépjen abba a mappába, ahová kicsomagolta a fájlokat, és futtassa a következő telepítési parancsokat:
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART  UCX_SERVER_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART /UPGRADE

1. Térjen vissza a mappát, amelyhez az egyesített telepítő letöltötte és MicrosoftAzureSiteRecoveryUnifiedSetup.exe a frissítés befejezéséhez futtassa. 

## <a name="upgrade-failure-due-to-the-3rd-party-folder-being-renamed"></a>Frissítse a 3. fél mappa átnevezését miatti hiba

A frissítés sikeres a 3. fél mappába kell nem nevezhető át.

A probléma megoldásához.

2. Indítsa el a Beállításszerkesztőt (regedit.exe), és nyissa meg a HKEY_LOCAL_MACHINE\SOFTWARE\WOW6432Node\InMage Systems\Installed Products\10 ágat.
3. Vizsgálja meg a `Build_Version` kulcs értékét. Ha a legújabb verzióra van beállítva, csökkentheti a verziószámot. Például, ha a legújabb verzió 9.22. \* és a `Build_Version` kulcs set ezt az értéket, majd 9.21-re csökkenteni.\*.
4. A legújabb Microsoft Azure Site Recovery egyesített telepítőjének letöltése:
   1. "A jelenleg támogatott kumulatív hivatkozások" szakaszában a [szolgáltatási hírek az Azure Site Recoveryben](service-updates-how-to.md##links-to-currently-supported-update-rollups) cikkre, válassza ki a szolgáltatót, amelyre frissít.
   2. Az összesítő lapon keresse meg a **adatainak frissítése** szakaszt, és töltse le a kumulatív frissítés a Microsoft Azure Site Recovery egyesített telepítőjének.
5. Nyisson meg egy parancssort, és lépjen abba a mappába, amelyhez letöltött egyesített telepítő fájl- és a kivonatot a telepítési fájlokat, a letöltés a következő parancsot, és MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q x:&lt;mappa elérési útját a kibontott fájlokat&gt;.

    A példában szereplő parancs:

    MicrosoftAzureSiteRecoveryUnifiedSetup.exe /q /x:C:\Temp\Extracted

4. A parancssorban lépjen abba a mappába, ahová kicsomagolta a fájlokat, és futtassa a következő telepítési parancsokat:
   
    CX_THIRDPARTY_SETUP.EXE /VERYSILENT /SUPPRESSMSGBOXES /NORESTART

5. A Feladatkezelő segítségével figyelheti a telepítés előrehaladását. Amikor CX_THIRDPARTY_SETUP folyamatát. Exe-fájl már nem jelenik meg a Feladatkezelőt, folytassa a következő lépéssel.
6. Győződjön meg arról, hogy létezik-e C:\thirdparty, és hogy a mappa tartalmaz-e a RRD kódtárakat.
1. Térjen vissza a mappát, amelyhez az egyesített telepítő letöltötte és MicrosoftAzureSiteRecoveryUnifiedSetup.exe a frissítés befejezéséhez futtassa. 