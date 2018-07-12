---
title: 5. frissítés telepítése a StorSimple 8000 sorozatú eszköz |} A Microsoft Docs
description: Azt ismerteti, hogyan telepítheti a StorSimple 8000 sorozatú eszköz StorSimple 8000 Series Update 5.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38698710"
---
# <a name="install-update-5-on-your-storsimple-device"></a>5. frissítés telepítése a StorSimple-eszköz

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan telepítése Update 5 egy korábbi verzióját szoftverek az Azure Portalon, és a gyorsjavítások metódussal egy StorSimple eszközön. A gyorsjavítás metódust használ a frissítés előtti 3 verzióit futtató eszközök 5. frissítés telepítése során. A gyorsjavítás módszer is használatos, amikor az átjáró nem a DATA 0 a StorSimple-eszköz hálózati adapteren van konfigurálva, és a frissítés előtti 1 szoftver verzióról frissíteni kívánt.

5. frissítése tartalmazza az eszköz szoftvere Storport és Spaceport, az operációs rendszer biztonsági frissítések és az operációs rendszer frissítéseit és a lemezfirmware-frissítések.  Az eszközhöz, Spaceport, Storport, biztonsági és más operációs rendszer frissítése a zavart nem okozó frissítések. A frissítések zavart nem okozó vagy rendszeres alkalmazhatók, vagy a gyorsjavítás metódus az Azure Portalon keresztül. A lemezfirmware-frissítések zavart okozó frissítések, és akkor alkalmaz, amikor az eszköz a gyorsjavítás metódus az eszköz a Windows PowerShell felületén keresztül karbantartási módban van.

> [!IMPORTANT]
> * 5. frissítése egy kötelező frissítés, és közvetlenül kell telepíteni. További információkért lásd: [Update 5 kiadási megjegyzései](storsimple-update5-release-notes.md).
> * Egy készletét manuális és automatikus előzetes ellenőrzések a telepítés során meghatározni az eszköz szempontjából a hardver állapotát, és a hálózati kapcsolat előtt kell elvégezni. Ezen előzetes ellenőrzések elvégzése csak akkor, ha az Azure Portalon alkalmazza a frissítéseket.
> * Erősen ajánlott a 3. frissítés előtti verziót futtató eszköz frissítésekor a frissítések telepítése a gyorsjavítás módszerrel. Ha problémába ütközik, [szóló támogatási jegyek rögzítése](storsimple-8000-contact-microsoft-support.md).
> * Azt javasoljuk, hogy telepítse a szoftver- és egyéb általános frissítések az Azure Portalon. Csak abban az csak meg, a Windows PowerShell felületét a (frissítések telepítése) eszközt, ha a frissítés előtti átjáró az ellenőrzés sikertelen, a portálon. A verzióra frissít, a készlettől, a frissítések órát is igénybe vehet 4 (vagy nagyobb) telepítéséhez. A karbantartási módú frissítések telepíteni kell az eszköz a Windows PowerShell felületéről. Karbantartási módú frissítések zavart okozó frissítések vannak, ezek eredményez olyan lefelé idő az eszköz.
> * Ha a nem kötelező a StorSimple Snapshot Manager fut, győződjön meg arról, hogy frissítette a Snapshot Manager verzióra frissítés 5-re az eszköz frissítése előtt.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-5-via-the-azure-portal"></a>5. frissítés telepítése az Azure Portalon
A következő lépésekkel, az eszköz frissítéséhez [Update 5](storsimple-update5-release-notes.md).

> [!NOTE]
> A Microsoft az eszközről további diagnosztikai adatokat kér le. Ennek eredményeképpen ha az üzemeltetési csapat azonosítja az eszközöket, amelyek nem sikerül, akkor jobban ellátni a összegyűjti az adatokat az eszközről, és diagnosztizálhatja a problémákat.

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update5-via-portal.md)]

Ellenőrizze, hogy fut-e az eszköz **StorSimple 8000 Series Update 5 (6.3.9600.17845)**. A **utolsó frissítés dátuma** kell módosítani.

Most láthatja, hogy a karbantartási módú frissítések érhetők el (Ez az üzenet előfordulhat, hogy továbbra is megjelennek a frissítések telepítése után legfeljebb 24 óránál). A karbantartási módú frissítés telepítésének lépéseit a következő szakasz részletezi.

[!INCLUDE [storsimple-8000-install-maintenance-mode-updates](../../includes/storsimple-8000-install-maintenance-mode-updates.md)]

## <a name="install-update-5-as-a-hotfix"></a>Update 5 egy gyorsjavítás telepítése

A szoftver verziójával, amely a gyorsjavítás metódussal lehet frissíteni a következők:

* 0.1, 0.2-es, 0,3 frissítése
* 1., 1.1-es, 1.2-es frissítés
* 2, 2.1-es, 2.2-es frissítés
* Update 3, 3.1.
* 4. frissítés

