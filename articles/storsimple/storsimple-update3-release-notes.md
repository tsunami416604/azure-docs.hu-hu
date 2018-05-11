---
title: A StorSimple 8000 Series Update 3 kibocsátási megjegyzései |} Microsoft Docs
description: Az új funkciók, problémák és megoldások ismerteti a StorSimple 8000 Series Update 3.
services: storsimple
documentationcenter: NA
author: alkohli
manager: jeconnoc
editor: ''
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 9308602b77903e553e26d6f0586d963a4c58230d
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Frissítse a StorSimple 8000 series eszköz 3 kibocsátási megjegyzései

## <a name="overview"></a>Áttekintés
Az alábbi kibocsátási megjegyzések új szolgáltatásait ismerteti, és a StorSimple 8000 Series Update 3 a kritikus megnyitott problémák azonosításához. Ebben a kiadásban szereplő StorSimple szoftverfrissítések listáját is tartalmaznak. 

Frissítés 3 bármely kiadásban (GA) vagy a frissítés 0,1 futtató frissítés 2.2 keresztül StorSimple eszközre alkalmazhatók. Az eszköz frissítése 3 társított verziója 6.3.9600.17759.

Tekintse át a kibocsátási megjegyzések a StorSimple megoldásban a frissítés telepítése előtt található információkat.

> [!IMPORTANT]
> * Frissítés 3 eszközhöz, LSI illesztőprogram és a belső vezérlőprogram és Storport és Spaceport frissíti. Ez a frissítés körülbelül 1,5-2 órát vesz igénybe. 
> * Új kiadásokban nem láthatók frissítések azonnal, mert a frissítések fázisokra bontva történő bevezetéséhez végezzük. Néhány napot várni, és a frissítések újra ezeket a majd vizsgálatát hamarosan elérhető lesz.
> 
> 

## <a name="whats-new-in-update-3"></a>What's new in Update 3
A következő fontos fejlesztést tartalmaz, és hibajavítások Update 3 végzett.

