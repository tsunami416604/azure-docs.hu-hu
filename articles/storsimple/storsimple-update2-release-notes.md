---
title: A StorSimple 8000 Series Update 2 kibocsátási megjegyzései |} Microsoft Docs
description: Az új funkciók, problémák és megoldások ismerteti a StorSimple 8000 Series Update 2.
services: storsimple
documentationcenter: NA
author: SharS
manager: carmonm
editor: ''
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: v-sharos
ms.openlocfilehash: 0667c62e2ee2039c2b96b8c4ba81619969c3611b
ms.sourcegitcommit: d28bba5fd49049ec7492e88f2519d7f42184e3a8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>A StorSimple 8000 Series Update 2 kibocsátási megjegyzései

## <a name="overview"></a>Áttekintés
Az alábbi kibocsátási megjegyzések új szolgáltatásait ismerteti, és a StorSimple 8000 Series Update 2 a kritikus megnyitott problémák azonosításához. A StorSimple szoftvert, az illesztőprogramok és a lemez belső vezérlőprogram-frissítésekre ebben a kiadásban szereplő listáját is tartalmazzák. 

2. frissítés bármely kiadásban (GA) vagy frissítés 0,1 futtató keresztül 1.2-es frissítés StorSimple eszközre alkalmazhatók. Az eszköz Update 2 társított verziója 6.3.9600.17673.

Tekintse át a kibocsátási megjegyzések a StorSimple megoldásban a frissítés telepítése előtt található információkat.

> [!IMPORTANT]
> * Ez a frissítés (beleértve a Windows-frissítések) megközelítőleg 4 – 7 órát vesz igénybe. 
> * 2. frissítés szoftver, LSI illesztőprogram és SSD-belső vezérlőprogram-frissítésekre rendelkezik.
> * Új kiadásokban nem láthatók frissítések azonnal, mert a frissítések fázisokra bontva történő bevezetéséhez végezzük. Néhány napot várni, és a frissítések újra ezeket a majd vizsgálatát hamarosan elérhető lesz.
> 
> 

## <a name="whats-new-in-update-2"></a>2. frissítés újdonságai
Frissítés 2 vezet be az alábbi új szolgáltatásokkal.

* **Helyileg rögzített kötetek** – a korábbi kiadásokban a StorSimple 8000 sorozat adatblokkjait volt rétegzett a felhőbe a felhasználás alapján. Nem lehetett garantálja, hogy blokkolja a helyi volna maradnak. A 2. frissítést egy kötet létrehozásakor jelölhet ki egy kötetet, a helyileg rögzített, és az elsődleges adatait, hogy a köteten nem helyezhető a felhőbe. A helyileg rögzített kötetek pillanatképek továbbra is kerülnek a felhőbe a biztonsági mentéshez, hogy a felhő alkalmas adatok mobilitás és a katasztrófa utáni helyreállítás céljából. Emellett módosíthatja a kötet típusa (Ez azt jelenti, hogy convert rétegzett kötet helyileg rögzített kötetek és a konvertálás helyileg rögzített kötetek Szintezett). 
* **A StorSimple virtuális eszköz fejlesztései** – korábban, a StorSimple 8000 series helyezkedik el a virtuális eszköz katasztrófa utáni helyreállítás vagy fejlesztési és tesztelési célú megoldásként. Hiba történt a virtuális eszköz (1100-as modell) csak egy modell. 2. frissítés két virtuáliseszköz-modellek vezet be: 
  
  * a 8010-es (korábbi nevén az 1100-as) – nincs változás; 30 TB kapacitású, és az Azure standard szintű tárolást használ.
  * 8020-as modellt – 64 TB kapacitású rendelkezik, és a prémium szintű tárolást használ javítja a teljesítményt.
    
    Nincs egyetlen virtuális merevlemez mind a virtuális eszköz két modellben (8010-es/8020-as modell). A virtuális eszköz első indításakor a platform paraméterek észleli, és alkalmazza a megfelelő modellverziója.
* **Hálózati fejlesztéseket** – 2. frissítés a következő hálózati fejlesztéseket tartalmazza:
  
  * Több hálózati adapter engedélyezheti a felhőben, hogy a feladatátvétel akkor fordulhat elő, ha meghibásodik egy hálózati Adaptert.
  * Útválasztási tovább fejlődtek, a felhő rögzített metrikák blokkok engedélyezve van.
  * Online módon tett újrapróbálkozás sikertelen erőforrások a feladatátvétel előtt.
  * Új riasztások service hibák.
* **Frissítési fejlesztései** – frissítse 1.2-es és korábbi verzióiban a StorSimple 8000 series két csatornákon keresztül frissült: Windows frissítés fürtözés, a iSCSI, és a stb és a Microsoft Update bináris fájljait és a belső vezérlőprogram.
    2. frissítés a Microsoft Update használja, minden frissítés. Ez rövidebb idő alatt, javítását vagy feladatátvételek során vezethetnek. 
* **Belső vezérlőprogram-frissítésekre** – a következő belső vezérlőprogram frissítések tartoznak:
  
  * LSI: lsi_sas2.sys 2.00.72.10 termékverziója
  * Csak SSD (HDD frissítések): XMGG, XGEG, KZ50, F6C2 és VR08
* **Proaktív támogatási** – 2. frissítés lehetővé teszi, hogy a Microsoft való lekérésére további diagnosztikai adatok az eszközről. Ha a műveleti csapata az eszközöket, amelyek problémákat tapasztal, vagy folyamatban, jobban ellátni a adatokat gyűjthetnek az eszköz és a problémák diagnosztizálásához. **2. frissítés elfogadásával, akkor lehetővé teszik a számunkra a proaktív támogatásához**.    

