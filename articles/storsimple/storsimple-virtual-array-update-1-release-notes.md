---
title: A StorSimple Virtual Array 1.0-ás frissítés kibocsátási megjegyzései |} A Microsoft Docs
description: A StorSimple Virtual Array 1.0-ás frissítés futó kritikus fontosságú megoldatlan problémák és megoldásaik ismerteti.
services: storsimple
documentationcenter: ''
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: alkohli
ms.openlocfilehash: fdf37a8360ec69017458fabee2a9e16aa2c160aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "60789671"
---
# <a name="storsimple-virtual-array-update-10-release-notes"></a>A StorSimple Virtual Array 1.0-ás frissítés kibocsátási megjegyzései

## <a name="overview"></a>Áttekintés

A következő kiadási megjegyzések a kritikus fontosságú megoldatlan problémák és a Microsoft Azure StorSimple Virtual Array frissítések megoldott problémák azonosításához.

A kibocsátási megjegyzésekben folyamatosan frissülnek, és ahogy ismertté kritikus problémák adódnak. A StorSimple Virtual Array üzembe helyezése, előtt alaposan tekintse át a kibocsátási megjegyzésekben található információkat.

1\.0-ás frissítés felel meg a szoftververzió **10.0.10296.0**.

> [!IMPORTANT]
> - Azok zavart okozó frissítések, és indítsa újra az eszközt. I/o van folyamatban, ha az eszköz leállást. A frissítés alkalmazása részletes utasításokért ugorjon [frissítése 1.0 telepítése](storsimple-virtual-array-install-update-1.md).
>
> - 1\. frissítés csak akkor használható, hogy az Azure Portalon keresztül, ha az eszköz fut a 0.6-os frissítés.

## <a name="whats-new-in-update-10"></a>1\.0-ás frissítés újdonságai

**1.0-ás frissítés a StorSimple-Eszközkezelő szolgáltatás hitelesítéssel kapcsolatos módosításokat tartalmaz, és üzembe szeretné helyezni, a lehető legrövidebb időn belül.** Ez a frissítés tartalmazza a következő fejlesztéseket és hibajavítások:

 - **Használja az Azure Active Directory (AAD) hitelesíteni a StorSimple-Eszközkezelő szolgáltatással** – az 1.0-ás frissítés és újabb verziók esetében, az Azure Active Directory segítségével hitelesíteni a StorSimple-Eszközkezelő szolgáltatással. A régi hitelesítési mechanizmust 2017 December elavulttá válik. Minden felhasználó be a tűzfalszabályok tartalmaznia kell az új hitelesítési URL-címeket. További információkért nyissa meg a felsorolt hitelesítési URL-címek a [a StorSimple Virtual Array hálózati követelményei](storsimple-ova-system-requirements.md).
 
    A hitelesítési URL-cím nem szerepel a tűzfalszabályokat, ha a felhasználók, amely a StorSimple-eszköz nem sikerült hitelesíteni a szolgáltatás kritikus riasztás jelenik meg. A felhasználó ezt a riasztást lát, szükség esetén az új hitelesítési URL-címét. További információért ugorjon [riasztások hálózatkezelés StorSimple](storsimple-virtual-array-manage-alerts.md).

 - **A teljesítmény javítása** -számos hibajavítást elkészült, a felhőalapú olvasási, rétegek és írásánál sebesség javítása érdekében. Ennek eredményeképpen a biztonsági mentési és helyreállítási teljesítmény javult iSCSI- és kiszolgáló-eszközökhöz.

 - **Szemétgyűjtési gyűjtemény fokozása** – ebben a kiadásban rendelkezik, amely a szemétgyűjtési adatgyűjtési ciklus, amikor az eszköz- és storage-fiók el két távoli régióban teljesítménye növelhető, hibajavításokat tartalmaz.

 - **Naplózás fokozása** – ebben a kiadásban a szemétgyűjtés és i/o-elérési út kapcsolódó naplózási érintő fejlesztéseket tartalmaz.


## <a name="issues-fixed-in-update-10"></a>Az 1.0-ás frissítés megoldott problémák

Az alábbi táblázat hibáinak javításai ebben a kiadásban összegzését tartalmazza.

