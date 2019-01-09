---
title: A StorSimple Virtual Array frissítések kibocsátási megjegyzések |} A Microsoft Docs
description: A StorSimple Virtual Array a 0.3 futó kritikus megoldatlan problémák és megoldásaik ismerteti.
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
ms.sourcegitcommit: 818d3e89821d101406c3fe68e0e6efa8907072e7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/09/2019
ms.locfileid: "54117673"
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>A StorSimple Virtual Array frissítés 0,3 kibocsátási megjegyzései
## <a name="overview"></a>Áttekintés
A következő kiadási megjegyzések a kritikus fontosságú megoldatlan problémák és a Microsoft Azure StorSimple Virtual Array frissítések megoldott problémák azonosításához.

A kibocsátási megjegyzésekben folyamatosan frissülnek, és ahogy ismertté kritikus problémák adódnak. A StorSimple Virtual Array üzembe helyezése, előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

A 0.3 felel meg a szoftververzió **10.0.10288.0**.

> [!NOTE]
> Azok zavart okozó frissítések, és indítsa újra az eszközt. I/o van folyamatban, ha az eszköz leállást.
> 
> 

## <a name="whats-new-in-the-update-03"></a>A frissítés 0,3 újdonságai
A 0.3 elsősorban hibajavítás build. Ebben a verzióban a korábbi verzióban biztonsági mentési hibák eredményez több hibák elhárítása.

## <a name="issues-fixed-in-the-update-03"></a>A frissítés 0,3 megoldott problémák
Az alábbi táblázat hibáinak javításai ebben a kiadásban összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |Biztonsági másolatok |A problémát jelent a korábbi kiadásokban, ahol a biztonsági mentés sikertelen lenne egy fájlmegosztás esetében. Ha a probléma lépett fel, a biztonsági mentési feladat sikertelen lesz, és a egy kritikus riasztás megjelent a StorSimple Manager szolgáltatás értesíti a felhasználót. A probléma nem érintette a megosztások vagy az adatokhoz való hozzáférés található adatokat. Az alapvető ok lett azonosítva, és javítva a kiadásban. <br></br> A javítás nem érvényes visszamenőlegesen a megosztásokat, amely már jelent meg a probléma. Ügyfelek, akik azért jelent meg a probléma kell először a alkalmazni a 0.3, majd Microsoft Support teljes rendszer biztonsági mentését, hogy a probléma megoldásához. Helyett kapcsolatba lépne a Microsoft Support, ügyfelek is helyreállíthatja az új megosztásra az érintett megosztások megfelelő biztonsági. |
| 2 |iSCSI |A korábbi kiadásokban a kötetek eltűnik, ha másol adatokat egy olyan kötetre a StorSimple Virtual Array a problémát jelent. A probléma lett javítva a kiadásban. <br></br> A javítások érvénybe csak az újonnan létrehozott kötetek. A javításokat nem visszamenőlegesen alkalmazni köteteket, amelyek már jelennek meg a probléma. Ügyfelek végigvitelével az érintett kötetek online a klasszikus Azure portálon keresztül, készítsen biztonsági mentést ezekhez a kötetekhez, és állítsa vissza az új kötetek ezeket a köteteket. |

## <a name="known-issues-in-the-update-03"></a>A frissítés 0,3 ismert problémái
Az alábbi táblázat a StorSimple Virtual Array az ismert problémák összegzését tartalmazza, és a kiadási jelezve a korábbi kiadásokban a problémák tartalmazza. 

