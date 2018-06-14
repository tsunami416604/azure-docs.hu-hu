---
title: 5. frissítésétől telepítse a StorSimple 8000 series eszköz |} Microsoft Docs
description: Ismerteti a StorSimple 8000 Series Update 5 telepítéséhez a StorSimple 8000 series eszközön.
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
ms.openlocfilehash: d6e17c7609fd41b8f4457edda373f6882a1a9d2b
ms.sourcegitcommit: 99d29d0aa8ec15ec96b3b057629d00c70d30cfec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/25/2018
ms.locfileid: "28108720"
---
# <a name="install-update-5-on-your-storsimple-device"></a>5. frissítésétől a StorSimple eszköz telepítése

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan frissítés 5 telepíthető a StorSimple eszköz egy korábbi verzióját szoftver az Azure-portálon, és a gyorsjavítások a módszerrel. A gyorsjavítás módszert történt a frissítés előtti 3 verzióit futtató eszközök frissítés 5 telepítésének kísérlete közben. A gyorsjavítás metódus is használatos, amikor egy átjáró konfigurálva van a hálózati adaptert, mint a DATA 0 a StorSimple eszköz és a frissítés előtti 1 szoftver verziójából származó frissíteni kívánt.

Frissítés 5 tartalmazza az eszköz szoftvere Storport és Spaceport, az operációs rendszer biztonsági frissítések és az operációs rendszer frissítéseit és a belső vezérlőprogram-frissítésekre.  Az eszközhöz, Spaceport, Storport, biztonsági és egyéb operációs rendszer frissítése nem zavaró frissítések érhetők el. A nem zavaró vagy rendszeres frissítés akkor alkalmazható, az Azure-portálon vagy a gyorsjavítások a módszerrel. A lemez belső vezérlőprogram-frissítésekre zavaró frissítések érhetők el, és alkalmazza a rendszer, amikor az eszköz a gyorsjavítások a módszerrel a Windows PowerShell felületén az eszköz karbantartási módban van.

> [!IMPORTANT]
> * 5. frissítésétől egy kötelező frissítés, és azonnal kell telepíteni. További információkért lásd: [frissítés 5 kibocsátási megjegyzések](storsimple-update5-release-notes.md).
> * Manuális és automatikus előtti ellenőrzés történik az eszköz állapotának hardver állapot és hálózati kapcsolatok tekintetében telepítése előtt. Az előzetes ellenőrzések csak akkor, ha a frissítések telepítését az Azure-portálon történik.
> * Határozottan javasoljuk, hogy 3. frissítés előtti verzióit futtató eszközök frissítésekor a frissítések telepítése gyorsjavítások a módszerrel. Ha problémába ütközik, [egy támogatási jegy jelentkezzen](storsimple-8000-contact-microsoft-support.md).
> * Azt javasoljuk, hogy telepítse a szoftver- és egyéb rendszeres frissítéseket az Azure-portálon. Csak abban az csak lépjen a Windows PowerShell felületét (a frissítések telepítése) az eszköz, ha a frissítés előtti átjáró ellenőrzés sikertelen, a portálon. Attól függően, a verzióra frissít, a, a frissítések órát is igénybe vehet 4 (vagy újabb) telepítéséhez. A karbantartási mód frissítéseket telepíteni kell az eszköz a Windows PowerShell felületén keresztül. Karbantartási mód frissítések zavaró frissítések érhetők el, mert ezek eredményez le egyszerre az eszközhöz.
> * Ha a választható StorSimple Snapshot Manager fut, győződjön meg arról, hogy frissítette a Snapshot Manager verzióra frissítés 5 az eszköz frissítése előtt.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>5. frissítésétől telepítse az Azure-portálon
Az eszköz frissítése a következő lépésekkel [frissítése 5](storsimple-update5-release-notes.md).

> [!NOTE]
> Microsoft lekéri az eszközről további diagnosztikai adatokat. Ennek eredményeképpen ha a műveleti csapata az eszközöket, amelyek problémákat tapasztal, vagy folyamatban, jobban ellátni a adatokat gyűjthetnek az eszköz és a problémák diagnosztizálásához.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Győződjön meg arról, hogy az eszköz fut. **a StorSimple 8000 Series Update 5 (6.3.9600.17845)**. A **utolsó frissítés dátuma** kell módosítani.

