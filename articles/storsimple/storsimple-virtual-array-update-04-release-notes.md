---
title: StorSimple Virtual Array Update 0.4 kiadási megjegyzések| Microsoft dokumentumok
description: A cikk a 0.4-es frissítést futtató StorSimple virtuális tömb kritikus nyitott problémáit és megoldásait ismerteti.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60334794"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>StorSimple Virtual Array Update 0.4 kiadási megjegyzések

## <a name="overview"></a>Áttekintés

A következő kiadási megjegyzések azonosítják a kritikus nyitott problémákat és a Microsoft Azure StorSimple virtual array frissítések megoldott problémáit.

A kiadási megjegyzések folyamatosan frissülnek, és a kerülő megoldáshoz igénylő kritikus problémák felderítése után a hozzájuk. A StorSimple virtuális tömb telepítése előtt alaposan tekintse át a kiadási megjegyzésekben található információkat.

A 0.4-es frissítés a **10.0.10289.0**szoftververziónak felel meg.

> [!NOTE]
> A frissítések zavaróak, és újraindítják az eszközt. Ha az I/O folyamatban van, az eszköz állásidőt ad.


## <a name="whats-new-in-the-update-04"></a>A 0.4-es frissítés újdonságai
Update 0.4 elsősorban egy hiba-fix épít párosul néhány fejlesztést. Ebben a verzióban számos hibát, ami biztonsági mentési hibák az előző verzióban már orvosolta. A fő fejlesztések és hibajavítások a következők:

- **Biztonsági mentési teljesítmény javítása** – Ez a kiadás számos kulcsfontosságú fejlesztést hajtott végre a biztonsági mentés teljesítményének javítása érdekében. Ennek eredményeképpen a nagy számú fájlt tartalmazó biztonsági mentések jelentős csökkenést látnak a teljes és növekményes biztonsági mentések befejezéséhez.

- **Továbbfejlesztett visszaállítási teljesítmény** – Ez a kiadás olyan fejlesztéseket tartalmaz, amelyek jelentősen javítják a visszaállítási teljesítményt nagy számú fájl használata esetén. Ha 2- 4 millió fájlt használ, javasoljuk, hogy a fejlesztések megtekintéséhez egy 16 GB RAM-mal rendelkező virtuális tömböt létesítsen. Ha kevesebb, mint 2 millió fájlt használ, a virtuális gép minimális követelménye továbbra is 8 GB RAM.

- **A támogatási csomag fejlesztései** – A fejlesztések közé tartozik a lemez, a processzor, a memória, a hálózat és a felhő statisztikáinak naplózása a támogatási csomagba, ezáltal javítva az eszközproblémák diagnosztizálását/hibakeresését.

- **Helyileg rögzített iSCSI-kötetek 200 GB-ra korlátozva** – Helyileg rögzített kötetek esetén azt javasoljuk, hogy a StorSimple virtuális tömbön 200 GB-os iSCSI-kötetre korlátozza a kötetet. A többszintű kötetek helyi foglalása továbbra is a kiosztott kötet méretének 10 %-a, de 200 GB-os felső határa. 

- **Biztonsági mentéssel kapcsolatos hibajavítások** – A szoftver korábbi verzióiban problémák merültek fel a biztonsági mentésekkel kapcsolatban, amelyek biztonsági mentési hibákat okozhattak. Ezekkel a hibákkal ebben a kiadásban foglalkoztunk.


## <a name="issues-fixed-in-the-update-04"></a>A 0.4-es frissítésben javított problémák

Az alábbi táblázat az ebben a kiadásban javított problémákat tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |Biztonsági mentés teljesítménye|A korábbi kiadásokban a nagy számú fájlt tartalmazó biztonsági mentések végrehajtása hosszú időt vesz igénybe (a napok sorrendjében). Ebben a kiadásban a teljes és a növekményes biztonsági mentések jelentős csökkenést látnak a befejezésig. |
| 2 |Támogatási csomag|A lemez-, processzor-, memória-, hálózati és felhőstatisztikák most már be vannak jelentkezve a támogatási naplókba, így a támogatási csomagok nagyon hatékonyak az eszközproblémák elhárításában.|
| 3 |Backup |A korábbi kiadásokban a hosszú ideig futó biztonsági mentések az eszköz önfeledt ségének összeroppanását eredményezhetik, ami biztonsági mentési hibákat eredményezhet. Ez a hiba a jelen kiadásban azzal foglalkozik, hogy egyszerre legfeljebb 5 biztonsági mentést engedélyez.|
| 4 |iSCSI | A korábbi kiadásokban a rétegzett vagy helyileg rögzített kötetek helyi foglalása a kiosztott kötet méretének 10%-a volt. Ebben a kiadásban az összes (helyileg rögzített vagy rétegzett) iSCSI-kötet helyi foglalása 10 %-ra korlátozódik, legfeljebb 200 GB-os (2 TB-nál nagyobb rétegzett kötetek esetén), ezáltal több helyet szabadítva fel a helyi lemezen. Azt javasoljuk, hogy a helyileg rögzített kötetek ebben a kiadásban legfeljebb 200 GB.We recommend that the locally pind in this release is limited to 200 GB.|


## <a name="known-issues-in-the-update-04"></a>Ismert problémák a 0.4-es frissítésben

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
| **15.** |Fájlkiszolgáló |A szimbolikus hivatkozások nem támogatottak. | |
| **16.** |Fájlkiszolgáló |A Windows encrypting file system (EFS) által védett fájlok, amelyekmásolása vagy a StorSimple Virtual Array fájlkiszolgálón vannak tárolva, nem támogatott konfigurációt eredményeznek.  | |

## <a name="next-step"></a>Következő lépés
[Telepítse a 0.4-es frissítést](storsimple-virtual-array-install-update-04.md) a StorSimple virtuális tömbre.

## <a name="references"></a>Referencia
Régebbi kiadási megjegyzést keres? Ugrás: 

* [StorSimple virtuális tömb frissítése 0.3 Kibocsátási megjegyzések](storsimple-ova-update-03-release-notes.md)
* [StorSimple virtuális tömb frissítése 0.1 és 0.2 Kibocsátási megjegyzések](storsimple-ova-update-01-release-notes.md)
* [StorSimple virtuális tömb általános rendelkezésre állási kibocsátási megjegyzések](storsimple-ova-pp-release-notes.md)

