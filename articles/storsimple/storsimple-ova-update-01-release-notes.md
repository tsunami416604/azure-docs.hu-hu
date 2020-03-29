---
title: StorSimple Virtual Array updates kiadási megjegyzések| Microsoft dokumentumok
description: A 0.2-es és 0.1-es frissítést futtató StorSimple virtuális tömb kritikus nyitott problémáit és megoldásait ismerteti.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60629288"
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>StorSimple Virtual Array Update 0.2 és 0.1 kiadási megjegyzések
## <a name="overview"></a>Áttekintés
A következő kiadási megjegyzések azonosítják a kritikus nyitott problémákat és a Microsoft Azure StorSimple virtual array frissítések megoldott problémáit. (A Microsoft Azure StorSimple virtuális tömb a helyszíni StorSimple virtuális eszköz vagy a StorSimple virtuális eszköz néven is ismert.) 

A kiadási megjegyzések folyamatosan frissülnek, és a kerülő megoldáshoz igénylő kritikus problémák felderítése után a hozzájuk. A StorSimple virtuális eszköz telepítése előtt alaposan tekintse át a kiadási megjegyzésekben található információkat.

A 0.2-es frissítés a **10.0.10280.0**szoftververziónak felel meg; A 0.1-es verzió a **10.0.10279.0**verzió. Az alábbi szakaszok az egyes frissítések változásait sorolják fel. 

> [!NOTE]
> A frissítések zavaróak, és újraindítják az eszközt. Ha az I/O folyamatban van, az eszköz állásidőt kap.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>A 0.2-es frissítésben javított problémák
A 0.2-es frissítés az alábbi táblázatban ismertetett javításon kívül tartalmazza a 0.1-es frissítésösszes módosítását:

| Szolgáltatás | Probléma |
| --- | --- |
| Frissítések |A legutóbbi kiadásban a frissítések nem lettek automatikusan észlelve az Azure klasszikus portálján, ezért a helyi webes felhasználói felületet kellett használnia a frissítések telepítéséhez. Ez a probléma ebben a kiadásban kivan javítva. A 0.2-es frissítés telepítése után telepítheti a jövőbeli frissítéseket az Azure klasszikus portálhasználatával. |

## <a name="whats-new-in-the-update-01"></a>A 0.1-es frissítés újdonságai
A 0.1-es frissítés a következő hibajavításokat és fejlesztéseket tartalmazza. 

* **Továbbfejlesztett rugalmasság a felhőkimaradások:** Ez a kiadás számos hibajavítások körül vész-helyreállítási, biztonsági mentés, visszaállítás, és rétegezés esetén a felhőbeli kapcsolat zavar. 
* **Továbbfejlesztett visszaállítási teljesítmény:** Ez a kiadás olyan hibajavításokat is rendelkezik, amelyek jelentősen csökkentették a visszaállítási feladatok befejezési idejét.
* **Automatikus térvisszanyerésoptimalizálás:** Ha az adatokat vékonyan kiépített köteteken törli, a fel nem használt tárolóblokkokat újra kell igényelni. Ez a kiadás javította a felhőből történő térvisszanyerési folyamatot, ami azt eredményezte, hogy a fel nem használt terület gyorsabban elérhetővé vált, mint az előző verziók.
* **Új virtuális lemezképek**: Új Virtuális merevlemez, VHDX és VMDK már elérhető az Azure klasszikus portálon keresztül. Ezeket a képeket letöltheti új 0.1-es frissítési eszközök kiépítéséhez.
* **A feladatok állapotának javítása a portálon:** A szoftver korábbi verziójában a feladatállapot-jelentés a portálon nem volt részletes. Ez a probléma ebben a kiadásban megoldódott.
* **Tartományhoz való csatlakozás élménye**: Az eszköz tartományhoz való csatlakozásával és átnevezésével kapcsolatos hibajavítások.

## <a name="issues-fixed-in-the-update-01"></a>A 0.1-es frissítésben javított problémák
Az alábbi táblázat az ebben a kiadásban javított problémákat tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |Vmdk |Egyes VMware-verziókban az operációs rendszer lemeze ritka nak tekinthető, amely riasztásokat okoz, és megzavarja a normál működést. Ez volt fix ebben a kiadásban. |
| 2 |iSCSI-kiszolgáló |Az utolsó kiadásban a felhasználónak meg kellett adnia egy átjárót a StorSimple virtuális eszköz minden egyes engedélyezett hálózati illesztőjéhez. Ez a viselkedés megváltozik ebben a kiadásban, így a felhasználónak legalább egy átjárót kell konfigurálnia az összes engedélyezett hálózati adapterhez. |
| 3 |Támogatási csomag |A szoftver korábbi verziójában a támogatási csomag gyűjteménye sikertelen volt, ha a csomagméretek mérete 1 GB-nál nagyobb volt. Ez a probléma ebben a kiadásban kivan javítva. |
| 4 |Felhőbeli hozzáférés |Az utolsó kiadásban, ha a StorSimple virtuális tömb nem rendelkezik hálózati kapcsolattal, és újraindult, a helyi felhasználói felület kapcsolódási problémákat. A probléma ebben a kiadásban megoldva. |
| 5 |Figyelési diagramok |Az előző kiadásban egy eszköz feladatátvételt követően a felhőkapacitás-kihasználtsági diagramok helytelen értékeket jelenítmeg az Azure klasszikus portálon. Ez az aktuális kiadásban rögzített. |

## <a name="known-issues-in-the-update-01"></a>Ismert problémák a 0.1-es frissítésben
Az alábbi táblázat a StorSimple virtuális tömb ismert problémáinak összefoglalását tartalmazza, és tartalmazza az előző kiadásokból származó kiadások kiadását. **Az ebben a kiadásban említett kiadási kiadások csillaggal vannak jelölve. Szinte az összes kérdés ebben a listában átkerült a GA kiadása StorSimple Virtual Array.**