| Nem. | Szolgáltatás | Probléma | Megkerülő megoldás és megjegyzések |
| --- | --- | --- | --- |
| **1.** |Frissítések |Az előzetes kiadásban létrehozott virtuális eszközre nem lehet frissíteni egy támogatott általánosan elérhető verzióra. |Ezek a virtuális eszközök feladatátvételt kell végrehajtani a végleges kiadás vész-helyreállítási munkafolyamat használatával. |
| **2.** |Kiépített adatlemez |Miután ellátta bizonyos megadott méretű adatlemez és a megfelelő virtuális StorSimple-eszközt hozott létre, meg kell nem tartalomtól az adatlemezt. Elvesztését eredményezi, a helyi rétegeken az eszköz összes adatának tegye kísérletet. | |
| **3.** |Csoportházirend |Ha egy eszköz a tartományhoz, a Csoportházirend alkalmazása kedvezőtlen hatással lehet az eszköz műveletet. |Győződjön meg arról, hogy a virtuális tömb a saját szervezeti egység (OU) az Active Directory, és nem a csoportházirend-objektumok (GPO) beállítva rajta. |
| **4.** |Helyi webes felhasználói felületen |Ha az Internet Explorer (IE ESC) engedélyezve vannak a fejlett biztonsági funkcióknak, előfordulhat, hogy néhány helyi webes felhasználói Kezelőfelületi lapok például hibaelhárítás vagy karbantartási nem működik megfelelően. Gombok ezeken a lapokon is előfordulhat, hogy nem működik. |Kapcsolja ki az Internet Explorer fokozott biztonsági funkciók. |
| **5.** |Helyi webes felhasználói felületen |Hyper-V virtuális gépen a hálózati adapterek, a webes felhasználói felületén jelennek meg, 10 GB/s felületeihez. |Ez a viselkedés a Hyper-V egy másolatát. A Hyper-V a virtuális hálózati adapterek 10 GB/s mindig látható. |
| **6.** |Rétegzett kötetek vagy megosztások |A rétegzett kötetek nem támogatott. a StorSimple együtt használható alkalmazások zárolása bájttartományt. Ha bájt tartomány zárolás engedélyezve van, nem StorSimple rétegezést működik. |Ajánlott a mértékeket tartalmazza: <br></br>Kapcsolja ki az alkalmazáslogika a zárolás bájttartományt.<br></br>Válassza ki az alkalmazáshoz tartozó adatokat a rétegzett kötetek ellentétben a gyors helyi kötetek kell.<br></br>*Ismeret*: Ha engedélyezve van az bájt tartomány zárolás használatával helyileg rögzített kötetekről, a helyileg rögzített kötet lehet online még a visszaállítás befejezése előtt. Ezekben az esetekben ha a visszaállítás van folyamatban, majd meg kell várnia a visszaállítás befejeződését. |
| **7.** |Rétegzett megosztás |Nagy fájlok használata lassú réteg felskálázása eredményezhet. |Ha nagy méretű fájlok dolgozik, azt javasoljuk, hogy a megosztás méretének % 3-nál kisebb-e a legnagyobb fájlt. |
| **8.** |Használt kapacitás megosztások |Látni fogyasztás megoszthatja, amikor nem szerepel megjeleníthető adat a megosztáson. Ennek oka az, a felhasznált kapacitás megosztások metaadatokat tartalmaz. | |
| **9.** |Vészhelyreállítás |Csak az ugyanahhoz a tartományhoz, mint a forráseszközt a fájlkiszolgáló vész-helyreállítási hajtható végre. Ebben a kiadásban nem támogatott a vész-helyreállítási cél eszközhöz egy másik tartományban található. |Ez egy későbbi kiadástól kezdve van megvalósítva. |
| **10.** |Azure PowerShell |A StorSimple virtuális eszközre nem kezelhető ebben a kiadásban az Azure Powershellen keresztül. |A virtuális eszközök a management a klasszikus Azure portálra, és a helyi webes felületén keresztül kell elvégezni. |
| **11.** |Jelszó módosítása |A virtuális tömb eszköz konzol csak en-US billentyűzet formátumú bemeneti fogad el. | |
| **12.** |CHAP |A CHAP hitelesítő adatok ezután nem lehet eltávolítani. Emellett ha módosítja a CHAP hitelesítő adatokat, meg kell le a köteteket, és hogy azok online, a módosítás érvénybe léptetéséhez. |Ezzel a problémával egy későbbi kiadástól kezdve. |
| **13.** |iSCSI server |A "használt tárolási" az iSCSI-kötet jelenik meg a StorSimple Manager szolgáltatás és az iSCSI-gazdagép eltérő lehet. |Az iSCSI-gazdagép rendelkezik a fájlrendszer nézetet.<br></br>Az eszköz látja, ha a kötet volt a maximális méret lefoglalt blokkok. |
| **14.** |Fájlkiszolgáló |Ha egy fájl egy mappában van egy másik Data Stream (ADS) társítva, a HIRDETÉSEK nem biztonsági mentése vagy visszaállítása vész-helyreállítási, klónozás és az elemszintű helyreállítás. | |

## <a name="next-step"></a>Következő lépés
[Telepítse a 0.3](storsimple-ova-install-update-01.md) az a StorSimple Virtual Array.

## <a name="references"></a>Referencia
Egy régebbi kiadási Megjegyzés keres? Ugrás: 

* [A StorSimple Virtual Array frissítés 0.1, 0.2-es és kibocsátási megjegyzések](storsimple-ova-update-01-release-notes.md)
* [A StorSimple Virtual Array általános rendelkezésre állási kibocsátási megjegyzései](storsimple-ova-pp-release-notes.md)

