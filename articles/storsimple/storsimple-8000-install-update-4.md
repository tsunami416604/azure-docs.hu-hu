---
title: Telepítse a StorSimple 8000 sorozatú eszköz Update 4 |} A Microsoft Docs
description: Azt ismerteti, hogyan telepítheti a StorSimple 8000 sorozatú eszköz StorSimple 8000 Series Update 4.
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
ms.sourcegitcommit: 0a84b090d4c2fb57af3876c26a1f97aac12015c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2018
ms.locfileid: "38630194"
---
# <a name="install-update-4-on-your-storsimple-device"></a>Telepítse a StorSimple-eszköz Update 4

## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan telepítse a StorSimple eszköz egy korábbi verzióját szoftverek az Azure Portalon, és a gyorsjavítások metódussal Update 4. A gyorsjavítás módszert az átjáró konfigurálva van egy hálózati adapter nem a DATA 0 a StorSimple-eszköz és a frissítés előtti 1 szoftver verzióról frissíteni szeretne.

4. frissítéssel eszköz szoftverének, USM belső vezérlőprogram, LSI-illesztőt és belső vezérlőprogramja, Storport és Spaceport, az operációs rendszer biztonsági frissítések és számos más operációs rendszer frissítéseit tartalmazza.  Az eszközhöz, USM belső vezérlőprogram, Spaceport, Storport és más operációs rendszer frissítése a zavart nem okozó frissítések. A frissítések zavart nem okozó vagy rendszeres alkalmazhatók, vagy a gyorsjavítás metódus az Azure Portalon keresztül. A lemezfirmware-frissítések zavart okozó frissítések, és csak a gyorsjavítás metódus az eszköz a Windows PowerShell felületén keresztül lehet alkalmazni.

> [!IMPORTANT]
> * Egy készletét manuális és automatikus előzetes ellenőrzések a telepítés során meghatározni az eszköz szempontjából a hardver állapotát, és a hálózati kapcsolat előtt kell elvégezni. Ezen előzetes ellenőrzések elvégzése csak akkor, ha az Azure Portalon alkalmazza a frissítéseket.
> * Azt javasoljuk, hogy telepítse a szoftver- és egyéb általános frissítések az Azure Portalon. Csak abban az csak meg, a Windows PowerShell felületét a (frissítések telepítése) eszközt, ha a frissítés előtti átjáró az ellenőrzés sikertelen, a portálon. A verzióra frissít, a készlettől, a frissítések órát is igénybe vehet 4 (vagy nagyobb) telepítéséhez. A karbantartási módú frissítések is telepíteni kell az eszköz a Windows PowerShell felületéről. Karbantartási módú frissítések zavart okozó frissítések vannak, ezek az eszköz egy lefelé időt eredményez.
> * Ha a nem kötelező a StorSimple Snapshot Manager fut, győződjön meg arról, hogy frissítette a Snapshot Manager-verzió Update 4-re az eszköz frissítése előtt.


