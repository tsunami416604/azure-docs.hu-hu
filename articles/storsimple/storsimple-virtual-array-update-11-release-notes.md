---
title: StorSimple Virtual Array Update 1.1 kiadási megjegyzések| Microsoft dokumentumok
description: Az 1.1-es frissítést futtató StorSimple virtuális tömb kritikus nyitott problémáit és megoldásait ismerteti.
services: storsimple
documentationcenter: ''
author: alkohli
manager: twooley
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/22/2018
ms.author: alkohli
ms.openlocfilehash: 55103d6307614f8796c41c35d6345e1fc3aca261
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60789637"
---
# <a name="storsimple-virtual-array-update-11-release-notes"></a>StorSimple Virtual Array Update 1.1 kiadási megjegyzések

## <a name="overview"></a>Áttekintés

A következő kiadási megjegyzések azonosítják a kritikus nyitott problémákat és a Microsoft Azure StorSimple virtual array frissítések megoldott problémáit.

A kiadási megjegyzések folyamatosan frissülnek, és a kerülő megoldáshoz igénylő kritikus problémák felderítése után a hozzájuk. A StorSimple virtuális tömb telepítése előtt alaposan tekintse át a kiadási megjegyzésekben található információkat.

Az 1.1-es frissítés a **10.0.10307.0**szoftververziónak felel meg.

> [!IMPORTANT]
> - A frissítések zavaróak, és újraindítják az eszközt. Ha az I/O folyamatban van, az eszköz állásidőt ad. A frissítés telepítésével kapcsolatos részletes útmutatásért látogasson el az [1.1-es frissítés telepítéséhez.](storsimple-virtual-array-install-update-11.md)
>
> - Az 1.1-es frissítés csak akkor érhető el az Azure Portalon keresztül, ha az eszköz1.0-s frissítése fut.

## <a name="whats-new-in-update-11"></a>Az 1.1-es frissítés újdonságai

A frissítés a következő fejlesztéseket és hibajavításokat tartalmazza:

 - **Biztonsági mentési hibák** - javultak a felhőhibákkal és a magas CPU-felhasználással kapcsolatos növekvő rugalmasság növelésével.
 - **Naplózás** – A részletes módnaplózás módosításai akkor történtek, amikor az eszköz támogatási munkamenetben van.


## <a name="issues-fixed-in-update-11"></a>Az 1.1-es frissítésben javított problémák

Az alábbi táblázat az ebben a kiadásban javított problémákat tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |Biztonsági másolatok| Ez a kiadás olyan módosításokat tartalmaz, amelyek javították a biztonsági mentési hibákat a felhőhibák kal és a magas CPU-használattal való rugalmasság növelésével.|
| 2 |Naplózás| Ez a kiadás a naplózás módosításait tartalmazza, miközben az eszköz részletes módban támogatási munkamenetben van.|


## <a name="known-issues-in-update-11"></a>Ismert problémák az 1.1-es frissítésben

Az alábbi táblázat a StorSimple virtuális tömb ismert problémáinak összefoglalását tartalmazza, és tartalmazza az előző kiadásokból származó kiadások kiadását.