Most láthatja, hogy a karbantartási mód frissítések érhetők el (Ez az üzenet előfordulhat, hogy továbbra is megjelennek a frissítések telepítése után legfeljebb 24 órában). A karbantartási mód frissítés telepítésének lépéseit a következő szakaszban részletes leírást.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>5. frissítésétől gyorsjavítás telepítése

A gyorsjavítások a módszerrel frissíthető szoftver verziók az alábbiak:

* Frissítéskezelés 0,1 vagy 0,2 0,3
* 1., 1.1-es, 1.2 frissítése
* 2, 2.1, 2.2 frissítése
* Frissítés 3 3.1.
* 4. frissítés

> [!NOTE] 
> Az ajánlott eljárás frissíteni 5 telepítéséhez van az Azure-portálon, az Update 3 és újabb verzióra frissítésekor. 3. frissítés előtti verzióit futtató eszközök frissítésekor, ez az eljárás használható. Is használhatja ezt az eljárást, ha az átjáró ellenőrzése sikertelen, az Azure portálon keresztül frissítések telepítése közben. Az ellenőrzés sikertelen lesz, ha egy nem DATA 0 hálózati adapterén rendelt átjáró és az eszköz frissítése 1-nél korábbi szoftver verziót futtat.

A gyorsjavítás metódus a következő három lépést foglal magában:

1. Töltse le a gyorsjavítás a Microsoft Update katalógusból.
2. Telepítse a, és ellenőrizze a normál üzemmód gyorsjavítások.
3. Telepítse a, és ellenőrizze a karbantartási mód gyorsjavítást.

#### <a name="download-updates-for-your-device"></a>Az eszköz frissítéseinek letöltése

Töltse le, és a gyorsjavítások telepítése az előírt sorrendben és a javasolt mappák:

| Sorrend | KB | Leírás | Frissítés típusa | Telepítés időpontja |A mappa telepítéséhez|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Szoftverfrissítés<br> Töltse le mindkét _HcsSoftwareUpdate.exe_ és _CisMSDAgent.exe_ |Rendszeres <br></br>Non-disruptive |~ 25 perc |FirstOrderUpdate|

Frissített 4 frissítést futtató eszközről, ha csak az operációs rendszer összegző frissítések telepítésével második rendelés frissítésként kell.

| Sorrend | KB | Leírás | Frissítés típusa | Telepítés időpontja |A mappa telepítéséhez|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |Az operációs rendszer összegző frissítések csomag <br> Töltse le a Windows Server 2012 R2 verzióra |Rendszeres <br></br>Non-disruptive |- |SecondOrderUpdate|

Ha 3 frissítést futtató eszközről telepítése vagy korábbi, telepítse az összegző frissítések mellett a következő.

| Sorrend | KB | Leírás | Frissítés típusa | Telepítés időpontja |A mappa telepítéséhez|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI illesztőprogram és a belső vezérlőprogram-frissítésekre <br> A legpontosabb Beállításhoz vezérlőprogram-frissítés (3.38 verzió) |Rendszeres <br></br>Non-disruptive |~ 3 óra <br> (2/a. tartalmazza. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Az operációs rendszer biztonsági frissítések csomag <br> Töltse le a Windows Server 2012 R2 verzióra |Rendszeres <br></br>Non-disruptive |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Az operációs rendszer frissítéseinek csomag <br> Töltse le a Windows Server 2012 R2 verzióra |Rendszeres <br></br>Non-disruptive |- |SecondOrderUpdate|


Előfordulhat, hogy is telepítendő fölött a fenti táblázatokban látható frissítések lemez belső vezérlőprogram-frissítésekre. Ellenőrizheti, hogy szükséges-e a lemez belső vezérlőprogram-frissítésekre futtatásával a `Get-HcsFirmwareVersion` parancsmag. Ha ezek a belső vezérlőprogram verzióinak futtatja: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, akkor nem szükséges a frissítések telepítéséhez.

| Sorrend | KB | Leírás | Frissítés típusa | Telepítés időpontja | A mappa telepítéséhez|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Lemez belső vezérlőprogram |Karbantartás <br></br>Zavaró |~ 30 mins | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Update 4 frissítését, ha a teljes telepítési idő 4 óra közel van.
> * Ez az eljárás használata a frissítés előtt győződjön róla, hogy mind a eszközvezérlők online állapotban és a hardverösszetevők kifogástalan.

A következő lépésekkel töltse le és telepítse a gyorsjavítást.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>További lépések
További információ a [frissítés 5 kiadás](storsimple-update5-release-notes.md).

