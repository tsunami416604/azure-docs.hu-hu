---
title: StorSimple 8000 series update 2.2 kibocsátási megjegyzések | Microsoft dokumentumok
description: A storSimple 8000 sorozat 2.2-es frissítésének új szolgáltatásait, problémáit és kerülő megoldásait ismerteti.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60531060"
---
# <a name="storsimple-8000-series-update-22-release-notes"></a>StorSimple 8000 sorozat 2.2-es kiadási megjegyzések

## <a name="overview"></a>Áttekintés
A következő kibocsátási megjegyzések ismertetik az új funkciókat, és azonosítják a StorSimple 8000 series 2.2-es frissítésének kritikus nyitott problémáit. Tartalmaznak továbbá egy listát a StorSimple szoftverfrissítések szerepelnek ebben a kiadásban.

A 2.2-es frissítés bármely Release (GA) vagy Update 0.1 update 2.1-es frissítéssel működő StorSimple eszközre alkalmazható. A 2.2-es frissítéshez tartozó eszközverzió: 6.3.9600.17708.

Kérjük, tekintse át a kiadási megjegyzésekben található információkat, mielőtt telepítené a frissítést a StorSimple-megoldásban.

> [!IMPORTANT]
> * A 2.2-es frissítés csak szoftverfrissítéseket tartalmaz. A frissítés telepítése körülbelül 1,5–2 órát vesz igénybe. 
> * Ha a 2.1-es frissítést futtatja, javasoljuk, hogy a lehető leghamarabb alkalmazza a 2.2-es frissítést.
> * Az új kiadások esetében előfordulhat, hogy nem jelennek meg azonnal a frissítések, mert a frissítések fokozatos bevezetését tesszük. Várjon néhány napot, majd keresse meg újra a frissítéseket, mivel ezek hamarosan elérhetővé válnak.
> 
> 

## <a name="whats-new-in-update-22"></a>A 2.2-es frissítés újdonságai
A 2.2-es frissítésben a következő kulcsfontosságú fejlesztések történtek.

* **Automatikus térvisszanyerésoptimalizálás** – Ha az adatok at vékonyan kiépített köteteken törlik, a fel nem használt tárolóblokkokat újra kell igényelni. Ez a kiadás javította a felhőből történő térvisszanyerési folyamatot, ami azt eredményezte, hogy a fel nem használt terület gyorsabban elérhetővé vált, mint az előző verziók.
* **Pillanatkép teljesítményének javítása** – A 2.2-es frissítés javította a felhőbeli pillanatkép feldolgozásának idejét bizonyos esetekben, ahol nagy köteteket használnak, és minimális vagy semmilyen adatváltozás van. A forgatókönyv, amely előnyös lenne ez a fejlesztés lenne az archív kötetek.
* **A támogatási csomag összegyűjtésének megkeményedése** – A támogatási csomag összegyűjtésének és feltöltésének módja javult ebben a kiadásban. 
* **Frissítésmegbízhatósági fejlesztések** – Ez a kiadás olyan hibajavításokat tartalmaz, amelyek a frissítés megbízhatóságának javulását eredményezik.

## <a name="issues-fixed-in-update-22"></a>A 2.2-es frissítésben javított problémák
Az alábbi táblázatok a 2.2-es és 2.1-es frissítésekben javított problémák at tartalmazza.    

