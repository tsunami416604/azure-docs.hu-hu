---
title: StorSimple 8000 series update 1.2 kibocsátási megjegyzések | Microsoft dokumentumok
description: A storSimple 8000 sorozat 1.2-es frissítésének új szolgáltatásait, problémáit és kerülő megoldásait ismerteti.
services: storsimple
documentationcenter: NA
author: alkohli
manager: timlt
editor: ''
ms.assetid: 6c9aae87-6f77-44b8-b7fa-ebbdc9d8517c
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 11/03/2017
ms.author: alkohli
ms.custom: H1Hack27Feb2017
ms.openlocfilehash: 11138857e33eec0f854ddb61956ea24c858c49a5
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "60531026"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>Frissítés 1.2 kiadási megjegyzések a StorSimple 8000 sorozatú eszközhöz

## <a name="overview"></a>Áttekintés
A következő kibocsátási megjegyzések ismertetik az új funkciókat, és azonosítják a StorSimple 8000 series 1.2-es frissítésének kritikus nyitott problémáit. Tartalmaznak továbbá egy listát a StorSimple szoftver, illesztőprogram és lemez firmware frissítéseket ebben a kiadásban. 

Az 1.2-es frissítés bármely Release (GA), Update 0.1, Update 0.2 vagy Update 0.3 szoftvert futtató StorSimple-eszközre alkalmazható. Az 1.2-es frissítés nem érhető el, ha az eszközön az 1.1-es vagy az 1.1-es frissítés fut. Ha az eszközön a Release (GA) fut, kérjük, [forduljon a Microsoft támogatási szolgálatához,](storsimple-contact-microsoft-support.md) hogy segítséget nyújtson a frissítés telepítéséhez.

Az alábbi táblázat az 1., 1.1 és 1.2 frissítéseknek megfelelő eszközszoftver-verziókat sorolja fel.

| Ha fut frissítés ... | ez a készülék szoftververziója. |
| --- | --- |
| 1.2-es frissítés |6.3.9600.17584 |
| 1.1-es frissítés |6.3.9600.17521 |
| 1.0-s frissítés |6.3.9600.17491 |

Kérjük, tekintse át a kiadási megjegyzésekben található információkat, mielőtt telepítené a frissítést a StorSimple-megoldásban. További információt az [1.2-es frissítés StorSimple eszközre való telepítése című témakörben talál.](storsimple-install-update-1.md) 

> [!IMPORTANT]
> * A frissítés telepítése körülbelül 5–10 órát vesz igénybe (beleértve a Windows-frissítéseket is). 
> * Az 1.2-es frissítés szoftveres, LSI-illesztőprogram- és lemezbelső-vezérlőprogram-frissítéseket tartalmaz. A telepítéshez kövesse az [1.2-es frissítés telepítésének utasításait a StorSimple eszközre](storsimple-install-update-1.md).
> * Az új kiadások esetében előfordulhat, hogy nem jelennek meg azonnal a frissítések, mert a frissítések fokozatos bevezetését tesszük. Scan frissítések néhány napon belül újra ezek hamarosan elérhetővé válnak.
> 
> 

## <a name="whats-new-in-update-12"></a>Az 1.2-es frissítés újdonságai
Ezeket a funkciókat először az 1. Az Update 1.2 kiadás, a legtöbb StorSimple felhasználók látná a következő új funkciók és fejlesztések:

* **Az 5000-7000 sorozatú eszközökről 8000 sorozatú eszközökre való áttérés** – Ez a kiadás egy új áttelepítési funkciót vezet be, amely lehetővé teszi a StorSimple 5000-7000 sorozatú készülékfelhasználók számára, hogy adataikat egy StorSimple 8000 sorozatú fizikai készülékre vagy virtuális készülékre telepítsék át. Az áttelepítési funkció nak két fő értékjavaslata van:                                                                  
  
  * **Az üzletmenet folytonossága**azáltal, hogy lehetővé teszi az 5000-7000 sorozatú készülékek meglévő adatainak 8000-es sorozatú készülékekre való migrálását.
  * **A 8000-es sorozatú készülékek továbbfejlesztett funkciókínálatai,** mint például a StorSimple Manager szolgáltatáson keresztül több készülék hatékony központosított kezelése, a hardver és a frissített firmware jobb osztálya, a virtuális készülékek, az adatmobilitás és a jövőbeli ütemterv funkciói.
    
    A StorSimple 5000-7000 sorozat 8000-es sorozatú eszközökre való áttelepítéséről az [áttelepítési útmutatóban](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) talál részleteket. 