## <a name="issues-fixed-in-update-2"></a>A 2. frissítésben megoldott problémák
Az alábbi táblázatok a frissítések 2 javított problémák összegzését tartalmazza.    

| Nem. | Szolgáltatás | Probléma | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Hálózati illesztők |Az 1. frissítést a frissítés után a StorSimple Manager szolgáltatás jelentette, hogy a adat2 és Data3 portokat egy tartományvezérlőn sikertelen volt. Ez a hiba kijavítása. |Igen |Nem |
| 2 |Frissítések |Az 1. frissítést a frissítés után hallható riasztások történt több eszközre a klasszikus Azure portálon. Ez a hiba kijavítása. |Igen |Nem |
| 3 |Openstack hitelesítés |Ha használatával Openstack a felhőbeli szolgáltató, sikerült hibaüzenet, hogy a felhőbeli hitelesítési karakterlánc túl hosszú volt-e. A probléma javítását. |Igen |Nem |

## <a name="known-issues-in-update-2"></a>Ismert problémák a 2. frissítés
A következő táblázat összefoglalja az ismert problémákról, ebben a kiadásban.

| Nem. | Szolgáltatás | Probléma | Megjegyzések / megoldás | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Lemez kvórum |Ritka esetekben ha a legtöbb, a EBOD ház egy 8600 eszköz lemezek megszakadt a kapcsolat nincs lemez kvórum eredményezi majd a tárolókészlet kerül offline. Offline állapotban maradnak, még akkor is, ha a lemez újracsatlakoztatását. |Szüksége lesz az eszköz újraindul. Ha a probléma továbbra is fennáll, forduljon a Microsoft Support további lépéseket. |Igen |Nem |
| 2 |Helytelen vezérlő azonosítója |A vezérlő helyettesítő végrehajtásakor vezérlő 0 lehet, hogy megjelennek, a vezérlő 1. Tartományvezérlő csere közben, amikor a kép betöltődik a társ-csomópont a vezérlő azonosítója is megjelennek kezdetben, a partner tartományvezérlő azonosítóját. Ritka esetben ez a viselkedés előfordulhat, hogy is látható. a rendszer újraindítását követően. |Nincs felhasználói beavatkozásra szükség. Ez a helyzet magától megoldódik, amikor a vezérlő helyettesítő befejeződése után. |Igen |Nem |
| 3 |Tárfiókok |A Storage szolgáltatás használatával törli a tárfiókot a nem támogatott lehetőséget. Ez a helyzet, amelyben felhasználói nem lehet adatokat beolvasni irányítja. | |Igen |Igen |
| 4 |Eszköz feladatátvétel |A különböző céleszközökre a azonos forráseszközről származó kötettároló több feladatátvétel nem támogatott. Több eszköz egyetlen elhalt eszközről feladatátvétel működésképtelenné teszi az adatok tulajdonjoga elvesztése átadja a feladatokat eszköz első kötettárolók. Egy feladatátvétel után ezek kötettárolók jelenik meg, vagy a klasszikus Azure portálon meg eltérően viselkednek. | |Igen |Nem |
| 5 |Telepítés |Során StorSimple Adapter SharePoint telepítéshez meg kell adnia egy eszköz IP-cím ahhoz, hogy a telepítés sikeres befejezéséhez. | |Igen |Nem |
| 6 |Webproxy |Ha a webproxy konfigurálása a megadott protokoll HTTPS-t, az eszköz-szolgáltatások közötti kommunikáció néven érinti, és kerül, hogy az eszköz offline állapotban. Támogatási csomag is hozható létre a folyamat során fel jelentős erőforrásokat az eszközön. |Győződjön meg arról, hogy a webalkalmazás-proxy URL-címe HTTP megadott protokollt. További információ: [Configure web proxy for your device](storsimple-configure-web-proxy.md) (Webproxy beállítása az eszközhöz). |Igen |Nem |
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
| 20 |Helyileg rögzített kötetek |Ha a rétegzett kötetek (létrehozott és 1.2-es frissítés a klónozott vagy korábbi) átalakítása egy helyileg rögzített kötetet próbál és az eszköz nincs elegendő szabad terület, vagy egy felhőalapú leállás van, a clone(s) is megsérült. |A probléma csak a köteteket, amelyek a szoftverfrissítések létrehozott és a klónozott frissítés előtti 2. Ez egy ritka eset legyen. | | |
| 21 |Kötet átalakítás |Nem frissíthető a kötet csatlakozik, amíg folyamatban van egy kötet átalakítás ACRs (helyileg rögzített rétegzett vagy fordítva). A ACRs frissítése adatsérülést eredményezhet. |Szükség esetén frissítse a mennyiségi átalakítás előtti ACRs, és ne végezzen további ACR frissítések az átalakítás ideje alatt megszakadnak. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>A tartományvezérlő és a belső vezérlőprogram frissítések Update 2.
Ebben a kiadásban frissíti, az illesztőprogram és az eszközön a lemez belső vezérlőprogramját.

* További információ a LSI belső vezérlőprogram frissítése, olvassa el a Microsoft Tudásbázis 3121900. 
* További információ a lemez belső vezérlőprogram frissítése, olvassa el a Microsoft Tudásbázis 3121899.

## <a name="virtual-device-updates-in-update-2"></a>A 2. frissítésben virtuális eszköz frissítése
A frissítés nem alkalmazható a virtuális eszköz. Új virtuális eszközök kell létrehozni. 

## <a name="next-step"></a>Következő lépés
Megtudhatja, hogyan [2. frissítés telepítése](storsimple-install-update-2.md) a StorSimple eszköz.

