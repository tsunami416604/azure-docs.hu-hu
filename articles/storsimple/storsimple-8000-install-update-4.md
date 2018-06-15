---
title: A StorSimple 8000 series eszköz Update 4 telepítése |} Microsoft Docs
description: Ismerteti a StorSimple 8000 Series Update 4 telepítése a StorSimple 8000 series eszközön.
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
ms.openlocfilehash: 57d6d63c55f8ad4da5d1905a1e209da454b0491c
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23874933"
---
# <a name="install-update-4-on-your-storsimple-device"></a>A StorSimple eszköz Update 4 telepítése

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan Update 4 telepíthető a StorSimple eszköz egy korábbi verzióját szoftver az Azure-portálon, és a gyorsjavítások a módszerrel. Amikor egy átjáró konfigurálva van a hálózati adaptert, mint a DATA 0 a StorSimple eszköz és a frissítés előtti 1 szoftver verziójából származó frissíteni kívánt a gyorsjavítás módszert használják.

Update 4 eszköz szoftver, a legpontosabb Beállításhoz belső vezérlőprogram, LSI illesztőprogram és a belső vezérlőprogram, Storport és Spaceport, az operációs rendszer biztonsági frissítések és az állomást, más operációs rendszer frissítéseit tartalmazza.  Az eszközhöz, a legpontosabb Beállításhoz belső vezérlőprogram, Spaceport, Storport és más operációs rendszer frissítése érdekében nem zavaró frissítések. A nem zavaró vagy rendszeres frissítés akkor alkalmazható, az Azure-portálon vagy a gyorsjavítások a módszerrel. A lemez belső vezérlőprogram-frissítésekre zavaró frissítések érhetők el, és a gyorsjavítások a módszerrel a Windows PowerShell felületén az eszköz csak akkor érvényesíthetők.

> [!IMPORTANT]
> * Manuális és automatikus előtti ellenőrzés történik az eszköz állapotának hardver állapot és hálózati kapcsolatok tekintetében telepítése előtt. Az előzetes ellenőrzések csak akkor, ha a frissítések telepítését az Azure-portálon történik.
> * Azt javasoljuk, hogy telepítse a szoftver- és egyéb rendszeres frissítéseket az Azure-portálon. Csak abban az csak lépjen a Windows PowerShell felületét (a frissítések telepítése) az eszköz, ha a frissítés előtti átjáró ellenőrzés sikertelen, a portálon. Attól függően, a verzióra frissít, a, a frissítések órát is igénybe vehet 4 (vagy újabb) telepítéséhez. A karbantartási mód frissítéseket is telepíteni kell az eszköz a Windows PowerShell felületén keresztül. Karbantartási mód frissítések zavaró frissítések érhetők el, mert ezek jár le egyszerre az eszközhöz.
> * Ha a választható StorSimple Snapshot Manager fut, győződjön meg arról, hogy frissítette a Snapshot Manager verziójának Update 4 az eszköz frissítése előtt.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Update 4 telepítése az Azure-portálon
Az eszköz frissítése a következő lépésekkel [Update 4](storsimple-update4-release-notes.md).

> [!NOTE]
> Microsoft lekéri az eszközről további diagnosztikai adatokat. Ennek eredményeképpen ha a műveleti csapata az eszközöket, amelyek problémákat tapasztal, vagy folyamatban, jobban ellátni a adatokat gyűjthetnek az eszköz és a problémák diagnosztizálásához. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Győződjön meg arról, hogy az eszköz fut. **a StorSimple 8000 Series Update 4 (6.3.9600.17820)**. A **utolsó frissítés dátuma** is módosítani kell.

* Most láthatja, hogy a karbantartási mód frissítések érhetők el (Ez az üzenet előfordulhat, hogy továbbra is megjelennek a frissítések telepítése után legfeljebb 24 órában). Karbantartási mód frissítések zavaró frissítések eszköz állásidőt eredményezhettek, és csak akkor érvényesíthetők, az eszköz a Windows PowerShell felületén keresztül érhetők el.