| Nem. | Funkció | Probléma |
| --- | --- | --- |
| 1 |AAD-alapú hitelesítés| Ez a kiadás, amely lehetővé teszi az AAD hitelesíteni a StorSimple Device Manager a módosításokat tartalmaz.|
| 2 |A szemétgyűjtés| A probléma lett jelentve, ahol az eszköz és a storage-fiókok eltérő régióban van, és az ügyfél jelentett ezzel hatással lenne a számlázás átmeneti hálózati hibák ügyfél telephelyén található. Ebben a kiadásban a probléma a rendszerriasztások mechanizmusában. |
| 3 |Teljesítmény| Ebben a kiadásban eredményez a visszaállítás/felhőbeli olvasás/szint / réteg teljesítményének fokozása ki módosításokat tartalmaz.|
| 4 |frissítés| Hiba történt a frissítés a korábbi kiadásokban az ügyfél telephelyén található biztonsági mentési hibák eredményező. Ebben a kiadásban a probléma megoldódik.|

## <a name="known-issues-in-update-10"></a>1\.0-ás frissítés ismert problémái

Az alábbi táblázat a StorSimple Virtual Array az ismert problémák összegzését tartalmazza, és a kiadási jelezve a korábbi kiadásokban a problémák tartalmazza.

| Nem. | Funkció | Probléma | Megkerülő megoldás és megjegyzések |
| --- | --- | --- | --- |
| **1.** |Frissítések |A virtuális tömbök az előzetes kiadásban létrehozott nem lehet frissíteni egy támogatott általánosan elérhető verzióra. |Ezek a virtuális tömbök kell végrehajtani a feladatátvételt a végleges kiadás vész-helyreállítási munkafolyamat használatával. |
| **2.** |Kiépített adatlemez |Egyszer ellátta adatlemez bizonyos megadott méretű és hozta létre a megfelelő StorSimple Virtual Array, kell nem bontsa ki a vagy az adatlemez zsugorítani. Elvesztését eredményezi, a helyi rétegeken az eszköz összes adatának tegye kísérletet. | |
| **3.** |Csoportházirend |Ha egy eszköz a tartományhoz, a Csoportházirend alkalmazása kedvezőtlen hatással lehet az eszköz műveletet. |Győződjön meg arról, hogy a virtuális tömb a saját szervezeti egység (OU) az Active Directory, és nem a csoportházirend-objektumok (GPO) beállítva rajta. |
| **4.** |Helyi webes felhasználói felületen |Ha az Internet Explorer (IE ESC) engedélyezve vannak a fejlett biztonsági funkcióknak, előfordulhat, hogy néhány helyi webes felhasználói Kezelőfelületi lapok például hibaelhárítás vagy karbantartási nem működik megfelelően. Gombok ezeken a lapokon is előfordulhat, hogy nem működik. |Kapcsolja ki az Internet Explorer fokozott biztonsági funkciók. |
| **5.** |Helyi webes felhasználói felületen |Hyper-V virtuális gépen a hálózati adapterek, a webes felhasználói felületén jelennek meg, 10 GB/s felületeihez. |Ez a viselkedés a Hyper-V egy másolatát. A Hyper-V a virtuális hálózati adapterek 10 GB/s mindig látható. |
| **6.** |Rétegzett kötetek vagy megosztások |A rétegzett kötetek nem támogatott. a StorSimple együtt használható alkalmazások zárolása bájttartományt. Ha bájt tartomány zárolás engedélyezve van, nem StorSimple rétegezést működik. |Ajánlott a mértékeket tartalmazza: <br></br>Kapcsolja ki az alkalmazáslogika a zárolás bájttartományt.<br></br>Válassza ki az alkalmazáshoz tartozó adatokat a rétegzett kötetek ellentétben a gyors helyi kötetek kell.<br></br>*Ismeret*: Ha engedélyezve van az bájt tartomány zárolás használatával helyileg rögzített kötetekről, a helyileg rögzített kötet lehet online még a visszaállítás befejezése előtt. Ezekben az esetekben ha a visszaállítás van folyamatban, majd meg kell várnia a visszaállítás befejeződését. |
| **7.** |Rétegzett megosztás |Nagy fájlok használata lassú réteg felskálázása eredményezhet. |Ha nagy méretű fájlok dolgozik, azt javasoljuk, hogy a megosztás méretének % 3-nál kisebb-e a legnagyobb fájlt. |
| **8.** |Használt kapacitás megosztások |Látni fogyasztás megoszthatja, amikor nem szerepel megjeleníthető adat a megosztáson. Ez a felhasználás azért, hogy a használt kapacitás megosztások metaadatokat tartalmaz. | |
| **9.** |Vészhelyreállítás |Csak az ugyanahhoz a tartományhoz, mint a forráseszközt a fájlkiszolgáló vész-helyreállítási hajtható végre. Ebben a kiadásban nem támogatott a vész-helyreállítási cél eszközhöz egy másik tartományban található. |Ez egy későbbi kiadástól kezdve van megvalósítva. További információért ugorjon [feladatátvétel és vészhelyreállítás a StorSimple Virtual Array helyreállítása](storsimple-virtual-array-failover-dr.md) |
| **10.** |Azure PowerShell |A StorSimple Virtual Arrayt az Azure PowerShell ebben a kiadásban nem kezelhetők. |A virtuális eszközök kezeléséhez az összes az Azure portal és a helyi webes felületén keresztül kell elvégezni. |
| **11.** |Jelszó módosítása |A virtuális tömb eszköz konzol csak fogad bemeneti az en-us billentyűzet formátum. | |
| **12.** |CHAP |A CHAP hitelesítő adatok ezután nem lehet eltávolítani. Emellett ha módosítja a CHAP hitelesítő adatokat, meg kell le a köteteket, és hogy azok online, a módosítás érvénybe léptetéséhez. |Ezzel a problémával egy későbbi kiadástól kezdve. |
| **13.** |iSCSI server |A "használt tárolási" az iSCSI-kötet jelenik meg a StorSimple-Eszközkezelő szolgáltatás és az iSCSI-gazdagép eltérő lehet. |Az iSCSI-gazdagép rendelkezik a fájlrendszer nézetet.<br></br>Az eszköz látja, ha a kötet volt a maximális méret lefoglalt blokkok. |
| **14.** |Fájlkiszolgáló |Ha egy fájl egy mappában van egy másik Data Stream (ADS) társítva, a HIRDETÉSEK nem biztonsági mentése vagy visszaállítása vész-helyreállítási, klónozás és az elemszintű helyreállítás. | |
| **15.** |Fájlkiszolgáló |Szimbolikus hivatkozások nem támogatottak. | |
| **16.** |Fájlkiszolgáló |Windows titkosított fájlrendszer (EFS által) során átmásolt védett, vagy a StorSimple Virtual Array fájl kiszolgáló eredményt konfigurációja nem támogatott tárolt fájlokat.  | |
| **17.** |Frissítések |Ha látja-e hiba kódja: 2359302 (hexadecimális 0x240006) közben a helyi felhasználói felületen gyorsjavítást telepíteni, majd ez azt jelenti, hogy a gyorsjavítás telepítve van az eszközön.   | |
| **18.** |Frissítések |Ha 1. frissítés telepítése a virtuális tömb helyi webes felületén használ, győződjön meg róla, hogy futnak-e a 0.6-os frissítés. Futtat egy verziója alacsonyabb, mint a 0.6-os frissítés, ha kell, először telepítse a 0.6-os frissítés, és ezután alkalmazza az 1. frissítés. Ha a frissítés előtti a 0.6-os verzióról 1.0-ás frissítés közvetlenül telepíteni, majd lesz lemarad néhány frissítést, és a figyelési diagramjait nem fog működni.   | |


## <a name="next-steps"></a>További lépések
[Telepítse az 1.0-ás frissítés](storsimple-virtual-array-install-update-1.md) az a StorSimple Virtual Array.

## <a name="references"></a>Referencia
Egy régebbi kiadási Megjegyzés keres? Ugrás:
*  [A StorSimple Virtual Array frissítés a 0.6-os kibocsátási megjegyzései](storsimple-virtual-array-update-06-release-notes.md)
* [A StorSimple Virtual Array frissítés 0,5 kibocsátási megjegyzései](storsimple-virtual-array-update-05-release-notes.md)
* [A StorSimple Virtual Array frissítés 0,4 kibocsátási megjegyzései](storsimple-virtual-array-update-04-release-notes.md)
* [A StorSimple Virtual Array frissítés 0,3 kibocsátási megjegyzései](storsimple-ova-update-03-release-notes.md)
* [A StorSimple Virtual Array frissítés 0.1, 0.2-es és kibocsátási megjegyzések](storsimple-ova-update-01-release-notes.md)
* [A StorSimple Virtual Array általános rendelkezésre állási kibocsátási megjegyzései](storsimple-ova-pp-release-notes.md)
