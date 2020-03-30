---
title: Telepített StorSimple-eszköz hibáinak elhárítása | Microsoft dokumentumok
description: Bemutatja, hogyan diagnosztizálhatja és javíthatja a jelenleg üzembe helyezett és működő StorSimple-eszközön előforduló hibákat.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: ea5d89ae-e379-423f-b68b-53785941d9d0
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: twooley
ms.openlocfilehash: ca79e4240c1a82e46bea44a9d018a3c681920480
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75933296"
---
# <a name="troubleshoot-an-operational-storsimple-device"></a>Működő StorSimple-eszköz – problémamegoldás
> [!NOTE]
> A StorSimple klasszikus portálja elavult. A StorSimple-eszközkezelők automatikusan átkerülnek az új Azure Portalra az elavulási ütemezésnek megfelelően. Erről az áthelyezésről kapni fog egy e-mailt és egy Portal-értesítést. Ez a dokumentum hamarosan el lesz távolítva. Ha kérdései vannak az áthelyezéssel kapcsolatban, tekintse meg a [Gyakori kérdések: Váltás az Azure Portalra](storsimple-8000-move-azure-portal-faq.md) szakaszt.

## <a name="overview"></a>Áttekintés
Ez a cikk hasznos hibaelhárítási útmutatást nyújt a StorSimple-eszköz üzembe helyezése és működése után felmerülő konfigurációs problémák megoldásához. Ismerteti a gyakori problémákat, a lehetséges okokat és az ajánlott lépéseket, amelyek segítenek a Microsoft Azure StorSimple futtatásakor esetlegesen felmerülő problémák megoldásában. Ez az információ a helyszíni StorSimple fizikai eszközre és a StorSimple virtuális eszközre egyaránt vonatkozik.

A cikk végén található a Microsoft Azure StorSimple-művelet során esetlegesen előforduló hibakódok listája, valamint a hibák elhárításához szükséges lépések. 

## <a name="setup-wizard-process-for-operational-devices"></a>A telepítővarázsló folyamata az eszközökhöz
A telepítővarázslóval[(Invoke-HcsSetupWizard][1]) ellenőrizheti az eszköz konfigurációját, és szükség esetén végrehajthatja a javítást.

Ha a telepítővarázslót egy korábban konfigurált és működő eszközön futtatja, a folyamat folyamata eltérő. Csak a következő bejegyzések módosíthatók:

* IP-cím, alhálózati maszk és átjáró
* Elsődleges DNS-kiszolgáló
* Elsődleges NTP-kiszolgáló
* Választható webproxy-konfiguráció

A telepítővarázsló nem hajtja végre a jelszógyűjtéssel és az eszközregisztrációval kapcsolatos műveleteket.

## <a name="errors-that-occur-during-subsequent-runs-of-the-setup-wizard"></a>A telepítővarázsló későbbi futtatásakor előforduló hibák
Az alábbi táblázat azokat a hibákat ismerteti, amelyekkel a telepítővarázsló üzemi eszközön történő futtatásakor találkozhat, a hibák lehetséges okait és a megoldásukhoz javasolt műveleteket. 

| Nem. | Hibaüzenet vagy feltétel | Lehetséges okok | Javasolt művelet |
|:--- |:--- |:--- |:--- |
| 1 |350032-es hiba: Az eszköz már inaktiválva van. |Ez a hibaüzenet akkor jelenik meg, ha a telepítővarázslót inaktivált eszközön futtatja. |A következő lépésekért forduljon a [Microsoft támogatási szolgálatához.](storsimple-contact-microsoft-support.md) Inaktivált eszköz nem helyezhető szolgálatba. Az eszköz ismételt aktiválása előtt gyári beállítások visszaállítása szükséges lehet. |
| 2 |Invoke-HcsSetupWizard : ERROR_INVALID_FUNCTION(Kivétel a HRESULT-ből: 0x80070001) |A DNS-kiszolgáló frissítése nem működik. A DNS-beállítások globális beállítások, amelyek az összes engedélyezett hálózati csatolóra vonatkoznak. |Engedélyezze a kapcsolatot, és alkalmazza újra a DNS-beállításokat. Ez megzavarhatja a hálózatot más engedélyezett összeköttetések számára, mert ezek a beállítások globálisak. |
| 3 |Az eszköz úgy tűnik, hogy online a StorSimple Manager szolgáltatás portálon, de amikor megpróbálja befejezni a minimális beállítás, és mentse a konfigurációt, a művelet sikertelen lesz. |A kezdeti telepítés során a webproxy nem volt konfigurálva, annak ellenére, hogy tényleges proxykiszolgáló volt a helyén. |A [Teszt-HcsmConnection parancsmag][2] segítségével keresse meg a hibát. Ha nem tudja kijavítani a problémát, forduljon a [Microsoft támogatási szolgálatához.](storsimple-contact-microsoft-support.md) |
| 4 |Invoke-HcsSetupWizard: Az érték nem esik a várt tartományba. |A hibás alhálózati maszk ezt a hibát hozza létre. A lehetséges okok a következők: <ul><li> Az alhálózati maszk hiányzik vagy üres.</li><li>Az Ipv6 előtag formátuma helytelen.</li><li>A kapcsolat felhőalapú, de az átjáró hiányzik vagy helytelen.</li></ul>Ne feledje, hogy a DATA 0 automatikusan felhőalapú, ha a telepítővarázslón keresztül konfigurálható. |A probléma megállapításához használja a 0.0.0.0 vagy a 256.256.256.256 alhálózatot, majd tekintse meg a kimenetet. Szükség szerint adja meg az alhálózati maszk, az átjáró és az Ipv6-előtag helyes értékeit. |

## <a name="error-codes"></a>Hibakódok
A hibák számsorrendben vannak felsorolva.

| Hiba száma | Hiba szövege vagy leírása | Ajánlott felhasználói művelet |
|:--- |:--- |:--- |
| 10502 |Hiba történt a tárfiók elérése közben. |Várjon néhány percet, majd próbálkozzon újra. Ha a hiba továbbra is fennáll, a következő lépésekért forduljon a Microsoft támogatási szolgálatához. |
| 40017 |A biztonsági mentési művelet nem sikerült, mert a biztonsági mentési házirendben megadott kötet nem található az eszközön. |Ha a hiba továbbra is fennáll, próbálkozzon újra a biztonsági mentési művelettel, forduljon a Microsoft támogatási szolgálatához. a következő lépésekhez. |
| 40018 |A biztonsági mentési művelet nem sikerült, mert a biztonsági mentési házirendben megadott kötetek egyike sem található az eszközön. |Ha a hiba továbbra is fennáll, próbálkozzon újra a biztonsági mentési művelettel, forduljon a Microsoft támogatási szolgálatához. a következő lépésekhez. |
| 390061 |A rendszer foglalt vagy nem érhető el. |Várjon néhány percet, majd próbálkozzon újra. Ha a hiba továbbra is fennáll, a következő lépésekért forduljon a Microsoft támogatási szolgálatához. |
| 390143 |Hiba történt a 390143-as hibakóddal. (Ismeretlen hiba.) |Ha a hiba továbbra is fennáll, a következő lépésekért forduljon a Microsoft támogatási szolgálatához. |

## <a name="next-steps"></a>További lépések
Ha nem tudja megoldani a problémát, kérjen segítséget a [Microsoft támogatási szolgálatától.](storsimple-contact-microsoft-support.md) 

[1]: https://technet.microsoft.com/%5Clibrary/Dn688135(v=WPS.630).aspx
[2]: https://technet.microsoft.com/%5Clibrary/Dn715782(v=WPS.630).aspx
