---
title: A StorSimple 8000 Series Update 2 kibocsátási megjegyzései |} A Microsoft Docs
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
ms.openlocfilehash: f23a507ab631be553613e22cafa037291548a8aa
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "64717139"
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>A StorSimple 8000 Series Update 2 kibocsátási megjegyzései

## <a name="overview"></a>Áttekintés
A következő kiadási megjegyzések az új funkciók ismertetik, és a StorSimple 8000 Series Update 2 a kritikus fontosságú megoldatlan problémák azonosításához. A StorSimple szoftver, az illesztőprogramok és az ebben a kiadásban szereplő lemezfirmware-frissítések listáját is tartalmazhatja. 

2\. frissítés bármely kiadásban elérhetővé tétel (GA) vagy 0.1-es frissítés keresztül 1.2-es frissítést futtató StorSimple-eszköz is alkalmazható. A 2. frissítés társított eszköz verziója 6.3.9600.17673.

Tekintse át a kibocsátási megjegyzések a StorSimple-megoldásban a frissítés telepítése előtt található információkat.

> [!IMPORTANT]
> * Ez a frissítés (beleértve a Windows-frissítések) megközelítőleg 4-7 órát vesz igénybe. 
> * 2\. frissítés rendelkezik, szoftverek, LSI-illesztőprogram és SSD-belső vezérlőprogram frissítése.
> * Az új kiadásokhoz, akkor előfordulhat, hogy nem jelenik meg frissítések azonnal, mivel egy fázisokra bontva történő bevezetéséhez a frissítések végzünk. Néhány napot várni, és ezeket újra, majd frissítéskeresés hamarosan elérhetővé válik.
> 
> 

## <a name="whats-new-in-update-2"></a>2\. frissítés újdonságai
2\. frissítés a következő új funkciókat vezet be.

* **A helyileg rögzített kötetekről** – a korábbi kiadásokban a StorSimple 8000 sorozat adatblokkokat a felhőben, használat alapján lettek rétegzett. Hiba történt semmilyen módon nem lehet garantálni, hogy helyi blokkok szeretne maradni. A 2. frissítés egy kötet létrehozásakor megadhat egy köteten, a felhőben nem lesz rétegzett kötetet és az elsődleges gyors helyi adatait. Helyileg rögzített kötetek pillanatképek továbbra is másolandó biztonsági mentés a felhőbe, hogy a felhőben is használható adat mobilitás és a vész-helyreállítási céllal. Emellett módosíthatja a kötet típusa (, amelyek a konvertálás rétegzett kötet gyors helyi kötetek és a konvertálás a helyileg rögzített kötetekhez, így a rétegzett). 
* **A StorSimple virtuális eszköz fejlesztései** – korábban, a StorSimple 8000 sorozat Elhelyezés a virtuális eszköz egy katasztrófa utáni helyreállítás vagy fejlesztési-tesztelési megoldás. Hiba történt a virtuális eszköz (1100-as modell) csak egy típusa. 2\. frissítés két virtuáliseszköz-modellel mutatja be: 
  
  * (korábbi nevén az 1100-as) – 8010-es nem változik; 30 TB kapacitású, és az Azure standard szintű tárolást használ.
  * 8020-as – 64 TB kapacitású rendelkezik, és jobb teljesítmény érdekében az Azure Premium szintű tárolást használ.
    
    Nincs egyetlen virtuális merevlemez mind a virtuális eszköz két modellben (8010/8020). A virtuális eszköz első indításakor a platform paraméterek észleli, és a megfelelő modell verzió érvényes.
* **Hálózati fejlesztéseket** – 2. frissítés a következő hálózati fejlesztéseket tartalmazza:
  
  * Több hálózati adapterrel a felhő esetén is engedélyezhető, így a feladatátvételi akkor fordulhat elő, ha meghibásodik egy hálózati Adaptert.
  * Útválasztási fejlesztések, felhőalapú rögzített metrikákkal blokkok engedélyezve van.
  * Online próbálja meg újra a sikertelen a feladatátvétel előtt álló.
  * Új riasztások service hibák.
