---
title: StorSimple Virtual Array updates kiadási megjegyzések| Microsoft dokumentumok
description: A cikk a 0.3-as frissítést futtató StorSimple virtuális tömb kritikus nyitott problémáit és megoldásait ismerteti.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: b197651a-3c40-4185-b23d-4c8f22cfa8f4
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 09/15/2016
ms.author: alkohli
ms.openlocfilehash: 635b5f4edf5d403c569b4957540fc105997b3e8e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60629271"
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>StorSimple Virtual Array Update 0.3 kiadási megjegyzések
## <a name="overview"></a>Áttekintés
A következő kiadási megjegyzések azonosítják a kritikus nyitott problémákat és a Microsoft Azure StorSimple virtual array frissítések megoldott problémáit.

A kiadási megjegyzések folyamatosan frissülnek, és a kerülő megoldáshoz igénylő kritikus problémák felderítése után a hozzájuk. A StorSimple virtuális tömb telepítése előtt alaposan tekintse át a kiadási megjegyzésekben található információkat.

A 0.3-as frissítés a **10.0.10288.0**szoftververziónak felel meg.

> [!NOTE]
> A frissítések zavaróak, és újraindítják az eszközt. Ha az I/O folyamatban van, az eszköz állásidőt ad.
> 
> 

## <a name="whats-new-in-the-update-03"></a>A 0.3-as frissítés újdonságai
Update 0.3 elsősorban egy hiba-fix épít. Ebben a verzióban számos hibát, ami biztonsági mentési hibák az előző verzióban már orvosolta.

## <a name="issues-fixed-in-the-update-03"></a>A 0.3-as frissítésben javított problémák
Az alábbi táblázat az ebben a kiadásban javított problémákat tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |Biztonsági másolatok |Probléma merült fel a korábbi kiadásban, ahol a biztonsági mentések nem fejeződtek be egy fájlmegosztáshoz. Ha ez a probléma történt, a biztonsági mentési feladat sikertelen lesz, és kritikus riasztás merült fel a StorSimple Manager szolgáltatás értesíti a felhasználót. Ez a probléma nem érintette a megosztásokon lévő adatokat vagy az adatokhoz való hozzáférést. A kiváltó ok a jelen kiadásban lett azonosítva és javítva. <br></br> A javítás visszamenőleges hatállyal nem vonatkozik azokra a megosztásokra, amelyek már látják ezt a problémát. A problémát tapasztaló felhasználóknak először a 0.3-as frissítést kell alkalmazniuk, majd a probléma megoldásához teljes rendszerbiztonsági mentést kell végrehajtaniuk a Microsoft támogatási szolgálatához. Ahelyett, hogy kapcsolatba lépne a Microsoft támogatási szolgálatával, az ügyfelek az érintett megosztások kifogástalan biztonsági mentéséből is visszaállíthatják az új megosztásokat. |
| 2 |iSCSI |Egy probléma volt látható a korábbi kiadásban, ahol a kötetek eltűnnek, amikor adatokat másol nak a StorSimple virtuális tömb kötetére. A probléma kilett javítva ebben a kiadásban. <br></br> A javítások csak az újonnan létrehozott köteteken lépnek érvénybe. A javítások visszamenőleges hatállyal nem vonatkoznak azokra a kötetekre, amelyek már látják ezt a problémát. Az ügyfelek nek azt tanácsoljuk, hogy az érintett kötetek online az Azure klasszikus portálon keresztül, készítsen biztonsági másolatot ezekről a kötetekről, majd állítsa vissza ezeket a köteteket az új kötetek. |

## <a name="known-issues-in-the-update-03"></a>Ismert problémák a 0.3-as frissítésben
Az alábbi táblázat a StorSimple virtuális tömb ismert problémáinak összefoglalását tartalmazza, és tartalmazza az előző kiadásokból származó kiadások kiadását. 

