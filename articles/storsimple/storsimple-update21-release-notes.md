---
title: StorSimple 8000 Series Update 2.2 kibocsátási megjegyzései |} A Microsoft Docs
description: Az új funkciók, problémák és megoldások ismerteti a StorSimple 8000 Series Update 2.2-es.
services: storsimple
documentationcenter: NA
author: alkohli
manager: carmonm
editor: ''
ms.assetid: 5cf03ea8-2a0f-4552-b6dc-7ea517783d7b
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.openlocfilehash: 12d11cddf077d4d07732490255d44e89ddaf3217
ms.sourcegitcommit: 1c2cf60ff7da5e1e01952ed18ea9a85ba333774c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/12/2019
ms.locfileid: "59527115"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>StorSimple 8000 Series Update 2.2 kibocsátási megjegyzései

## <a name="overview"></a>Áttekintés
A következő kiadási megjegyzések az új funkciók ismertetik, és a StorSimple 8000 Series Update 2.2 a kritikus fontosságú megoldatlan problémák azonosításához. Ebben a kiadásban a StorSimple szoftver frissítések listáját is tartalmaznak.

2.2-es frissítés bármilyen keresztül 2.1-es kiadás (GA) vagy 0.1-es frissítés futtató StorSimple-eszközre alkalmazhatók. Az eszköz társított 2.2-es frissítési verziója 6.3.9600.17708.

Tekintse át a kibocsátási megjegyzések a StorSimple-megoldásban a frissítés telepítése előtt található információkat.

> [!IMPORTANT]
> * 2.2-es frissítés kizárólag szoftverfrissítéseket tartalmaz. A frissítés telepítése körülbelül 1,5-2 órát vesz igénybe. 
> * Ha a 2.1-es frissítést futtatja, javasoljuk, 2.2-es frissítést minél hamarabb alkalmazni.
> * Az új kiadásokhoz, akkor előfordulhat, hogy nem jelenik meg frissítések azonnal, mivel egy fázisokra bontva történő bevezetéséhez a frissítések végzünk. Néhány napot várni, és ezeket újra, majd frissítéskeresés hamarosan elérhetővé válik.
> 
> 

## <a name="whats-new-in-update-22"></a>2.2-es frissítés újdonságai
A következő kulcsfontosságú fejlesztések történtek-e a 2.2-es frissítés.

* **Automatikus terület-visszaigénylést optimalizálási** – amikor dinamikusan kiosztott kötetek, a fel nem használt tárolási blokkokat kell visszaigényelni kívánt adat törlődik. Ebben a kiadásban javult a hely visszanyerése folyamat a felhőben, így ismertté váló gyorsabban a korábbi verziókhoz képest a szabad terület.
* **Pillanatkép készítése a teljesítményt érintő továbbfejlesztés** – 2.2-es frissítés javult az idő a felhőbeli pillanatkép bizonyos esetekben, ahol nagy méretű köteteket használ, és nincs minimális, sem az adatváltozás feldolgozásához. Ez a fejlesztés előnyös a forgatókönyv az archív kötetek lenne.
* **Korlátozások a támogatási csomag összegyűjtéséhez** – fejlesztések a támogatási csomag gyűjtött és ebben a kiadásban feltöltött módon történt. 
* **Frissítse a megbízhatóság fejlesztései** – ebben a kiadásban rendelkezik hibajavításokat tartalmaz, amelyek egy frissítés megbízhatósága.

## <a name="issues-fixed-in-update-22"></a>A frissítés 2.2 megoldott problémák
Az alábbi táblázat összefoglalja a frissítések 2.2-es és a 2.1-es kijavított problémák.    

