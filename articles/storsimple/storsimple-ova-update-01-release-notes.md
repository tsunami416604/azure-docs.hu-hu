---
title: A StorSimple Virtual Array frissítések kibocsátási megjegyzések |} A Microsoft Docs
description: A StorSimple Virtual Array 0.2-es és 0.1-es frissítés futó kritikus fontosságú megoldatlan problémák és megoldásaik ismerteti.
services: storsimple
documentationcenter: ''
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 3993864d-2ddd-4302-a2f1-8d737fba6eab
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 06/16/2016
ms.author: alkohli
ms.openlocfilehash: aad60024187ca180c002f119f4b975e8f69796e5
ms.sourcegitcommit: 3102f886aa962842303c8753fe8fa5324a52834a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2019
ms.locfileid: "60629288"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>A StorSimple Virtual Array frissítés 0.2-es és 0.1-es frissítés – kibocsátási megjegyzések
## <a name="overview"></a>Áttekintés
A következő kiadási megjegyzések a kritikus fontosságú megoldatlan problémák és a Microsoft Azure StorSimple Virtual Array frissítések megoldott problémák azonosításához. (A Microsoft Azure StorSimple Virtual Array az más néven a StorSimple virtuális eszköz helyi vagy a StorSimple virtuális eszköz.) 

A kibocsátási megjegyzésekben folyamatosan frissülnek, és ahogy ismertté kritikus problémák adódnak. A StorSimple virtuális eszköz üzembe helyezése, előtt gondosan tekintse át a kibocsátási megjegyzésekben található információkat.

A 0.2 felel meg a szoftververzió **10.0.10280.0**; 0.1-es frissítés verziója **10.0.10279.0**. Az alábbi szakaszok a módosításokat az egyes frissítések listája. 

> [!NOTE]
> Azok zavart okozó frissítések, és újraindítja az eszközt. I/o van folyamatban, ha az eszköz állásidő számítunk fel.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>A 0.2-es frissítés megoldott problémák
A 0.2 0.1-es frissítés a következő táblázat ismerteti a javítás mellett az összes módosításait tartalmazza:

| Szolgáltatás | Probléma |
| --- | --- |
| Frissítések |A legutóbbi verzió, a frissítések nem automatikusan észleli a klasszikus Azure portálon, így kellett használni a helyi webes felhasználói Felületet a frissítések telepítéséhez. Ebben a kiadásban a probléma megoldódik. 0.2-es frissítés telepítése után a jövőbeli frissítések a klasszikus Azure portál használatával is telepítheti. |

## <a name="whats-new-in-the-update-01"></a>0.1-es frissítés újdonságai
0.1-es frissítés a következő hibajavításokat és fejlesztéseket tartalmaz. 

* **Felhőbeli leállások jobb rugalmasság**: Ez a kiadás számos hibajavítást vész-helyreállítási, biztonsági mentés, visszaállítás és a egy felhőalapú kapcsolódási bekövetkező szolgáltatáskimaradás esetén rétegezést kapcsolatos rendelkezik. 
* **Továbbfejlesztett helyreállítási teljesítmény**: Ebben a kiadásban hibajavításokat tartalmaz, amely rendelkezik a visszaállítási feladat befejezési ideje le jelentősen Kivágás rendelkezik.
* **Automatikus terület-visszaigénylést optimalizálási**: Adatok törlése a dinamikusan kiosztott kötetek esetén a fel nem használt tárolási blokkokat kell vissza. Ebben a kiadásban javult a hely visszanyerése folyamat a felhőben, így ismertté váló gyorsabban a korábbi verziókhoz képest a szabad terület.
* **Új virtuális lemez lemezképek**: Új virtuális merevlemez VHDX és VMDK mostantól elérhetők a klasszikus Azure portálon keresztül. Ezek a lemezképek 0.1-es frissítés új eszközök kiépítése töltheti le.
* **Feladatok állapota a portálon pontosságának**: A szoftver korábbi verzióját, a feladat állapota a portálon reporting nem volt részletes. Ebben a kiadásban a probléma megoldásához.
* **Tartományhoz való csatlakozás élmény**: A tartományhoz, és az eszköz átnevezése kapcsolatos hibajavítások.

## <a name="issues-fixed-in-the-update-01"></a>Hibáinak 0.1-es frissítés javításai
Az alábbi táblázat hibáinak javításai ebben a kiadásban összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |VMDK |VMware verzióban az operációsrendszer-lemez ritka riasztásokat okozó és normális működés megszakítása jelent meg. Ez lett javítva a kiadásban. |
| 2 |iSCSI server |A legutóbbi kiadás a felhasználó volt szükséges megadni átjárót mindegyik engedélyezett hálózati interfész a StorSimple virtuális eszköz. Ez a viselkedés módosítva a kiadásban, hogy a felhasználó rendelkezik legalább egy átjáró engedélyezett hálózati adapterek konfigurálásához. |
| 3 |Támogatási csomag |A szoftver korábbi verzióját, a támogatási csomag gyűjtemény Ha a csomag mérete 1 GB-nál nagyobb volt, nem sikerült. Ebben a kiadásban a probléma megoldódik. |
| 4 |Cloud-access |A legutóbbi verzió a StorSimple Virtual Array nincs hálózati kapcsolat, majd újra lett indítva, ha a helyi felhasználói Felületet kell kapcsolódási problémák. Ez a probléma nem oldódik ebben a kiadásban. |
| 5 |Figyelési diagramok |Az előző verziót, a következő eszközön feladatátvétel a felhő kapacitás kihasználtsága diagramok helytelen értékeket a klasszikus Azure portálon jelennek meg. Ez javítja a jelenlegi kiadásban. |

