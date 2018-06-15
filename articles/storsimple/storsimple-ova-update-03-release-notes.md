---
title: A StorSimple virtuális tömb frissítések kibocsátási megjegyzései |} Microsoft Docs
description: A StorSimple virtuális tömb 0,3 Update futtatása kritikus megnyitott problémák és megoldásuk ismertetése
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
ms.openlocfilehash: fe9d4f6b232e9abcf1fe9fc5657044b6c72fedb8
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23875514"
---
# <a name="storsimple-virtual-array-update-03-release-notes"></a>A StorSimple virtuális tömb frissítés 0,3 kibocsátási megjegyzései
## <a name="overview"></a>Áttekintés
Az alábbi kibocsátási megjegyzések a kritikus megnyitott problémák és a Microsoft Azure StorSimple virtuális tömb frissítéseket megoldott problémák azonosításához.

A kibocsátási megjegyzések folyamatosan frissülnek, és megoldást igénylő kritikus fontosságú problémáit ismertté hozzáadásuk után. A StorSimple virtuális tömb telepítése előtt gondosan tekintse át a kibocsátási megjegyzésekben található információkat.

Frissítés 0,3 megfelel-e a szoftververzió **10.0.10288.0**.

> [!NOTE]
> Frissítések zavaró, és indítsa újra az eszközt. I/o van folyamatban, ha az eszköz azt eredményezi azok, háromszorosa a leállás.
> 
> 

## <a name="whats-new-in-the-update-03"></a>A frissítés 0,3 újdonságai
Frissítés 0,3 elsősorban hibajavítás build. Ebben a verzióban több hibák, ami azt eredményezi, a korábbi verziók egyikében biztonsági mentési hibák rendelkezik foglalkoztak.

## <a name="issues-fixed-in-the-update-03"></a>A frissítés 0,3 megoldott problémák
A következő táblázat az ebben a kiadásban javított összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |Biztonsági másolatok |Probléma fordult elő a korábbi verzióban, ahol a biztonsági mentés sikertelen lesz a fájlmegosztás befejezéséhez. Ha a probléma lépett fel, a biztonsági mentési feladat sikertelen lesz, és a kritikus riasztás kiadását okozó értesítsék a felhasználót a StorSimple Manager szolgáltatásban. A probléma nem érintett az adatokat a megosztásokat vagy a férhetnek hozzá az adatokhoz. Az alapvető ok lett azonosította, és ebben a kiadásban megoldotta. <br></br> A javítás nem vonatkozik visszamenőleges által már azért jelent meg a probléma. Az ügyfelek, akik azért jelent meg a probléma a kell először 0,3 frissítést, majd kérje a Microsoft Support teljes rendszer biztonsági mentést készíteni a probléma megoldásához. Lépjen kapcsolatba az Microsoft Support, helyett az ügyfelek is visszaállíthatja új megosztás egy megfelelő biztonsági másolatból, az érintett megosztások. |
| 2 |iSCSI |Probléma fordult elő a korábbi kiadás, ahol a kötetek eltűnik a StorSimple virtuális tömb kötetein adatok másolásakor. A probléma javítását a ebben a kiadásban. <br></br> A javítások érvénybe kizárólag a az újonnan létrehozott kötetek. A javítások nem vonatkoznak a visszamenőleges kötetek, amelyek már azért jelent meg a probléma. Az ügyfelek végigvitelével az érintett kötetek online a klasszikus Azure portálon keresztül, készítsen biztonsági mentést készíteni ezekhez a kötetekhez, és új kötetek majd visszaállítja ezeket a köteteket. |

## <a name="known-issues-in-the-update-03"></a>A frissítés 0,3 ismert problémák
A következő táblázat összefoglalja az ismert problémákról a StorSimple virtuális tömb, és a problémák kiadás jelezve a korábbi verzióiról tartalmazza. 

