---
title: A StorSimple eszköz üzembe helyezett hibaelhárítása |} A Microsoft Docs
description: Ismerteti, hogyan diagnosztizálhatja és megoldhatja a bekövetkező hibák, amelyek jelenleg üzembe helyezett és működési StorSimple eszközön.
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
ms.openlocfilehash: 8ad3f09bf46caf426b2008b583ebd2ff78522462
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64713066"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Működési StorSimple-eszköz hibaelhárítása
> [!NOTE]
> A StorSimple klasszikus portálja elavult. A StorSimple-eszközkezelők automatikusan átkerülnek az új Azure Portalra az elavulási ütemezésnek megfelelően. Erről az áthelyezésről kapni fog egy e-mailt és egy Portal-értesítést. Ez a dokumentum hamarosan el lesz távolítva. Kérdései vannak az áthelyezéssel, lásd: [– gyakori kérdések: Az Azure Portalra](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Áttekintés
Ez a cikk kapcsolatos konfigurációs problémák megoldásához hasznosak hibaelhárítási útmutatót, hogy találkozik, előfordulhat, hogy a StorSimple eszköz üzembe helyezett és működési után. Ismerteti a gyakori problémák lehetséges okok és javasolt lépések segítséget nyújtanak a Microsoft Azure StorSimple futtatásakor az esetleg előforduló problémák megoldásához. Ez az információ a StorSimple fizikai eszköz a helyszíni és a StorSimple virtuális eszköz is vonatkozik.

A Microsoft Azure StorSimple művelet során előforduló hibakódok listáját megtalálhatja a cikk végén valamint lépéseket szükségesek javítsa ki a hibákat. 

## <a name="setup-wizard-process-for-operational-devices"></a>A telepítő varázsló folyamat üzemeltetési eszközök
A telepítővarázsló használata ([Invoke-hcssetupwizard parancsmagot][1]) az eszköz konfigurálása és javítási intézkedéseket, ha szükséges.

Egy korábban konfigurált és üzemeltetési eszközök, a varázsló futtatásakor a folyamat eltér. Módosíthatja, hogy csak az alábbi bejegyzéseket:

* IP-cím, alhálózati maszk és átjáró
* Elsődleges DNS-kiszolgáló
* Elsődleges NTP-kiszolgáló
* Webproxy konfigurálása nem kötelező

A varázsló nem jelszó adatgyűjtési és -eszköz regisztrációja kapcsolatos műveletek végrehajtása.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>A telepítő varázsló későbbi futtatások során felmerülő hibák
A következő táblázat ismerteti a hibák fordulhatnak elő, amikor futtatja a egy működési eszközön telepítővarázsló, a hibák lehetséges okát, és javasolt műveletek a problémák megoldásához. 

| Nem. | Hiba vagy feltétel | Lehetséges okok | Javasolt művelet |
|:--- |:--- |:--- |:--- |
| 1 |350032\. hiba: Az eszköz már inaktiválva van. |Ez a hiba jelenik meg a varázsló futtatásakor az inaktivált eszközön. |A további lépésekért [forduljon a Microsoft ügyfélszolgálatához](storsimple-contact-microsoft-support.md). Inaktivált eszköz nem állítható. Ebben a szolgáltatásban. A gyári beállítások visszaállítását akkor lehet szükség, az eszköz újra aktiválása előtt. |
| 2 |Invoke-hcssetupwizard: parancsmagot ERROR_INVALID_FUNCTION (kivétel HRESULT: 0x80070001) |A DNS-kiszolgáló frissítése sikertelen. DNS-beállításainak globálisak, és minden engedélyezett hálózati adaptert érvényesek. |A felület engedélyezéséhez és a DNS-beállítások alkalmazásához újra. Ez előfordulhat, hogy zavarja a hálózat többi engedélyezett adapterek, mivel ezek a beállítások globális. |
| 3 |Az eszköz úgy tűnik, hogy a StorSimple Manager szolgáltatás portálon elérhető nem lesz, de meg a minimális telepítés befejezéséhez és a konfiguráció mentéséhez, a művelet sikertelen lesz. |Kezdeti beállítás során a webalkalmazás-proxy nem volt konfigurálva, annak ellenére, hogy hiba történt egy tényleges proxykiszolgáló helyen. |Használja a [Test-HcsmConnection parancsmaggal] [ 2] keresse meg a hibát. [Forduljon a Microsoft Support](storsimple-contact-microsoft-support.md) Ha nem sikerül, a probléma elhárításához. |
| 4 |Invoke-hcssetupwizard: parancsmagot Érték nem a várt tartományba esik. |Alhálózati maszk ezt a hibát eredményez. Lehetséges okok a következők: <ul><li> Az alhálózati maszk nem található vagy üres.</li><li>Az Ipv6-előtag formátuma nem megfelelő.</li><li>A felület felhőalapú, de az átjáró nem hiányzik vagy helytelen.</li></ul>Vegye figyelembe, hogy a DATA 0 automatikusan felhőképes, ha a telepítővarázsló keresztül lehet konfigurálni. |Annak megállapításához, a probléma, 0.0.0.0 vagy 256.256.256.256 alhálózatot használja, és tekintse meg a kimenetet. Adja meg a megfelelő értékeit az alhálózati maszk, az átjáró és az Ipv6-előtagot, igény szerint. |

## <a name="error-codes"></a>Hibakódok
Hibák numerikus sorrendben vannak felsorolva.

| Hiba száma | Hiba szövege vagy leírása | Ajánlott felhasználói művelet |
|:--- |:--- |:--- |
| 10502 |Hiba történt a tárfiók eléréséhez. |Várjon néhány percet, és próbálkozzon újra. Ha a hiba nem szűnik meg, kérjük, forduljon a Microsoft ügyfélszolgálatához a következő lépéseket. |
| 40017 |A biztonsági mentési művelet sikertelen volt, mint a biztonsági mentési szabályzatban megadott kötet nem található az eszközön. |Ismételje meg a biztonsági mentési műveletet, ha a hiba továbbra is fennáll, forduljon a Microsoft Support. a következő lépéseket. |
| 40018 |A biztonsági mentési művelet sikertelen volt, mert a kötetek a biztonsági mentési szabályzatban megadott egyike sem található az eszközön. |Ismételje meg a biztonsági mentési műveletet, ha a hiba továbbra is fennáll, forduljon a Microsoft Support. a következő lépéseket. |
| 390061 |A rendszer nem foglalt vagy nem érhető el. |Várjon néhány percet, és próbálkozzon újra. Ha a hiba nem szűnik meg, kérjük, forduljon a Microsoft ügyfélszolgálatához a következő lépéseket. |
| 390143 |Hiba történt a következő hibakóddal 390143. (Ismeretlen hiba.) |Ha a hiba továbbra is fennáll, forduljon a Microsoft Support a következő lépéseket. |

## <a name="next-steps"></a>További lépések
Ha a probléma megoldásához nem [forduljon a Microsoft Support](storsimple-contact-microsoft-support.md) segítségért. 

[1]: https://technet.microsoft.com/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/%5Clibrary/Dn715782(v=WPS.630).aspx
