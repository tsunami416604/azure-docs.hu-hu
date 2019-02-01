---
title: A StorSimple 8000 Series Update 3 kibocsátási megjegyzések |} A Microsoft Docs
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
ms.openlocfilehash: d18feba4ded3dfccb8f774112a7dc8d42b12f1d5
ms.sourcegitcommit: 5978d82c619762ac05b19668379a37a40ba5755b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/31/2019
ms.locfileid: "55488323"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>Frissítse a StorSimple 8000 sorozatú eszköz 3 kibocsátási megjegyzései

## <a name="overview"></a>Áttekintés
A következő kiadási megjegyzések az új funkciók ismertetik, és a StorSimple 8000 Series Update 3 a kritikus fontosságú megoldatlan problémák azonosításához. Ebben a kiadásban a StorSimple szoftver frissítések listáját is tartalmaznak. 

3. frissítés bármely kiadásban elérhetővé tétel (GA) vagy 0.1-es frissítés keresztül 2.2-es frissítést futtató StorSimple-eszköz is alkalmazható. Az eszközhöz társított Update 3 verziószáma 6.3.9600.17759.

Tekintse át a kibocsátási megjegyzések a StorSimple-megoldásban a frissítés telepítése előtt található információkat.

> [!IMPORTANT]
> * 3. frissítéssel rendelkezik eszköz szoftverének, LSI-illesztőprogram és a belső vezérlőprogram és Storport és Spaceport frissíti. A frissítés telepítése körülbelül 1,5-2 órát vesz igénybe. 
> * Az új kiadásokhoz, akkor előfordulhat, hogy nem jelenik meg frissítések azonnal, mivel egy fázisokra bontva történő bevezetéséhez a frissítések végzünk. Néhány napot várni, és ezeket újra, majd frissítéskeresés hamarosan elérhetővé válik.
> 
> 

## <a name="whats-new-in-update-3"></a>3. frissítés újdonságai
A következő kulcsfontosságú fejlesztések és hibajavítások az Update 3 került sor.

* **Terület-visszaigénylést változások automatikus** – Update 3 indítása a terület-visszaigénylést algoritmusok futnak a készenléti állapotban lévő vezérlőnek, így gyorsabb végrehajtását a rendszer. A terület-visszaigényléssel működéséhez szükséges portokon további információkért tekintse meg a [hálózati követelményeiben StorSimple](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device).
* **Nagyobb teljesítmény** – 3. frissítés javult az olvasási és írási teljesítményt, a felhőben.
* **Áttelepítéssel kapcsolatos fejlesztések** – ebben a kiadásban számos hibajavítás és fejlesztések elkészült a Migrálási szolgáltatás 5000/7000 sorozat eszközeiről a 8000-es sorozatú eszközökön. További információ az áttelepítés szolgáltatás használatához lépjen [5000/7000 sorozat eszközt 8000-es sorozatú eszköz áttelepítés](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b). 
* **Kapcsolódó javításokat figyelési** – ebben a kiadásban hibák kapcsolatos figyelési diagramokat, a szolgáltatás irányítópultján, és irányítópultját javítva lett.

## <a name="issues-fixed-in-update-3"></a>Az Update 3 megoldott problémák
Az alábbi táblázat összefoglalja a problémákat, amelyek a Update 3 javítva lett.    