> [!NOTE] 
> Az ajánlott módszer a telepítendő Update 5 van az Azure Portalon keresztül Update 3 és újabb verzióra való frissítésekor. 3. frissítés előtti verziót futtató eszköz a frissítés során ez az eljárás használható. Is használhatja ezt az eljárást, ha az átjáró ellenőrzése sikertelen, amikor telepíti a frissítéseket az Azure Portalon keresztül. Az ellenőrzés sikertelen, ha az átjáró nem a DATA 0 hálózati adapter rendelt és az eszköz frissítése 1-nél korábbi szoftverfrissítési verziót futtat.

A gyorsjavítás módszer a következő három lépésből áll:

1. A gyorsjavítások letöltése a Microsoft Update katalógusból.
2. Telepítse, és ellenőrizze a normál módú gyorsjavítások.
3. Telepítse, és ellenőrizze a karbantartási módú gyorsjavítás.

#### <a name="download-updates-for-your-device"></a>Töltse le a frissítéseket az eszközhöz

Le kell töltenie és a gyorsjavítások telepítése által előírt sorrendben, és a javasolt mappák:

| Sorrend | KB | Leírás | Frissítés típusa | Telepítés időpontja |Telepítési mappa|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4037264 |Szoftverfrissítés<br> Töltse le mindkét _HcsSoftwareUpdate.exe_ és _CisMSDAgent.exe_ |Rendszeres <br></br>Zavart nem okozó |~ 25 perc |FirstOrderUpdate|

Az Update 4-es eszközök frissítése, ha csak az operációs rendszer összegző frissítések telepítésével, a másodrendű frissítések kell.

| Sorrend | KB | Leírás | Frissítés típusa | Telepítés időpontja |Telepítési mappa|
| --- | --- | --- | --- | --- | --- |
| 2A. |KB4025336 |Az operációs rendszer az összegző frissítések csomag <br> Töltse le a Windows Server 2012 R2 verzióra |Rendszeres <br></br>Zavart nem okozó |- |SecondOrderUpdate|

Ha az Update 3-es eszközök telepítése, illetve a korábbi, telepítse az összegző frissítések mellett az alábbi.

| Sorrend | KB | Leírás | Frissítés típusa | Telepítés időpontja |Telepítési mappa|
| --- | --- | --- | --- | --- | --- |
| 2B. |KB4011841 <br> KB4011842 |LSI-illesztőt és a belső vezérlőprogram frissítése <br> USM belső vezérlőprogram frissítése (3.38 verzió) |Rendszeres <br></br>Zavart nem okozó |~ 3 óra <br> (2/a. tartalmazza. + 2B. + 2C.)|SecondOrderUpdate|
| 2C. |KB3139398 <br> KB3142030 <br> KB3108381 <br> KB3153704 <br> KB3174644 <br> KB3139914   |Az operációs rendszer biztonsági frissítések csomag <br> Töltse le a Windows Server 2012 R2 verzióra |Rendszeres <br></br>Zavart nem okozó |- |SecondOrderUpdate|
| 2D. |KB3146621 <br> KB3103616 <br> KB3121261 <br> KB3123538 |Az operációs rendszer frissítéseinek csomag <br> Töltse le a Windows Server 2012 R2 verzióra |Rendszeres <br></br>Zavart nem okozó |- |SecondOrderUpdate|


Emellett szükség lehet a frissítések az előző táblázatban látható felett lemezfirmware-frissítések telepítéséhez. Ellenőrizheti, hogy szükséges-e a lemezfirmware-frissítések futtatásával a `Get-HcsFirmwareVersion` parancsmagot. Ezek belső vezérlőprogrammal futtatásakor: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N003`, `0107`, akkor nem szükséges telepítenie ezeket a frissítéseket.

| Sorrend | KB | Leírás | Frissítés típusa | Telepítés időpontja | Telepítési mappa|
| --- | --- | --- | --- | --- | --- |
| 3. |KB4037263 |Lemezfirmware |Karbantartás <br></br>Azokat a káros |körülbelül 30 perc | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Ha frissít a Update 4, teljes telepítési idő közelében 4 órán keresztül van.
> * Az alábbi eljárás a frissítés előtt győződjön meg arról, hogy mindkét eszközvezérlő online, és minden hardverösszetevő kifogástalan állapotban.

Hajtsa végre az alábbi lépéseket, töltse le és telepítse a gyorsjavítást.

[!INCLUDE [storsimple-install-update5-hotfix](../../includes/storsimple-install-update5-hotfix.md)]

[!INCLUDE [storsimple-8000-install-troubleshooting](../../includes/storsimple-8000-install-troubleshooting.md)]

## <a name="next-steps"></a>További lépések
Tudjon meg többet a [Update 5 kiadás](storsimple-update5-release-notes.md).

