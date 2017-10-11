---
title: "A StorSimple virtuális tömb frissítés 0,5 kibocsátási megjegyzései |} Microsoft Docs"
description: "A StorSimple virtuális tömb 0,5 Update futtatása kritikus megnyitott problémák és megoldásuk ismertetése"
services: storsimple
documentationcenter: 
author: alkohli
manager: timlt
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/08/2017
ms.author: alkohli
ms.openlocfilehash: 4d020ff2b998da4cb52fe91e4d7d4b93544965a8
ms.sourcegitcommit: f537befafb079256fba0529ee554c034d73f36b0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2017
---
# <a name="storsimple-virtual-array-update-05-release-notes"></a>A StorSimple virtuális tömb frissítés 0,5 kibocsátási megjegyzései

## <a name="overview"></a>Áttekintés

Az alábbi kibocsátási megjegyzések a kritikus megnyitott problémák és a Microsoft Azure StorSimple virtuális tömb frissítéseket megoldott problémák azonosításához.

A kibocsátási megjegyzések folyamatosan frissülnek, és megoldást igénylő kritikus fontosságú problémáit ismertté hozzáadásuk után. A StorSimple virtuális tömb telepítése előtt gondosan tekintse át a kibocsátási megjegyzésekben található információkat.

Frissítés 0,5 megfelel-e a szoftververzió **10.0.10290.0**.

> [!NOTE]
> Frissítések zavaró, és indítsa újra az eszközt. I/o van folyamatban, ha az eszköz azt eredményezi azok, háromszorosa a leállás. A frissítés alkalmazásához a részletes útmutatást Itt [0,5. számú frissítés](storsimple-virtual-array-install-update-05.md).


## <a name="whats-new-in-the-update-05"></a>A frissítés 0,5 újdonságai
Frissítés 0,5 elsősorban hibajavítás build. A fő fejlesztése és növelő hibajavításokat a következők:

- **Biztonsági mentés rugalmassági fejlesztései** -ebben a kiadásban rendelkezik, amelyek javítják a biztonsági mentési rugalmassági javításokat. A korábbi kiadásokban a biztonsági mentések lejárt csak bizonyos kivételeket. Ebben a kiadásban újrapróbálkozik a biztonsági mentési kivételeket, és rugalmasabb, lehetővé teszi a biztonsági mentéseket.

- **Tárhely-használat figyelését frissítései** -2017. június 30. indítása, a tárhely-használat figyelését a StorSimple virtuális eszköz adatsorozat rendszerből. Ez a figyelési diagramok 0,4 vagy alacsonyabb Update futtatása az összes virtuális tömbök vonatkozik. A frissítés tartalmazza a további tárhely-használat figyelését az Azure-portálon a használatához szükséges módosításokat. A kritikus frissítés 2017. június 30. Folytatja a figyelési funkció használata előtt.


## <a name="issues-fixed-in-the-update-05"></a>A frissítés 0,5 megoldott problémák

A következő táblázat az ebben a kiadásban javított összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |Biztonsági mentési rugalmasság| A korábbi kiadásokban a biztonsági mentések lejárt csak bizonyos kivételeket. Ebben a kiadásban a biztonsági mentési kivételek ismételt megpróbálásával rugalmasabb, a biztonsági másolatok készítése javítást tartalmaz.|
| 2 |Figyelés| A tárhely-használat figyelését a StorSimple virtuális eszköz adatsorozathoz elavulttá válik 2017. június 30 indítása. Ez a művelet hatással van a figyelési diagramokat a StorSimple Device Manager szolgáltatás fut a StorSimple virtuális tömbök (1200-as modell). Ebben a kiadásban frissítést tartalmaz, amely engedélyezi a felhasználó további tárhely-használat figyelését túl 2017. június 30 virtuális tömbökön a használatához.|
| 3 |Fájlkiszolgáló| A korábbi kiadásokban a felhasználó véletlenül másolhatja a titkosított fájlokat a virtuális tömbhöz. Ebben a kiadásban, amely nem teszi lehetővé a titkosított fájlok másolása virtuális tömb javítást tartalmaz. Ha az eszközön a frissítés előtt meglévő titkosított fájlok, a biztonsági mentések fog jelezni, csak a titkosított fájlok törlése a rendszerből. |


## <a name="known-issues-in-the-update-05"></a>A frissítés 0,5 ismert problémák

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
| **8.** |Használható kapacitás megosztások |Előfordulhat, hogy látja fogyasztás megosztani, ha nincs adat a megosztáson. A felhasználás az oka, hogy a megosztások használt kapacitás metaadatokat tartalmaz. | |
| **9.** |Vészhelyreállítás |Csak a vész-helyreállítási ugyanahhoz a tartományhoz, amely a forráseszközt fájlkiszolgáló végezheti el. A céleszközön egy másik tartományban való katasztrófa utáni helyreállítás nem támogatott ebben a kiadásban. |Ez egy későbbi kiadásban van megvalósítva. További információkért látogasson el [a StorSimple virtuális tömb a feladatátvételi és katasztrófa-helyreállítás](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |A StorSimple virtuális eszköz az Azure PowerShell ebben a kiadásban nem kezelhetők. |A virtuális eszközök minden felügyeleti az Azure portál és a helyi webes felhasználói felületen keresztül kell végezni. |
| **11.** |A jelszó módosítása |A virtuális tömb eszköz konzol csak fogad bemeneti az en-us billentyűzet formátumban. | |
| **12.** |CHAP |CHAP hitelesítő adatok létrehozása után nem lesznek eltávolítva. Emellett ha CHAP hitelesítő adatok módosításához módosítania a kötetek offline állapotba, és hogy azok online, a változtatás érvénybe léptetéséhez. |Ezzel a problémával egy későbbi kiadásban. |
| **13.** |az iSCSI-kiszolgáló |A "használt tárolási" az iSCSI-kötet jelenik meg a StorSimple Device Manager szolgáltatás és az iSCSI-gazdagép eltérőek lehetnek. |Az iSCSI-gazdagép a fájlrendszer nézet tartozik.<br></br>Az eszköz látja, ha a kötet maximális méret lefoglalt blokkok. |
| **14.** |Fájlkiszolgáló |Ha egy fájlt egy mappában van egy alternatív Data Stream (ADS) társítva, a HIRDETÉSEK nem biztonsági mentése vagy visszaállítása vész-helyreállítási, a Klónozás és az elem szintű helyreállítás. | |
| **15.** |Fájlkiszolgáló |Nem támogatja a szimbolikus csatolást. | |
| **16.** |Fájlkiszolgáló |Fájlok által Windows titkosított fájlrendszer (EFS) keresztül másolását védett, vagy tárolja a StorSimple virtuális tömb fájl server eredmény beállításai nem támogatottak.  | |

## <a name="next-step"></a>Következő lépés
[A frissítés 0,5](storsimple-virtual-array-install-update-05.md) a StorSimple virtuális tömbben.

## <a name="references"></a>Referencia
Egy régebbi kiadási Megjegyzés keres? Ugrás:

* [A StorSimple virtuális tömb frissítés 0,4 kibocsátási megjegyzései](storsimple-virtual-array-update-04-release-notes.md)
* [A StorSimple virtuális tömb frissítés 0,3 kibocsátási megjegyzései](storsimple-ova-update-03-release-notes.md)
* [A StorSimple virtuális tömb frissítés 0,1 és 0,2 kibocsátási megjegyzései](storsimple-ova-update-01-release-notes.md)
* [A StorSimple virtuális tömb általános rendelkezésre állási kibocsátási megjegyzései](storsimple-ova-pp-release-notes.md)

