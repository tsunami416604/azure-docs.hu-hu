---
title: StorSimple Virtual Array Update 0.6 kiadási megjegyzések| Microsoft dokumentumok
description: A 0.6-os frissítést futtató StorSimple virtuális tömb kritikus nyitott problémáit és megoldásait ismerteti.
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
ms.date: 05/24/2017
ms.author: alkohli
ms.openlocfilehash: e984531feced2d61332e4c399848c12cd245a34a
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60870706"
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>StorSimple Virtual Array Update 0.6 kiadási megjegyzések

## <a name="overview"></a>Áttekintés

A következő kiadási megjegyzések azonosítják a kritikus nyitott problémákat és a Microsoft Azure StorSimple virtual array frissítések megoldott problémáit.

A kiadási megjegyzések folyamatosan frissülnek, és a kerülő megoldáshoz igénylő kritikus problémák felderítése után a hozzájuk. A StorSimple virtuális tömb telepítése előtt alaposan tekintse át a kiadási megjegyzésekben található információkat.

A 0.6-os frissítés a **10.0.10293.0**szoftververziónak felel meg.

> [!IMPORTANT]
> - A frissítések zavaróak, és újraindítják az eszközt. Ha az I/O folyamatban van, az eszköz állásidőt ad. A frissítés telepítésével kapcsolatos részletes útmutatásért látogasson el a [0.6-os frissítés telepítéséhez.](storsimple-virtual-array-install-update-06.md)
>
> - Javasoljuk, hogy azonnal telepítse a 0.6-os frissítést, mivel az kritikus biztonsági javításokat tartalmaz.


## <a name="whats-new-in-the-update-06"></a>A 0.6-os frissítés újdonságai
A 0.6-os frissítés kritikus frissítés, amelyet azonnal telepíteni kell. A frissítés a következő javításokat tartalmazza: 

- **Windows biztonsági javítások** – Ez a kiadás **a Windows kritikus biztonsági javításait javítja.** A biztonsági problémákkal és a kapcsolódó javításokkal kapcsolatos további információkért tekintse át az alábbi biztonsági frissítéseket:
    - [2016. december – Csak biztonsági minőségi frissítés a Windows 8.1 és a Windows Server 2012 R2 rendszerhez](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [2017. március – Csak biztonsági minőségi frissítés a Windows 8.1 és a Windows Server 2012 R2 rendszerhez](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [2017. május 9., KB4019213 (csak biztonsági frissítés)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- **Visszaállítási javítás** – A korábbi kiadásokban hiba történt, amely megakadályozta a visszaállítás befejezését. Ez a hiba ki lett javítva ebben a kiadásban.


## <a name="issues-fixed-in-the-update-06"></a>A 0.6-os frissítésben javított problémák

Az alábbi táblázat az ebben a kiadásban javított problémákat tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |Biztonság| Ez a kiadás a Windows biztonsági legfontosabb frissítéseit tartalmazza. Javasoljuk, hogy azonnal telepítse a frissítést.|
| 2 |Visszaállítás| A visszaállítás során volt egy versenyhelyzet, amely megakadályozta a visszaállítási feladat befejezését. A hibajavítás ezt a versenyállapotot orvosolja.|


## <a name="known-issues-in-the-update-06"></a>Ismert problémák a 0.6-os frissítésben

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
| **8.** |Megosztások használt kapacitása |Előfordulhat, hogy a megosztási felhasználás akkor jelenik meg, ha nincs adat a megosztáson. Ennek a felhasználásnak az az oka, hogy a megosztások használt kapacitása metaadatokat tartalmaz. | |
| **9.** |Vészhelyreállítás |A fájlkiszolgáló vészhelyreállítása csak a forráseszköz tartományával azonos tartományban hajtható végre. Ebben a kiadásban nem támogatott a vész-helyreállítás egy másik tartományban lévő céleszközre. |Ez egy későbbi kiadásban valósul meg. További információkért látogasson el a [StorSimple virtuális tömb feladatátvételi és vész-helyreállítási](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |A StorSimple virtuális eszközök ebben a kiadásban nem kezelhetők az Azure PowerShellen keresztül. |A virtuális eszközök felügyeletét az Azure Portalon és a helyi webes felhasználói felületen keresztül kell elvégezni. |
| **11.** |Jelszó módosítása |A virtuális tömbeszköz-konzol csak en-us billentyűzet formátumban fogadja a bevitelt. | |
| **12.** |CHAP |A létrehozott CHAP-hitelesítő adatok nem távolíthatók el. Ezenkívül ha módosítja a CHAP hitelesítő adatait, a köteteket offline állapotba kell helyeznie, majd online állapotba kell hoznia a módosítás érvénybe léptetéséhez. |Egy későbbi kiadásban foglalkozik ezzel a problémával. |
| **13.** |iSCSI-kiszolgáló |Az iSCSI-kötetek "Használt tárolója" a StorSimple Eszközkezelő szolgáltatásban és az iSCSI-állomáson eltérő lehet. |Az iSCSI-állomás fájlrendszer-nézete.<br></br>Az eszköz látja a lefoglalt blokkokat, amikor a kötet a maximális méreten volt. |
| **14.** |Fájlkiszolgáló |Ha egy mappában lévő fájlhoz alternatív adatfolyam (ADS) van társítva, az ADS-ről nem készül biztonsági mentés vagy visszaállítás vészhelyreállítás, klónozás és elemszintű helyreállítás révén. | |
| **15.** |Fájlkiszolgáló |A szimbolikus hivatkozások nem támogatottak. | |
| **16.** |Fájlkiszolgáló |A Windows encrypting file system (EFS) által védett fájlok, amelyekmásolása vagy a StorSimple Virtual Array fájlkiszolgálón vannak tárolva, nem támogatott konfigurációt eredményeznek.  | |
| **17.** |Frissítések |Ha a 2359302-es (hex 0x240006) hibakód látható, amikor gyorsjavítást próbál telepíteni a helyi felhasználói felületen keresztül, akkor ez azt jelenti, hogy a gyorsjavítás már telepítve van az eszközön.   | |

## <a name="next-step"></a>Következő lépés
[Telepítse a 0.6-os frissítést](storsimple-virtual-array-install-update-06.md) a StorSimple virtuális tömbre.

## <a name="references"></a>Referencia
Régebbi kiadási megjegyzést keres? Ugrás:

* [StorSimple virtuális tömb frissítése 0,5 kibocsátási megjegyzések](storsimple-virtual-array-update-05-release-notes.md)
* [StorSimple virtuális tömb frissítése 0.4 Kibocsátási megjegyzések](storsimple-virtual-array-update-04-release-notes.md)
* [StorSimple virtuális tömb frissítése 0.3 Kibocsátási megjegyzések](storsimple-ova-update-03-release-notes.md)
* [StorSimple virtuális tömb frissítése 0.1 és 0.2 Kibocsátási megjegyzések](storsimple-ova-update-01-release-notes.md)
* [StorSimple virtuális tömb általános rendelkezésre állási kibocsátási megjegyzések](storsimple-ova-pp-release-notes.md)

