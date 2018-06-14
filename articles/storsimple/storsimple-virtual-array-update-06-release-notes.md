---
title: A StorSimple virtuális tömb frissítés 0,6 kibocsátási megjegyzései |} Microsoft Docs
description: A StorSimple virtuális tömb 0,6 Update futtatása kritikus megnyitott problémák és megoldásuk ismertetése
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
ms.openlocfilehash: af202cf652300ee7897eb2dede33f38058fc2837
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
ms.locfileid: "23876438"
---
# <a name="storsimple-virtual-array-update-06-release-notes"></a>A StorSimple virtuális tömb frissítés 0,6 kibocsátási megjegyzései

## <a name="overview"></a>Áttekintés

Az alábbi kibocsátási megjegyzések a kritikus megnyitott problémák és a Microsoft Azure StorSimple virtuális tömb frissítéseket megoldott problémák azonosításához.

A kibocsátási megjegyzések folyamatosan frissülnek, és megoldást igénylő kritikus fontosságú problémáit ismertté hozzáadásuk után. A StorSimple virtuális tömb telepítése előtt gondosan tekintse át a kibocsátási megjegyzésekben található információkat.

Frissítés 0,6 megfelel-e a szoftververzió **10.0.10293.0**.

> [!IMPORTANT]
> - Frissítések zavaró, és indítsa újra az eszközt. I/o van folyamatban, ha az eszköz azt eredményezi azok, háromszorosa a leállás. A frissítés alkalmazásához a részletes útmutatást Itt [0,6. számú frissítés](storsimple-virtual-array-install-update-06.md).
>
> - Határozottan javasoljuk, hogy azonnal tartalmaz, így a kritikus fontosságú biztonsági javítások 0,6 frissítés telepítése.


## <a name="whats-new-in-the-update-06"></a>A frissítés 0,6 újdonságai
Frissítés 0,6 fontos frissítése, és azonnal kell telepíteni. A frissítés a következő javításait tartalmazzák: 

- **Windows biztonsági javítások** -ebben a kiadásban rendelkezik **Windows kritikus fontosságú biztonsági javítások**. Tekintse át a biztonsági problémák és a kapcsolódó javításaival kapcsolatos további információt a következő biztonsági frissítések:
    - [2016. december csak minőségi biztonsági frissítés a Windows 8.1 és Windows Server 2012 R2](https://support.microsoft.com/help/3205400/december-2016-security-only-quality-update-for-windows-8.1-and-windows-server-2012-r2)
    - [Március 2017 csak minőségi biztonsági frissítés a Windows 8.1 és Windows Server 2012 R2](https://support.microsoft.com/help/4012213/march-2017-security-only-quality-update-for-windows-8-1-and-windows-server-2012-r23)
    - [2017. Előfordulhat, hogy 9 – KB4019213 (csak biztonsági frissítés)](https://support.microsoft.com/help/4019213/windows-8-update-kb4019213)

- **Állítsa vissza a javítás** -a korábbi kiadásokban merült fel a hiba, amely megakadályozná a visszaállítás befejezését. Ez a hiba kijavítása ebben a kiadásban.


## <a name="issues-fixed-in-the-update-06"></a>A frissítés 0,6 megoldott problémák

A következő táblázat az ebben a kiadásban javított összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |Biztonság| Ez a kiadás fontos Windows biztonsági frissítéseket tartalmaz. Javasoljuk, hogy a frissítés azonnal telepítését.|
| 2 |Visszaállítás| A visszaállítás során hiba történt, amely megakadályozza a visszaállítási feladat befejezését versenyhelyzet. A hibajavítás a versenyhelyzet megoldást.|


## <a name="known-issues-in-the-update-06"></a>A frissítés 0,6 ismert problémák

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
| **17.** |Frissítések |Ha a hiba kódja: 2359302 (hexadecimális 0x240006) közben a helyi felhasználói felületen gyorsjavítást telepíteni, majd ez azt jelenti, hogy a gyorsjavítás telepítve van az eszközön.   | |

## <a name="next-step"></a>Következő lépés
[A frissítés 0,6](storsimple-virtual-array-install-update-06.md) a StorSimple virtuális tömbben.

## <a name="references"></a>Referencia
Egy régebbi kiadási Megjegyzés keres? Ugrás:

* [A StorSimple virtuális tömb frissítés 0,5 kibocsátási megjegyzései](storsimple-virtual-array-update-05-release-notes.md)
* [A StorSimple virtuális tömb frissítés 0,4 kibocsátási megjegyzései](storsimple-virtual-array-update-04-release-notes.md)
* [A StorSimple virtuális tömb frissítés 0,3 kibocsátási megjegyzései](storsimple-ova-update-03-release-notes.md)
* [A StorSimple virtuális tömb frissítés 0,1 és 0,2 kibocsátási megjegyzései](storsimple-ova-update-01-release-notes.md)
* [A StorSimple virtuális tömb általános rendelkezésre állási kibocsátási megjegyzései](storsimple-ova-pp-release-notes.md)