## <a name="known-issues-in-the-update-01"></a>0.1-es frissítés ismert problémái
Az alábbi táblázat a StorSimple Virtual Array az ismert problémák összegzését tartalmazza, és a kiadási jelezve a korábbi kiadásokban a problémák tartalmazza. **Az ebben a kiadásban feljegyzett problémák kiadás vannak, egy csillaggal jelölve. A listában szereplő szinte összes problémát a StorSimple Virtual Array az általánosan elérhető kiadás átvitt rendelkezik.**

| Nem. | Szolgáltatás | Probléma | Megkerülő megoldás és megjegyzések |
| --- | --- | --- | --- |
| **1.** |Frissítések |Az előzetes kiadásban létrehozott virtuális eszközre nem lehet frissíteni egy támogatott általánosan elérhető verzióra. |Ezek a virtuális eszközök feladatátvételt kell végrehajtani a végleges kiadás vész-helyreállítási munkafolyamat használatával. |
| **2.** |Kiépített adatlemez |Miután ellátta bizonyos megadott méretű adatlemez és a megfelelő virtuális StorSimple-eszközt hozott létre, meg kell nem tartalomtól az adatlemezt. Kísérlet ehhez a helyi rétegeken az eszköz összes adatának adatvesztést eredményez. | |
| **3.** |Csoportházirend |Ha egy eszköz a tartományhoz, a Csoportházirend alkalmazása kedvezőtlen hatással lehet az eszköz műveletet. |Győződjön meg arról, hogy a virtuális tömb a saját szervezeti egység (OU) az Active Directory, és nem a csoportházirend-objektumok (GPO) beállítva rajta. |
| **4.** |Helyi webes felhasználói felületen |Ha az Internet Explorer (IE ESC) engedélyezve vannak a fejlett biztonsági funkcióknak, előfordulhat, hogy néhány helyi webes felhasználói Kezelőfelületi lapok például hibaelhárítás vagy karbantartási nem működik megfelelően. Gombok ezeken a lapokon is előfordulhat, hogy nem működik. |Kapcsolja ki az Internet Explorer fokozott biztonsági funkciók. |
| **5.** |Helyi webes felhasználói felületen |Hyper-V virtuális gépen a hálózati adapterek, a webes felhasználói felületén jelennek meg, 10 GB/s felületeihez. |Ez a viselkedés a Hyper-V egy másolatát. A Hyper-V a virtuális hálózati adapterek 10 GB/s mindig látható. |
| **6.** |Rétegzett kötetek vagy megosztások |A rétegzett kötetek nem támogatott. a StorSimple együtt használható alkalmazások zárolása bájttartományt. Ha bájt tartomány zárolás engedélyezve van, a StorSimple-rétegezést fog működni. |Ajánlott a mértékeket tartalmazza: <br></br>Kapcsolja ki az alkalmazáslogika a zárolás bájttartományt.<br></br>Válassza ki az alkalmazáshoz tartozó adatokat a rétegzett kötetek ellentétben a gyors helyi kötetek kell.<br></br>*Ismeret*: Ha engedélyezve van az bájt tartomány zárolás használatával helyileg rögzített kötetekről, vegye figyelembe, hogy a helyileg rögzített kötet lehet online még a visszaállítás befejezése előtt. Ezekben az esetekben ha a visszaállítás van folyamatban, majd meg kell várnia a visszaállítás befejeződését. |
| **7.** |Rétegzett megosztás |Nagy fájlok használata lassú réteg felskálázása eredményezhet. |Ha nagy méretű fájlok dolgozik, azt javasoljuk, hogy a megosztás méretének % 3-nál kisebb-e a legnagyobb fájlt. |
| **8.** |Használt kapacitás megosztások |Látni fogyasztás hiányában a megosztáson lévő adatok megosztása. Ennek oka az, a felhasznált kapacitás megosztások metaadatokat tartalmaz. | |
| **9.** |Vészhelyreállítás |Csak az ugyanahhoz a tartományhoz, mint a forráseszközt a fájlkiszolgáló vész-helyreállítási hajtható végre. Ebben a kiadásban nem támogatott a vész-helyreállítási cél eszközhöz egy másik tartományban található. |Ez egy későbbi kiadástól kezdve hajtja végre. |
| **10.** |Azure PowerShell |A StorSimple virtuális eszközre nem kezelhető ebben a kiadásban az Azure Powershellen keresztül. |A virtuális eszközök a management a klasszikus Azure portálra, és a helyi webes felületén keresztül kell elvégezni. |
| **11.** |Jelszó módosítása |A virtuális tömb eszköz konzol csak en-US billentyűzet formátumú bemeneti fogad el. | |
| **12.** |CHAP |A CHAP hitelesítő adatok ezután nem lehet eltávolítani. Emellett ha a CHAP-hitelesítő adatok módosítását, szüksége lesz a kötetek offline állapotba, és hogy azok online, a módosítás érvénybe léptetéséhez. |Ezek egy későbbi kiadástól kezdve kibocsátásokban megtörténik. |
| **13.** |iSCSI server |A "használt tárolási" az iSCSI-kötet jelenik meg a StorSimple Manager szolgáltatás és az iSCSI-gazdagép eltérő lehet. |Az iSCSI-gazdagép rendelkezik a fájlrendszer nézetet.<br></br>Az eszköz látja, ha a kötet volt a maximális méret lefoglalt blokkok. |
| **14.** |File server* |Ha egy fájl egy mappában van egy másik Data Stream (ADS) társítva, a HIRDETÉSEK nem biztonsági mentése vagy visszaállítása vész-helyreállítási, klónozás és az elemszintű helyreállítás. | |

## <a name="next-step"></a>Következő lépés
[Telepítse a frissítéseket](storsimple-ova-install-update-01.md) az a StorSimple Virtual Array.

