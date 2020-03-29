---
title: A 4. Microsoft dokumentumok
description: A cikk a StorSimple 8000 Series 4 sorozatú 4-es sorozatának telepítését ismerteti a StorSimple 8000 sorozatú eszközön.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 08/02/2017
ms.author: alkohli
ms.openlocfilehash: 5b48cbd1020cfd51fe989a9be33197f2735f21f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60860517"
---
# <a name="install-update-4-on-your-storsimple-device"></a>A 4.

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag bemutatja, hogyan telepítheti a 4. A gyorsjavítási módszert akkor használja a rendszer, ha az átjáró a StorSimple eszköz DATA 0 szolgáltatásától eltérő hálózati illesztőn van konfigurálva, és az 1.

A 4- es frissítés tartalmazza az eszközszoftvert, az USM firmware-t, az LSI-illesztőprogramot és a belső vezérlőprogramot, a Storport és spaceportot, az operációs rendszer biztonsági frissítéseit és számos más operációs rendszerfrissítést.  Az eszközszoftver, az USM firmware, a Spaceport, a Storport és az operációs rendszer egyéb frissítései nem zavaró frissítések. A nem zavaró vagy rendszeres frissítések et az Azure Portalon vagy a gyorsjavítási módszeren keresztül lehet alkalmazni. A lemez belső vezérlőprogramjának frissítései zavaró frissítések, és csak az eszköz Windows PowerShell felületén keresztül alkalmazhatók a gyorsjavítási módszerrel.

> [!IMPORTANT]
> * A telepítés előtt manuális és automatikus előzetes ellenőrzéseket végeznek az eszköz állapotának meghatározására a hardverállapot és a hálózati kapcsolat szempontjából. Ezek az előzetes ellenőrzések csak akkor hajtják végre, ha alkalmazza a frissítéseket az Azure Portalon.
> * Javasoljuk, hogy telepítse a szoftvert és más rendszeres frissítéseket az Azure Portalon keresztül. Csak akkor lépjen az eszköz Windows PowerShell-felületére (a frissítések telepítéséhez), ha a frissítés előtti átjáró ellenőrzése sikertelen a portálon. Attól függően, hogy melyik verzióról frissít, a frissítések telepítése (vagy nagyobb) 4 órát (vagy annál hosszabb) is igénybe vehet. A karbantartási mód frissítéseit az eszköz Windows PowerShell felületén keresztül is telepíteni kell. Mivel a karbantartási mód frissítései zavaró frissítések, ezek az eszköz leállási idejét eredményezik.
> * Ha fut a választható StorSimple Snapshot Manager, győződjön meg arról, hogy frissítette a Snapshot Manager verziója a 4-es frissítés az eszköz frissítése előtt.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>4. frissítés telepítése az Azure portalon keresztül
Az alábbi lépésekkel frissítheti az eszközt [a 4- es frissítésre.](storsimple-update4-release-notes.md)

> [!NOTE]
> A Microsoft további diagnosztikai információkat vesz fel az eszközről. Ennek eredményeképpen, amikor műveleti csapatunk azonosítja a problémákat okozó eszközöket, jobban fel vagyunk szerelve az eszközről származó adatok gyűjtésére és a problémák diagnosztizálására. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Ellenőrizze, hogy az eszközön fut-e **a StorSimple 8000 Series 4 frissítése (6.3.9600.17820)**. Az **utolsó aktualizált dátumot** is módosítani kell.

* Most látni fogja, hogy a Karbantartási mód frissítései elérhetők (ez az üzenet a frissítések telepítése után is megjelenhet). A karbantartási mód frissítései olyan zavaró frissítések, amelyek eszközleállást eredményeznek, és csak az eszköz Windows PowerShell felületén keresztül alkalmazhatók.

