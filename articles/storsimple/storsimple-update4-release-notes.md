---
title: A StorSimple 8000 Series Update 4 kibocsátási megjegyzései |} A Microsoft Docs
description: Az új funkciók, problémák és megoldások ismerteti a StorSimple 8000 Series Update 4.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: ''
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/23/2018
ms.author: alkohli
ms.openlocfilehash: ef95ca7b9f94690b607e37fbf5d9378c2f2bcfda
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64698640"
---
# <a name="storsimple-8000-series-update-4-release-notes"></a>A StorSimple 8000 Series Update 4 kibocsátási megjegyzései

## <a name="overview"></a>Áttekintés

A következő kiadási megjegyzések az új funkciók ismertetik, és a StorSimple 8000 Series Update 4 a kritikus fontosságú megoldatlan problémák azonosításához. Ebben a kiadásban a StorSimple szoftver frissítések listáját is tartalmaznak. 

4\. frissítéssel bármilyen kiadásban elérhetővé tétel (GA) vagy 0.1-es frissítés keresztül frissítés 3.1-et futtató StorSimple-eszközre alkalmazhatók. Az eszközhöz társított Update 4 verziószáma 6.3.9600.17820.

Tekintse át a kibocsátási megjegyzések a StorSimple-megoldásban a frissítés telepítése előtt található információkat.

> [!IMPORTANT]
> * 4\. frissítéssel eszköz szoftverének, USM belső vezérlőprogram, LSI-illesztőprogram és a belső vezérlőprogram, lemezfirmware, Storport és Spaceport, biztonsági és más operációs rendszer frissítése rendelkezik. Ez a frissítés körülbelül 4 óra vesz igénybe. Lemezfirmware-frissítés zavart okozó frissítést, és az eszköz egy állásidőt eredményez. Azt javasoljuk, hogy alkalmazza-e az eszköz naprakészen tartani Update 4. 
> * Az új kiadásokhoz, akkor előfordulhat, hogy nem jelenik meg frissítések azonnal, mivel egy fázisokra bontva történő bevezetéséhez a frissítések végzünk. Néhány napot várni, és ezeket újra, majd frissítéskeresés hamarosan elérhetővé válik.

## <a name="whats-new-in-update-4"></a>What's new in Update 4

A következő kulcsfontosságú fejlesztések és hibajavítások az Update 4 került sor.

* **Intelligensebb automatikus terület-visszaigénylést algoritmusok** – a frissítés 4, az algoritmusokat a továbbfejlesztett módosítsa a hely visszanyerése ciklusok alapján a várható regenerált szabad terület a felhőben beállításainak automatikus terület-visszanyerést. 

* **A helyileg rögzített kötetekhez teljesítményjavítás** – Update 4 javult a teljesítmény helyileg rögzített kötetek forgatókönyvekben, amelyek magas adatbetöltés (kötet mérete hasonló adatok).

