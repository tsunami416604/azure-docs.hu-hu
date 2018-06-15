---
title: A StorSimple eszköz üzembe helyezett hibaelhárítása |} Microsoft Docs
description: Ismerteti a StorSimple eszközön a jelenleg telepített és működő bekövetkező hibák megoldásában.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: cf037f7f1c1384b654a7144485d38f569eb7c167
ms.sourcegitcommit: e2adef58c03b0a780173df2d988907b5cb809c82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2018
ms.locfileid: "32187334"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Az operatív StorSimple eszköz hibáinak elhárítása
> [!NOTE]
> A StorSimple klasszikus portálja elavult. A StorSimple-eszközkezelők automatikusan átkerülnek az új Azure Portalra az elavulási ütemezésnek megfelelően. Erről az áthelyezésről kapni fog egy e-mailt és egy Portal-értesítést. Ez a dokumentum hamarosan el lesz távolítva. Ha kérdései vannak az áthelyezéssel kapcsolatban, tekintse meg a [Gyakori kérdések: Váltás az Azure Portalra](storsimple-8000-move-azure-portal-faq.md) szakaszt.

## <a name="overview"></a>Áttekintés
A cikkben kapcsolatos konfigurációs problémák hasznos hibaelhárítási útmutatót, hogy esetleg felmerülő után a StorSimple eszköz üzembe helyezett és működik. Kapcsolatos gyakori hibák, lehetséges okok, és a Microsoft Azure StorSimple futtatásakor az esetleg előforduló problémák megoldását segítő javasolt lépéseket ismerteti. Ez az információ a StorSimple fizikai eszköz helyszíni, mind a StorSimple virtuális eszköz vonatkozik.

A Microsoft Azure StorSimple művelet során esetleg előforduló hibakódok listáját megtalálja a cikk végén valamint lépéseket is tehet javítsa ki a hibákat. 

## <a name="setup-wizard-process-for-operational-devices"></a>A telepítő varázsló folyamat működési eszközökhöz
A telepítő varázsló ([Invoke-HcsSetupWizard][1]) az eszközök konfigurációját és javítási művelet elvégzése, ha szükséges.

A telepítővarázsló a korábban konfigurált és működési eszköz futtatásakor a folyamatábra nem azonos. Módosíthatja, hogy csak az alábbi bejegyzéseket:

* IP-cím, alhálózati maszk és átjáró
* Elsődleges DNS-kiszolgáló
* Elsődleges NTP-kiszolgáló
* Nem kötelező webproxy-konfigurációja

A telepítővarázsló nem jelszó adatgyűjtési és -eszköz regisztrációs kapcsolatos műveletek végrehajtása.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>A telepítési varázsló ezt követő futtatása során előforduló hibák
Az alábbi táblázat a hibákat, hogy a telepítő varázsló működési-eszközön, a hibák lehetséges okát futtatásakor előforduló, és javasolt műveletek azok megoldását ismerteti. 

| Nem. | Hiba vagy a feltétel | Lehetséges okok | Javasolt művelet |
|:--- |:--- |:--- |:--- |
| 1 |350032. hiba: Az eszköz már inaktív. |Ez a hiba jelenik meg a telepítővarázsló az inaktív állapotú eszköz futtatásakor. |[Forduljon a Microsoft Support](storsimple-contact-microsoft-support.md) további lépéseket. Deaktivált eszköz szolgáltatást nem lehet rendezni. A gyári beállítások visszaállítása lehet szükség, mielőtt az eszköz újra kell aktiválni. |
| 2 |Invoke-HcsSetupWizard: ERROR_INVALID_FUNCTION (kivétel HRESULT: 0x80070001) |A DNS-kiszolgáló frissítése sikertelen. DNS-beállítások globálisak, és minden engedélyezett hálózati adaptert alkalmaz a rendszer. |Engedélyezze a felületet, és a DNS-beállítások alkalmazásához újra. Az zavart okozhat a hálózat más engedélyezett kapcsolatok mivel ezek a beállítások globális. |
| 3 |Az eszköz úgy tűnik, hogy online állapotú legyen a StorSimple Manager szolgáltatás portálján, de kísérli meg a minimális beállításának befejezése és a konfiguráció mentéséhez, a művelet sikertelen lesz. |Kezdeti beállítás során a webalkalmazás-proxy nem lett konfigurálva, annak ellenére, hogy hiba történt egy tényleges proxykiszolgáló helyen. |Használja a [Test-HcsmConnection parancsmag] [ 2] a hiba található. [Forduljon a Microsoft Support](storsimple-contact-microsoft-support.md) Ha nem sikerül, a probléma elhárítása érdekében. |
| 4 |Invoke-HcsSetupWizard: Érték kívül esik a várt tartományon. |Alhálózati maszk ezt a hibát eredményez. Lehetséges okok a következők: <ul><li> Az alhálózati maszk nem található vagy üres.</li><li>Az Ipv6-előtag formátuma nem megfelelő.</li><li>A felület a felhőalapú, de az átjáró hiányzik vagy nem helyes.</li></ul>Vegye figyelembe, hogy a DATA 0 automatikusan felhő-engedélyezett, ha a telepítővarázsló segítségével konfigurálható. |Annak megállapításához, a problémát, 0.0.0.0 vagy 256.256.256.256 használni, és tekintse meg a kimeneti. Adja meg helyes értékeit az alhálózati maszk, az átjárót és az Ipv6-előtagot, igény szerint. |

## <a name="error-codes"></a>Hibakódok
Hibák numerikus sorrendben vannak felsorolva.

| Hiba száma | Hibaüzenet-szöveg vagy leírása | Ajánlott teendő |
|:--- |:--- |:--- |
| 10502 |Hiba történt a tárfiók elérése során. |Várjon néhány percet, és próbálkozzon újra. Ha a probléma továbbra is fennáll, további lépéseket kérjük, forduljon a Microsoft ügyfélszolgálatához. |
| 40017 |A biztonsági mentési művelet sikertelen volt, mivel a biztonsági mentési házirendben megadott kötet nem található az eszközön. |Próbálja megismételni a biztonsági mentési műveletet, ha a probléma továbbra is fennáll, forduljon a Microsoft Support. a következő lépéseket. |
| 40018 |A biztonsági mentési művelet sikertelen volt, mert nincs megadva a biztonsági mentési házirendben kötetek találhatók, az eszközön. |Próbálja megismételni a biztonsági mentési műveletet, ha a probléma továbbra is fennáll, forduljon a Microsoft Support. a következő lépéseket. |
| 390061 |A rendszer nem foglalt vagy nem érhető el. |Várjon néhány percet, és próbálkozzon újra. Ha a probléma továbbra is fennáll, további lépéseket kérjük, forduljon a Microsoft ügyfélszolgálatához. |
| 390143 |Hiba történt a következő hibakóddal 390143. (Ismeretlen hiba történt.) |Ha a probléma továbbra is fennáll, forduljon a Microsoft Support további lépéseket. |

## <a name="next-steps"></a>További lépések
Ha a probléma megoldásához nem [forduljon a Microsoft Support](storsimple-contact-microsoft-support.md) segítségért. 

[1]: https://technet.microsoft.com/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/%5Clibrary/Dn715782(v=WPS.630).aspx