| Nem. | Szolgáltatás | Probléma | Megoldás/megjegyzések |
| --- | --- | --- | --- |
| **1.** |Frissítések |Az előzetes verzióban létrehozott virtuális tömbök nem frissíthetők támogatott általános elérhetőségi verzióra. |Ezeket a virtuális tömböket vész-helyreállítási (DR) munkafolyamattal kell átvenni az általános rendelkezésre állási kiadás. |
| **2.** |Kiépített adatlemez |Miután kiépített egy adott méretű adatlemezt, és létrehozta a megfelelő StorSimple virtuális tömböt, nem bonthatja ki vagy zsugoríthatja az adatlemezt. A kísérlet az eszköz helyi szintjein lévő összes adat elvesztését eredményezi. | |
| **3.** |Csoportházirend |Ha egy eszköz tartományhoz csatlakozik, a csoportházirend alkalmazása hátrányosan befolyásolhatja az eszköz működését. |Győződjön meg arról, hogy a virtuális tömb a saját szervezeti egységében (OU) található az Active Directory számára, és nincsenek csoportházirend-objektumok alkalmazására. |
| **4.** |Helyi webes felhasználói felület |Ha az Internet Explorer (IE ESC) fokozott biztonsági szolgáltatásai engedélyezve vannak, előfordulhat, hogy egyes helyi webes felhasználói felületi lapok, például a Hibaelhárítás vagy a Karbantartás nem működnek megfelelően. Előfordulhat, hogy az ezeken az oldalakon lévő gombok sem működnek. |Kapcsolja ki az Internet Explorer továbbfejlesztett biztonsági szolgáltatásait. |
| **5.** |Helyi webes felhasználói felület |A Hyper-V virtuális gépeken a webes felhasználói felület hálózati interfészei 10 Gbit/s-os felületként jelennek meg. |Ez a viselkedés tükrözi a Hyper-V. A Hyper-V mindig 10 Gb/s-ot jelenít meg a virtuális hálózati adapterek esetében. |
| **6.** |Rétegzett kötetek vagy megosztások |A StorSimple rétegzett kötetekkel dolgozó alkalmazások bájttartomány-zárolása nem támogatott. Ha a bájttartomány-zárolás engedélyezve van, a StorSimple rétegezés nem működik. |Az ajánlott intézkedések a következők: <br></br>Kapcsolja ki a bájttartomány zárolását az alkalmazáslogikában.<br></br>Válassza ki, hogy az alkalmazás adatait helyileg rögzített kötetek, szemben a rétegzett kötetek.<br></br>*Figyelmeztetés:* Ha a helyileg rögzített kötetek és bájttartomány-zárolás engedélyezve van, a helyileg rögzített kötet még a visszaállítás befejezése előtt is online állapotba hozható. Ilyen esetekben, ha egy visszaállítás folyamatban van, akkor meg kell várnia a visszaállítás befejezését. |
| **7.** |Többszintű részvények |A nagy fájlok kal végzett munka lassú szintkiesést eredményezhet. |Nagyméretű fájlokkal végzett munka esetén azt javasoljuk, hogy a legnagyobb fájl kisebb, mint a megosztási méret 3%-a. |
| **8.** |Megosztások használt kapacitása |Előfordulhat, hogy a megosztási felhasználás akkor jelenik meg, ha nincs adat a megosztáson. Ennek a felhasználásnak az az oka, hogy a megosztások használt kapacitása metaadatokat tartalmaz. | |
| **9.** |Vészhelyreállítás |A fájlkiszolgáló vészhelyreállítása csak a forráseszköz tartományával azonos tartományban hajtható végre. Ebben a kiadásban nem támogatott a vész-helyreállítás egy másik tartományban lévő céleszközre. |Ez egy későbbi kiadásban valósul meg. További információkért látogasson el a [StorSimple virtuális tömb feladatátvételi és vész-helyreállítási](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |A StorSimple virtuális tömbök ebben a kiadásban nem kezelhetők az Azure PowerShellen keresztül. |A virtuális eszközök felügyeletét az Azure Portalon és a helyi webes felhasználói felületen keresztül kell elvégezni. |
| **11.** |Jelszó módosítása |A virtuális tömbeszköz-konzol csak en-us billentyűzet formátumban fogadja a bevitelt. | |
| **12.** |CHAP |A létrehozott CHAP-hitelesítő adatok nem távolíthatók el. Ezenkívül ha módosítja a CHAP hitelesítő adatait, a köteteket offline állapotba kell helyeznie, majd online állapotba kell hoznia a módosítás érvénybe léptetéséhez. |Egy későbbi kiadásban foglalkozik ezzel a problémával. |
| **13.** |iSCSI-kiszolgáló |Az iSCSI-kötetek "Használt tárolója" a StorSimple Eszközkezelő szolgáltatásban és az iSCSI-állomáson eltérő lehet. |Az iSCSI-állomás fájlrendszer-nézete.<br></br>Az eszköz látja a lefoglalt blokkokat, amikor a kötet a maximális méreten volt. |
| **14.** |Fájlkiszolgáló |Ha egy mappában lévő fájlhoz alternatív adatfolyam (ADS) van társítva, az ADS-ről nem készül biztonsági mentés vagy visszaállítás vészhelyreállítás, klónozás és elemszintű helyreállítás révén. | |
| **15.** |Fájlkiszolgáló |A szimbolikus hivatkozások nem támogatottak. | |
| **16.** |Fájlkiszolgáló |A Windows encrypting file system (EFS) által védett fájlok, amelyekmásolása vagy a StorSimple Virtual Array fájlkiszolgálón vannak tárolva, nem támogatott konfigurációt eredményeznek.  | |
| **17.** |Frissítések |Ha a 2359302-es (hex 0x240006) hibakód látható, amikor gyorsjavítást próbál telepíteni a helyi felhasználói felületen keresztül, akkor ez azt jelenti, hogy a gyorsjavítás már telepítve van az eszközön.   | |
| **18.** |Frissítések |Ha a helyi webes felhasználói felületsegítségével telepíti az 1. Ha a 0.6-os frissítésnél alacsonyabb verziót futtat, először telepítenie kell a 0.6-os frissítést, majd telepítenie kell az 1. Ha közvetlenül telepíti az 1.0-s frissítést egy frissítés előtti 0.6-os verzióból, akkor hiányozni fog néhány frissítés, és a figyelési diagramok nem fognak működni.   | |


## <a name="next-steps"></a>További lépések
[Telepítse az 1.1-es frissítést](storsimple-virtual-array-install-update-11.md) a StorSimple virtuális tömbre.

## <a name="references"></a>Referencia
Régebbi kiadási megjegyzést keres? Ugrás:
* [StorSimple virtual array update 1.0 kibocsátási megjegyzések](storsimple-virtual-array-update-1-release-notes.md)
* [StorSimple virtuális tömb frissítése 0,6 Kibocsátási megjegyzések](storsimple-virtual-array-update-06-release-notes.md)
* [StorSimple virtuális tömb frissítése 0,5 kibocsátási megjegyzések](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple virtuális tömb frissítése 0.4 Kibocsátási megjegyzések](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple virtuális tömb frissítése 0.3 Kibocsátási megjegyzések](storsimple-ova-update-03-release-notes.md)
* [StorSimple virtuális tömb frissítése 0.1 és 0.2 Kibocsátási megjegyzések](storsimple-ova-update-01-release-notes.md)
* [StorSimple virtuális tömb általános rendelkezésre állási kibocsátási megjegyzések](storsimple-ova-pp-release-notes.md)
