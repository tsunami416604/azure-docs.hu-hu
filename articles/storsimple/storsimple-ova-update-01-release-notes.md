---
title: A StorSimple virtuális tömb frissítések kibocsátási megjegyzései |} Microsoft Docs
description: A StorSimple virtuális tömb 0,2 és 0,1 Update futtatása kritikus megnyitott problémák és megoldásuk ismertetése
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
ms.openlocfilehash: c4ccde9635b3874864baa9d4d262ff5ddcf2a425
ms.sourcegitcommit: 6fcd9e220b9cd4cb2d4365de0299bf48fbb18c17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/05/2018
---
# <a name="storsimple-virtual-array-update-02-and-01-release-notes"></a>A StorSimple virtuális tömb frissítés 0,2 és 0,1 kibocsátási megjegyzései
## <a name="overview"></a>Áttekintés
Az alábbi kibocsátási megjegyzések a kritikus megnyitott problémák és a Microsoft Azure StorSimple virtuális tömb frissítéseket megoldott problémák azonosításához. (A Microsoft Azure StorSimple virtuális tömbjének értéke is a StorSimple virtuális eszköz a helyszínen vagy a StorSimple virtuális eszköz.) 

A kibocsátási megjegyzések folyamatosan frissülnek, és megoldást igénylő kritikus fontosságú problémáit ismertté hozzáadásuk után. A StorSimple virtuális eszköz telepítése előtt gondosan tekintse át a kibocsátási megjegyzésekben található információkat.

Frissítés 0,2 megfelel-e a szoftververzió **10.0.10280.0**; Frissítés 0,1 verziója **10.0.10279.0**. Az az alábbi szakaszok tartalmazzák a módosításokat minden egyes frissítés. 

> [!NOTE]
> Frissítések zavaró és az eszköz újraindul. I/o van folyamatban, ha az eszköz állásidő gyakorisága.
> 
> 

## <a name="issues-fixed-in-the-update-02"></a>A frissítés 0,2 megoldott problémák
A frissítés 0,2 Update 0,1 mellett a következő táblázat ismerteti a javítás összes módosításokat tartalmaz:

| Szolgáltatás | Probléma |
| --- | --- |
| Frissítések |Az utolsó kiadásban frissítések nem automatikusan észleli a klasszikus Azure portálon, így volt szükség a helyi webes felhasználói felület frissítéseinek telepítéséhez. Ez a probléma fennáll ebben a kiadásban. Miután telepítette a frissítési 0,2, a klasszikus Azure portál használatával a jövőbeli frissítésekre is telepítheti. |

## <a name="whats-new-in-the-update-01"></a>A frissítés 0,1 újdonságai
Frissítés 0,1 a következő hibajavításokat és fejlesztéseket tartalmaz. 

* **A felhő kimaradások jobb rugalmasság**: Ez a kiadás számos hibajavítást vész-helyreállítási, biztonsági mentése, visszaállítása és a felhő kapcsolat becsukódjon rétegezéséhez rendelkezik. 
* **Továbbfejlesztett visszaállítási teljesítmény**: Ebben a kiadásban rendelkezik, amely rendelkezik a végrehajtási időt a helyreállítási feladatok jelentősen kevesebb hibajavításokat tartalmaz.
* **Automatikus terület-visszanyerést optimalizálási**: adat törlődik a dinamikusan kiosztott kötetek, a nem használt tárolási blokkokat kell vissza lehessen igényelni. Ebben a kiadásban javult a terület-visszanyerést folyamat a felhőből, ami azt eredményezi, a nem használt terület ismertté váló gyorsabb a korábbi verziókhoz képest.
* **Új virtuális lemez képek**: új virtuális merevlemez VHDX és VMDK is elérhető a klasszikus Azure portálon keresztül. Ezeket a lemezképeket új frissítés 0,1 központilag töltheti le.
* **A feladatok állapotát a portál pontosságának javítása**: a szoftver korábbi verzióját, a feladat állapotát a portál jelentéseihez nem lett a részletes. Ebben a kiadásban a probléma megoldásához.
* **Tartományi csatlakozási élmény**: tartományhoz, és az eszköz átnevezése kapcsolatos hibajavítások.

## <a name="issues-fixed-in-the-update-01"></a>A frissítés 0,1 megoldott problémák
A következő táblázat az ebben a kiadásban javított összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |VMDK |VMware verzióban az operációs rendszer lemezének ritka riasztásokat okozó és a normál működés megszakítása fordult elő. Ez volt a rögzített ebben a kiadásban. |
| 2 |iSCSI server |A felhasználó legutóbbi kiadása szükséges megadni a StorSimple virtuális eszköz engedélyezett hálózati csatolóhoz átjárót. Ez a viselkedés ebben a kiadásban módosul, hogy a felhasználó rendelkezik-e az összes engedélyezett hálózati illesztő legalább egy átjáró konfigurálásához. |
| 3 |Támogatási csomag |A szoftver verziója korábbi sikertelen, ha a csomag mérete 1 GB-nál nagyobb volt a csomag gyűjtemény támogatja. Ez a probléma fennáll ebben a kiadásban. |
| 4 |Felhő hozzáférés |Az utolsó kiadásban a StorSimple virtuális tömb nem rendelkezett a hálózati kapcsolatot, és újra lett indítva, ha a helyi felhasználói felület kellene kapcsolódási problémák. Ez a probléma javítását az ebben a kiadásban. |
| 5 |Figyelési diagramok |A korábbi változatban, a következő eszköz feladatátvétel a felhő kapacitás kihasználtságát diagramok helytelen értékeket a klasszikus Azure portálon jelenik meg. Ez a jelenlegi kiadásban rögzített. |

