---
title: StorSimple 8000 series Update 3 kiadási megjegyzések
description: A storSimple 8000 sorozat 3.
author: alkohli
ms.assetid: 2158aa7a-4ac3-42ba-8796-610d1adb984d
ms.service: storsimple
ms.topic: conceptual
ms.date: 01/09/2018
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 4b61caecd67881eb08c82ea0c26522c63c3e8396
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79254611"
---
# <a name="update-3-release-notes-for-your-storsimple-8000-series-device"></a>3. frissítés isztaa a StorSimple 8000 sorozatú eszközhöz

## <a name="overview"></a>Áttekintés
A következő kibocsátási megjegyzések ismertetik az új funkciókat, és azonosítják a StorSimple 8000 sorozat 3. Tartalmaznak továbbá egy listát a StorSimple szoftverfrissítések szerepelnek ebben a kiadásban. 

A 3-as frissítés bármely Release (GA) vagy Update 0.1 update 2.2-es frissítéssel működő StorSimple eszközre alkalmazható. A 3.frissítéshez tartozó eszközverzió a 6.3.9600.17759.

Kérjük, tekintse át a kiadási megjegyzésekben található információkat, mielőtt telepítené a frissítést a StorSimple-megoldásban.

> [!IMPORTANT]
> * 3. frissítés eszköz szoftver, LSI driver és firmware, valamint a Storport és spaceport frissítéseket. A frissítés telepítése körülbelül 1,5–2 órát vesz igénybe. 
> * Az új kiadások esetében előfordulhat, hogy nem jelennek meg azonnal a frissítések, mert a frissítések fokozatos bevezetését tesszük. Várjon néhány napot, majd keresse meg újra a frissítéseket, mivel ezek hamarosan elérhetővé válnak.
> 
> 

## <a name="whats-new-in-update-3"></a>A 3.
A következő kulcsfontosságú fejlesztések és hibajavítások történtek a 3.