| Nem | Szolgáltatás | Probléma | Fizikai eszközre vonatkozik | Virtuális eszközre vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Állomás teljesítménye |A korábbi kiadásban a gazdagépoldali teljesítményproblémák at figyeltek meg egy helyileg rögzített kötet létrehozása és a rétegzett kötet helyileg rögzített kötetté történő átalakítása során. Ezek a problémák ebben a kiadásban ki vannak javítva, ami a kötet létrehozásának és konvertálási eljárásainak során a gazdagép teljesítményének javulását eredményezi. |Igen |Nem |
| 2 |Helyileg rögzített kötetek |Ritka esetekben a rendszer összeomlik, amikor helyileg rögzített kötetet hoz létre. Ez a hiba ki lett javítva ebben a kiadásban. |Igen |Nem |
| 3 |Rétegezés |Voltak szórványos összeomlik, amikor a metaadatok a StorSimple felhőalapú készülékek (8010 és 8020) rétegzett a felhőbe. Ez a probléma ebben a kiadásban kivan javítva. |Nem |Igen |
| 4 |Pillanatkép létrehozása |A növekményes pillanatképek létrehozásával kapcsolatos problémák nagy kötetekkel és minimális adatforgalom nélkül merültek fel. Ezek a problémák ebben a kiadásban vannak kijavítva. |Igen |Igen |
| 5 |Nyílt köteges hitelesítés |Amikor az Openstacket felhőszolgáltatóként használja, a felhasználó egy ritkán előforduló hibába ütközik a hitelesítéshez kapcsolódóan, ahol a JSON-elemző összeomlást eredményezett. Ez a hiba ebben a kiadásban javítva. |Igen |Nem |
| 6 |Gazdaoldali másolat |A szoftver korábbi verzióiban az ODX időzítésével kapcsolatos ritka hiba volt látható, amikor az adatokat egyik kötetről egy másik kötetre másolta. Ez vezérlő feladatátvételt eredményezne, és a rendszer helyreállítási módba kerülhet. Ez a hiba ebben a kiadásban javítva. |Igen |Nem |
| 7 |Windows Management Instrumentation (WMI) |A szoftver korábbi verzióiban a webproxy-hibák több példánya is volt a "ManagementException\<> A szolgáltató betöltési hibája" kivételével. Ez a hiba wmi memóriavesztésnek tulajdonítható, és most javítva van. |Igen |Nem |
| 8 |Frissítés |Bizonyos ritka esetekben a szoftver korábbi verzióiban a felhasználó "CisPowershellHcsscripterror" hibaüzenetet kapott, amikor frissítéseket próbált bekeresni vagy telepíteni. Ez a probléma ebben a kiadásban kivan javítva. |Igen |Igen |
| 9 |Támogatási csomag |Ebben a kiadásban a támogatási csomag összegyűjtésének és feltöltésének módja tovább fejlődött. |Igen |Igen |

## <a name="known-issues-in-update-22"></a>Ismert problémák a 2.2-es frissítésben
Az alábbi táblázat az ebben a kiadásban ismertetett ismert problémákat tartalmazza.