## <a name="known-issues-in-the-update-01"></a>A frissítés 0,1 ismert problémák
A következő táblázat összefoglalja az ismert problémákról a StorSimple virtuális tömb, és a problémák kiadás jelezve a korábbi verzióiról tartalmazza. **Az ebben a kiadásban az áttelepítés előtt feljegyzett problémák kiadásban csillag jelöli. A listában szereplő szinte minden probléma a StorSimple virtuális tömb GA kiadás átvitt rendelkezik.**

| Nem. | Szolgáltatás | Probléma | Megkerülő megoldás/megjegyzések |
| --- | --- | --- | --- |
| **1.** |Frissítések |A virtuális eszközök előzetes létrehozott támogatott általánosan rendelkezésre álló verziója nem frissíthető. |A virtuális eszközök használata a vész-helyreállítási munkafolyamat általános rendelkezésre állási kiadásának kell feladatátvételt. |
| **2.** |Kiépített adatlemez |Ha a megadott méretű adatlemezt ellátta, és a megfelelő StorSimple virtuális eszköz létrehozása kell nem bontsa ki, vagy a adatlemez zsugorítani. Ennek a végrehajtására tett kísérlet a helyi rétegeken az eszköz összes adatának adatvesztést eredményez. | |
| **3.** |Csoportházirend |Ha egy eszköz a tartományhoz, a Csoportházirend alkalmazása kedvezőtlen hatással lehet az eszköz műveletet. |Győződjön meg arról, hogy a virtuális tömb saját szervezeti egységben (OU) az Active Directory és nem a csoportházirend-objektumok (GPO) vonatkoznak rá. |
| **4.** |Helyi webes felhasználói felület |A fokozott biztonság funkció engedélyezése az Internet Explorerben (IE ESC), előfordulhat, hogy egyes helyi felhasználói felület weboldalak például hibaelhárítás vagy karbantartási nem működik megfelelően. Gombok ezeken a lapokon nem is működnek. |Kapcsolja ki az Internet Explorer fokozott biztonsági funkciók. |
| **5.** |Helyi webes felhasználói felület |A Hyper-V virtuális gépen a hálózati adapterek, a webes felhasználói felületén jelennek meg, 10 GB/s felületeihez. |Ez a viselkedés a Hyper-V tükre. Hyper-V mindig látható a virtuális hálózati adapterek 10 GB/s. |
| **6.** |Rétegzett kötetek vagy megosztások |A rétegzett kötetek nem támogatott StorSimple együtt használható alkalmazások zárolást bájttartomány. Ha bájt tartomány zárolás engedélyezve van, a StorSimple rétegezéséhez nem fog működni. |Ajánlott mértékeket tartalmazza: <br></br>Kapcsolja ki a bájttartomány zárolás a az alkalmazás logikáját.<br></br>Válassza ki az alkalmazás adatokat a rétegzett kötetek figyelésekor helyileg rögzített kötetekhez kell.<br></br>*Ismeret*: Ha használatával helyileg rögzített kötetek, és engedélyezve van az bájt tartomány zárolás, vegye figyelembe, hogy a helyileg rögzített kötet lehet online még a visszaállítás befejezése előtt. Ezekben az esetekben ha a helyreállítás van folyamatban, majd meg kell várnia a visszaállítás befejeződését. |
| **7.** |Rétegzett megosztások |Nagy fájlok használata a kimenő lassú réteg eredményezheti. |Amikor olyan nagy fájlok, azt javasoljuk, hogy a legnagyobb fájl a fájlmegosztás méretének % 3-nál kisebb. |
| **8.** |Használható kapacitás megosztások |Előfordulhat, hogy látja fogyasztás hiányában a megosztáson adatok megosztása. Ennek oka az, a használt kapacitás megosztások metaadatokat tartalmaz. | |
| **9.** |Vészhelyreállítás |Csak a vész-helyreállítási ugyanahhoz a tartományhoz, amely a forráseszközt fájlkiszolgáló végezheti el. A céleszközön egy másik tartományban való katasztrófa utáni helyreállítás nem támogatott ebben a kiadásban. |Ez egy későbbi kiadásban hajtják végre. |
| **10.** |Azure PowerShell |A StorSimple virtuális eszköz az Azure PowerShell ebben a kiadásban nem kezelhetők. |A virtuális eszközök minden felügyeleti a klasszikus Azure portálra, és a helyi webes felhasználói felületen keresztül kell végezni. |
| **11.** |A jelszó módosítása |A virtuális tömb eszköz konzol csak en-US billentyűzet formátumú bemenetet fogad el. | |
| **12.** |CHAP |CHAP hitelesítő adatok létrehozása után nem lesznek eltávolítva. Továbbá ha módosítja a CHAP hitelesítő adatokat, szüksége lesz a kötetek offline állapotba, és hogy azok online, a változtatás érvénybe léptetéséhez. |Ezek kiiktatása a későbbi kiadásra. |
| **13.** |iSCSI server |A "használt tárolási" az iSCSI-kötet jelenik meg a StorSimple Manager szolgáltatás és az iSCSI-gazdagép eltérőek lehetnek. |Az iSCSI-gazdagép a fájlrendszer nézet tartozik.<br></br>Az eszköz látja, ha a kötet maximális méret lefoglalt blokkok. |
| **14.** |File server* |Ha egy fájlt egy mappában van egy alternatív Data Stream (ADS) társítva, a HIRDETÉSEK nem biztonsági mentése vagy visszaállítása vész-helyreállítási, a Klónozás és az elem szintű helyreállítás. | |

## <a name="next-step"></a>Következő lépés
[Frissítések telepítése](storsimple-ova-install-update-01.md) a StorSimple virtuális tömbben.