| Nem | Szolgáltatás | Probléma | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Gazdagép-teljesítmény |A korábbi kiadásokban gazdagép-oldali teljesítménnyel kapcsolatos problémákat észlelt egy helyileg rögzített kötet létrehozása és a egy helyileg rögzített kötet a rétegzett kötetek alakítása során. Ezek a problémák vannak rögzítve, ebben a kiadásban, és ezáltal a kötet létrehozása és átalakítási eljárások során a gazdagép-teljesítmény javulása eredményez. |Igen |Nem |
| 2 |Helyileg rögzített kötetek |Ritka esetekben a rendszer összeomlási lenne egy helyileg rögzített kötet létrehozásakor. Ez a hiba nem határoztak meg ebben a kiadásban. |Igen |Nem |
| 3 |rétegezést |Ott volt, szórványos szoftverleállások, ha a StorSimple Cloud Appliance (8010 és 8020) metaadatait rétegzett a felhőbe. Ebben a kiadásban a probléma megoldódik. |Nem |Igen |
| 4 |Pillanatkép létrehozása |Problémák volt kapcsolódó forgatókönyvekben nagy méretű köteteket növekményes pillanatkép létrehozását és minimális, sem az adatváltozás. Ezek a problémák vannak rögzítve, ebben a kiadásban. |Igen |Igen |
| 5 |Openstack-hitelesítés |Openstack használ a felhőszolgáltató, amikor a felhasználó futna, az alkalmi hiba a hitelesítéshez kapcsolódó ahol eredményezett, a JSON-elemző, egy összeomlási. Ez kijavítanak ebben a kiadásban. |Igen |Nem |
| 6 |Gazdagép-oldali másolási |Korábbi verzióiban a szoftver egy alkalmi hiba az odx-et időzítési kapcsolatos jelent, ha az adatok másolásának egyik kötetről egy másik kötetre. Ez a vezérlő feladatátvétele eredményezne, és a rendszer potenciálisan tehet lépnek helyreállítási módba. Ez kijavítanak ebben a kiadásban. |Igen |Nem |
| 7 |Windows Management Instrumentation (WMI) |A korábbi verziókban a szoftver a proxy hiba azzal a kivétellel, több példánya voltak "\<managementexception hiba > nem sikerült a betöltés". Ezt a hibát egy WMI-memóriavesztés teljesítménykapacitást volt, és most már rögzített. |Igen |Nem |
| 8 |Frissítés |Bizonyos ritka esetekben a szoftver, a korábbi verzióiban a felhasználó egy "CisPowershellHcsscripterror" kapott, az áttekinthetőséget vagy a frissítések telepítése közben. Ebben a kiadásban a probléma megoldódik. |Igen |Igen |
| 9 |Támogatási csomag |Ebben a kiadásban a módját, a támogatási csomag gyűjtött és feltöltött fejlesztések történtek. |Igen |Igen |

## <a name="known-issues-in-update-22"></a>Ismert problémák a 2.2-es frissítés
Az alábbi táblázat összefoglalja az ismert problémákról, ebben a kiadásban.