* Töltse le a karbantartási mód frissítéseit a felsorolt lépésekkel töltse le a [GYORSjavításokat](#to-download-hotfixes) a KB4011837 frissítésének kereséséhez és letöltéséhez, amely telepíti a lemez belső vezérlőprogramjának frissítéseit (a többi frissítést már telepíteni kell). A karbantartási mód frissítéseinek telepítéséhez kövesse a [karbantartási mód gyorsjavításainak telepítéséhez](#to-install-and-verify-maintenance-mode-hotfixes) a karbantartási mód telepítésével kapcsolatos lépések ben felsorolt lépéseket.

## <a name="install-update-4-as-a-hotfix"></a>A 4.
A 4.frissítés telepítésének ajánlott módszere az Azure Portalon keresztül történik.

Ezt az eljárást akkor használja, ha nem sikerül az átjáró ellenőrzése, amikor megpróbálja telepíteni a frissítéseket az Azure Portalon keresztül. Az ellenőrzés sikertelen, mivel egy átjáró nincs hozzárendelve egy nem DATA 0 hálózati adapterhez, és az eszköz az 1.

A gyorsjavítási módszerrel frissíthető szoftververziók a következők:

* Frissítés 0.1, 0.2, 0.3
* Frissítés 1, 1.1, 1.2
* Frissítés 2, 2.1, 2.2
* 3.1. frissítés


A gyorsjavítási módszer a következő három lépést foglalja magában:

1. Töltse le a gyorsjavításokat a Microsoft Update katalógusból.
2. Telepítse és ellenőrizze a normál módú gyorsjavításokat.
3. Telepítse és ellenőrizze a karbantartási mód gyorsjavítását.

#### <a name="download-updates-for-your-device"></a>Frissítések letöltése az eszközre

Az előírt sorrendben és a javasolt mappákban le kell töltenie és telepítenie kell a következő gyorsjavításokat:

| Rendelés | KB | Leírás | Frissítéstípus | Telepítés időpontja |Telepítés mappában|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Szoftverfrissítés |Hagyományos <br></br>Nem zavaró |~ 25 mins |FirstOrderUpdate|
| (2A) Az (1) bekezdés ben s |KB4011841 <br> KB4011842 |LSI-illesztőprogram- és belső vezérlőprogram-frissítések <br> AZ USM firmware frissítése (3.38-as verzió) |Hagyományos <br></br>Nem zavaró |~ 3 óra <br> (beleértve a 2A-t. + 2B. + 2C.)|SecondOrderUpdate|
| 2B. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |Operációs rendszer biztonsági frissítési csomagja <br> A Windows Server 2012 R2 letöltése |Hagyományos <br></br>Nem zavaró |- |SecondOrderUpdate|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |Operációs rendszer frissítési csomagja <br> A Windows Server 2012 R2 letöltése |Hagyományos <br></br>Nem zavaró |- |SecondOrderUpdate|

Előfordulhat, hogy az előző táblázatokban látható összes frissítésen felül lemezbelső-vezérlőprogramokat is telepítenie kell. A `Get-HcsFirmwareVersion` parancsmag futtatásával ellenőrizheti, hogy szükség van-e a lemez belső vezérlőprogramjának frissítésére. Ha a következő belső `XMGJ`vezérlőprogram-verziókat `0106`futtatja: , , `XGEG` `KZ50`, `F6C2`, `VR08`, `N002`, akkor nem kell telepítenie ezeket a frissítéseket.

| Rendelés | KB | Leírás | Frissítéstípus | Telepítés időpontja | Telepítés mappában|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Lemez belső vezérlőprogramja |Karbantartás <br></br>Zavaró |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Ezt az eljárást csak egyszer kell végrehajtani a 4. Az Azure Portal on további frissítések alkalmazása.
> * Ha a 3-as vagy 3.1-es frissítésről frissít, a teljes telepítési idő megközelíti a 4 órát.
> * Mielőtt ezzel az eljárással alkalmaznák a frissítést, győződjön meg arról, hogy mind az eszközvezérlők online állapotban vannak, mind az összes hardverösszetevő kifogástalan.

A gyorsjavítások letöltéséhez és telepítéséhez hajtsa végre az alábbi lépéseket.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>További lépések
További információ a [4.](storsimple-update4-release-notes.md)