| Nem | Szolgáltatás | Probléma | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Gazdagép-oldali adatok migrálása |A korábbi kiadásokban a StorSimple felhőalapú készülék folyamatban volt a kapcsolat nélküli egy gazdagép-oldali adatok migrálása során. Ebben a kiadásban a probléma megoldódik. |Nem |Igen |
| 2 |A gyors helyi kötetek |A korábbi változatban voltak i/o hibák, a kötet átalakítás sikertelen és a helyileg rögzített kötetekhez datapath hibák kapcsolatos problémákat. Ezek a problémák alapvető oka és javítva a kiadásban. |Igen |Nem |
| 3 |Figyelés |Jelentéskészítés a egységek és a monitorozás, valamint eszköz irányítópult diagramok, hibás információknak a helyileg rögzített kötetekhez több problémáiról volt. Ezek a problémák vannak rögzítve, ebben a kiadásban. |Igen |Nem |
| 4 |Nagy írási i/o |Használata esetén a nagy írási számítási feladatok esetében a StorSimple használatakor a felhasználó futna egy alkalmi programhiba, ahol munkakészletének volt folyamatban rétegzett a felhőbe. Ez kijavítanak ebben a kiadásban. |Igen |Igen |
| 5 |Backup |Egyes ritka esetekben a szoftver korábbi verzióiban felhasználó elvégez egy távoli klónozott biztonsági másolatot, ha azok felhőalapú hibákká futna, és a művelet akkor hibaüzenetet a. Ebben a kiadásban a probléma kijavítása, és a művelet sikeresen befejeződik. |Igen |Igen |
| 6 |Biztonsági mentési házirend |Bizonyos esetekben a ritkán használt szoftverek, a korábbi verzióiban hiba történt a biztonsági mentési szabályzat törlése kapcsolatos hibát. Ebben a kiadásban a probléma megoldódik. |Igen |Igen |

## <a name="known-issues-in-update-3"></a>3. frissítés ismert problémái
Az alábbi táblázat összefoglalja az ismert problémákról, ebben a kiadásban.