* **Frissítési fejlesztései** – az 1.2-es frissítés, és korábban, a StorSimple 8000-es sorozat két csatornákon keresztül frissült: Windows Update a fürtözés, iSCSI, és így tovább és bináris fájlok és belső vezérlőprogramok továbbítását a Microsoft Update.
    2\. frissítés a Microsoft Update használja, minden frissítés. Ez vagy a feladatátvétel végrehajtása kevesebb időt kell vezethet. 
* **Belső vezérlőprogram frissítése** – a belső vezérlőprogram az alábbi frissítések tartoznak:
  
  * LSI: lsi_sas2.sys 2.00.72.10 termékverzió
  * Csak SSD (HDD frissítések): XMGG, XGEG, KZ50, F6C2 és VR08
* **Proaktív támogatási** – 2. frissítés lehetővé teszi, hogy a Microsoft való lekérésére további diagnosztikai adatok az eszközről. Ha az üzemeltetési csapat azonosítja az eszközöket, amelyek nem sikerül, akkor jobban ellátni a összegyűjti az adatokat az eszközről, és diagnosztizálhatja a problémákat. **2. frissítés elfogadásával, engedélyezi a számunkra, hogy proaktív támogatásához**.    

## <a name="issues-fixed-in-update-2"></a>A 2. frissítésben megoldott problémák
Az alábbi táblázat összefoglalja a problémákat, amelyek a frissítések 2 javítva lett.    

| Nem. | Funkció | Probléma | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Hálózati illesztők |1\. frissítés, a frissítés után a StorSimple Manager szolgáltatás jelentette, hogy a adat2 és Data3 portok egy tartományvezérlőn sikertelen volt. Ez a probléma megoldódott. |Igen |Nem |
| 2 |Frissítések |1\. frissítés, a frissítés után hallható riasztás riasztások történt több eszközre a klasszikus Azure portálon. Ez a probléma megoldódott. |Igen |Nem |
| 3 |Openstack-hitelesítés |Openstack felhőszolgáltatást használ, amikor kaphassák meg, hogy a felhőalapú hitelesítés karakterlánc túl hosszú volt hiba. Ez megoldottuk. |Igen |Nem |

## <a name="known-issues-in-update-2"></a>Ismert problémák a 2. frissítésben
Az alábbi táblázat összefoglalja az ismert problémákról, ebben a kiadásban.

