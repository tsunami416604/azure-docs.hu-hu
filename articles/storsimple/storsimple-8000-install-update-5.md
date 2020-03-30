---
title: Az 5. Microsoft dokumentumok
description: A cikk a StorSimple 8000 Series 5 sorozat5-ös sorozatának telepítését ismerteti a StorSimple 8000 sorozatú eszközön.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/13/2017
ms.author: alkohli
ms.openlocfilehash: d86e77ef0148c0fac3dfa31153364de153b094ef
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79267884"
---
# <a name="install-update-5-on-your-storsimple-device"></a>Az 5.

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag bemutatja, hogyan telepítheti az 5. A gyorsjavítási módszert akkor használja a rendszer, ha az 5. A gyorsjavítási módszert akkor is használja a rendszer, ha az átjáró a StorSimple eszköz DATA 0 szolgáltatásától eltérő hálózati illesztőn van konfigurálva, és az 1.

Az 5- ös frissítés tartalmazza az eszközszoftvereket, a Storport és a Spaceport, az operációs rendszer biztonsági frissítéseit és az operációs rendszer frissítéseit, valamint a lemez belső vezérlőprogramjának frissítéseit.  Az eszköz szoftver, Spaceport, Storport, biztonság, és egyéb operációs rendszer frissítések nem zavaró frissítéseket. A nem zavaró vagy rendszeres frissítések et az Azure Portalon vagy a gyorsjavítási módszeren keresztül lehet alkalmazni. A lemez belső vezérlőprogramjának frissítései zavaró frissítések, és akkor kerülnek alkalmazásra, ha az eszköz karbantartási üzemmódban van az eszköz Windows PowerShell felületét használó gyorsjavítási módszerrel.

> [!IMPORTANT]
> * Az 5-ös frissítés kötelező frissítés, amelyet azonnal telepíteni kell. További információt az [5.](storsimple-update5-release-notes.md)
> * A telepítés előtt manuális és automatikus előzetes ellenőrzéseket végeznek az eszköz állapotának meghatározására a hardverállapot és a hálózati kapcsolat szempontjából. Ezek az előzetes ellenőrzések csak akkor hajtják végre, ha alkalmazza a frissítéseket az Azure Portalon.
> * Javasoljuk, hogy a 3. Ha bármilyen problémát tapasztal, [jelentkezzen be egy támogatási jegyet.](storsimple-8000-contact-microsoft-support.md)
> * Javasoljuk, hogy telepítse a szoftvert és más rendszeres frissítéseket az Azure Portalon keresztül. Csak akkor lépjen az eszköz Windows PowerShell-felületére (a frissítések telepítéséhez), ha a frissítés előtti átjáró ellenőrzése sikertelen a portálon. Attól függően, hogy melyik verzióról frissít, a frissítések telepítése (vagy nagyobb) 4 órát (vagy annál hosszabb) is igénybe vehet. A karbantartási mód frissítéseit az eszköz Windows PowerShell felületén keresztül kell telepíteni. Mivel a karbantartási mód frissítései zavaró frissítések, ezek az eszköz leállási idejét eredményezik.
> * Ha fut a választható StorSimple Snapshot Manager, győződjön meg arról, hogy frissítette a Snapshot Manager verziója 5-ös frissítésre az eszköz frissítése előtt.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>Az 5.
Az alábbi lépésekkel frissítheti az eszközt az [5-ös frissítésre.](storsimple-update5-release-notes.md)

> [!NOTE]
> A Microsoft további diagnosztikai információkat vesz fel az eszközről. Ennek eredményeképpen, amikor műveleti csapatunk azonosítja a problémákat okozó eszközöket, jobban fel vagyunk szerelve az eszközről származó adatok gyűjtésére és a problémák diagnosztizálására.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Ellenőrizze, hogy az eszközön fut-e a **StorSimple 8000 Series 5-ös frissítése (6.3.9600.17845)**. Az **utolsó frissítés dátumát** módosítani kell.

Most látni fogja, hogy a Karbantartási mód frissítései elérhetők (ez az üzenet a frissítések telepítése után is megjelenhet). A karbantartási mód frissítésének telepítésének lépéseit a következő szakasz részletezi.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Az 5.

