---
title: A StorSimple virtuális tömb frissítés 0,4 kibocsátási megjegyzései |} Microsoft Docs
description: A StorSimple virtuális tömb 0,4 Update futtatása kritikus megnyitott problémák és megoldásuk ismertetése
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
ms.openlocfilehash: cc2b025b7f3e28954c7f95409ffab03e5cbcf13d
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23927760"
---
# <a name="storsimple-virtual-array-update-04-release-notes"></a>A StorSimple virtuális tömb frissítés 0,4 kibocsátási megjegyzései

## <a name="overview"></a>Áttekintés

Az alábbi kibocsátási megjegyzések a kritikus megnyitott problémák és a Microsoft Azure StorSimple virtuális tömb frissítéseket megoldott problémák azonosításához.

A kibocsátási megjegyzések folyamatosan frissülnek, és megoldást igénylő kritikus fontosságú problémáit ismertté hozzáadásuk után. A StorSimple virtuális tömb telepítése előtt gondosan tekintse át a kibocsátási megjegyzésekben található információkat.

Frissítés 0,4 megfelel-e a szoftververzió **10.0.10289.0**.

> [!NOTE]
> Frissítések zavaró, és indítsa újra az eszközt. I/o van folyamatban, ha az eszköz azt eredményezi azok, háromszorosa a leállás.


## <a name="whats-new-in-the-update-04"></a>A frissítés 0,4 újdonságai
Frissítés 0,4 elsősorban néhány kiegészítésre alapján kialakulhat hibajavítás build. Ebben a verzióban több hibák, ami azt eredményezi, a korábbi verziók egyikében biztonsági mentési hibák rendelkezik foglalkoztak. A fő fejlesztése és növelő hibajavításokat a következők:

- **Biztonsági mentés a teljesítményt javító** -ebben a kiadásban által végrehajtott biztonsági mentési teljesítményének javítása érdekében több kulcs fejlesztései. Ennek eredményeképpen a biztonsági mentések, például fájlok nagy számú lásd: befejezéséhez, a teljes és növekményes biztonsági mentések időben jelentős mértékben csökken.

- **Visszaállítás hatékonyabb** -ebben a kiadásban továbbfejlesztett jelentősen fejleszti a a visszaállítási teljesítményt, ha sok fájlt használ. 2 – 4 millió fájlokat használnak, azt javasoljuk, hogy egy virtuális tömb 16 GB RAM memóriával az fejlesztések megjelenítéséhez ellátásához. Legalább 2 millió fájlok használata esetén a minimális követelmény a virtuális gép továbbra is 8 GB RAM-MAL.

- **Támogatási csomag fejlesztései** – a fejlesztések közé tartoznak a lemez, Processzor, memória, hálózati és a támogatási csomagot, így javul az eszköz problémák diagnosztizálása/hibakeresés folyamatát a felhő statisztikai adatait a bejelentkezés.

- **Korlát helyileg rögzített 200 GB-os köteteket iSCSI** -a helyileg rögzített kötetekhez, azt javasoljuk, hogy korlátozza egy 200 GB-os iSCSI kötetre a StorSimple virtuális tömbben. A rétegzett kötetek helyi foglalás továbbra is a kiépített kötet mérete 10 %-át lesznek, de tárfiókonként maximum 200 GB-os. 

- **Biztonsági mentésével kapcsolatos hibajavítások** - szoftver, a korábbi verzióinál történtek, amelyek biztonsági mentési hibák másolatokra történő visszaállításával kapcsolatos problémákat. Ezek a hibák rendelkezik foglalkoztak. Ebben a kiadásban.


## <a name="issues-fixed-in-the-update-04"></a>A frissítés 0,4 megoldott problémák

A következő táblázat az ebben a kiadásban javított összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |Biztonsági mentés teljesítményét|A korábbi kiadásokban a biztonsági mentések sok fájlt érintő időt vesz igénybe egy hosszú ideig (sorrendjében nap). Ebben a kiadásban mind a teljes és növekményes biztonsági mentések tekintse meg a befejezési idő jelentős mértékben csökken. |
| 2 |Támogatási csomag|Lemez, a CPU, a memória, a hálózati és a felhő statisztikák most már bejelentkezett a támogatási naplókat, így a támogatási csomag nagyon hatékonyan bármely eszköz problémák elhárítása.|
| 3 |Biztonsági mentés |A korábbi kiadásokban biztonsági másolatok hosszú ideig futó eredményezheti a terület crunch az eszközön, ami azt eredményezi, biztonsági mentési hibák. Ez a hiba a legfeljebb 5 biztonsági mentéseket a várólistára egyidejűleg tételével címzettjei ebben a kiadásban.|
| 4 |iSCSI | A korábbi kiadásokban a rétegzett vagy helyileg rögzített kötetek helyi fenntartása a kiépített kötet mérete 10 % volt. Ebben a kiadásban az összes iSCSI-kötet (helyileg rögzített vagy Szintezett) a helyi foglalás korlátozódik, amely legfeljebb 10 % 200 GB-ot (nagyobb, mint 2 TB rétegzett kötetek) ezáltal felszabadítás fel több lemezterületet a helyi lemezen. Azt javasoljuk, hogy ebben a kiadásban a helyileg rögzített kötetekhez legfeljebb 200 GB-os lehet-e.|


## <a name="known-issues-in-the-update-04"></a>A frissítés 0,4 ismert problémák

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
| **15.** |Fájlkiszolgáló |Nem támogatja a szimbolikus csatolást. | |
| **16.** |Fájlkiszolgáló |Fájlok által Windows titkosított fájlrendszer (EFS) keresztül másolását védett, vagy tárolja a StorSimple virtuális tömb fájl server eredmény beállításai nem támogatottak.  | |

## <a name="next-step"></a>Következő lépés
[A frissítés 0,4](storsimple-virtual-array-install-update-04.md) a StorSimple virtuális tömbben.

## <a name="references"></a>Referencia
Egy régebbi kiadási Megjegyzés keres? Ugrás: 

* [A StorSimple virtuális tömb frissítés 0,3 kibocsátási megjegyzései](storsimple-ova-update-03-release-notes.md)
* [A StorSimple virtuális tömb frissítés 0,1 és 0,2 kibocsátási megjegyzései](storsimple-ova-update-01-release-notes.md)
* [A StorSimple virtuális tömb általános rendelkezésre állási kibocsátási megjegyzései](storsimple-ova-pp-release-notes.md)

