---
title: "A StorSimple eszköz frissítése 3 telepítéséhez |} Microsoft Docs"
description: "Ismerteti a StorSimple 8000 Series Update 3 telepítéséhez a StorSimple 8000 series eszközön."
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: 
ms.assetid: c6c4634d-4f3a-4bc4-b307-a22bf18664e1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 2b99b9cd52dd28f7f62b5d8d5ffe32339a67f82a
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="install-update-3-on-your-storsimple-8000-series-device"></a>A StorSimple 8000 series eszközön Update 3 telepítéséhez

> [!NOTE]
> A klasszikus portál StorSimple elavult. A StorSimple eszköz kezelői automatikusan áthelyezi a érvénytelenítése ütemezés szerint az új Azure-portálon. Kapni fog egy e-mailek és a portál értesítései az áthelyezés. Ez a dokumentum hamarosan is kell vonni. Az áthelyezés kapcsolatos kérdéseivel lásd: [– gyakori kérdések: áthelyezése az Azure-portálon](storsimple-8000-move-azure-portal-faq.md).


## <a name="overview"></a>Áttekintés

Ez az oktatóanyag azt ismerteti, hogyan 3 frissítés telepítése a StorSimple eszköz egy korábbi verzióját szoftver a klasszikus Azure portálon keresztül, és a gyorsjavítások a módszerrel. Amikor egy átjáró konfigurálva van a hálózati adaptert, mint a DATA 0 a StorSimple eszköz és a frissítés előtti 1 szoftver verziójából származó frissíteni kívánt a gyorsjavítás módszert használják.

Frissítés 3 eszköz szoftver, a LSI illesztőprogram és a belső vezérlőprogram, Storport és Spaceport frissíti. Ha frissíti a 2. frissítés vagy korábbi verziójú, is szükségesek az iSCSI, a WMI-alkalmazásához, és bizonyos esetekben lemez a belső vezérlőprogram-frissítésekre. Az eszközhöz, WMI, iSCSI, LSI illesztőprogram, Spaceport és Storport javítások nem zavaró frissítések érhetők el. Ezeket a frissítéseket a klasszikus Azure portálon keresztül lehet alkalmazni. A lemez belső vezérlőprogram-frissítésekre zavaró frissítések érhetők el, és csak akkor érvényesíthetők, az eszköz a Windows PowerShell felületén keresztül.

> [!IMPORTANT]
> * Manuális és automatikus előtti ellenőrzés történik az eszköz állapotának hardver állapot és hálózati kapcsolatok tekintetében telepítése előtt. Az előzetes ellenőrzések csak akkor, ha a frissítések telepítését a klasszikus Azure-portálon történik.
> * Azt javasoljuk, hogy telepítse a szoftver- és illesztőprogram frissítéseket, a klasszikus Azure portálon keresztül. Csak akkor, ha a frissítés előtti átjáró ellenőrzés sikertelen, a portálon lépjen a Windows PowerShell felületen, az eszköz (a frissítések telepítése). Attól függően, a verzióra frissít, a, a frissítések telepítése 1.5-2.5 óráig is eltarthat. A karbantartási mód frissítéseket telepíteni kell az eszköz a Windows PowerShell felületén keresztül. Karbantartási mód frissítések zavaró frissítések érhetők el, mert az eszköz teljesen állásidő.
> * Ha a választható StorSimple Snapshot Manager fut, győződjön meg arról, hogy frissítette a Snapshot Manager verzióra frissítés 2 az eszköz frissítése előtt.

[!INCLUDE [storsimple-preparing-for-update](../../includes/storsimple-preparing-for-updates.md)]

## <a name="install-update-3-via-the-azure-classic-portal"></a>3. frissítés telepítése a klasszikus Azure portálon keresztül
Az eszköz frissítése a következő lépésekkel [frissítése 3](storsimple-update3-release-notes.md).

> [!NOTE]
> Ha Ön alkalmazzák a 2. frissítés vagy újabb verziók (beleértve a 2.1-es frissítés), a Microsoft fog tudni lekéréses további diagnosztikai adatok az eszközről. Ezen adatok segítenek a StorSimple eszközök tapasztal problémákat és a problémák diagnosztizálásával eszközök azonosításához. Elfogadásával Update 2 vagy újabb, hogy lehetővé teszik a számunkra a proaktív támogatásához.


[!INCLUDE [storsimple-install-update2-via-portal](../../includes/storsimple-install-update2-via-portal.md)]

Győződjön meg arról, hogy az eszköz fut. **a StorSimple 8000 Series Update 3 (6.3.9600.17759)**. A **utolsó frissítés dátuma** módosul. 
   - 2. frissítés előtt verziójából származó frissítésekor, láthatja, hogy a karbantartási mód frissítések érhetők el. Ez az üzenet előfordulhat, hogy továbbra is megjelennek a frissítések telepítése után legfeljebb 24 órában.
     Karbantartási mód frissítések, amelyek eszköz állásidőt eredményezhettek zavaró frissítések érhetők el. Ezek a frissítések csak akkor alkalmazható, az eszköz a Windows PowerShell felületén keresztül. Bizonyos esetekben 1.2-es frissítés futtatásakor a lemez belső vezérlőprogram már lehet, hogy naprakész, és nem karbantartás módban frissítéseket telepíteni kell.
   - Ha frissíteni a 2. frissítés vagy újabb, az eszköz kell naprakész. Ugorjon a következő lépéssel.