| Nem. | Szolgáltatás | Probléma | Megkerülő megoldás/megjegyzések |
| --- | --- | --- | --- |
| **1.** |Frissítések |A virtuális eszközök előzetes létrehozott támogatott általánosan rendelkezésre álló verziója nem frissíthető. |A virtuális eszközök használata a vész-helyreállítási munkafolyamat általános rendelkezésre állási kiadásának kell feladatátvételt. |
| **2.** |Kiépített adatlemez |Ha a megadott méretű adatlemezt ellátta, és a megfelelő StorSimple virtuális eszköz létrehozása kell nem bontsa ki, vagy a adatlemez zsugorítani. Kísérlet történt egy elvesztését eredményezi, a helyi rétegeken az eszköz összes adatának tegye. | |
| **3.** |Csoportházirend |Ha egy eszköz a tartományhoz, a Csoportházirend alkalmazása kedvezőtlen hatással lehet az eszköz műveletet. |Győződjön meg arról, hogy a virtuális tömb saját szervezeti egységben (OU) az Active Directory és nem a csoportházirend-objektumok (GPO) vonatkoznak rá. |
| **4.** |Helyi webes felhasználói felület |A fokozott biztonság funkció engedélyezése az Internet Explorerben (IE ESC), előfordulhat, hogy egyes helyi felhasználói felület weboldalak például hibaelhárítás vagy karbantartási nem működik megfelelően. Gombok ezeken a lapokon nem is működnek. |Kapcsolja ki az Internet Explorer fokozott biztonsági funkciók. |
| **5.** |Helyi webes felhasználói felület |A Hyper-V virtuális gépen a hálózati adapterek, a webes felhasználói felületén jelennek meg, 10 GB/s felületeihez. |Ez a viselkedés a Hyper-V tükre. Hyper-V mindig látható a virtuális hálózati adapterek 10 GB/s. |
| **6.** |Rétegzett kötetek vagy megosztások |A rétegzett kötetek nem támogatott StorSimple együtt használható alkalmazások zárolást bájttartomány. Ha bájt tartomány zárolás engedélyezve van, nem StorSimple rétegezéséhez működik. |Ajánlott mértékeket tartalmazza: <br></br>Kapcsolja ki a bájttartomány zárolás a az alkalmazás logikáját.<br></br>Válassza ki az alkalmazás adatokat a rétegzett kötetek figyelésekor helyileg rögzített kötetekhez kell.<br></br>*Ismeret*: Ha engedélyezve van az bájt tartomány zárolás használatával helyileg rögzített kötetek, a helyileg rögzített kötet lehet online még a visszaállítás befejezése előtt. Ezekben az esetekben ha a helyreállítás van folyamatban, majd meg kell várnia a visszaállítás befejeződését. |
| **7.** |Rétegzett megosztások |Nagy fájlok használata a kimenő lassú réteg eredményezheti. |Amikor olyan nagy fájlok, azt javasoljuk, hogy a legnagyobb fájl a fájlmegosztás méretének % 3-nál kisebb. |
| **8.** |Használható kapacitás megosztások |Előfordulhat, hogy látja fogyasztás megosztani, ha nincs adat a megosztáson. Ennek oka az, a használt kapacitás megosztások metaadatokat tartalmaz. | |
| **9.** |Vészhelyreállítás |Csak a vész-helyreállítási ugyanahhoz a tartományhoz, amely a forráseszközt fájlkiszolgáló végezheti el. A céleszközön egy másik tartományban való katasztrófa utáni helyreállítás nem támogatott ebben a kiadásban. |Ez egy későbbi kiadásban van megvalósítva. |
| **10.** |Azure PowerShell |A StorSimple virtuális eszköz az Azure PowerShell ebben a kiadásban nem kezelhetők. |A virtuális eszközök minden felügyeleti a klasszikus Azure portálra, és a helyi webes felhasználói felületen keresztül kell végezni. |
| **11.** |A jelszó módosítása |A virtuális tömb eszköz konzol csak en-US billentyűzet formátumú bemenetet fogad el. | |
| **12.** |CHAP |CHAP hitelesítő adatok létrehozása után nem lesznek eltávolítva. Emellett ha CHAP hitelesítő adatok módosításához módosítania a kötetek offline állapotba, és hogy azok online, a változtatás érvénybe léptetéséhez. |Ezzel a problémával egy későbbi kiadásban. |
| **13.** |az iSCSI-kiszolgáló |A "használt tárolási" az iSCSI-kötet jelenik meg a StorSimple Manager szolgáltatás és az iSCSI-gazdagép eltérőek lehetnek. |Az iSCSI-gazdagép a fájlrendszer nézet tartozik.<br></br>Az eszköz látja, ha a kötet maximális méret lefoglalt blokkok. |
| **14.** |Fájlkiszolgáló |Ha egy fájlt egy mappában van egy alternatív Data Stream (ADS) társítva, a HIRDETÉSEK nem biztonsági mentése vagy visszaállítása vész-helyreállítási, a Klónozás és az elem szintű helyreállítás. | |

## <a name="next-step"></a>Következő lépés
[A frissítés 0,3](storsimple-ova-install-update-01.md) a StorSimple virtuális tömbben.

## <a name="references"></a>Referencia
Egy régebbi kiadási Megjegyzés keres? Ugrás: 

* [A StorSimple virtuális tömb frissítés 0,1 és 0,2 kibocsátási megjegyzései](storsimple-ova-update-01-release-notes.md)
* [A StorSimple virtuális tömb általános rendelkezésre állási kibocsátási megjegyzései](storsimple-ova-pp-release-notes.md)