| Nem. | Szolgáltatás | Probléma | Megoldás/megjegyzések |
| --- | --- | --- | --- |
| **1.** |Frissítések |Az előzetes verzióban létrehozott virtuális eszközök nem frissíthetők támogatott általános rendelkezésre állási verzióra. |Ezeket a virtuális eszközöket vész-helyreállítási (DR) munkafolyamattal kell átvenni az általános rendelkezésre állási kiadás. |
| **2.** |Kiépített adatlemez |Miután kiépített egy adott méretű adatlemezt, és létrehozta a megfelelő StorSimple virtuális eszközt, nem bonthatja ki vagy zsugoríthatja az adatlemezt. Ennek megkísérlése az eszköz helyi szintjein lévő összes adat elvesztését eredményezi. | |
| **3.** |Csoportházirend |Ha egy eszköz tartományhoz csatlakozik, a csoportházirend alkalmazása hátrányosan befolyásolhatja az eszköz működését. |Győződjön meg arról, hogy a virtuális tömb a saját szervezeti egységében (OU) található az Active Directory számára, és nincsenek csoportházirend-objektumok alkalmazására. |
| **4.** |Helyi webes felhasználói felület |Ha az Internet Explorer (IE ESC) fokozott biztonsági szolgáltatásai engedélyezve vannak, előfordulhat, hogy egyes helyi webes felhasználói felületi lapok, például a Hibaelhárítás vagy a Karbantartás nem működnek megfelelően. Előfordulhat, hogy az ezeken az oldalakon lévő gombok sem működnek. |Kapcsolja ki az Internet Explorer továbbfejlesztett biztonsági szolgáltatásait. |
| **5.** |Helyi webes felhasználói felület |A Hyper-V virtuális gépeken a webes felhasználói felület hálózati interfészei 10 Gbit/s-os felületként jelennek meg. |Ez a viselkedés tükrözi a Hyper-V. A Hyper-V mindig 10 Gb/s-ot jelenít meg a virtuális hálózati adapterek esetében. |
| **6.** |Rétegzett kötetek vagy megosztások |A StorSimple rétegzett kötetekkel dolgozó alkalmazások bájttartomány-zárolása nem támogatott. Ha a bájttartomány zárolása engedélyezve van, a StorSimple rétegezés nem fog működni. |Az ajánlott intézkedések a következők: <br></br>Kapcsolja ki a bájttartomány zárolását az alkalmazáslogikában.<br></br>Válassza ki, hogy az alkalmazás adatait helyileg rögzített kötetek, szemben a rétegzett kötetek.<br></br>*Figyelmeztetés:* Ha a helyileg rögzített kötetek és bájttartomány-zárolás engedélyezve van, vegye figyelembe, hogy a helyileg rögzített kötet már a visszaállítás befejezése előtt is online állapotban lehet. Ilyen esetekben, ha egy visszaállítás folyamatban van, akkor meg kell várnia a visszaállítás befejezését. |
| **7.** |Többszintű részvények |A nagy fájlok kal végzett munka lassú szintkiesést eredményezhet. |Nagyméretű fájlokkal végzett munka esetén azt javasoljuk, hogy a legnagyobb fájl kisebb, mint a megosztási méret 3%-a. |
| **8.** |Megosztások használt kapacitása |A megosztásra vonatkozó adatok hiányában a megosztásmegosztási felhasználás ttapasztalhatja meg. Ennek az az oka, hogy a megosztások használt kapacitása metaadatokat tartalmaz. | |
| **9.** |Vészhelyreállítás |A fájlkiszolgáló vészhelyreállítása csak a forráseszköz tartományával azonos tartományban hajtható végre. Ebben a kiadásban nem támogatott a vész-helyreállítás egy másik tartományban lévő céleszközre. |Ez egy későbbi kiadásban kerül végrehajtásra. |
| **10.** |Azure PowerShell |A StorSimple virtuális eszközök ebben a kiadásban nem kezelhetők az Azure PowerShellen keresztül. |A virtuális eszközök felügyeletét az Azure klasszikus portálján és a helyi webes felhasználói felületen keresztül kell elvégezni. |
| **11.** |Jelszó módosítása |A virtuális tömbeszköz-konzol csak en-US billentyűzet formátumban fogadja a bevitelt. | |
| **12.** |CHAP |A létrehozott CHAP-hitelesítő adatok nem távolíthatók el. Ezenkívül ha módosítja a CHAP hitelesítő adatait, a köteteket offline állapotba kell helyeznie, majd online állapotba kell hoznia a módosítás érvénybe léptetéséhez. |Ezekkel egy későbbi kiadásban foglalkozunk. |
| **13.** |iSCSI-kiszolgáló |Az iSCSI-kötetek "Használt tárolója" a StorSimple Manager szolgáltatásban és az iSCSI-állomásban eltérő lehet. |Az iSCSI-állomás fájlrendszer-nézete.<br></br>Az eszköz látja a lefoglalt blokkokat, amikor a kötet a maximális méreten volt. |
| **14.** |Fájlkiszolgáló* |Ha egy mappában lévő fájlhoz alternatív adatfolyam (ADS) van társítva, az ADS-ről nem készül biztonsági mentés vagy visszaállítás vészhelyreállítás, klónozás és elemszintű helyreállítás révén. | |

## <a name="next-step"></a>Következő lépés
[Telepítse a frissítéseket](storsimple-ova-install-update-01.md) a StorSimple virtuális tömbre.