A karbantartási mód frissítések letöltése a felsorolt lépéseket követve [gyorsjavítások letöltése](#to-download-hotfixes) keresését, és töltse le a KB3121899, mely telepíti lemez belső vezérlőprogram-frissítésekre (a más frissítések már telepítve kell lennie mostanra). Kövesse a felsorolt lépéseket [telepítse, és ellenőrizze a karbantartási mód gyorsjavítások](#to-install-and-verify-maintenance-mode-hotfixes) a karbantartási mód telepítendő frissítések. 

## <a name="install-update-3-as-a-hotfix"></a>3. frissítés gyorsjavítás telepítése
Ez az eljárás használható, ha az átjáró ellenőrzése sikertelen, a klasszikus Azure portálon keresztül frissítések telepítése közben. Az ellenőrzés sikertelen, egy nem DATA 0 hálózati adapterén rendelt átjáró és az eszköz 1. frissítés előtti szoftvert verziót futtat.

A gyorsjavítások a módszerrel frissíthető szoftver verziók az alábbiak:

* Frissítéskezelés 0,1 vagy 0,2 0,3
* 1., 1.1-es, 1.2 frissítése
* 2, 2.1, 2.2 frissítése 

> [!IMPORTANT]
> * Ha az eszköz kiadásban (GA) verziója fut, lépjen kapcsolatba [Microsoft Support](storsimple-contact-microsoft-support.md) segítik a frissítést.
> 
> 

A gyorsjavítás metódus a következő három lépést foglal magában:

1. Töltse le a gyorsjavítás a Microsoft Update katalógusból.
2. Telepítse a, és ellenőrizze a normál üzemmód gyorsjavítások.
3. Telepítse, és a karbantartási mód gyorsjavítás ellenőrzése (csak frissítésekor a frissítés előtti 2 szoftver).

#### <a name="download-updates-for-your-device"></a>Az eszköz frissítéseinek letöltése
**Ha az eszköz fut frissítés 2.1-es vagy 2.2**, töltse le és telepítse a következő gyorsjavításokat által előírt sorrendben:

| Sorrendje | KB | Leírás | Frissítés típusa | Telepítés időpontja |
| --- | --- | --- | --- | --- |
| 1. |KB3186843 |Szoftverfrissítési &#42; |Rendszeres <br></br>A nem zavaró |~ 45 perc |
| 2. |KB3186859 |LSI illesztőprogram és a belső vezérlőprogram |Rendszeres <br></br>A nem zavaró |~ 20 perc |
| 3. |KB3121261 |Storport és Spaceport javítás </br> Windows Server 2012 R2 |Rendszeres <br></br>A nem zavaró |~ 20 perc |

&#42;  *Vegye figyelembe, hogy a szoftverfrissítés két bináris fájlt tartalmaz: eszköz szoftverfrissítés végrehajtásával kerüli meg a `all-hcsmdssoftwareupdate` és végrehajtásával kerüli meg a Konfigurációelemek és az Mds-ügynök `all-cismdsagentupdatebundle`. Az eszköz szoftverfrissítés előtt a Konfigurációelemek és Mds ügynök rendszerre telepíthető. Az aktív vezérlővel keresztül is újra kell indítani a `Restart-HcsController` parancsmag a Konfigurációelemek és az Mds-ügynök telepítését követően frissítse (és frissíti a fennmaradó alkalmazása előtt).* 

**Ha az eszköz fut-e frissítés 0,1, 0,2, 0,3, 1.0-s, 1.1-es, 1.2-es vagy 2.0-s**, meg kell frissítések letöltése és telepítése mellett az szoftvert, és a gyorsjavítások LSI illesztőprogram és a belső vezérlőprogram (az előző táblázatban látható), az előírt sorrendben:

| Sorrendje | KB | Leírás | Frissítés típusa | Telepítés időpontja |
| --- | --- | --- | --- | --- |
| 4. |KB3146621 |az iSCSI-csomag |Rendszeres <br></br>A nem zavaró |~ 20 perc |
| 5. |KB3103616 |WMI-csomag |Rendszeres <br></br>A nem zavaró |~ 12 perc |

<br></br>

**Ha az eszköz 0,2, 0,3, 1.0-s, 1.1-es és 1.2-es verzió fut**, is szeretne telepíteni fölött a fenti táblázatokban látható frissítések lemez belső vezérlőprogram-frissítésekre. Ellenőrizheti, hogy szükséges-e a lemez belső vezérlőprogram-frissítésekre futtatásával a `Get-HcsFirmwareVersion` parancsmag. Ha ezek a belső vezérlőprogram verzióinak futtatja: `XMGG`, `XGEG`, `KZ50`, `F6C2`, `VR08`, akkor nem szükséges a frissítések telepítéséhez.

| Sorrendje | KB | Leírás | Frissítés típusa | Telepítés időpontja |
| --- | --- | --- | --- | --- |
| 6. |KB3121899 |Lemez belső vezérlőprogram |Karbantartás <br></br>Zavaró |~ 30 perc |

<br></br>

> [!IMPORTANT]
> * Ezt az eljárást kell alkalmazni a 3. frissítés csak egyszer hajtható végre. A klasszikus Azure portál segítségével soron következő frissítések alkalmazásához.
> * Frissítés 2.2 frissítését, ha a teljes telepítési idő 1.1 óra közel van.
> * Ez az eljárás használata a frissítés előtt győződjön róla, hogy mind a eszközvezérlők online állapotban és a hardverösszetevők kifogástalan.
> 
> 

A következő lépésekkel töltse le és telepítse a gyorsjavítást.

[!INCLUDE [storsimple-install-update3-hotfix](../../includes/storsimple-install-update3-hotfix.md)]

[!INCLUDE [storsimple-install-troubleshooting](../../includes/storsimple-install-troubleshooting.md)]

## <a name="next-steps"></a>Következő lépések
További információ a [Update 3 kiadásra](storsimple-update3-release-notes.md).