| Nem. | Szolgáltatás | Probléma | Megjegyzések és megoldás | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Lemez kvórum |Ritka esetekben a többsége a EBOD ház-8600-as eszköz a lemezek le vannak választva, nincs lemez kvórum, így ha a tárolókészlet fog folytassa offline állapotban van. Offline állapotban marad, akkor is, ha a lemez újracsatlakoztatását. |Indítsa újra az eszközt kell. Ha a probléma tartósan fennáll, forduljon a Microsoft Support a következő lépéseket. |Igen |Nem |
| 2 |Nem megfelelő vezérlő azonosítója |A vezérlő helyettesítő hajtja végre, amikor 0. vezérlő előfordulhat, hogy megjelenjen 1. vezérlő. Csere, a vezérlő során a kép betöltésekor a társ-csomópontból a vezérlő azonosító konferenciateremként jelenjen meg kezdetben a partner-tartományvezérlő-azonosító néven Ritka esetekben a rendszer újraindítását követően is ezt a viselkedést is látható. |Nincs szükség felhasználói beavatkozásra. Ez a helyzet megoldódik, a vezérlő cseréje befejeződése után. |Igen |Nem |
| 3 |Tárfiókok |A Storage szolgáltatás törli a storage-fiók használata nem támogatott forgatókönyv. Ez a helyzet, amelyben a felhasználói adatokat nem lehet beolvasni vezet. | |Igen |Igen |
| 4 |Eszköz-feladatátvétel |A kötettároló a azonos forráseszközről származó különböző céleszközökre több lehetségessé nem támogatott. Feladatátvétel több eszköz egyetlen kézbesíthetetlen eszközt az fogja elérhetővé tenni a kötettároló feladatátvétele az eszköz első elveszíti az adatok tulajdonjoga. A feladatátvétel után ezek kötettárolók jelennek meg, illetve eltérően viselkednek, megtekintheti őket a klasszikus Azure portálon. | |Igen |Nem |
| 5 |Telepítés |StorSimple Adapter for SharePoint telepítési, során meg kell adnia egy eszköz IP-cím ahhoz, hogy a telepítés sikeres befejezéséhez. | |Igen |Nem |
| 6 |Webproxy |-E a webproxy konfigurálása a megadott protokoll HTTPS-t, az eszköz-to-service kommunikációs hatással lesz, és kerül, hogy az eszköz offline állapotban van. Támogatási csomag is jön létre, a folyamat, jelentős erőforrásokat az eszközön. |Győződjön meg arról, hogy a webalkalmazás-proxy URL-címe van-e a HTTP protokoll megadott. További információ: [Configure web proxy for your device](storsimple-configure-web-proxy.md) (Webproxy beállítása az eszközhöz). |Igen |Nem |
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
| 17 |Helyileg rögzített kötetek |A visszaállítási feladat közzéteszi az ideiglenes pillanatkép biztonsági másolatából a biztonságimásolat-katalógus, de csak a visszaállítási feladat időtartama. Ezenkívül tesz közzé egy virtuális lemez csoport előtaggal **tmpCollection** a a **biztonsági mentési házirendek** oldalon, de csak a visszaállítási feladat időtartama. |Ez akkor fordulhat elő, ha rendelkezik a a visszaállítási feladat csak helyileg rögzített kötetek vagy helyileg rögzített és a rétegzett kötetek kombinációját. Ha a visszaállítási feladat csak a rétegzett kötetek is tartalmaz, majd ezt a viselkedést nem történik. Nincs szükség felhasználói beavatkozásra. |Igen |Nem |
| 18 |Helyileg rögzített kötetek |Ha megszakítja a visszaállítási feladat és a vezérlő feladatátvétele akkor fordul elő, később jelennek meg a visszaállítási feladat azonnal **sikertelen** helyett **megszakított**. Ha a visszaállítási feladat sikertelen lesz, és a vezérlő feladatátvétele akkor fordul elő, később jelennek meg a visszaállítási feladat azonnal **megszakított** helyett **sikertelen**. |Ez akkor fordulhat elő, ha rendelkezik a a visszaállítási feladat csak helyileg rögzített kötetek vagy helyileg rögzített és a rétegzett kötetek kombinációját. Ha a visszaállítási feladat csak a rétegzett kötetek is tartalmaz, majd ezt a viselkedést nem történik. Nincs szükség felhasználói beavatkozásra. |Igen |Nem |
| 19 |Helyileg rögzített kötetek |Ha megszakítja a visszaállítási feladat, vagy ha a helyreállítás sikertelen lesz, és ezután következik be a vezérlő feladatátvétele, egy további visszaállítási feladat jelenik meg a **feladatok** lapot. |Ez akkor fordulhat elő, ha rendelkezik a a visszaállítási feladat csak helyileg rögzített kötetek vagy helyileg rögzített és a rétegzett kötetek kombinációját. Ha a visszaállítási feladat csak a rétegzett kötetek is tartalmaz, majd ezt a viselkedést nem történik. Nincs szükség felhasználói beavatkozásra. |Igen |Nem |
| 20 |Helyileg rögzített kötetek |Ha egy rétegzett kötet (létrehozott és a klónozott a frissítés 1.2-es vagy korábbi) átalakítása helyileg rögzített kötetet próbál és az eszköz nincs elegendő szabad terület, vagy egy cloud-beli szolgáltatáskimaradás van, a clone(s) is sérült. |A probléma csak a köteteket, amelyek a létrehozott és a klónozott frissítés előtti 2.1 szoftvert. Ez egy ritka eset legyen. | | |
| 21 |Kötet-átalakítás |Nem frissítik az ACR-EK, amíg folyamatban van egy kötet átalakítás kötet csatlakoztatva (a helyileg rögzített rétegzett vagy fordítva). Az ACR-EK frissítése adatsérülést eredményezhet. |Ha szükséges, frissítse az ACR-EK, a kötet konvertálás előtt, és ne végezzen további ACR frissítések amíg folyamatban van az átalakítás. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>A 2.2-es frissítés a vezérlő- és belsővezérlőprogram-frissítések
Ebben a kiadásban kizárólag szoftveralapú frissítés rendelkezik. Azonban ha frissít, a 2. frissítés előtti verziót, akkor telepítenie kell illesztőprogram, Storport, Spaceport, és (bizonyos esetekben) lemez a lemezfirmware-frissítések az eszközön.

Az illesztőprogram, Storport, Spaceport és lemezfirmware-frissítések telepítésével kapcsolatos további információkért lásd: [2.2-es frissítés telepítése](storsimple-install-update-21.md) a StorSimple eszközön.

## <a name="virtual-device-updates-in-update-22"></a>2.2-es frissítés a virtuális eszköz frissítése
Ez a frissítés a virtuális eszközre nem alkalmazható. Új virtuális eszközök kell létrehozni. 

## <a name="next-step"></a>Következő lépés
Ismerje meg, hogyan [2.2-es frissítés telepítése](storsimple-install-update-21.md) a StorSimple eszközön.