* **Intenzitástérkép-alapú visszaállítási** – a vészhelyreállítás (DR), a következő, a korábbi kiadásokban az adatok a felhőből az adathozzáférési mintáknak megfelelően lassú teljesítményt eredményez visszaállítva. 

    Egy új szolgáltatást tartalmazza az Update 4, hogy nyomon követi a gyakran használt adatok egy intenzitástérkép létrehozásához, amikor az eszköz előtt DR használatban van (leggyakrabban használt adattömbök nagy van, mivel kisebb használt adattömbök alacsony megadott hőtérképrészlet rendelkezik). DR-után a StorSimple az intenzitástérkép az automatikus visszaállítása és a felhőbeli adatok rehidratálási. 

    Az összes visszaállítások immár alapú intenzitástérkép visszaállítás. Hogyan alapú intenzitástérkép visszaállítása és a rehidratálás feladatok megszakítása és további információkért látogasson el [Windows PowerShell-parancsmag-referencia a StorSimple](https://technet.microsoft.com/library/dn688168.aspx).

* **A StorSimple-Diagnostics eszköz** – az Update 4, a StorSimple-Diagnostics eszköz kiadásra, hogy könnyen diagnosztizálásához és kapcsolatos hibák elhárítása kapcsolódó rendszer, a hálózat, a teljesítmény és a hardver összetevő állapotát. Ez az eszköz fut a Windows PowerShell storsimple-höz készült. További információért ugorjon [hibaelhárítása a StorSimple diagnosztikai eszköz használata](storsimple-8000-diagnostics.md).

* **Felhasználóifelület-alapú StorSimple áttelepítési eszköz** -előtt ebben a kiadásban 5000 – 7000 sorozatú származó adatok áttelepítése a felhasználók egy része a migrálási munkafolyamat az Azure PowerShell-felületén végrehajtásához szükséges. Ebben a kiadásban egy könnyen használható felületen StorSimple áttelepítési eszköz legyen elérhető támogatás megkönnyítése érdekében a migrálás ugyanabban a munkafolyamatban. Ez az eszköz esetén, a helyreállítási gyűjtő számára is lehetővé teheti. 

* **FIPS-vel kapcsolatos módosításokat** - e és újabb verziók esetében kiadás, FIPS az a StorSimple 8000 sorozatú eszközök alapértelmezés szerint engedélyezve van, a Microsoft Azure Government és az Azure nyilvános felhő fiókok esetében.

* **Frissítse a módosítások** – ebben a kiadásban a hibák frissítése kapcsolatos hibák kijavítása megtörtént.

* **Riasztás a lemezhiba** – ebben a kiadásban új riasztás, amely figyelmezteti a felhasználót a közelgő lemezhiba kerül. Ha ez a riasztás tapasztal, forduljon a Microsoft Support tehetnek a cserelemezt. További információért ugorjon [a StorSimple eszköz hardveres riasztások](storsimple-8000-manage-alerts.md#hardware-alerts).

* **Vezérlő cseréje módosítások** – ebben a kiadásban olyan parancsmagot, amely lehetővé teszi, hogy a felhasználót, hogy a tartományvezérlő cseréjét állapotának lekérdezése kerül. További információkért látogasson el a [lekérdezés vezérlő cseréje állapota parancsmag](https://technet.microsoft.com/library/dn688168.aspx).


## <a name="issues-fixed-in-update-4"></a>Az Update 4 megoldott problémák

Az alábbi táblázat a Update 4 javított hibák összegzését tartalmazza.    

| Nem | Funkció | Probléma | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Feladatátvétel |A korábbi kiadásokban a feladatátvétel után hiba történt az ügyfél telephelyén megfigyelt tisztítással kapcsolatos. Ebben a kiadásban a probléma megoldódik. |Igen |Igen |
| 2 |Helyileg rögzített kötetek |A korábbi változatban hiba történt a kapcsolódó kötetek létrehozását a helyileg rögzített kötetekhez, amely a kötet létrehozása hibákat eredményez. Ez a probléma alapvető oka volt, és javítva a kiadásban. |Igen |Nem |
| 3 |Támogatási csomag |Az előző verzió, a támogatási csomagot, amely egy System.OutOfMemory kivétel vagy egyéb támogatási csomag létrehozása hibát eredményez hibákat eredményez kapcsolatos problémák voltak. A programhibákat ebben a kiadásban. |Igen |Igen |
| 4 |Figyelés |Az előző verzió, a hiba diagramok helyileg monitoring szolgáltatással kapcsolatos probléma rögzített kötetekről ahol EB fogyasztás volt látható. Ez a hiba megoldódott-e ebben a kiadásban. |Igen |Igen |
| 5 |Migrálás |Előző kiadásban számos, a 8000-es sorozatú eszközökön 5000 – 7000 sorozatú áttelepítési megbízhatóságát kapcsolatos problémák voltak. Ezek a problémák rendelkezik foglalkoztak ebben a kiadásban. |Igen |Igen |
| 6 |frissítés |A korábbi kiadásokban, ha egy frissítési hiba lépett fel, a tartományvezérlők akkor lépnek helyreállítási módba, és ezért a felhasználó a frissítés nem folytatható, és forduljon a Microsoft Support kellene. <br> Ez a viselkedés ebben a kiadásban módosítva lett. Ha a felhasználó rendelkezik frissítési hiba fordul elő, ha mindkét vezérlő ugyanazt a verziót (4-es frissítést) futnak, a vezérlők nem lépnek helyreállítási módba. Ha a felhasználó ezt a hibát észlel, azt javasoljuk, hogy Várjon, amíg egy kis, és próbálkozzon újra a frissítést. Az újrapróbálkozás telepítés sikeres lehet. Ha az újrapróbálkozás nem sikerül, majd, forduljon a Microsoft Support. |Igen |Igen |


## <a name="known-issues-in-update-4-from-previous-releases"></a>A korábbi kiadások Update 4 ismert problémái

Nincsenek új ismert problémák az Update 4. A korábbi kiadások Update 4 áthozva problémák listája, nyissa meg [Update 3 kibocsátási megjegyzések](storsimple-update3-release-notes.md#known-issues-in-update-3).

## <a name="serial-attached-scsi-sas-controller-and-firmware-updates-in-update-4"></a>Soros csatlakozású SCSI (SAS) vezérlő és belső vezérlőprogramja frissítéseinek az a 4. frissítés

Ebben a kiadásban a SAS-vezérlő és LSI illesztőprogram és belső vezérlőprogramja frissítéseinek rendelkezik. Ezek a frissítések telepítésével kapcsolatos további információkért lásd: [telepítse Update 4-es](storsimple-install-update-4.md) a StorSimple eszközön.

## <a name="virtual-device-updates-in-update-4"></a>Az Update 4 virtuális eszköz frissítése

A StorSimple Cloud Appliance eszköz (más néven a virtuális eszköz) nem lehet alkalmazni ezt a frissítést. Új virtuális eszközök kell létrehozni. 

## <a name="next-step"></a>Következő lépés

Ismerje meg, hogyan [telepítse Update 4-es](storsimple-install-update-4.md) a StorSimple eszközön.

