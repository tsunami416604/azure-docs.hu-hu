---
title: "A StorSimple virtuális tömb 1.0-s frissítésében kibocsátási megjegyzései |} Microsoft Docs"
description: "Kritikus megnyitott problémák és megoldások ismerteti a StorSimple virtuális tömb futtató 1.0-s frissítésében."
services: storsimple
documentationcenter: 
author: alkohli
manager: jeconnoc
editor: 
ms.assetid: 
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: 777718c4893f1edab3613be5dc941e2716d4c972
ms.sourcegitcommit: 295ec94e3332d3e0a8704c1b848913672f7467c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/06/2017
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>A StorSimple virtuális tömb 1.0-s frissítésében kibocsátási megjegyzései

## <a name="overview"></a>Áttekintés

Az alábbi kibocsátási megjegyzések a kritikus megnyitott problémák és a Microsoft Azure StorSimple virtuális tömb frissítéseket megoldott problémák azonosításához.

A kibocsátási megjegyzések folyamatosan frissülnek, és megoldást igénylő kritikus fontosságú problémáit ismertté hozzáadásuk után. A StorSimple virtuális tömb telepítése előtt gondosan tekintse át a kibocsátási megjegyzésekben található információkat.

Frissítés 1.0 megfelel-e a szoftververzió **10.0.10296.0**.

> [!IMPORTANT]
> - Frissítések zavaró, és indítsa újra az eszközt. I/o van folyamatban, ha az eszköz azt eredményezi azok, háromszorosa a leállás. A frissítés alkalmazásához a részletes útmutatást Itt [frissítése 1.0 telepítése](storsimple-virtual-array-install-update-1.md).
>
> - 1. frissítés csak akkor az Azure-portálon elérhető, ha az eszköz fut-e frissítés 0,6.

## <a name="whats-new-in-update-10"></a>What's new in 1.0-s frissítésében

**Frissítés 1.0 StorSimple Device Manager szolgáltatás hitelesítéssel kapcsolatos módosításokat tartalmaz, és kell telepíteni a adatairól.** A frissítés a következő és hibajavításokat tartalmaz:

 - **Használja az Azure Active Directory (AAD) a StorSimple eszköz Manager szolgáltatásban való hitelesítéshez szükséges** – a frissítés 1.0-s és újabb verziók esetében, az Azure Active Directory segítségével hitelesíti a StorSimple eszköz Manager szolgáltatásban. A régi hitelesítési mechanizmus December 2017 elavulttá válik. A felhasználók tartalmaznia kell az új hitelesítési URL-címek be a tűzfalszabályok között. További információkért látogasson el a felsorolt hitelesítési URL-címek a [hálózatkezelési követelményei a StorSimple virtuális tömb](storsimple-ova-system-requirements.md).
 
    A hitelesítés URL-címe nem szerepel a tűzfalszabályok, a felhasználók látják a kritikus riasztás, amely a StorSimple-eszköz nem tudta hitelesíteni a szolgáltatással. Ha a felhasználók ezt a figyelmeztetést, az új hitelesítési URL-cím szükséges. További információkért látogasson el [riasztások hálózati StorSimple](storsimple-virtual-array-manage-alerts.md).

 - **A teljesítmény javítása** -számos hibajavítást volt kész felhő olvasások, a réteg beépülő modulok és a réteg mintáit sebesség növelése érdekében. Ennek eredményeképpen a biztonsági mentés és a visszaállítási teljesítménye javult iSCSI és a fájl-kiszolgálói eszközök.

 - **Szemétgyűjtési gyűjtemény fokozása** -ebben a kiadásban, ha az eszköz és a tárolási fiók van két távoli régió szemétgyűjtési adatgyűjtési ciklus teljesítményének javítása hibajavítások rendelkezik.

 - **Naplózási fokozása** – Ez a kiadás naplózási szemétgyűjtés és i/o-elérési út kapcsolatos fejlesztéseket tartalmaz.


## <a name="issues-fixed-in-update-10"></a>A frissítés 1.0 megoldott problémák

A következő táblázat az ebben a kiadásban javított összegzését tartalmazza.