* **Automatikus terület-visszanyerést módosítások** – indítása Update 3, a terület-visszanyerést algoritmusok futtassa a gyorsabb végrehajtását a rendszer a készenléti vezérlőn. A terület-visszaigényléssel működéséhez szükséges portokon további információkért tekintse meg a [hálózati követelményei StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **A teljesítményt javító** – Update 3 javult a felhőbe olvasási és írási teljesítmény.
* **Áttelepítéssel kapcsolatos fejlesztések** – ebben a kiadásban több programhiba javítások és fejlesztések volt szükség áttelepítés funkció 5000/7000-es adatsorozat eszközökről, 8000 sorozat eszközeire. Az áttelepítés szolgáltatásának használatáról további információkért látogasson el [8000 sorozat eszköz 5000/7000-es adatsorozat eszköz áttelepítés](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Figyelési kapcsolódó javítások** – ebben a kiadásban hibák diagramokat, a szolgáltatás irányítópultján, figyelés és a kapcsolódó eszköz irányítópult javítva lett.

## <a name="issues-fixed-in-update-3"></a>A frissítés 3 megoldott problémák
A következő táblázatok frissítése 3. javított problémák összegzését tartalmazza.    

| Nem | Szolgáltatás | Probléma | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Gazdagép-oldali adatok áttelepítése |A korábbi verzióban a StorSimple felhő készülék lett offline állapotra vált, a gazdagép-oldali adatok áttelepítése során. Ez a probléma fennáll ebben a kiadásban. |Nem |Igen |
| 2 |Helyileg rögzített kötetek |A korábbi változatban nem voltak i/o hibák, a kötet konvertálási hibák és a helyileg rögzített kötetekhez datapath sikertelen kapcsolatos problémákat. Ezek a problémák legfelső szintű okozott és rögzített ebben a kiadásban. |Igen |Nem |
| 3 |Figyelés |Nem voltak egységek reporting és figyelési eszközök irányítópult diagramok ahol megfelelő információkat a helyileg rögzített kötetekhez jelent meg, valamint több problémáiról. Ebben a kiadásban ezek a problémák kerültek. |Igen |Nem |
| 4 |Nagy mennyiségű írási i/o |StorSimple használata esetén a nagy írási műveletek munkaterhelések használatakor a felhasználó fog futni egy alkalomszerű hiba be ahol munkakészletének volt folyamatban rétegzett kiterjeszti a felhőbe. A kijavítanak ebben a kiadásban. |Igen |Igen |
| 5 |Backup |Bizonyos ritka esetekben a szoftver, a korábbi verzióiban felhasználó elvégez egy távoli Klónozás biztonsági másolatot, ha azok volna hibákba felhő, és a művelet akkor hibaüzenetet a. Ebben a kiadásban javítani a hibát, és a művelet sikeresen befejeződik. |Igen |Igen |
| 6 |Biztonsági mentési házirend |Bizonyos esetekben a ritka a szoftver, a korábbi kiadásokban történt a biztonsági mentési házirend törlésének kapcsolatos hiba. Ez a probléma fennáll ebben a kiadásban. |Igen |Igen |

## <a name="known-issues-in-update-3"></a>A frissítés 3 ismert problémák
A következő táblázat összefoglalja az ismert problémákról, ebben a kiadásban.

| Nem. | Szolgáltatás | Probléma | Megjegyzések / megoldás | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Lemez kvórum |Ritka esetekben ha a legtöbb, a EBOD ház egy 8600 eszköz lemezek megszakadt a kapcsolat nincs lemez kvórum eredményezi majd a tárolókészlet kerül offline. Offline állapotban maradnak, még akkor is, ha a lemez újracsatlakoztatását. |Szüksége lesz az eszköz újraindul. Ha a probléma továbbra is fennáll, forduljon a Microsoft Support további lépéseket. |Igen |Nem |
| 2 |Helytelen vezérlő azonosítója |A vezérlő helyettesítő végrehajtásakor vezérlő 0 lehet, hogy megjelennek, a vezérlő 1. Tartományvezérlő csere közben, amikor a kép betöltődik a társ-csomópont a vezérlő azonosítója is megjelennek kezdetben, a partner tartományvezérlő azonosítóját. Ritka esetben ez a viselkedés előfordulhat, hogy is látható. a rendszer újraindítását követően. |Nincs felhasználói beavatkozásra szükség. Ez a helyzet magától megoldódik, amikor a vezérlő helyettesítő befejeződése után. |Igen |Nem |
| 3 |Tárfiókok |A Storage szolgáltatás használatával törli a tárfiókot a nem támogatott lehetőséget. Ez a helyzet, amelyben felhasználói nem lehet adatokat beolvasni irányítja. | |Igen |Igen |
| 4 |Eszköz feladatátvétel |A különböző céleszközökre a azonos forráseszközről származó kötettároló több feladatátvétel nem támogatott. Több eszköz egyetlen elhalt eszközről feladatátvétel működésképtelenné teszi az adatok tulajdonjoga elvesztése átadja a feladatokat eszköz első kötettárolók. Egy feladatátvétel után ezek kötettárolók jelenik meg, vagy a klasszikus Azure portálon meg eltérően viselkednek. | |Igen |Nem |
| 5 |Telepítés |Során StorSimple Adapter SharePoint telepítéshez meg kell adnia egy eszköz IP-cím ahhoz, hogy a telepítés sikeres befejezéséhez. | |Igen |Nem |
| 6 |Webproxy |Ha a webproxy konfigurálása a megadott protokoll HTTPS-t, az eszköz-szolgáltatások közötti kommunikáció néven érinti, és kerül, hogy az eszköz offline állapotban. Támogatási csomag is hozható létre a folyamat során fel jelentős erőforrásokat az eszközön. |Győződjön meg arról, hogy a webalkalmazás-proxy URL-címe HTTP megadott protokollt. További információ: [Configure web proxy for your device](storsimple-8000-configure-web-proxy.md) (Webproxy beállítása az eszközhöz). |Igen |Nem |
| 7 |Webproxy |Ha konfigurált és engedélyezett a webalkalmazás-proxy regisztrált egy eszközt, majd szüksége lesz az eszköz aktív vezérlőjén újraindítására. | |Igen |Nem |
| 8 |Magas felhő késéssel és nagy i/o-munkaterhelés |Amikor a StorSimple eszköz nagyon magas felhő késések (másodperc sorrendben) és a magas i/o-munkaterhelés észlel, az eszköz kötetek csökkent állapotba, és az i/o "az eszköz nem áll készen" hiba miatt sikertelen lehet. |Szüksége lesz, manuálisan indítsa újra a eszközvezérlők vagy egy eszköz feladatátvétel ebben a helyzetben helyreállítás végrehajtása. |Igen |Nem |
| 9 |Azure PowerShell |A StorSimple parancsmag használatakor **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object - először 1 - várakozási** jelölje ki az első objektumot, így hozhat létre egy új **VolumeContainer** objektum, a parancsmag az összes található objektumokat adja vissza. |A parancsmag burkolása zárójelek között az alábbiak szerint: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object - First 1 - várakozási** |Igen |Igen |
| 10 |Migrálás |Több kötet tároló áttelepítési lett átadva, az Európai legfrissebb biztonsági mentés esetén csak az első kötettároló pontos. Emellett a párhuzamos áttelepítés után az első kötettároló az első 4 biztonsági másolatok települnek indul. |Azt javasoljuk, hogy egyszerre több kötet tároló át. |Igen |Nem |
| 11 |Migrálás |A visszaállítás után kötetek nem adódnak hozzá a biztonsági mentési házirend vagy a virtuális lemez. |Szüksége lesz a biztonsági mentési házirend ahhoz, hogy készítsen biztonsági másolatot, ezek a kötetek hozzá. |Igen |Igen |
| 12 |Migrálás |Az áttelepítés befejezése után az adatsorozat 5000/7000-es eszköz nem hozzáférést kell az áttelepített adatok tárolókat. |Azt javasoljuk, hogy törölje az áttelepített adatok tárolókat, az áttelepítés befejezése és véglegesítése után. |Igen |Nem |
| 13 |Klónozás és vész-Helyreállítási |1. frissítést futtató a StorSimple eszköz nem klónozza vagy katasztrófa utáni helyreállítás 1 frissítés előtti szoftvert futtató eszközre. |Szüksége lesz a cél-eszköz frissítése 1 frissítése lehetővé ezen műveletek |Igen |Igen |
| 14 |Migrálás |Áttelepítés konfigurációs biztonsági másolat nem tud az adatsorozat 5000-7000-es eszközön, kötet csoportok nem társított kötetekkel vannak. |Nincs társított kötetek üres kötet csoportok törlése, és ismételje meg a konfigurációs biztonsági másolat. |Igen |Nem |
| 15 |Az Azure PowerShell-parancsmagok és a helyileg rögzített kötetek |Nem hozható létre egy helyileg rögzített kötet Azure PowerShell-parancsmagok használatával. (Azure PowerShell használatával hoz létre kötet lesz helyezhető el.) |Mindig a StorSimple Manager szolgáltatás segítségével konfigurálhatja a helyileg rögzített kötetekhez. |Igen |Nem |
| 16 |A helyileg rögzített kötetekhez szabad terület |Ha töröl egy helyileg rögzített kötetet, a szabad lemezterület az új kötetekre esetleg nem azonnal frissíti. A StorSimple Manager szolgáltatás körülbelül óránként frissíti a rendelkezésre álló helyi területet. |Várjon, amíg egy óraba az új kötet létrehozásakor. |Igen |Nem |
| 17 |Helyileg rögzített kötetek |A visszaállítási feladat mutatja meg az ideiglenes pillanatkép biztonsági másolatából a biztonságimásolat-katalógus, de csak a visszaállítási feladat időtartama. Emellett elérhetővé teszi a virtuális lemez csoport előtaggal rendelkező **tmpCollection** a a **biztonsági mentési házirendek** oldalon, de csak a visszaállítási feladat időtartamára. |Ez akkor fordulhat elő, ha a visszaállítási feladat csak a helyi számítógépre van rögzítve, kötetek vagy a helyileg rögzített és a rétegzett kötetek kombinációját. Ha a visszaállítási feladat csak a rétegzett kötetek is tartalmazza, majd ezt a viselkedést nem történik. Meg kell adni a felhasználói beavatkozás nélkül. |Igen |Nem |
| 18 |Helyileg rögzített kötetek |Ha a helyreállítási feladat megszakítása, a tartományvezérlő feladatátvétel közvetlenül ezek után jelennek meg a helyreállítási feladat **sikertelen** helyett **visszavonva**. Ha a helyreállítási feladat sikertelen lesz, a tartományvezérlő feladatátvétel közvetlenül ezek után jelennek meg a helyreállítási feladat **visszavonva** helyett **sikertelen**. |Ez akkor fordulhat elő, ha a visszaállítási feladat csak a helyi számítógépre van rögzítve, kötetek vagy a helyileg rögzített és a rétegzett kötetek kombinációját. Ha a visszaállítási feladat csak a rétegzett kötetek is tartalmazza, majd ezt a viselkedést nem történik. Meg kell adni a felhasználói beavatkozás nélkül. |Igen |Nem |
| 19 |Helyileg rögzített kötetek |Ha a helyreállítási feladat megszakítása vagy a visszaállítás sikertelen lesz, majd a tartományvezérlő feladatátvétel, ha egy további visszaállítási feladat jelenik meg a **feladatok** lap. |Ez akkor fordulhat elő, ha a visszaállítási feladat csak a helyi számítógépre van rögzítve, kötetek vagy a helyileg rögzített és a rétegzett kötetek kombinációját. Ha a visszaállítási feladat csak a rétegzett kötetek is tartalmazza, majd ezt a viselkedést nem történik. Meg kell adni a felhasználói beavatkozás nélkül. |Igen |Nem |
| 20 |Helyileg rögzített kötetek |Ha a rétegzett kötetek (létrehozott és 1.2-es frissítés a klónozott vagy korábbi) átalakítása egy helyileg rögzített kötetet próbál és az eszköz nincs elegendő szabad terület, vagy egy felhőalapú leállás van, a clone(s) is megsérült. |A probléma csak a köteteket, amelyek a létrehozott és a klónozott frissítés előtti 2.1 szoftvert. Ez egy ritka eset legyen. | | |
| 21 |Kötet átalakítás |Nem frissíthető a kötet csatlakozik, amíg folyamatban van egy kötet átalakítás ACRs (helyileg rögzített rétegzett vagy fordítva). A ACRs frissítése adatsérülést eredményezhet. |Szükség esetén frissítse a mennyiségi átalakítás előtti ACRs, és ne végezzen további ACR frissítések az átalakítás ideje alatt megszakadnak. | | |
| 22 |Frissítések |Frissítés 3 alkalmazásakor a **karbantartási** a klasszikus Azure portálon oldal jelenik meg a következő üzenet Update 2 kapcsolódó – "a StorSimple 8000 series Update 2 lehetővé teszi a Microsoft proaktív gyűjthet naplózási adatok az eszközről, ha azt észleli a potenciális problémákat a". Ez félrevezető módon azt jelzi, hogy az eszköz frissítése folyamatban frissítés 2. Miután az eszköz a frissített Update 3 succeesfully, ez az üzenet véglegesen eltűnik. |Ez a viselkedés egy későbbi kiadásban lesz kijavítva. |Igen |Nem |

## <a name="controller-and-firmware-updates-in-update-3"></a>A tartományvezérlő és a belső vezérlőprogram frissítések Update 3.
Ebben a kiadásban rendelkezik LSI illesztőprogram és a belső vezérlőprogram frissítések. A LSI illesztőprogram és a belső vezérlőprogram-frissítésekre telepítéséről további információkért lásd: [Update 3 telepítéséhez](storsimple-install-update-3.md) a StorSimple eszköz.

## <a name="virtual-device-updates-in-update-3"></a>A frissítés 3 virtuális eszköz frissítése
A frissítés nem alkalmazható a StorSimple felhő készülék (más néven a virtuális eszköz). Új virtuális eszközök kell létrehozni. 

## <a name="next-step"></a>Következő lépés
Megtudhatja, hogyan [Update 3 telepítéséhez](storsimple-install-update-3.md) a StorSimple eszköz.