| Nem. | Szolgáltatás | Probléma | Megjegyzések és megoldás | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Lemez kvórum |Ritka esetekben a többsége a EBOD ház-8600-as eszköz a lemezek le vannak választva, nincs lemez kvórum, így ha a tárolókészlet fog folytassa offline állapotban van. Offline állapotban marad, akkor is, ha a lemez újracsatlakoztatását. |Indítsa újra az eszközt kell. Ha a probléma tartósan fennáll, forduljon a Microsoft Support a következő lépéseket. |Igen |Nem |
| 2 |Nem megfelelő vezérlő azonosítója |A vezérlő helyettesítő hajtja végre, amikor 0. vezérlő előfordulhat, hogy megjelenjen 1. vezérlő. Csere, a vezérlő során a kép betöltésekor a társ-csomópontból a vezérlő azonosító konferenciateremként jelenjen meg kezdetben a partner-tartományvezérlő-azonosító néven Ritka esetekben a rendszer újraindítását követően is ezt a viselkedést is látható. |Nincs szükség felhasználói beavatkozásra. Ez a helyzet megoldódik, a vezérlő cseréje befejeződése után. |Igen |Nem |
| 3 |Tárfiókok |A Storage szolgáltatás törli a storage-fiók használata nem támogatott forgatókönyv. Ez a helyzet, amelyben a felhasználói adatokat nem lehet beolvasni vezet. | |Igen |Igen |
| 4 |Eszköz-feladatátvétel |A kötettároló a azonos forráseszközről származó különböző céleszközökre több lehetségessé nem támogatott. Feladatátvétel több eszköz egyetlen kézbesíthetetlen eszközt az fogja elérhetővé tenni a kötettároló feladatátvétele az eszköz első elveszíti az adatok tulajdonjoga. A feladatátvétel után ezek kötettárolók jelennek meg, illetve eltérően viselkednek, megtekintheti őket a klasszikus Azure portálon. | |Igen |Nem |
| 5 |Telepítés |StorSimple Adapter for SharePoint telepítési, során meg kell adnia egy eszköz IP-cím ahhoz, hogy a telepítés sikeres befejezéséhez. | |Igen |Nem |
| 6 |Webproxy |-E a webproxy konfigurálása a megadott protokoll HTTPS-t, az eszköz-to-service kommunikációs hatással lesz, és kerül, hogy az eszköz offline állapotban van. Támogatási csomag is jön létre, a folyamat, jelentős erőforrásokat az eszközön. |Győződjön meg arról, hogy a webalkalmazás-proxy URL-címe van-e a HTTP protokoll megadott. További információ: [Configure web proxy for your device](storsimple-8000-configure-web-proxy.md) (Webproxy beállítása az eszközhöz). |Igen |Nem |
| 7 |Webproxy |Ha konfigurálja, és engedélyezze a webalkalmazás-proxy egy regisztrált eszközön, majd kell indítania az aktív vezérlőn az eszközön. | |Igen |Nem |
| 8 |Felhőalapú nagy késést és nagy i/o-munkaterhelés |A StorSimple eszköz (sorrendben másodperc) nagyon magas felhőalapú késéseket és nagy i/o-munkaterhelés ütközik, amikor az eszköz köteteihez egy csökkentett teljesítményű állapotba kerülnek, és az i/o "az eszköz nem áll készen" hiba miatt sikertelenek lehetnek. |Szüksége lesz, manuálisan indítsa újra a eszközvezérlők vagy helyreállíthatja az ebben a helyzetben eszköz feladatátvételt hajt végre. |Igen |Nem |
| 9 |Azure PowerShell |A StorSimple parancsmag használatakor **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object - először a 1 - Wait** jelölje ki az első objektumot, így hozhat létre egy új **VolumeContainer** objektum, a parancsmag az összes található objektumokat adja vissza. |A következő zárójelben használó parancsmagot: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Igen |Igen |
| 10 |Migrálás |Több kötettárolóba továbbítódnak az áttelepítéshez, a DRÓN legfrissebb biztonsági mentés esetén csak az első kötettároló esetén pontosak. Az első 4 biztonsági másolatok az első kötettároló áttelepítésük párhuzamos áttelepítés fogja elindítani. |Azt javasoljuk, hogy egyszerre egy kötettárolót át. |Igen |Nem |
| 11 |Migrálás |A visszaállítás után kötetek nem adódnak hozzá a biztonsági mentési szabályzat vagy a virtuális lemez csoport. |Ezen kötetek hozzáadása a biztonsági mentési szabályzatot a biztonsági másolatok létrehozásához kell. |Igen |Igen |
| 12 |Migrálás |Az áttelepítés befejezése után az 5000/7000 sorozat eszköz nem kell elérni az áttelepített adatokat tárolókat. |Azt javasoljuk, hogy az áttelepítés befejezése és véglegesített után törölje az áttelepített adatokat tárolókat. |Igen |Nem |
| 13 |Klónozás és Vészhelyreállítás |1. frissítést futtató StorSimple-eszköz nem klónozza vagy vészhelyreállításra 1 frissítés előtti szoftvert futtató eszközre. |Update 1, hogy ezeket a műveleteket a céleszköz frissíteni kell |Igen |Igen |
| 14 |Migrálás |Konfiguráció biztonsági másolata az áttelepítéshez 5000 – 7000 sorozatú eszköz sikertelen lehet, ha vannak a kötet-csoportokhoz társított kötetek a. |Nincs társított köteteket a kötet üres csoportok törlése, és ismételje meg a konfiguráció biztonsági másolata. |Igen |Nem |
| 15 |Az Azure PowerShell-parancsmagok és a gyors helyi kötetek |Azure PowerShell-parancsmagok használatával helyileg rögzített kötetet nem hozható létre. (Minden kötetet hoz létre az Azure Powershellen keresztül rétegezettek lesznek.) |A gyors helyi kötetek konfigurálni mindig használja a StorSimple Manager szolgáltatásban. |Igen |Nem |
| 16 |A gyors helyi kötetek rendelkezésre álló terület |Ha töröl egy helyileg rögzített kötetet, az új kötetek rendelkezésre álló terület nem frissíthető közvetlenül. A StorSimple Manager szolgáltatás körülbelül óránként frissíti a rendelkezésre álló helyi területet. |Várjon egy órát, mielőtt az új kötet létrehozásakor. |Igen |Nem |
| 17 |A gyors helyi kötetek |A visszaállítási feladat közzéteszi az ideiglenes pillanatkép biztonsági másolatából a biztonságimásolat-katalógus, de csak a visszaállítási feladat időtartama. Ezenkívül tesz közzé egy virtuális lemez csoport előtaggal **tmpCollection** a a **biztonsági mentési házirendek** oldalon, de csak a visszaállítási feladat időtartama. |Ez akkor fordulhat elő, ha rendelkezik a a visszaállítási feladat csak helyileg rögzített kötetek vagy helyileg rögzített és a rétegzett kötetek kombinációját. Ha a visszaállítási feladat csak a rétegzett kötetek is tartalmaz, majd ezt a viselkedést nem történik. Nincs szükség felhasználói beavatkozásra. |Igen |Nem |
| 18 |A gyors helyi kötetek |Ha megszakítja a visszaállítási feladat és a vezérlő feladatátvétele akkor fordul elő, később jelennek meg a visszaállítási feladat azonnal **sikertelen** helyett **megszakított**. Ha a visszaállítási feladat sikertelen lesz, és a vezérlő feladatátvétele akkor fordul elő, később jelennek meg a visszaállítási feladat azonnal **megszakított** helyett **sikertelen**. |Ez akkor fordulhat elő, ha rendelkezik a a visszaállítási feladat csak helyileg rögzített kötetek vagy helyileg rögzített és a rétegzett kötetek kombinációját. Ha a visszaállítási feladat csak a rétegzett kötetek is tartalmaz, majd ezt a viselkedést nem történik. Nincs szükség felhasználói beavatkozásra. |Igen |Nem |
| 19 |A gyors helyi kötetek |Ha megszakítja a visszaállítási feladat, vagy ha a helyreállítás sikertelen lesz, és ezután következik be a vezérlő feladatátvétele, egy további visszaállítási feladat jelenik meg a **feladatok** lapot. |Ez akkor fordulhat elő, ha rendelkezik a a visszaállítási feladat csak helyileg rögzített kötetek vagy helyileg rögzített és a rétegzett kötetek kombinációját. Ha a visszaállítási feladat csak a rétegzett kötetek is tartalmaz, majd ezt a viselkedést nem történik. Nincs szükség felhasználói beavatkozásra. |Igen |Nem |
| 20 |A gyors helyi kötetek |Ha egy rétegzett kötet (létrehozott és a klónozott a frissítés 1.2-es vagy korábbi) átalakítása helyileg rögzített kötetet próbál és az eszköz nincs elegendő szabad terület, vagy egy cloud-beli szolgáltatáskimaradás van, a clone(s) is sérült. |A probléma csak a köteteket, amelyek a létrehozott és a klónozott frissítés előtti 2.1 szoftvert. Ez egy ritka eset legyen. | | |
| 21 |Kötet-átalakítás |Nem frissítik az ACR-EK, amíg folyamatban van egy kötet átalakítás kötet csatlakoztatva (a helyileg rögzített rétegzett vagy fordítva). Az ACR-EK frissítése adatsérülést eredményezhet. |Ha szükséges, frissítse az ACR-EK, a kötet konvertálás előtt, és ne végezzen további ACR frissítések amíg folyamatban van az átalakítás. | | |
| 22 |Frissítések |3. frissítés alkalmazása esetén a **karbantartási** oldal a klasszikus Azure portálon jelenik meg, a következő üzenet kapcsolatos 2. frissítés – "a StorSimple 8000 series 2-es frissítés lehetővé teszi a Microsoft megelőző a gyűjtése a naplófájlba információk az eszközről, ha a rendszer azt észleli, hogy a lehetséges problémák". Ez az félrevezető módon azt jelzi, hogy az eszköz frissítése folyamatban van a 2. frissítés. Után az eszköz sikeres frissítése Update 3-ra, ez az üzenet véglegesen eltűnik. |Ez a viselkedés javítja egy későbbi kiadásban. |Igen |Nem |

## <a name="controller-and-firmware-updates-in-update-3"></a>Az Update 3 vezérlő és a belsővezérlőprogram-frissítések
Ebben a kiadásban rendelkezik LSI illesztőprogram- és belsővezérlőprogram-frissítések. Az LSI-illesztőt és a lemezfirmware-frissítések telepítésével kapcsolatos további információkért lásd: [Update 3 telepítése](storsimple-install-update-3.md) a StorSimple eszközön.

## <a name="virtual-device-updates-in-update-3"></a>Virtuális eszköz frissítéseit a Update 3
A StorSimple Cloud Appliance eszköz (más néven a virtuális eszköz) nem lehet alkalmazni ezt a frissítést. Új virtuális eszközök kell létrehozni. 

## <a name="next-step"></a>Következő lépés
Ismerje meg, hogyan [Update 3 telepítése](storsimple-install-update-3.md) a StorSimple eszközön.