| Nem. | Szolgáltatás | Probléma |
| --- | --- | --- |
| 1 |Az AAD-alapú hitelesítés| Ez a kiadás, amely lehetővé teszi az AAD hitelesítéséhez a StorSimple Device Manager a módosításokat tartalmaz.|
| 2 |A szemétgyűjtés| Probléma történt, ha az eszköz- és tárolási fiókok különböző régiókban és az ügyfél így érintő számlázási időszakos hálózati hibákról küldött jelentést ügyfél telephelyén található. Ebben a kiadásban a hiba javítását. |
| 3 |Teljesítmény| Ebben a kiadásban a felhőhöz vagy visszaállítási olvasás/rétegű eredményez / kimenő teljesítményjavulást réteg módosításokat tartalmaz.|
| 4 |Frissítés| Hiba történt a frissítés a korábbi verzióban ügyfél telephelyén található biztonsági mentési hibák eredményező kapcsolatos problémát. Ez a probléma fennáll ebben a kiadásban.|

## <a name="known-issues-in-update-10"></a>A frissítés 1.0 ismert problémák

A következő táblázat összefoglalja az ismert problémákról a StorSimple virtuális tömb, és a problémák kiadás jelezve a korábbi verzióiról tartalmazza.

| Nem. | Szolgáltatás | Probléma | Megkerülő megoldás/megjegyzések |
| --- | --- | --- | --- |
| **1.** |Frissítések |Az előzetes létrehozott virtuális tömbök támogatott általánosan rendelkezésre álló verziója nem frissíthető. |Ezek a virtuális tömbök használata a vész-helyreállítási munkafolyamat általános rendelkezésre állási kiadásának kell feladatátvételt. |
| **2.** |Kiépített adatlemez |A megadott méretű adatlemezt kiépítése után, és hozott létre a megfelelő virtuális StorSimple-tömb, kell nem bontsa ki vagy adatlemeze zsugorítani. Kísérlet történt egy elvesztését eredményezi, a helyi rétegeken az eszköz összes adatának tegye. | |
| **3.** |Csoportházirend |Ha egy eszköz a tartományhoz, a Csoportházirend alkalmazása kedvezőtlen hatással lehet az eszköz műveletet. |Győződjön meg arról, hogy a virtuális tömb saját szervezeti egységben (OU) az Active Directory és nem a csoportházirend-objektumok (GPO) vonatkoznak rá. |
| **4.** |Helyi webes felhasználói felület |A fokozott biztonság funkció engedélyezése az Internet Explorerben (IE ESC), előfordulhat, hogy egyes helyi felhasználói felület weboldalak például hibaelhárítás vagy karbantartási nem működik megfelelően. Gombok ezeken a lapokon nem is működnek. |Kapcsolja ki az Internet Explorer fokozott biztonsági funkciók. |
| **5.** |Helyi webes felhasználói felület |A Hyper-V virtuális gépen a hálózati adapterek, a webes felhasználói felületén jelennek meg, 10 GB/s felületeihez. |Ez a viselkedés a Hyper-V tükre. Hyper-V mindig látható a virtuális hálózati adapterek 10 GB/s. |
| **6.** |Rétegzett kötetek vagy megosztások |A rétegzett kötetek nem támogatott StorSimple együtt használható alkalmazások zárolást bájttartomány. Ha bájt tartomány zárolás engedélyezve van, nem StorSimple rétegezéséhez működik. |Ajánlott mértékeket tartalmazza: <br></br>Kapcsolja ki a bájttartomány zárolás a az alkalmazás logikáját.<br></br>Válassza ki az alkalmazás adatokat a rétegzett kötetek figyelésekor helyileg rögzített kötetekhez kell.<br></br>*Ismeret*: Ha engedélyezve van az bájt tartomány zárolás használatával helyileg rögzített kötetek, a helyileg rögzített kötet lehet online még a visszaállítás befejezése előtt. Ezekben az esetekben ha a helyreállítás van folyamatban, majd meg kell várnia a visszaállítás befejeződését. |
| **7.** |Rétegzett megosztások |Nagy fájlok használata a kimenő lassú réteg eredményezheti. |Amikor olyan nagy fájlok, azt javasoljuk, hogy a legnagyobb fájl a fájlmegosztás méretének % 3-nál kisebb. |
| **8.** |Használható kapacitás megosztások |Előfordulhat, hogy látja fogyasztás megosztani, ha nincs adat a megosztáson. A felhasználás az oka, hogy a megosztások használt kapacitás metaadatokat tartalmaz. | |
| **9.** |Vészhelyreállítás |Csak a vész-helyreállítási ugyanahhoz a tartományhoz, amely a forráseszközt fájlkiszolgáló végezheti el. A céleszközön egy másik tartományban való katasztrófa utáni helyreállítás nem támogatott ebben a kiadásban. |Ez egy későbbi kiadásban van megvalósítva. További információkért látogasson el [a StorSimple virtuális tömb a feladatátvételi és katasztrófa-helyreállítás](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |A StorSimple virtuális tömbök az Azure PowerShell ebben a kiadásban nem kezelhetők. |A virtuális eszközök minden felügyeleti az Azure portál és a helyi webes felhasználói felületen keresztül kell végezni. |
| **11.** |A jelszó módosítása |A virtuális tömb eszköz konzol csak fogad bemeneti az en-us billentyűzet formátumban. | |
| **12.** |CHAP |CHAP hitelesítő adatok létrehozása után nem lesznek eltávolítva. Emellett ha CHAP hitelesítő adatok módosításához módosítania a kötetek offline állapotba, és hogy azok online, a változtatás érvénybe léptetéséhez. |Ezzel a problémával egy későbbi kiadásban. |
| **13.** |az iSCSI-kiszolgáló |A "használt tárolási" az iSCSI-kötet jelenik meg a StorSimple Device Manager szolgáltatás és az iSCSI-gazdagép eltérőek lehetnek. |Az iSCSI-gazdagép a fájlrendszer nézet tartozik.<br></br>Az eszköz látja, ha a kötet maximális méret lefoglalt blokkok. |
| **14.** |Fájlkiszolgáló |Ha egy fájlt egy mappában van egy alternatív Data Stream (ADS) társítva, a HIRDETÉSEK nem biztonsági mentése vagy visszaállítása vész-helyreállítási, a Klónozás és az elem szintű helyreállítás. | |
| **15.** |Fájlkiszolgáló |Nem támogatja a szimbolikus csatolást. | |
| **16.** |Fájlkiszolgáló |Fájlok által Windows titkosított fájlrendszer (EFS) keresztül másolását védett, vagy tárolja a StorSimple virtuális tömb fájl server eredmény beállításai nem támogatottak.  | |
| **17.** |Frissítések |Ha a hiba kódja: 2359302 (hexadecimális 0x240006) közben a helyi felhasználói felületen gyorsjavítást telepíteni, majd ez azt jelenti, hogy a gyorsjavítás telepítve van az eszközön.   | |
| **18.** |Frissítések |Ha a helyi webes felhasználói felület segítségével 1. frissítés telepítése a virtuális tömbben, győződjön meg róla, hogy futnak-e frissítés 0,6. Ha verzióját egy kisebb, mint 0,6 frissíteni, akkor először telepítse a frissítést 0,6, és ezután alkalmazza az 1. frissítés. Ha közvetlenül telepíteni 1.0-s frissítésében frissítés előtti 0,6 verziójából származó, majd néhány frissítést nem teljesíti, és a figyelési diagramok nem fog működni.   | |


## <a name="next-steps"></a>Következő lépések
[A frissítés 1.0](storsimple-virtual-array-install-update-1.md) a StorSimple virtuális tömbben.

## <a name="references"></a>Referencia
Egy régebbi kiadási Megjegyzés keres? Ugrás:
*  [A StorSimple virtuális tömb frissítés 0,6 kibocsátási megjegyzései](storsimple-virtual-array-update-06-release-notes.md)
* [A StorSimple virtuális tömb frissítés 0,5 kibocsátási megjegyzései](storsimple-virtual-array-update-05-release-notes.md)
* [A StorSimple virtuális tömb frissítés 0,4 kibocsátási megjegyzései](storsimple-virtual-array-update-04-release-notes.md)
* [A StorSimple virtuális tömb frissítés 0,3 kibocsátási megjegyzései](storsimple-ova-update-03-release-notes.md)
* [A StorSimple virtuális tömb frissítés 0,1 és 0,2 kibocsátási megjegyzései](storsimple-ova-update-01-release-notes.md)
* [A StorSimple virtuális tömb általános rendelkezésre állási kibocsátási megjegyzései](storsimple-ova-pp-release-notes.md)
