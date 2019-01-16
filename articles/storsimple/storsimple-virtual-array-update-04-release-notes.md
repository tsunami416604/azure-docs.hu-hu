---
title: A StorSimple Virtual Array frissítés 0,4 kibocsátási megjegyzései |} A Microsoft Docs
description: A StorSimple Virtual Array a 0.4 futó kritikus megoldatlan problémák és megoldásaik ismerteti.
services: storsimple
documentationcenter: ''
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 04/05/2017
ms.author: alkohli
ms.openlocfilehash: 06a3469507631d032535bce62b01d964e99dc603
ms.sourcegitcommit: 3ba9bb78e35c3c3c3c8991b64282f5001fd0a67b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/15/2019
ms.locfileid: "54319401"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>A StorSimple Virtual Array frissítés 0,4 kibocsátási megjegyzései

## <a name="overview"></a>Áttekintés

A következő kiadási megjegyzések a kritikus fontosságú megoldatlan problémák és a Microsoft Azure StorSimple Virtual Array frissítések megoldott problémák azonosításához.

A kibocsátási megjegyzésekben folyamatosan frissülnek, és ahogy ismertté kritikus problémák adódnak. A StorSimple Virtual Array üzembe helyezése, előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

A 0.4 felel meg a szoftververzió **10.0.10289.0**.

> [!NOTE]
> Azok zavart okozó frissítések, és indítsa újra az eszközt. I/o van folyamatban, ha az eszköz leállást.


## <a name="whats-new-in-the-update-04"></a>0,4 frissítés újdonságai
A 0.4 elsősorban a hibajavítás build néhány kiegészítésre szolgáltatással párosítva. Ebben a verzióban a korábbi verzióban biztonsági mentési hibák eredményez több hibák elhárítása. A fő fejlesztéseket és hibajavítások – a következők:

- **Biztonsági mentés a teljesítményt érintő továbbfejlesztés** – ebben a kiadásban által végrehajtott biztonsági mentési teljesítményének javítása érdekében több kulcsfontosságú fejlesztések. Ennek eredményeképpen a nagy mennyiségű fájlt érintő biztonsági mentések lásd: a teljes és növekményes biztonsági mentések elvégzéséhez szükséges idő jelentős csökkentését.

- **Bővített helyreállítási teljesítmény** – ebben a kiadásban, amely a visszaállítási teljesítménye jelentősen javíthatja a nagy mennyiségű fájlt használatakor fejlesztéseket tartalmaz. 2 – 4 millió fájl használata esetén javasoljuk, hogy telepítsen egy virtuális tömböt, 16 GB RAM memóriával a fejlesztések megtekintéséhez. Kevesebb mint 2 millió fájl használata esetén a minimális követelmény a virtuális gép továbbra is 8 GB RAM-MAL.

- **Támogatási csomag fejlesztései** – a fejlesztések között szerepel a statisztika a lemez, Processzor, memória, hálózati és a támogatási csomagot, ezáltal javul a problémák diagnosztizálása és hibakeresés folyamatát, felhő-naplózás.

- **Korlát helyileg rögzített 200 GB-os kötetek iSCSI** – a helyileg rögzített kötetekhez, javasoljuk, hogy korlátozza a 200 GB-os iSCSI-kötet, az a StorSimple Virtual Array. A rétegzett kötetek helyi foglalás továbbra is a létesített kötet méretének 10 %-át lesznek, de a 200 GB-os maximumon. 

- **Biztonsági mentésével kapcsolatos hibajavítások** – szoftver, a korábbi verzióiban történtek a biztonsági mentések, amelyek a biztonsági mentési hibák miatt kapcsolatos problémákat. Ebben a kiadásban ezek a hibák elhárítása.


## <a name="issues-fixed-in-the-update-04"></a>A frissítés 0,4 megoldott problémák

Az alábbi táblázat hibáinak javításai ebben a kiadásban összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |Biztonsági mentés teljesítményét|A korábbi kiadásokban a nagy mennyiségű fájlt érintő biztonsági mentések állna Ez egy hosszú ideig (sorrendjében nap). Ebben a kiadásban a teljes és növekményes biztonsági tekintse meg a befejezési idő jelentős csökkentését. |
| 2 |Támogatási csomag|Lemez, Processzor, memória, hálózati és felhőalapú statisztika most már bejelentkezett a támogatási naplók, így nagyon hatékony, a bármely eszköz kapcsolatos hibák elhárítása a támogatási csomagokat.|
| 3 |Backup |Egy hely elemzése az eszközön a biztonsági mentési hibák eredményez a hatékonyabb játékelemzésért biztonsági mentések hosszú ideig futó okozhat a korábbi kiadásokban. Ezt a hibát a azáltal, hogy a várólistába helyezni egyszerre legfeljebb 5 biztonsági mentések címzettjei ebben a kiadásban.|
| 4 |iSCSI | A korábbi kiadásokban a helyi foglalás rétegzett vagy helyileg rögzített kötetek esetén a létesített kötet méretének 10 % volt. Ebben a kiadásban az összes iSCSI-kötet (helyileg rögzített vagy rétegzett) a helyi foglalás korlátozva, legfeljebb 200 GB-ig (a rétegzett kötetek 2 TB-nál nagyobb), mely legfeljebb 10 %-os ezáltal felszabadítás fel több lemezterületet a helyi lemezen. Azt javasoljuk, hogy ebben a kiadásban a helyileg rögzített kötetekhez legfeljebb 200 GB-os lehet.|


## <a name="known-issues-in-the-update-04"></a>A frissítés 0,4 ismert problémái

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
| **15.** |Fájlkiszolgáló |Szimbolikus hivatkozások nem támogatottak. | |
| **16.** |Fájlkiszolgáló |Windows titkosított fájlrendszer (EFS által) során átmásolt védett, vagy a StorSimple Virtual Array fájl kiszolgáló eredményt konfigurációja nem támogatott tárolt fájlokat.  | |

## <a name="next-step"></a>Következő lépés
[Telepítse a 0.4](storsimple-virtual-array-install-update-04.md) az a StorSimple Virtual Array.

## <a name="references"></a>Referencia
Egy régebbi kiadási Megjegyzés keres? Ugrás: 

* [A StorSimple Virtual Array frissítés 0,3 kibocsátási megjegyzései](storsimple-ova-update-03-release-notes.md)
* [A StorSimple Virtual Array frissítés 0.1, 0.2-es és kibocsátási megjegyzések](storsimple-ova-update-01-release-notes.md)
* [A StorSimple Virtual Array általános rendelkezésre állási kibocsátási megjegyzései](storsimple-ova-pp-release-notes.md)