* **Az Azure Government Portalon elérhető** – a StorSimple már elérhető az Azure Government portálon. Megtudhatja, hogyan [helyezhet üzembe egy StorSimple-eszközt az Azure Government Portalon.](storsimple-deployment-walkthrough-gov.md)
* **Más felhőszolgáltatók támogatása** – A többi támogatott felhőszolgáltató az Amazon S3, az Amazon S3 RRS, A HP és az OpenStack (béta).
* **Frissítés a legújabb tárolási API-kra** – Ezzel a kiadással a StorSimple frissült a legújabb Azure Storage-szolgáltatás API-kra. Az 1. 2016. augusztus 1-ig a [Storage-szolgáltatás verzióinak eltávolításáról szóló](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx)frissített közleményben foglaltaknak megfelelően ezek az API-k elavultak lesznek. Elengedhetetlen, hogy a StorSimple 8000 sorozat 1-es frissítése 2016 augusztus 1-je előtt legyen. Ha ezt nem teszi meg, a StorSimple eszközök nem fognak megfelelően működni.
* **Zónaredundáns tárolás támogatása (ZRS)** – A Storage API-k legújabb verziójára való frissítéssel a StorSimple 8000 sorozat a helyileg redundáns tárolás (LRS) és a georedundáns tárolás (GRS) mellett támogatja a zónaredundáns tárolást (ZRS). A ZRS-részletekért tekintse meg ezt [a cikket az Azure Storage redundancia-beállításairól.](../storage/common/storage-redundancy.md)
* **Továbbfejlesztett kezdeti üzembe helyezési és frissítési élmény** – Ebben a kiadásban a telepítési és frissítési folyamatok továbbfejlesztettek. A telepítővarázslón keresztülvégzett telepítés továbbhalad, hogy visszajelzést adjon a felhasználónak, ha a hálózati konfiguráció és a tűzfal beállításai helytelenek. További diagnosztikai parancsmagokkal segítünk az eszköz hálózati hibáinak elhárításában. A [hibaelhárításhoz](storsimple-troubleshoot-deployment.md) használt új diagnosztikai parancsmagokról a hibaelhárításhoz használt hibaelhárítási útmutatóban olvashat bővebben.

## <a name="issues-fixed-in-update-12"></a>Az 1.2-es frissítésben javított problémák
Az alábbi táblázat az 1.2-es, 1.1-es és 1-es frissítésekben javított problémákat tartalmazza.    

| Nem. | Szolgáltatás | Probléma | Javítva a frissítésben | Fizikai eszközre vonatkozik | Virtuális eszközre vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Windows PowerShell storsimple-hez |Amikor egy felhasználó távolról hozzáfért a StorSimple eszközhöz a Windows PowerShell for StorSimple használatával, majd elindította a telepítővarázslót, összeomlás történt, amint a Data 0 IP-t beírta. Ez a hiba most javítva az 1. |1. frissítés |Igen |Igen |
| 2 |Gyári beállítások visszaállítása |Bizonyos esetekben, amikor a gyári beállítások visszaállítása, a StorSimple eszköz beragadt, és megjelenik ez az üzenet: **Visszaad a gyári folyamatban van (fázis 8)**. Ez akkor történt, ha a billentyűkombináció+C billentyűkombinációt a parancsmag folyamatban lévő állapotában nyomta meg. Ez a hiba most javítva. |1. frissítés |Igen |Nem |
| 3 |Gyári beállítások visszaállítása |Miután egy sikertelen kettős vezérlő gyári beállítások visszaállítása, akkor lehetővé tette, hogy folytassa az eszköz regisztrációját. Ez nem támogatott rendszerkonfigurációt eredményezett. Az 1. |1. frissítés |Igen |Nem |
| 4 |Gyári beállítások visszaállítása |Bizonyos esetekben hamis pozitív eltérési riasztások merültek fel. Az 1. |1. frissítés |Igen |Nem |
| 5 |Gyári beállítások visszaállítása |Ha a gyári beállítások visszaállítása a befejezés előtt megszakadt, az eszköz helyreállítási módba lépett, és nem tette lehetővé a Windows PowerShell storSimple-hez való elérését. Ez a hiba most javítva. |1. frissítés |Igen |Nem |
| 6 |Vészhelyreállítás |A vész-helyreállítási (DR) hiba lett javítva, amelyben a DR sikertelen lenne a céleszközön lévő biztonsági mentések felderítése során. |1. frissítés |Igen |Igen |
| 7 |LED-ek figyelése |Bizonyos esetekben a készülék hátulján lévő LED-ek figyelése nem jelezte a helyes állapotot. A kék LED ki volt kapcsolva. A DATA 0 és a DATA 1 LED-ek akkor is villogtak, ha ezek a felületek nincsenek konfigurálva. A probléma ki lett javítva, és a LED-ek figyelése most a helyes állapotot jelzi. |1. frissítés |Igen |Nem |
| 8 |LED-ek figyelése |Bizonyos esetekben az 1. A javítás ezen kiadásában a probléma ki lett javítva. |1.2-es frissítés |Igen |Nem |
| 9 |Hálózati illesztők |A korábbi verziókban egy nem irányítható átjáróval konfigurált StorSimple-eszköz offline állapotba kerülhet. Ebben a kiadásban a Data 0 útválasztási metrikája lett a legalacsonyabb; ezért még akkor is, ha más hálózati adapterek felhőalapú, az eszközről érkező összes felhőforgalom a Data 0-n keresztül lesz irányítva. |1. frissítés |Igen |Igen |
| 10 |Biztonsági másolatok |Az 1.1-es frissítés ben egy hiba, amely miatt a biztonsági mentések 24 nap után meghibásodtak, ki lett javítva az 1.1-es frissítésben. |1.1-es frissítés |Igen |Igen |
| 11 |Biztonsági másolatok |A korábbi verziókban fellépő hiba gyenge teljesítményt eredményezett az alacsony változási rátával rendelkező felhőbeli pillanatképek esetében. Ez a hiba ki lett javítva ebben a javítási kiadásban. |1.2-es frissítés |Igen |Igen |
| 12 |Frissítések |Ebben a javítási kiadásban kijavították az 1. |1.2-es frissítés |Igen |Igen |