[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-4-via-the-azure-portal"></a>Update 4 telepítse az Azure Portalon
A következő lépésekkel, az eszköz frissítéséhez [4-es frissítést](storsimple-update4-release-notes.md).

> [!NOTE]
> A Microsoft az eszközről további diagnosztikai adatokat kér le. Ennek eredményeképpen ha az üzemeltetési csapat azonosítja az eszközöket, amelyek nem sikerül, akkor jobban ellátni a összegyűjti az adatokat az eszközről, és diagnosztizálhatja a problémákat. 

[!INCLUDE [storsimple-8000-install-update4-via-portal](../../includes/storsimple-8000-install-update4-via-portal.md)]

Ellenőrizze, hogy fut-e az eszköz **StorSimple 8000 Series Update 4 (6.3.9600.17820)**. A **utolsó frissítés dátuma** is módosítani kell.

* Most láthatja, hogy a karbantartási módú frissítések érhetők el (Ez az üzenet előfordulhat, hogy továbbra is megjelennek a frissítések telepítése után legfeljebb 24 óránál). Karbantartási módú frissítések zavart okozó frissítések, amelyek eszköz állásidőt eredményez, és csak az eszköz a Windows PowerShell felületéről alkalmazható.

* Töltse le a karbantartási módú frissítések szereplő lépések segítségével [gyorsjavítások letöltése](#to-download-hotfixes) keresse meg és töltse le a kb4011837 jelű csomagot, mely telepíti lemezfirmware-frissítések (a más frissítések már telepítve kell lennie már). Kövesse a felsorolt lépéseket [karbantartási módú gyorsjavítások telepítése és ellenőrzése](#to-install-and-verify-maintenance-mode-hotfixes) telepítése a karbantartási mód frissíti.

## <a name="install-update-4-as-a-hotfix"></a>Telepítse a Update 4-es gyorsjavítást
4. frissítés telepítése az ajánlott módszer van, az Azure Portalon keresztül.

Ez az eljárás használható, ha az átjáró ellenőrzése sikertelen, amikor telepíti a frissítéseket az Azure Portalon keresztül. Az ellenőrzés sikertelen, az átjáró nem a DATA 0 hálózati adapter rendelt és az eszköz 1. frissítés előtti szoftvert verziót futtat.

A szoftver verziójával, amely a gyorsjavítás metódussal lehet frissíteni a következők:

* 0.1, 0.2-es, 0,3 frissítése
* 1., 1.1-es, 1.2-es frissítés
* 2, 2.1-es, 2.2-es frissítés
* Update 3, 3.1.


A gyorsjavítás módszer a következő három lépésből áll:

1. A gyorsjavítások letöltése a Microsoft Update katalógusból.
2. Telepítse, és ellenőrizze a normál módú gyorsjavítások.
3. Telepítse, és ellenőrizze a karbantartási módú gyorsjavítás.

#### <a name="download-updates-for-your-device"></a>Töltse le a frissítéseket az eszközhöz

Le kell töltenie és a gyorsjavítások telepítése által előírt sorrendben, és a javasolt mappák:

| Sorrend | KB | Leírás | Frissítés típusa | Telepítés időpontja |Telepítési mappa|
| --- | --- | --- | --- | --- | --- |
| 1. |KB4011839 |Szoftverfrissítés |Rendszeres <br></br>Zavart nem okozó |~ 25 perc |FirstOrderUpdate|
| 2A. |KB4011841 <br> KB4011842 |LSI-illesztőt és a belső vezérlőprogram frissítése <br> USM belső vezérlőprogram frissítése (3.38 verzió) |Rendszeres <br></br>Zavart nem okozó |~ 3 óra <br> (2/a. tartalmazza. + 2B. + 2C.)|SecondOrderUpdate|
| 2B. |KB3139398, KB3108381 <br> KB3205400, KB3142030 <br> KB3197873, KB3197873 <br> KB3192392, KB3153704 <br> KB3174644, KB3139914  |Az operációs rendszer biztonsági frissítések csomag <br> A Windows Server 2012 R2 letöltése |Rendszeres <br></br>Zavart nem okozó |- |SecondOrderUpdate|
| 2C. |KB3210083, KB3103616 <br> KB3146621, KB3121261 <br> KB3123538 |Az operációs rendszer frissítéseinek csomag <br> A Windows Server 2012 R2 letöltése |Rendszeres <br></br>Zavart nem okozó |- |SecondOrderUpdate|

Emellett szükség lehet a frissítések az előző táblázatban látható felett lemezfirmware-frissítések telepítéséhez. Ellenőrizheti, hogy szükséges-e a lemezfirmware-frissítések futtatásával a `Get-HcsFirmwareVersion` parancsmagot. Ezek belső vezérlőprogrammal futtatásakor: `XMGJ`, `XGEG`, `KZ50`, `F6C2`, `VR08`, `N002`, `0106`, akkor nem szükséges telepítenie ezeket a frissítéseket.

| Sorrend | KB | Leírás | Frissítés típusa | Telepítés időpontja | Telepítési mappa|
| --- | --- | --- | --- | --- | --- |
| 3. |KB3121899 |Lemezfirmware |Karbantartás <br></br>Azokat a káros |körülbelül 30 perc | ThirdOrderUpdate |

<br></br>

> [!IMPORTANT]
> * Ez az eljárás csak egyszer hajtható végre Update 4 a alkalmazni kell. Az Azure portal segítségével a alkalmazni ezt követő frissítésekben.
> * Ha frissíti a 3. frissítés vagy 3.1-et, a teljes telepítési idő közelében 4 órán keresztül van.
> * Az alábbi eljárás a frissítés előtt győződjön meg arról, hogy mindkét eszközvezérlő online, és minden hardverösszetevő kifogástalan állapotban.

Hajtsa végre az alábbi lépéseket, töltse le és telepítse a gyorsjavítást.

[!INCLUDE [storsimple-install-update4-hotfix](../../includes/storsimple-install-update4-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>További lépések
Tudjon meg többet a [Update 4 kiadás](storsimple-update4-release-notes.md).

