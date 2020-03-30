---
title: StorSimple 8000 Series Update 2 release notes | Microsoft dokumentumok
description: A storSimple 8000 sorozat 2.
services: storsimple
documentationcenter: NA
author: twooley
manager: carmonm
editor: ''
ms.assetid: e2c8bffd-7fc5-4b77-b632-a4f59edacc3a
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: twooley
ms.openlocfilehash: 4e57fffd2f74ae1b14f51537c92299607f193ad5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75934057"
---
# <a name="storsimple-8000-series-update-2-release-notes"></a>StorSimple 8000 series Update 2 kiadási megjegyzések

## <a name="overview"></a>Áttekintés
A következő kibocsátási megjegyzések ismertetik az új funkciókat, és azonosítják a StorSimple 8000 series 2. Tartalmaznak továbbá egy listát a StorSimple szoftver, illesztőprogram és lemez firmware frissítéseket ebben a kiadásban. 

A 2. A 2.

Kérjük, tekintse át a kiadási megjegyzésekben található információkat, mielőtt telepítené a frissítést a StorSimple-megoldásban.

> [!IMPORTANT]
> * A frissítés telepítése körülbelül 4–7 órát vesz igénybe (beleértve a Windows-frissítéseket is). 
> * A 2- es frissítés szoftverrel, LSI-illesztőprogrammal és SSD firmware-frissítéssel rendelkezik.
> * Az új kiadások esetében előfordulhat, hogy nem jelennek meg azonnal a frissítések, mert a frissítések fokozatos bevezetését tesszük. Várjon néhány napot, majd keresse meg újra a frissítéseket, mivel ezek hamarosan elérhetővé válnak.
> 
> 

## <a name="whats-new-in-update-2"></a>A 2.
A 2- es frissítés a következő új funkciókat mutatja be.

* **Helyileg rögzített kötetek** – A StorSimple 8000 sorozat korábbi kiadásaiban az adatblokkok használat alapján rétegeztek a felhőbe. Nem volt mód arra, hogy garantáljuk, hogy a blokkok a helyieknél maradnak. A 2- es frissítésben kötet létrehozásakor kijelölhet egy kötetet helyileg rögzítettként, és az adott kötet elsődleges adatai nem lesznek rétegezve a felhőbe. A helyileg rögzített kötetek pillanatképei továbbra is átkerülnek a felhőbe biztonsági mentéscéljából, így a felhő adatmobilitási és vész-helyreállítási célokra használható. Ezenkívül módosíthatja a kötet típusát (azaz a rétegzett köteteket helyileg rögzített kötetekké konvertálhatja, és a helyileg rögzített köteteket rétegzettre konvertálhatja). 
* **StorSimple virtuális eszköz fejlesztések** – Korábban a StorSimple 8000 sorozat elhelyezni a virtuális eszköz, mint egy vész-helyreállítási vagy fejlesztési/vizsgálati megoldás. Csak egy virtuális eszközmodell volt (1100-as modell). A 2- es frissítés két virtuális eszközmodellt vezet be: 
  
  * 8010 (korábbi nevén a 1100) - Nincs változás; kapacitása 30 TB, és az Azure standard tárolóját használja.
  * 8020 – 64 TB kapacitással rendelkezik, és az Azure Premium storage-t használja a jobb teljesítmény érdekében.
    
    Mindkét virtuális eszközmodellhez (8010/8020) egyetlen virtuális merevlemez érhető el. Amikor először indítja el a virtuális eszközt, észleli a platform paramétereit, és a megfelelő modellverziót alkalmazza.
* **Hálózati fejlesztések** – A 2.
  
  * Több hálózati adapter is engedélyezhető a felhőben, így a feladatátvétel akkor fordulhat elő, ha egy hálózati adapter meghibásodik.
  * Útválasztási fejlesztések, a felhőengedélyező blokkok rögzített metrikákkal.
  * Sikertelen erőforrások online újrapróbálkozása feladatátvétel előtt.
  * Új riasztások a szolgáltatás hibák.
* **Fejlesztések frissítése** – Az 1.2-es és korábbi frissítésben a StorSimple 8000 sorozat két csatornán keresztül frissült: a Windows Update a fürtözéshez, az iSCSI és így tovább, valamint a Microsoft Update bináris fájlokhoz és firmware-hez.
    A 2. Ez kevesebb idő javításhoz vagy feladatátvételhez vezethet. 
* **Firmware frissítések** – A következő firmware frissítések szerepelnek:
  
  * LSI: lsi_sas2.sys termék verzió 2.00.72.10
  * Csak SSD (HDD-frissítések nélkül): XMGG, XGEG, KZ50, F6C2 és VR08
* **Proaktív támogatás** – A 2. Amikor műveleti csapatunk azonosítja a problémákat okozó eszközöket, jobban fel vagyunk szerelve az eszközről származó adatok gyűjtésére és a problémák diagnosztizálására. **A 2.**    

## <a name="issues-fixed-in-update-2"></a>A 2.
Az alábbi táblázatok a 2.    

| Nem. | Szolgáltatás | Probléma | Fizikai eszközre vonatkozik | Virtuális eszközre vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Hálózati illesztők |Az 1-es frissítésre való frissítés után a StorSimple Manager szolgáltatás arról számolt be, hogy a Data2 és a Data3 portok nem sikerültek egy vezérlőn. A probléma ki lett javítva. |Igen |Nem |
| 2 |Frissítések |Az 1.frissítésre való frissítés után hallható riasztástörtént az Azure klasszikus portálján több eszközön. A probléma ki lett javítva. |Igen |Nem |
| 3 |Nyílt köteges hitelesítés |Amikor az Openstacket felhőszolgáltatóként használja, hibaüzenetjelenhet meg, hogy a felhőalapú hitelesítési karakterlánc túl hosszú volt. Ezt a problémát megoldottuk. |Igen |Nem |

## <a name="known-issues-in-update-2"></a>Ismert problémák a 2.
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
| 20 |Helyileg rögzített kötetek |Ha egy rétegzett kötetet (az 1.2-es vagy korábbi frissítéssel létrehozott és klónozott) helyileg rögzített kötetté próbál konvertálni, és az eszköz kimerül a területből, vagy felhőkimaradás van, akkor a klón(ok) megsérülhetnek. |Ez a probléma csak a 2. Ez egy ritka forgatókönyv. | | |
| 21 |Kötetátalakítás |Ne frissítse a kötethez csatolt AR-eket, amíg a kötetátalakítás folyamatban van (helyileg rögzített rétegezve vagy fordítva). Az AKR-ek frissítése adatsérüléshez vezethet. |Szükség esetén frissítse az ACR-eket a kötetkonvertálás előtt, és ne készítsen további ACR-frissítéseket, amíg az átalakítás folyamatban van. | | |

## <a name="controller-and-firmware-updates-in-update-2"></a>Vezérlő és belső vezérlőprogram frissítése a 2.
Ez a kiadás frissíti az illesztőprogramot és a lemez belső vezérlőprogramját az eszközön.

* Az LSI belső vezérlőprogramjának frissítéséről a Microsoft Tudásbázis 3121900 számú cikkében olvashat bővebben. 
* A lemez belső vezérlőprogramjának frissítéséről a Microsoft Tudásbázis 3121899 számú cikkében olvashat bővebben.

## <a name="virtual-device-updates-in-update-2"></a>Virtuális eszközfrissítések a 2.
Ez a frissítés nem alkalmazható a virtuális eszközre. Új virtuális eszközöket kell létrehozni. 

## <a name="next-step"></a>Következő lépés
További információ a [2.](storsimple-install-update-2.md)