## <a name="known-issues-in-update-12"></a>Ismert problémák az 1.2-es frissítésben
Az alábbi táblázat az ebben a kiadásban ismertetett ismert problémákat tartalmazza.

| Nem. | Szolgáltatás | Probléma | Megjegyzések/kerülő megoldás | Fizikai eszközre vonatkozik | Virtuális eszközre vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Lemez kvóruma |Ritka esetekben, ha egy 8600-as eszköz EBOD-házában lévő lemezek többsége le van választva, így nincs lemezkvórum, akkor a tárolókészlet offline állapotban lesz. Akkor is offline állapotban marad, ha a lemezek újra csatlakoznak. |Újra kell indítania az eszközt. Ha a probléma továbbra is fennáll, a következő lépésekért forduljon a Microsoft támogatási szolgálatához. |Igen |Nem |
| 2 |Helytelen vezérlőazonosító |A vezérlő cseréjekor a 0 vezérlő vezérlő 1 vezérlőként jelenhet meg. A vezérlő cseréje során, amikor a rendszerkép betöltődik a társcsomópontról, a vezérlőazonosító kezdetben a társvezérlő azonosítójaként jeleníthető meg. Ritka esetekben ez a viselkedés a rendszer újraindítása után is látható. |Nincs szükség felhasználói műveletre. Ez a helyzet magától megoldódik, miután a vezérlő cseréje befejeződött. |Igen |Nem |
| 3 |Tárfiókok |A storage szolgáltatás a tárfiók törlése nem támogatott forgatókönyv. Ez olyan helyzetet eredményez, amelyben a felhasználói adatok nem olvashatók be. |Igen |Igen | |
| 4 |Eszköz feladatátvétel |Egy kötettároló több feladatátvétele ugyanarról a forráseszközről a különböző céleszközökre nem támogatott. Az egyetlen lehalt eszközről több eszközre történő eszközfeladat-átvétel miatt az első feladatátvételi eszközön a kötettárolók elveszítik az adatok tulajdonjogát. Egy ilyen feladatátvétel után ezek a kötettárolók jelennek meg, vagy másképp viselkednek, amikor megtekinti őket az Azure klasszikus portálon. | |Igen |Nem |
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

## <a name="physical-device-updates-in-update-12"></a>Fizikai eszközfrissítések az 1.2-es frissítésben
Ha az 1.2-es javítást fizikai eszközre alkalmazza (az 1. frissítés előtti verziók futtatása), a szoftver verziója 6.3.9600.17584-re változik.

## <a name="controller-and-firmware-updates-in-update-12"></a>Vezérlő és firmware frissítések az 1.2-es frissítésben
Ez a kiadás frissíti az illesztőprogramot és a lemez belső vezérlőprogramját az eszközön.

* A SAS-vezérlő frissítéséről a [Microsoft Azure StorSimple Appliance LSI SAS-vezérlők 1.](https://support.microsoft.com/kb/3043005) 
* A lemez belső vezérlőprogramjának frissítéséről a [Lemez belső vezérlőprogramjának 1.](https://support.microsoft.com/kb/3063416)

## <a name="virtual-device-updates-in-update-12"></a>Virtuális eszközfrissítések az 1.2-es frissítésben
Ez a frissítés nem alkalmazható a virtuális eszközre. Új virtuális eszközöket kell létrehozni. 

## <a name="next-steps"></a>További lépések
* [Telepítse az 1.2-es frissítést az eszközre.](storsimple-install-update-1.md)