| Nem. | Szolgáltatás | Probléma | Megjegyzések / kerülő megoldás | Fizikai eszközre vonatkozik | Virtuális eszközre vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Lemez kvóruma |Ritka esetekben, ha egy 8600-as eszköz EBOD-házában lévő lemezek többsége le van választva, így nincs lemezkvórum, akkor a tárolókészlet offline állapotba kerül. Akkor is offline állapotban marad, ha a lemezek újra csatlakoznak. |Újra kell indítania az eszközt. Ha a probléma továbbra is fennáll, a következő lépésekért forduljon a Microsoft támogatási szolgálatához. |Igen |Nem |
| 2 |Helytelen vezérlőazonosító |A vezérlő cseréjekor a 0 vezérlő vezérlő 1 vezérlőként jelenhet meg. A vezérlő cseréje során, amikor a rendszerkép betöltődik a társcsomópontról, a vezérlőazonosító kezdetben a társvezérlő azonosítójaként jeleníthető meg. Ritka esetekben ez a viselkedés a rendszer újraindítása után is látható. |Nincs szükség felhasználói műveletre. Ez a helyzet magától megoldódik, miután a vezérlő cseréje befejeződött. |Igen |Nem |
| 3 |Tárfiókok |A storage szolgáltatás a tárfiók törlése nem támogatott forgatókönyv. Ez olyan helyzetet eredményez, amelyben a felhasználói adatok nem olvashatók be. | |Igen |Igen |
| 4 |Eszköz feladatátvétel |Egy kötettároló több feladatátvétele ugyanarról a forráseszközről a különböző céleszközökre nem támogatott. Az egyetlen lehalt eszközről több eszközre történő feladatátvétel miatt az első feladatátvételi eszközön a kötettárolók elveszítik az adatok tulajdonjogát. Egy ilyen feladatátvétel után ezek a kötettárolók jelennek meg, vagy másképp viselkednek, amikor megtekinti őket az Azure klasszikus portálon. | |Igen |Nem |
| 5 |Telepítés |A StorSimple Adapter sharepoint-telepítés során meg kell adnia egy eszköz IP-címét ahhoz, hogy a telepítés sikeresen befejeződhetjen. | |Igen |Nem |
| 6 |Webproxy |Ha a webproxy-konfiguráció https-t hoz meg a megadott protokollként, akkor az eszközről a szolgáltatásra irányuló kommunikációt érinti, és az eszköz offline állapotba kerül. Támogatási csomagok is jönnek létre a folyamat során, fogyasztása jelentős erőforrásokat az eszközön. |Győződjön meg arról, hogy a webproxy URL-címe HTTP protokollt biztosít a megadott protokollként. További információ: [Configure web proxy for your device](storsimple-configure-web-proxy.md) (Webproxy beállítása az eszközhöz). |Igen |Nem |
| 7 |Webproxy |Ha konfigurálja és engedélyezi a webproxyt egy regisztrált eszközön, akkor újra kell indítania az aktív vezérlőt az eszközön. | |Igen |Nem |
| 8 |Magas felhőkésleltetés és magas I/O-munkaterhelés |Ha a StorSimple-eszköz nagyon magas felhőkésések (másodpercek sorrendje) és magas I/O-számítási feladatok kombinációjával találkozik, az eszközkötetek csökkentett állapotba kerülnek, és az I/O sikertelen lehet egy "eszköz nem kész" hibával. |Ebben a helyzetben manuálisan kell újraindítania az eszközvezérlőket, vagy végre kell hajtania egy eszköz feladatátvételt. |Igen |Nem |
| 9 |Azure PowerShell |A StorSimple **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object -First 1 -Wait** az első objektum kijelöléséhez, hogy új **VolumeContainer** objektumot hozhasson létre, a parancsmag az összes objektumot visszaadja. |A parancsmag tördelése zárójelben a következőkszerint: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object -First 1 -Wait** |Igen |Igen |
| 10 |Migrálás |Ha több kötettároló taszat oldja meg az áttelepítéshez, a legutóbbi biztonsági mentéshez szükséges biztonsági másolat csak az első kötettárolóhoz érhető el. Emellett a párhuzamos áttelepítés az első kötettároló első 4 biztonsági másolatának áttelepítése után indul el. |Azt javasoljuk, hogy egyszerre csak egy kötettárolót telepítsen át. |Igen |Nem |
| 11 |Migrálás |A visszaállítás után a kötetek nem kerülnek be a biztonsági másolat házirendbe vagy a virtuális lemezcsoportba. |A biztonsági mentések létrehozásához hozzá kell adnia ezeket a köteteket egy biztonsági mentési házirendhez. |Igen |Igen |
| 12 |Migrálás |Az áttelepítés befejezése után az 5000/7000-es sorozatú eszköz nem férhet hozzá az áttelepített adattárolókhoz. |Azt javasoljuk, hogy törölje az áttelepített adattárolókat az áttelepítés befejezése és véglegesítése után. |Igen |Nem |
| 13 |Klón és DR |Az 1. |Frissítenie kell a céleszközt az 1. |Igen |Igen |
| 14 |Migrálás |Az áttelepítés konfigurációs biztonsági mentése sikertelen lehet egy 5000-7000-es sorozatú eszközön, ha kötetek nem rendelkező kötetek vannak. |Törölje az összes társított kötet nélküli üres kötetcsoportot, majd próbálkozzon újra a konfigurációs biztonsági másolattal. |Igen |Nem |
| 15 |Azure PowerShell-parancsmagok és helyileg rögzített kötetek |Az Azure PowerShell-parancsmagokon keresztül nem hozhat létre helyileg rögzített kötetet. (Az Azure PowerShellen keresztül létrehozott kötetek rétegzettek lesznek.) |Mindig használja a StorSimple Manager szolgáltatást a helyileg rögzített kötetek konfigurálásához. |Igen |Nem |
| 16 |Hely a helyileg rögzített kötetek számára |Ha helyileg rögzített kötetet töröl, előfordulhat, hogy az új kötetek számára rendelkezésre álló terület nem frissül azonnal. A StorSimple Manager szolgáltatás óránként frissíti a rendelkezésre álló helyi területet. |Várjon egy órát, mielőtt megpróbál létrehozni az új kötetet. |Igen |Nem |
| 17 |Helyileg rögzített kötetek |A visszaállítási feladat elérhetővé teszi az ideiglenes pillanatkép biztonsági mentést a biztonsági mentési katalógusban, de csak a visszaállítási feladat időtartama alatt. Emellett a **Biztonsági mentési házirendek** lapon elérhetővé teszi a **tmpCollection** előtaggal rendelkező virtuális lemezcsoportot, de csak a visszaállítási feladat időtartama alatt. |Ez a viselkedés akkor fordulhat elő, ha a visszaállítási feladat csak helyileg rögzített kötetek vagy helyileg rögzített és rétegzett kötetek vegyesen rendelkezik. Ha a visszaállítási feladat csak rétegzett köteteket tartalmaz, akkor ez a viselkedés nem fordul elő. Felhasználói beavatkozásra nincs szükség. |Igen |Nem |
| 18 |Helyileg rögzített kötetek |Ha megszakítja a visszaállítási feladatot, és a vezérlő feladatátvételközvetlenül utána történik, a visszaállítási feladat a **Megszakított**helyett **sikertelen** lesz. Ha egy visszaállítási feladat sikertelen, és a vezérlő feladatátvételközvetlenül utána történik, a visszaállítási feladat sikertelen helyett **a Megszakítva** parancsot jeleníti **meg.** |Ez a viselkedés akkor fordulhat elő, ha a visszaállítási feladat csak helyileg rögzített kötetek vagy helyileg rögzített és rétegzett kötetek vegyesen rendelkezik. Ha a visszaállítási feladat csak rétegzett köteteket tartalmaz, akkor ez a viselkedés nem fordul elő. Felhasználói beavatkozásra nincs szükség. |Igen |Nem |
| 19 |Helyileg rögzített kötetek |Ha megszakítja a visszaállítási feladatot, vagy ha egy visszaállítás sikertelen, majd egy vezérlő feladatátvétel történik, egy további visszaállítási feladat jelenik meg a **Feladatok** lapon. |Ez a viselkedés akkor fordulhat elő, ha a visszaállítási feladat csak helyileg rögzített kötetek vagy helyileg rögzített és rétegzett kötetek vegyesen rendelkezik. Ha a visszaállítási feladat csak rétegzett köteteket tartalmaz, akkor ez a viselkedés nem fordul elő. Felhasználói beavatkozásra nincs szükség. |Igen |Nem |
| 20 |Helyileg rögzített kötetek |Ha egy rétegzett kötetet (az 1.2-es vagy korábbi frissítéssel létrehozott és klónozott) helyileg rögzített kötetté próbál konvertálni, és az eszköz kimerül a területből, vagy felhőkimaradás van, akkor a klón(ok) megsérülhetnek. |Ez a probléma csak a 2.1-es frissítés előtti szoftverrel létrehozott és klónozott kötetek esetén fordul elő. Ez egy ritka forgatókönyv. | | |
| 21 |Kötetátalakítás |Ne frissítse a kötethez csatolt AR-eket, amíg a kötetátalakítás folyamatban van (helyileg rögzített rétegezve vagy fordítva). Az AKR-ek frissítése adatsérüléshez vezethet. |Szükség esetén frissítse az ACR-eket a kötetkonvertálás előtt, és ne készítsen további ACR-frissítéseket, amíg az átalakítás folyamatban van. | | |

## <a name="controller-and-firmware-updates-in-update-22"></a>A vezérlő és a firmware frissítései a 2.2-es frissítésben
Ez a kiadás csak szoftveres frissítéseket tartalmaz. Ha azonban a 2.

Az illesztőprogram, a Storport, a Spaceport és a lemez belső vezérlőprogramjának frissítésének frissítésével kapcsolatos további tudnivalókért olvassa el [a 2.2-es frissítés telepítése](storsimple-install-update-21.md) a StorSimple-eszközre című témakört.

## <a name="virtual-device-updates-in-update-22"></a>Virtuális eszközfrissítések a 2.2-es frissítésben
Ez a frissítés nem alkalmazható a virtuális eszközre. Új virtuális eszközöket kell létrehozni. 

## <a name="next-step"></a>Következő lépés
További információ a [2.2-es frissítés](storsimple-install-update-21.md) storsimple-eszközre való telepítéséről.