A gyorsjavítási módszerrel frissíthető szoftververziók a következők:

* Frissítés 0.1, 0.2, 0.3
* Frissítés 1, 1.1, 1.2
* Frissítés 2, 2.1, 2.2
* 3.1. frissítés
* 4-es frissítés

> [!NOTE] 
> Az 5- ös frissítés telepítésének ajánlott módszere az Azure Portalon keresztül történik, amikor 3. A 3. Ezt az eljárást akkor is használhatja, ha nem sikerül az átjáró ellenőrzése, amikor megpróbálja telepíteni a frissítéseket az Azure Portalon keresztül. Az ellenőrzés sikertelen, ha egy átjáró t rendelt egy nem DATA 0 hálózati adapterhez, és az eszköz az 1.

A gyorsjavítási módszer a következő három lépést foglalja magában:

1. Töltse le a gyorsjavításokat a Microsoft Update katalógusból.
2. Telepítse és ellenőrizze a normál módú gyorsjavításokat.
3. Telepítse és ellenőrizze a karbantartási mód gyorsjavítását.

#### <a name="download-updates-for-your-device"></a>Frissítések letöltése az eszközre

Az előírt sorrendben és a javasolt mappákban le kell töltenie és telepítenie kell a következő gyorsjavításokat:

| Rendelés | KB | Leírás | Frissítéstípus | Telepítés időpontja |Telepítés mappában|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Szoftverfrissítés<br> A _HcsSoftwareUpdate.exe_ és a _CisMSDAgent.exe fájl letöltése_ |Hagyományos <br></br>Nem zavaró |~ 25 mins |FirstOrderUpdate|

Ha a 4.

| Rendelés | KB | Leírás | Frissítéstípus | Telepítés időpontja |Telepítés mappában|
| --- | --- | --- | --- | --- | --- |
| (2A) Az (1) bekezdés ben s |KB4025336 |Az operációs rendszer összesítő frissítési csomagja <br> A Windows Server 2012 R2 verziójának letöltése |Hagyományos <br></br>Nem zavaró |- |SecondOrderUpdate|

Ha a 3.

| Rendelés | KB | Leírás | Frissítéstípus | Telepítés időpontja |Telepítés mappában|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI-illesztőprogram- és belső vezérlőprogram-frissítések <br> AZ USM firmware frissítése (3.38-as verzió) |Hagyományos <br></br>Nem zavaró |~ 3 óra <br> (beleértve a 2A-t. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Operációs rendszer biztonsági frissítési csomagja <br> A Windows Server 2012 R2 verziójának letöltése |Hagyományos <br></br>Nem zavaró |- |SecondOrderUpdate|
| 2d. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Operációs rendszer frissítési csomagja <br> A Windows Server 2012 R2 verziójának letöltése |Hagyományos <br></br>Nem zavaró |- |SecondOrderUpdate|


Előfordulhat, hogy az előző táblázatokban látható összes frissítésen felül lemezbelső-vezérlőprogramokat is telepítenie kell. A `Get-HcsFirmwareVersion` parancsmag futtatásával ellenőrizheti, hogy szükség van-e a lemez belső vezérlőprogramjának frissítésére. Ha a következő belső `XMGJ`vezérlőprogram-verziókat `0107`futtatja: , , `XGEG` `KZ50`, `F6C2`, `VR08`, `N003`, akkor nem kell telepítenie ezeket a frissítéseket.

| Rendelés | KB | Leírás | Frissítéstípus | Telepítés időpontja | Telepítés mappában|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Lemez belső vezérlőprogramja |Karbantartás <br></br>Zavaró |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Ha a 4-es frissítésről frissít, a teljes telepítési idő megközelíti a 4 órát.
> * Mielőtt ezzel az eljárással alkalmaznák a frissítést, győződjön meg arról, hogy mind az eszközvezérlők online állapotban vannak, mind az összes hardverösszetevő kifogástalan.

A gyorsjavítások letöltéséhez és telepítéséhez hajtsa végre az alábbi lépéseket.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>További lépések
További információ az [5.](storsimple-update5-release-notes.md)