| Nem. | Szolgáltatás | Probléma | Megoldás/megjegyzések |
| --- | --- | --- | --- |
| **1.** |Frissítések |Az előzetes verzióban létrehozott virtuális eszközök nem frissíthetők támogatott általános rendelkezésre állási verzióra. |Ezeket a virtuális eszközöket vész-helyreállítási (DR) munkafolyamattal kell átvenni az általános rendelkezésre állási kiadás. |
| **2.** |Kiépített adatlemez |Miután kiépített egy adott méretű adatlemezt, és létrehozta a megfelelő StorSimple virtuális eszközt, nem bonthatja ki vagy zsugoríthatja az adatlemezt. A kísérlet az eszköz helyi szintjein lévő összes adat elvesztését eredményezi. | |
| **3.** |Csoportházirend |Ha egy eszköz tartományhoz csatlakozik, a csoportházirend alkalmazása hátrányosan befolyásolhatja az eszköz működését. |Győződjön meg arról, hogy a virtuális tömb a saját szervezeti egységében (OU) található az Active Directory számára, és nincsenek csoportházirend-objektumok alkalmazására. |
| **4.** |Helyi webes felhasználói felület |Ha az Internet Explorer (IE ESC) fokozott biztonsági szolgáltatásai engedélyezve vannak, előfordulhat, hogy egyes helyi webes felhasználói felületi lapok, például a Hibaelhárítás vagy a Karbantartás nem működnek megfelelően. Előfordulhat, hogy az ezeken az oldalakon lévő gombok sem működnek. |Kapcsolja ki az Internet Explorer továbbfejlesztett biztonsági szolgáltatásait. |
| **5.** |Helyi webes felhasználói felület |A Hyper-V virtuális gépeken a webes felhasználói felület hálózati interfészei 10 Gbit/s-os felületként jelennek meg. |Ez a viselkedés tükrözi a Hyper-V. A Hyper-V mindig 10 Gb/s-ot jelenít meg a virtuális hálózati adapterek esetében. |
| **6.** |Rétegzett kötetek vagy megosztások |A StorSimple rétegzett kötetekkel dolgozó alkalmazások bájttartomány-zárolása nem támogatott. Ha a bájttartomány-zárolás engedélyezve van, a StorSimple rétegezés nem működik. |Az ajánlott intézkedések a következők: <br></br>Kapcsolja ki a bájttartomány zárolását az alkalmazáslogikában.<br></br>Válassza ki, hogy az alkalmazás adatait helyileg rögzített kötetek, szemben a rétegzett kötetek.<br></br>*Figyelmeztetés:* Ha a helyileg rögzített kötetek és bájttartomány-zárolás engedélyezve van, a helyileg rögzített kötet még a visszaállítás befejezése előtt is online állapotba hozható. Ilyen esetekben, ha egy visszaállítás folyamatban van, akkor meg kell várnia a visszaállítás befejezését. |
| **7.** |Többszintű részvények |A nagy fájlok kal végzett munka lassú szintkiesést eredményezhet. |Nagyméretű fájlokkal végzett munka esetén azt javasoljuk, hogy a legnagyobb fájl kisebb, mint a megosztási méret 3%-a. |
| **8.** |Megosztások használt kapacitása |Előfordulhat, hogy a megosztási felhasználás akkor jelenik meg, ha nincs adat a megosztáson. Ennek az az oka, hogy a megosztások használt kapacitása metaadatokat tartalmaz. | |
| **9.** |Vészhelyreállítás |A fájlkiszolgáló vészhelyreállítása csak a forráseszköz tartományával azonos tartományban hajtható végre. Ebben a kiadásban nem támogatott a vész-helyreállítás egy másik tartományban lévő céleszközre. |Ez egy későbbi kiadásban valósul meg. |
| **10.** |Azure PowerShell |A StorSimple virtuális eszközök ebben a kiadásban nem kezelhetők az Azure PowerShellen keresztül. |A virtuális eszközök felügyeletét az Azure klasszikus portálján és a helyi webes felhasználói felületen keresztül kell elvégezni. |
| **11.** |Jelszó módosítása |A virtuális tömbeszköz-konzol csak en-US billentyűzet formátumban fogadja a bevitelt. | |
| **12.** |CHAP |A létrehozott CHAP-hitelesítő adatok nem távolíthatók el. Ezenkívül ha módosítja a CHAP hitelesítő adatait, a köteteket offline állapotba kell helyeznie, majd online állapotba kell hoznia a módosítás érvénybe léptetéséhez. |Egy későbbi kiadásban foglalkozik ezzel a problémával. |
| **13.** |iSCSI-kiszolgáló |Az iSCSI-kötetek "Használt tárolója" a StorSimple Manager szolgáltatásban és az iSCSI-állomásban eltérő lehet. |Az iSCSI-állomás fájlrendszer-nézete.<br></br>Az eszköz látja a lefoglalt blokkokat, amikor a kötet a maximális méreten volt. |
| **14.** |Fájlkiszolgáló |Ha egy mappában lévő fájlhoz alternatív adatfolyam (ADS) van társítva, az ADS-ről nem készül biztonsági mentés vagy visszaállítás vészhelyreállítás, klónozás és elemszintű helyreállítás révén. | |

## <a name="next-step"></a>Következő lépés
[Telepítse a 0.3-as frissítést](storsimple-ova-install-update-01.md) a StorSimple virtuális tömbre.

## <a name="references"></a>Referencia
Régebbi kiadási megjegyzést keres? Ugrás: 

* [StorSimple virtuális tömb frissítése 0.1 és 0.2 Kibocsátási megjegyzések](storsimple-ova-update-01-release-notes.md)
* [StorSimple virtuális tömb általános rendelkezésre állási kibocsátási megjegyzések](storsimple-ova-pp-release-notes.md)