* **Az automatikus térvisszanyerés immár a** 3- as frissítéstől kezdve a rendszer készenléti vezérlőjében fut, ami gyorsabb végrehajtást eredményez. A térvisszanyeréshez szükséges portokról a [StorSimple hálózati követelményeiben](storsimple-8000-system-requirements.md#networking-requirements-for-your-storsimple-device)talál további információt.
* **Teljesítménybeli fejlesztések** – A 3.
* **Migrációval kapcsolatos fejlesztések** – Ebben a kiadásban számos hibajavítást és fejlesztést hajtottak végre az 5000/7000 sorozatú eszközökről 8000-es sorozatú eszközökre vonatkozó áttelepítési funkcióhoz. Az áttelepítési funkció használatával kapcsolatos további információkért látogasson el az [Áttelepítés 5000/7000 sorozatú eszközről a 8000-es sorozatú eszközre.](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) 
* **Kapcsolódó javítások figyelése** – Ebben a kiadásban a figyelési diagramokkal, a szolgáltatás irányítópultjával és az eszköz irányítópultjával kapcsolatos hibákat kijavították.

## <a name="issues-fixed-in-update-3"></a>A 3.
Az alábbi táblázatok a 3.    

| Nem | Szolgáltatás | Probléma | Fizikai eszközre vonatkozik | Virtuális eszközre vonatkozik |
| --- | --- | --- | --- | --- |
| 1 |Gazdagépoldali adatáttelepítés |A korábbi kiadásban a StorSimple Cloud Appliance offline állapotba került egy gazdagépoldali adatáttelepítés során. Ez a probléma ebben a kiadásban kivan javítva. |Nem |Igen |
| 2 |Helyileg rögzített kötetek |Az előző kiadásban a helyileg rögzített kötetek I/O-hibákkal, kötet-konvertálási hibákkal és datapath-hibákkal kapcsolatos problémák merültek fel. Ezek a problémák gyökér okozta és javított ebben a kiadásban. |Igen |Nem |
| 3 |Figyelés |A jelentési egységekés a figyelés, valamint az eszközirányítópult-diagramok több olyan probléma is voltak, ahol helytelen információk jelentek meg a helyileg rögzített kötetek esetében. Ezek a problémák ebben a kiadásban vannak kijavítva. |Igen |Nem |
| 4 |Nehéz írja I / O |A StorSimple nehéz írási műveleteket tartalmazó számítási feladatok használatakor a felhasználó egy ritka hibába ütközik, ahol a munkakészlet rétegzett a felhőbe. Ez a hiba ebben a kiadásban javítva. |Igen |Igen |
| 5 |Backup |Bizonyos ritka esetekben a szoftver korábbi verzióiban, amikor a felhasználó biztonsági másolatot készített egy távoli klónról, felhőhibákba ütköztek, és a művelet hiba történt. Ebben a kiadásban a probléma megoldódott, és a művelet sikeresen befejeződött. |Igen |Igen |
| 6 |Biztonsági mentési házirend |Bizonyos ritka esetekben a szoftver korábbi kiadásaiban hiba történt a biztonsági mentési házirend törlésével kapcsolatban. Ez a probléma ebben a kiadásban kivan javítva. |Igen |Igen |

## <a name="known-issues-in-update-3"></a>Ismert problémák a 3.
Az alábbi táblázat az ebben a kiadásban ismertetett ismert problémákat tartalmazza.

| Nem. | Szolgáltatás | Probléma | Megjegyzések / kerülő megoldás | Fizikai eszközre vonatkozik | Virtuális eszközre vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Lemez kvóruma |Ritka esetekben, ha egy 8600-as eszköz EBOD-házában lévő lemezek többsége le van választva, így nincs lemezkvórum, akkor a tárolókészlet offline állapotba kerül. Akkor is offline állapotban marad, ha a lemezek újra csatlakoznak. |Újra kell indítania az eszközt. Ha a probléma továbbra is fennáll, a következő lépésekért forduljon a Microsoft támogatási szolgálatához. |Igen |Nem |
| 2 |Helytelen vezérlőazonosító |A vezérlő cseréjekor a 0 vezérlő vezérlő 1 vezérlőként jelenhet meg. A vezérlő cseréje során, amikor a rendszerkép betöltődik a társcsomópontról, a vezérlőazonosító kezdetben a társvezérlő azonosítójaként jeleníthető meg. Ritka esetekben ez a viselkedés a rendszer újraindítása után is látható. |Nincs szükség felhasználói műveletre. Ez a helyzet magától megoldódik, miután a vezérlő cseréje befejeződött. |Igen |Nem |
| 3 |Tárfiókok |A storage szolgáltatás a tárfiók törlése nem támogatott forgatókönyv. Ez olyan helyzetet eredményez, amelyben a felhasználói adatok nem olvashatók be. | |Igen |Igen |
| 4 |Eszköz feladatátvétel |Egy kötettároló több feladatátvétele ugyanarról a forráseszközről a különböző céleszközökre nem támogatott. Az egyetlen lehalt eszközről több eszközre történő feladatátvétel miatt az első feladatátvételi eszközön a kötettárolók elveszítik az adatok tulajdonjogát. Egy ilyen feladatátvétel után ezek a kötettárolók jelennek meg, vagy másképp viselkednek, amikor megtekinti őket az Azure klasszikus portálon. | |Igen |Nem |
| 5 |Telepítés |A StorSimple Adapter sharepoint-telepítés során meg kell adnia egy eszköz IP-címét ahhoz, hogy a telepítés sikeresen befejeződhetjen. | |Igen |Nem |
| 6 |Webproxy |Ha a webproxy-konfiguráció https-t hoz meg a megadott protokollként, akkor az eszközről a szolgáltatásra irányuló kommunikációt érinti, és az eszköz offline állapotba kerül. Támogatási csomagok is jönnek létre a folyamat során, fogyasztása jelentős erőforrásokat az eszközön. |Győződjön meg arról, hogy a webproxy URL-címe HTTP protokollt biztosít a megadott protokollként. További információ: [Configure web proxy for your device](storsimple-8000-configure-web-proxy.md) (Webproxy beállítása az eszközhöz). |Igen |Nem |
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
| 22 |Frissítések |A 3. **Maintenance** Ez félrevezető, mivel azt jelzi, hogy az eszköz frissítése a 2. Miután az eszköz sikeresen frissíthető a 3-as frissítésre, ez az üzenet eltűnik. |Ez a viselkedés egy későbbi kiadásban lesz javítva. |Igen |Nem |

## <a name="controller-and-firmware-updates-in-update-3"></a>Vezérlő és belső vezérlőprogram frissítése a 3.
Ez a kiadás LSI illesztőprogram- és firmware-frissítésekkel rendelkezik. Az LSI-illesztőprogram és a belső vezérlőprogram frissítéseinek telepítéséről a [3.](storsimple-install-update-3.md)

## <a name="virtual-device-updates-in-update-3"></a>Virtuális eszközfrissítések a 3.
Ez a frissítés nem alkalmazható a StorSimple cloud appliance (más néven a virtuális eszköz). Új virtuális eszközöket kell létrehozni. 

## <a name="next-step"></a>Következő lépés
További információ a [3.](storsimple-install-update-3.md)

