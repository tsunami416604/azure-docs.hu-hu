---
title: "A StorSimple 8000 kibocsátási verziója kibocsátási megjegyzései |} Microsoft Docs"
description: "Új funkciók, nyissa meg a problémákat és rendelkezésre megkerülő megoldások ismerteti a 2014. július a Microsoft Azure StorSimple release."
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: 
ms.assetid: 12f1796e-37c3-42b4-b997-a84fc1950c20
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: e6e087ad61ee54fef3552895c00a478fa51e2072
ms.sourcegitcommit: 0930aabc3ede63240f60c2c61baa88ac6576c508
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2017
---
# <a name="storsimple-8000-series-release-version-release-notes---july-2014"></a>A StorSimple 8000 Series kiadásával kibocsátási megjegyzések – 2014. július

> [!NOTE]
> A klasszikus portál StorSimple elavult. A StorSimple eszköz kezelői automatikusan áthelyezi a érvénytelenítése ütemezés szerint az új Azure-portálon. Kapni fog egy e-mailek és a portál értesítései az áthelyezés. Ez a dokumentum hamarosan is kell vonni. Az áthelyezés kapcsolatos kérdéseivel lásd: [– gyakori kérdések: áthelyezése az Azure-portálon](storsimple-8000-move-azure-portal-faq.md).

## <a name="overview"></a>Áttekintés

A következő kibocsátási megjegyzések a kritikus megnyitott problémák azonosításához a StorSimple 8000 Series a Microsoft Azure StorSimple 2014. július általános elérhetőségével (GA) kiadásában. Ebben a kiadásban szoftververzió 6.3.9600.17215 felel meg.

Hacsak másként nincs megadva, a kibocsátási megjegyzéseket összes modellt a StorSimple eszköz vonatkozik. A kibocsátási megjegyzések folyamatosan frissülnek; megoldást igénylő kritikus fontosságú problémáit ismertté hozzáadásuk után. Mielőtt telepíti a Microsoft Azure StorSimple megoldáshoz, vegye figyelembe a következőket.

## <a name="known-issues-in-this-release"></a>Ebben a kiadásban ismert problémák

A következő táblázat összefoglalja az ismert problémákról, ebben a kiadásban.  

| Nem. | Szolgáltatás | Probléma | Megjegyzések/megoldás | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Gyári beállítások visszaállítása |Bizonyos esetekben a gyári beállítások visszaállítása, végrehajtásakor a StorSimple eszköz előfordulhat, hogy, és megjeleníti ezt az üzenetet: **a gyári alaphelyzetbe állítása folyamatban van a (fázis 8)**. Ez akkor fordul elő, ha a CTRL + C gombra, amíg folyamatban van a parancsmagot. |CTRL + C nem nyomja meg a gyári beállítások visszaállítása kezdeményezése után. Ha már ebben az állapotban, lépjen kapcsolatba a Microsoft Support további lépéseket. |Igen |Nem |
| 2 |Lemez kvórum |Bizonyos ritkán előforduló esetekben, ha a legtöbb, a EBOD ház egy 8600 eszköz lemezek megszakadt a kapcsolat nincs lemez kvórum eredményezi majd a tárolókészlet offline állapotba kerül. Offline állapotban maradnak, még akkor is, ha a lemez újracsatlakoztatását. |Szüksége lesz az eszköz újraindul. Ha a probléma továbbra is fennáll, forduljon a Microsoft Support további lépéseket. |Igen |Nem |
| 3 |Felhőalapú pillanatfelvétel hibák |Ritka esetekben a egy felhő-pillanatfelvételt sikertelen lehet a hibával **elérte a maximális biztonsági korlátot**. Ez akkor fordul elő, ha ugyanazon az eszközön, az azonos eredeti kötet már törölt 255 online klónok túllépi. | |Igen |Igen |
| 4 |Helytelen vezérlő azonosítója |A vezérlő helyettesítő végrehajtásakor vezérlő 0 lehet, hogy megjelennek, a vezérlő 1. Tartományvezérlő csere közben, amikor a kép betöltődik a társ-csomópont a vezérlő azonosítója is megjelennek kezdetben, a partner tartományvezérlő azonosítóját. Ritka esetben ez a viselkedés előfordulhat, hogy is látható. a rendszer újraindítását követően. |Nincs felhasználói beavatkozásra szükség. Ez a helyzet magától megoldódik, amikor a vezérlő helyettesítő befejeződése után. |Igen |Nem |
| 5 |Eszközfigyelési diagramok |A StorSimple Manager szolgáltatásban az eszköz figyelési diagramok nem egyszerű vagy az NTLM-hitelesítés engedélyezve van a proxykiszolgáló beállításait az eszközön. |Módosítsa a webproxy-konfigurációja az eszközt a StorSimple Manager szolgáltatás úgy, hogy a hitelesítés beállítása nincs regisztrálva. Ehhez futtassa a Windows PowerShell parancsmag StorSimple Set-HcsWebProxy. |Igen |Igen |
| 6 |Tárfiókok |A Storage szolgáltatás használatával törli a tárfiókot a nem támogatott lehetőséget. Ez a helyzet, amelyben felhasználói nem lehet adatokat beolvasni irányítja. | |Igen |Igen |
| 7 |Feladat-visszavétel |A feladat-visszavétel vész-helyreállítási 24 órán belül nem támogatott. | |Igen |Nem |
| 8 |Eszköz feladatátvétel |A különböző céleszközökre a azonos forráseszközről származó kötettároló több feladatátvétel nem támogatott. Átkapcsolás egyetlen elhalt eszközről több eszközre működésképtelenné teszi az adatok tulajdonjoga elvesztése átadja a feladatokat eszköz első kötettárolók. Egy feladatátvétel után ezek kötettárolók jelenik meg, vagy a klasszikus Azure portálon meg eltérően viselkednek. | |Igen |Nem |
| 9 |Telepítés |Során StorSimple Adapter SharePoint telepítéshez meg kell adnia egy eszköz IP-cím, a telepítés sikeres befejezéséhez. | |Igen |Nem |
| 10 |Hálózati illesztők |DATA 2 és a DATA 3 hálózati adapterek a szoftver lettek cserélve. |Ha konfigurálja a konfigurációkezelővel, forduljon a Microsoft Support. |Igen |Nem |