* A karbantartási mód frissítések letöltése a felsorolt lépéseket követve [gyorsjavítások letöltése](#to-download-hotfixes) keresését, és töltse le a KB4011837, mely telepíti lemez belső vezérlőprogram-frissítésekre (a más frissítések már telepítve kell lennie mostanra). Kövesse a felsorolt lépéseket [telepítse, és ellenőrizze a karbantartási mód gyorsjavítások](#to-install-and-verify-maintenance-mode-hotfixes) a karbantartási mód telepítendő frissítések.

## <a name="install-update-4-as-a-hotfix"></a>Update 4 telepíti egy gyorsjavítás
Update 4 telepítése az ajánlott módszer van az Azure-portálon.

Ez az eljárás használható, ha az átjáró ellenőrzése sikertelen, az Azure portálon keresztül frissítések telepítése közben. Az ellenőrzés sikertelen, egy nem DATA 0 hálózati adapterén rendelt átjáró és az eszköz 1. frissítés előtti szoftvert verziót futtat.

A gyorsjavítások a módszerrel frissíthető szoftver verziók az alábbiak:

* Frissítéskezelés 0,1 vagy 0,2 0,3
* 1., 1.1-es, 1.2 frissítése
* 2, 2.1, 2.2 frissítése
* Frissítés 3 3.1.


A gyorsjavítás metódus a következő három lépést foglal magában:

1. Töltse le a gyorsjavítás a Microsoft Update katalógusból.
2. Telepítse a, és ellenőrizze a normál üzemmód gyorsjavítások.
3. Telepítse a, és ellenőrizze a karbantartási mód gyorsjavítást.

#### <a name="download-updates-for-your-device"></a>Az eszköz frissítéseinek letöltése

Töltse le, és a gyorsjavítások telepítése az előírt sorrendben és a javasolt mappák:

| Sorrendje | KB | Leírás | Frissítés típusa | Telepítés időpontja |A mappa telepítéséhez|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Szoftverfrissítés |Rendszeres <br></br>A nem zavaró |~ 25 perc |FirstOrderUpdate|
| 2A. |KB4011841 <br> KB4011842 |LSI illesztőprogram és a belső vezérlőprogram-frissítésekre <br> A legpontosabb Beállításhoz vezérlőprogram-frissítés (3.38 verzió) |Rendszeres <br></br>A nem zavaró |~ 3 óra <br> (2/a. tartalmazza. + 2B. + 2 C.)|SecondOrderUpdate|
| 2B. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |Az operációs rendszer biztonsági frissítések csomag <br> Töltse le a Windows Server 2012 R2 rendszerben |Rendszeres <br></br>A nem zavaró |- |SecondOrderUpdate|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |Az operációs rendszer frissítéseinek csomag <br> Töltse le a Windows Server 2012 R2 rendszerben |Rendszeres <br></br>A nem zavaró |- |SecondOrderUpdate|

Előfordulhat, hogy is telepítendő fölött a fenti táblázatokban látható frissítések lemez belső vezérlőprogram-frissítésekre. Ellenőrizheti, hogy szükséges-e a lemez belső vezérlőprogram-frissítésekre futtatásával a `Get-HcsFirmwareVersion` parancsmag. Ha ezek a belső vezérlőprogram verzióinak futtatja: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N002`, `0106`, akkor nem szükséges a frissítések telepítéséhez.

| Sorrendje | KB | Leírás | Frissítés típusa | Telepítés időpontja | A mappa telepítéséhez|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Lemez belső vezérlőprogram |Karbantartás <br></br>Zavaró |~ 30 perc | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Ez az eljárás csak egyszer hajtható végre Update 4 alkalmazni kell. Az Azure portál segítségével soron következő frissítések alkalmazásához.
> * 3. frissítés vagy 3.1 frissítését, a teljes telepítési idő akkor megközelítőleg 4 óra.
> * Ez az eljárás használata a frissítés előtt győződjön róla, hogy mind a eszközvezérlők online állapotban és a hardverösszetevők kifogástalan.

A következő lépésekkel töltse le és telepítse a gyorsjavítást.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Következő lépések
További információ a [Update 4 kiadás](storsimple-update4-release-notes.md).

