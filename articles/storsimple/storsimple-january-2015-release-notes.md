---
title: "A StorSimple 8000 frissítés 0,2 kibocsátási megjegyzései |} Microsoft Docs"
description: "Az új szolgáltatásokat és javításokat, nyissa meg problémák és rendelkezésre megkerülő megoldások ismerteti a 2015. januári a Microsoft Azure StorSimple release (frissítés 0,2)."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: d9684ae3-b38f-4678-9d70-e5dbc6b03350
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: f2b147f32f10208b8daa391095a7d0094a0c2c09
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="storsimple-8000-series-update-02-release-notes---january-2015"></a>A StorSimple 8000 Series Update 0,2 kibocsátási megjegyzések – 2015. januári
> [!NOTE]
> A klasszikus portál StorSimple elavult. A StorSimple eszköz kezelői automatikusan áthelyezi a érvénytelenítése ütemezés szerint az új Azure-portálon. Kapni fog egy e-mailek és a portál értesítései az áthelyezés. Ez a dokumentum hamarosan is kell vonni. Az áthelyezés kapcsolatos kérdéseivel lásd: [– gyakori kérdések: áthelyezése az Azure-portálon](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Áttekintés
Az alábbi kibocsátási megjegyzések a Microsoft Azure storsimple 2015. januári frissítés a kritikus megnyitott problémák azonosításához. Az ebben a kiadásban szereplő StorSimple szoftver és a belső vezérlőprogram frissítés listáját is tartalmaznak. Ez az a második kiadás után a StorSimple 8000 Series kiadási verziójával történt a 2014. július általánosan elérhető.

A frissítés nem módosul a fizikai eszköz szoftverének verziójával. októberi frissítés. Verzió 6.3.9600.17312 továbbra is. Kép: a virtuális eszköz által használt kép ebben a kiadásban megváltozott. Ezért minden az új virtuális eszköz létrehozása után 1/20/2015 megjeleníti a 6.3.9600.17361 verzióra.  

Tekintse át a kibocsátási megjegyzések a 2015. januári frissítés szerepel a következő információkat.

> [!IMPORTANT]
> * A frissítés nem érhető el a Windows Update, és nem telepíthető, például más frissítéseket. Az eszköz nem fog kapni a frissítés, még akkor is, ha a frissítéseket alkalmazza a klasszikus Azure portál használatával. A frissítés után 2015. januári 20 létrehozott virtuális eszközök csak vonatkozik. 
> * A StorSimple január kiadása nem tartalmaz a StorSimple fizikai eszköz módosításait. Elérhető Windows-frissítések továbbra is alkalmazhat a virtuális eszköz, többek között a legutóbbi biztonsági javítások, de nem jelenik meg a StorSimple fizikai eszköz verziója megváltozik.
> 
> 

## <a name="whats-new-in-the-january-release"></a>What's new in a január kiadás
A frissítés a kötetek offline állapotra vált, a virtuális eszközön kapcsolódó javítást tartalmaz. (Lásd: [ebben a kiadásban javított](#issues-fixed-in-the-january-release).)  

A frissítés nem tartalmaz új szolgáltatások vagy funkciók.  

## <a name="issues-fixed-in-the-january-release"></a>A január kiadásban megoldott problémák
A következő táblázat ismerteti a problémát, a frissítés rögzített.

| Nem. | Szolgáltatás | Probléma | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Kötetek offline állapotra vált |Ha több percig magas felhő késések továbbra is fennáll, a StorSimple virtuális eszköz kötetek kapcsolat nélküli módba az állomáson. Ez a javítás felhő késések, ezáltal minimalizálja a helyzetekben, amelyek a kötetek offline állapotba állomáson küszöbértéke növeli. |Nem |Igen |

## <a name="known-issues-in-the-january-release"></a>Ismert problémák a január kiadásban
A következő táblázat összefoglalja az ismert problémákról, ebben a kiadásban.

| Nem. | Szolgáltatás | Probléma | Megjegyzések/megoldás | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Gyári beállítások visszaállítása |Bizonyos esetekben a gyári beállítások visszaállítása, végrehajtásakor a StorSimple eszköz előfordulhat, hogy, és megjeleníti ezt az üzenetet: **a gyári alaphelyzetbe állítása folyamatban van a (8 fázis).** Ez akkor fordul elő, ha a CTRL + C gombra, amíg folyamatban van a parancsmagot. |CTRL + C nem nyomja meg a gyári beállítások visszaállítása kezdeményezése után. Ha már ebben az állapotban, forduljon a Microsoft Support további lépéseket. |Igen |Nem |
| 2 |Lemez kvórum |Bizonyos ritkán előforduló esetekben ha a legtöbb, a EBOD ház egy 8600 eszköz lemezek megszakadt a kapcsolat nincs lemez kvórum eredményezi majd a tárolókészlet lesz kapcsolva a hálózatról. Offline állapotban maradnak, még akkor is, ha a lemez újracsatlakoztatását. |Szüksége lesz az eszköz újraindul. Ha a probléma továbbra is fennáll, forduljon a Microsoft Support további lépéseket. |Igen |Nem |
| 3 |Felhőalapú pillanatfelvétel hibák |Ritka esetekben a egy felhő-pillanatfelvételt sikertelen lehet a hibával **elérte a maximális biztonsági korlátot**. Ez akkor fordul elő, ha ugyanazon az eszközön, az azonos eredeti kötet már törölt 255 online klónok túllépi. | |Igen |Igen |
| 4 |Helytelen vezérlő azonosítója |A vezérlő helyettesítő végrehajtásakor vezérlő 0 lehet, hogy megjelennek, a vezérlő 1. Tartományvezérlő csere közben, amikor a kép betöltődik a társ-csomópont a vezérlő azonosítója is megjelennek kezdetben, a partner tartományvezérlő azonosítóját.  Ritka esetben ez a viselkedés előfordulhat, hogy is látható. a rendszer újraindítását követően. |Nincs felhasználói beavatkozásra szükség. Ez a helyzet magától megoldódik, amikor a vezérlő helyettesítő befejeződése után. |Igen |Nem |
| 5 |Eszközfigyelési diagramok |A StorSimple Manager szolgáltatásban az eszköz figyelési diagramok nem egyszerű vagy az NTLM-hitelesítés engedélyezve van a proxykiszolgáló beállításait az eszközön. |Módosítsa a webproxy-konfigurációja az eszközt a StorSimple Manager szolgáltatás úgy, hogy a hitelesítés beállítása nincs regisztrálva. Ehhez futtassa a StorSimple Set-HcsWebProxy parancsmag a Windows PowerShell. |Igen |Igen |
| 6 |Tárfiókok |A Storage szolgáltatás használatával törli a tárfiókot a nem támogatott lehetőséget. Ez a helyzet, amelyben felhasználói nem lehet adatokat beolvasni irányítja. | |Igen |Igen |
| 7 |Eszköz feladatátvétel |A különböző céleszközökre a azonos forráseszközről származó kötettároló több feladatátvétel nem támogatott. |Több eszköz egyetlen elhalt eszközről feladatátvétel működésképtelenné teszi az adatok tulajdonjoga elvesztése átadja a feladatokat eszköz első kötettárolók. Egy feladatátvétel után ezek kötettárolók jelenik meg, vagy a klasszikus Azure portálon meg eltérően viselkednek. |Igen |Nem |
| 8 |Telepítés |Során StorSimple Adapter SharePoint telepítéshez meg kell adnia egy eszköz IP-cím ahhoz, hogy a telepítés sikeres befejezéséhez. | |Igen |Nem |
| 9 |Webproxy |Ha a webproxy konfigurálása a megadott protokoll HTTPS-t, az eszköz-szolgáltatások közötti kommunikáció néven érinti, és kerül, hogy az eszköz offline állapotban. Támogatási csomag is hozható létre a folyamat során fel jelentős erőforrásokat az eszközön. |Győződjön meg arról, hogy a webalkalmazás-proxy URL-címe HTTP megadott protokollt. További információ hogyan [az eszköz webalkalmazás-proxy konfigurálása](storsimple-configure-web-proxy.md). |Igen |Nem |
| 10 |Webproxy |Ha konfigurált és engedélyezett a webalkalmazás-proxy regisztrált egy eszközt, majd szüksége lesz az eszköz aktív vezérlőjén újraindítására. | |Igen |Nem |
| 11 |Magas felhő késéssel és nagy i/o-munkaterhelés |Amikor a StorSimple eszköz nagyon magas felhő késések (másodperc sorrendben) és a magas i/o-munkaterhelés észlel, az eszköz kötetek csökkent állapotba, és az i/o "az eszköz nem áll készen" hiba miatt sikertelen lehet. |Szüksége lesz, manuálisan indítsa újra a eszközvezérlők vagy egy eszköz feladatátvétel ebben a helyzetben helyreállítás végrehajtása. |Igen |Nem |

## <a name="physical-device-updates-in-the-january-release"></a>A kiadási januárban fizikai eszköz frissítése
A frissítés nem tartalmaz bármilyen más jellegű módosítását a StorSimple eszközt.

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-the-january-release"></a>Soros csatlakozású SCSI (SAS) vezérlő és a belső vezérlőprogram-frissítésekre januárban kiadás
Ez a kiadás nem tartalmaz frissítéseket soros csatlakozású SCSI (SAS) vagy a belső vezérlőprogram. Az illesztőprogram frissítéséhez a októberben 2014 kiadásban volt. 

## <a name="virtual-device-updates-in-the-january-release"></a>Virtuális eszköz frissítések januárban kiadás
Ez a kiadás egy frissített lemezképet a virtuális eszköz tartalmaz. 2015. januári 20 után létrehozott összes virtuális eszközre a szoftververzió jelenik meg: 6.3.9600.17361.