| Nem. | Funkció | Probléma | Megjegyzések és megoldás | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Lemez kvórum |Ritka esetekben a többsége a EBOD ház-8600-as eszköz a lemezek le vannak választva, nincs lemez kvórum, így ha a tárolókészlet fog folytassa offline állapotban van. Offline állapotban marad, akkor is, ha a lemez újracsatlakoztatását. |Indítsa újra az eszközt kell. Ha a probléma tartósan fennáll, forduljon a Microsoft Support a következő lépéseket. |Igen |Nem |
| 2 |Nem megfelelő vezérlő azonosítója |A vezérlő helyettesítő hajtja végre, amikor 0. vezérlő előfordulhat, hogy megjelenjen 1. vezérlő. Csere, a vezérlő során a kép betöltésekor a társ-csomópontból a vezérlő azonosító konferenciateremként jelenjen meg kezdetben a partner-tartományvezérlő-azonosító néven Ritka esetekben a rendszer újraindítását követően is ezt a viselkedést is látható. |Nincs szükség felhasználói beavatkozásra. Ez a helyzet megoldódik, a vezérlő cseréje befejeződése után. |Igen |Nem |
| 3 |Tárfiókok |A Storage szolgáltatás törli a storage-fiók használata nem támogatott forgatókönyv. Ez a helyzet, amelyben a felhasználói adatokat nem lehet beolvasni vezet. | |Igen |Igen |
| 4 |Eszköz-feladatátvétel |A kötettároló a azonos forráseszközről származó különböző céleszközökre több lehetségessé nem támogatott. Feladatátvétel több eszköz egyetlen kézbesíthetetlen eszközt az fogja elérhetővé tenni a kötettároló feladatátvétele az eszköz első elveszíti az adatok tulajdonjoga. A feladatátvétel után ezek kötettárolók jelennek meg, illetve eltérően viselkednek, megtekintheti őket a klasszikus Azure portálon. | |Igen |Nem |
| 5 |Telepítés |StorSimple Adapter for SharePoint telepítési, során meg kell adnia egy eszköz IP-cím ahhoz, hogy a telepítés sikeres befejezéséhez. | |Igen |Nem |
| 6 |Webalkalmazás-proxy |-E a webproxy konfigurálása a megadott protokoll HTTPS-t, az eszköz-to-service kommunikációs hatással lesz, és kerül, hogy az eszköz offline állapotban van. Támogatási csomag is jön létre, a folyamat, jelentős erőforrásokat az eszközön. |Győződjön meg arról, hogy a webalkalmazás-proxy URL-címe van-e a HTTP protokoll megadott. További információ: [Configure web proxy for your device](storsimple-configure-web-proxy.md) (Webproxy beállítása az eszközhöz). |Igen |Nem |
| 7 |Webalkalmazás-proxy |Ha konfigurálja, és engedélyezze a webalkalmazás-proxy egy regisztrált eszközön, majd kell indítania az aktív vezérlőn az eszközön. | |Igen |Nem |
| 8 |Felhőalapú nagy késést és nagy i/o-munkaterhelés |A StorSimple eszköz (sorrendben másodperc) nagyon magas felhőalapú késéseket és nagy i/o-munkaterhelés ütközik, amikor az eszköz köteteihez egy csökkentett teljesítményű állapotba kerülnek, és az i/o "az eszköz nem áll készen" hiba miatt sikertelenek lehetnek. |Szüksége lesz, manuálisan indítsa újra a eszközvezérlők vagy helyreállíthatja az ebben a helyzetben eszköz feladatátvételt hajt végre. |Igen |Nem |
| 9 |Azure PowerShell |A StorSimple parancsmag használatakor **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object - először a 1 - Wait** jelölje ki az első objektumot, így hozhat létre egy új **VolumeContainer** objektum, a parancsmag az összes található objektumokat adja vissza. |A következő zárójelben használó parancsmagot: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Igen |Igen |
| 10 |Migrálás |Több kötettárolóba továbbítódnak az áttelepítéshez, a DRÓN legfrissebb biztonsági mentés esetén csak az első kötettároló esetén pontosak. Az első 4 biztonsági másolatok az első kötettároló áttelepítésük párhuzamos áttelepítés fogja elindítani. |Azt javasoljuk, hogy egyszerre egy kötettárolót át. |Igen |Nem |
| 11 |Migrálás |A visszaállítás után kötetek nem adódnak hozzá a biztonsági mentési szabályzat vagy a virtuális lemez csoport. |Ezen kötetek hozzáadása a biztonsági mentési szabályzatot a biztonsági másolatok létrehozásához kell. |Igen |Igen |
| 12 |Migrálás |Az áttelepítés befejezése után az 5000/7000 sorozat eszköz nem kell elérni az áttelepített adatokat tárolókat. |Azt javasoljuk, hogy az áttelepítés befejezése és véglegesített után törölje az áttelepített adatokat tárolókat. |Igen |Nem |
| 13 |Klónozás és Vészhelyreállítás |1\. frissítést futtató StorSimple-eszköz nem klónozza vagy vészhelyreállításra 1 frissítés előtti szoftvert futtató eszközre. |Update 1, hogy ezeket a műveleteket a céleszköz frissíteni kell |Igen |Igen |
| 14 |Migrálás |Konfiguráció biztonsági másolata az áttelepítéshez 5000 – 7000 sorozatú eszköz sikertelen lehet, ha vannak a kötet-csoportokhoz társított kötetek a. |Nincs társított köteteket a kötet üres csoportok törlése, és ismételje meg a konfiguráció biztonsági másolata. |Igen |Nem |
| 15 |Az Azure PowerShell-parancsmagok és a gyors helyi kötetek |Azure PowerShell-parancsmagok használatával helyileg rögzített kötetet nem hozható létre. (Minden kötetet hoz létre az Azure Powershellen keresztül rétegezettek lesznek.) |A gyors helyi kötetek konfigurálni mindig használja a StorSimple Manager szolgáltatásban. |Igen |Nem |
| 16 |A gyors helyi kötetek rendelkezésre álló terület |Ha töröl egy helyileg rögzített kötetet, az új kötetek rendelkezésre álló terület nem frissíthető közvetlenül. A StorSimple Manager szolgáltatás körülbelül óránként frissíti a rendelkezésre álló helyi területet. |Várjon egy órát, mielőtt az új kötet létrehozásakor. |Igen |Nem |
| 17 |Helyileg rögzített kötetek |A visszaállítási feladat közzéteszi az ideiglenes pillanatkép biztonsági másolatából a biztonságimásolat-katalógus, de csak a visszaállítási feladat időtartama. Ezenkívül tesz közzé egy virtuális lemez csoport előtaggal **tmpCollection** a a **biztonsági mentési házirendek** oldalon, de csak a visszaállítási feladat időtartama. |Ez akkor fordulhat elő, ha rendelkezik a a visszaállítási feladat csak helyileg rögzített kötetek vagy helyileg rögzített és a rétegzett kötetek kombinációját. Ha a visszaállítási feladat csak a rétegzett kötetek is tartalmaz, majd ezt a viselkedést nem történik. Nincs szükség felhasználói beavatkozásra. |Igen |Nem |
| 18 |Helyileg rögzített kötetek |Ha megszakítja a visszaállítási feladat és a vezérlő feladatátvétele akkor fordul elő, később jelennek meg a visszaállítási feladat azonnal **sikertelen** helyett **megszakított**. Ha a visszaállítási feladat sikertelen lesz, és a vezérlő feladatátvétele akkor fordul elő, később jelennek meg a visszaállítási feladat azonnal **megszakított** helyett **sikertelen**. |Ez akkor fordulhat elő, ha rendelkezik a a visszaállítási feladat csak helyileg rögzített kötetek vagy helyileg rögzített és a rétegzett kötetek kombinációját. Ha a visszaállítási feladat csak a rétegzett kötetek is tartalmaz, majd ezt a viselkedést nem történik. Nincs szükség felhasználói beavatkozásra. |Igen |Nem |
| 19 |Helyileg rögzített kötetek |Ha megszakítja a visszaállítási feladat, vagy ha a helyreállítás sikertelen lesz, és ezután következik be a vezérlő feladatátvétele, egy további visszaállítási feladat jelenik meg a **feladatok** lapot. |Ez akkor fordulhat elő, ha rendelkezik a a visszaállítási feladat csak helyileg rögzített kötetek vagy helyileg rögzített és a rétegzett kötetek kombinációját. Ha a visszaállítási feladat csak a rétegzett kötetek is tartalmaz, majd ezt a viselkedést nem történik. Nincs szükség felhasználói beavatkozásra. |Igen |Nem |
| 20 |Helyileg rögzített kötetek |Ha egy rétegzett kötet (létrehozott és a klónozott a frissítés 1.2-es vagy korábbi) átalakítása helyileg rögzített kötetet próbál és az eszköz nincs elegendő szabad terület, vagy egy cloud-beli szolgáltatáskimaradás van, a clone(s) is sérült. |A probléma csak a köteteket, amelyek a létrehozott és a klónozott frissítés előtti 2 szoftvert. Ez egy ritka eset legyen. | | |
| 21 |Kötet-átalakítás |Nem frissítik az ACR-EK, amíg folyamatban van egy kötet átalakítás kötet csatlakoztatva (a helyileg rögzített rétegzett vagy fordítva). Az ACR-EK frissítése adatsérülést eredményezhet. |Ha szükséges, frissítse az ACR-EK, a kötet konvertálás előtt, és ne végezzen további ACR frissítések amíg folyamatban van az átalakítás. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>A 2. frissítésben vezérlő és a belsővezérlőprogram-frissítések
Ebben a kiadásban az illesztőprogram és az eszközön a lemezfirmware frissíti.

* További információ a LSI belső vezérlőprogram frissítése, a Microsoft Tudásbázis 3121900. 
* További információ a lemezfirmware frissítése, a Microsoft Tudásbázis 3121899.

## <a name="virtual-device-updates-in-update-2"></a>Virtuális eszköz frissítése a 2. frissítésben
Ez a frissítés a virtuális eszközre nem alkalmazható. Új virtuális eszközök kell létrehozni. 

## <a name="next-step"></a>Következő lépés
Ismerje meg, hogyan [Update 2 telepítése](storsimple-install-update-2.md) a StorSimple eszközön.

