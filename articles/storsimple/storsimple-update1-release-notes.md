---
title: A StorSimple 8000 Series Update 1.2 kibocsátási megjegyzései |} Microsoft Docs
description: Az új funkciók, problémák és megoldások ismerteti a StorSimple 8000 Series Update 1.2-es.
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
ms.openlocfilehash: 7810027e4ab9df6742b4431d12daf9ba2b678bf6
ms.sourcegitcommit: f06925d15cfe1b3872c22497577ea745ca9a4881
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/27/2018
ms.locfileid: "37061809"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>1.2-es kibocsátási megjegyzések a StorSimple 8000 series eszköz frissítése

## <a name="overview"></a>Áttekintés
Az alábbi kibocsátási megjegyzések új szolgáltatásait ismerteti, és a StorSimple 8000 Series Update 1.2 a kritikus megnyitott problémák azonosításához. A StorSimple szoftver, illesztőprogram és lemez belső vezérlőprogram-frissítésekre ebben a kiadásban szereplő listáját is tartalmazzák. 

1.2-es frissítés bármely kiadásban (GA), a frissítés 0,1, a frissítés 0,2 vagy a szoftverfrissítés 0,3 futtató StorSimple-eszközre alkalmazhatók. 1.2-es frissítés nem érhető el, ha az eszköz fut, 1. frissítés vagy frissítési 1.1. Ha az eszköz kiadásban (GA) fut, akkor segítségért [forduljon a Microsoft Support](storsimple-contact-microsoft-support.md) segítik a frissítés telepítése.

Az alábbi táblázatban a megfelelő frissítéseket, 1, 1.1-es és 1.2-es eszköz szoftververziók.

| Ha az update futtatása... | Ez az eszköz szoftververziók. |
| --- | --- |
| 1.2-es frissítés |6.3.9600.17584 |
| 1.1-es frissítés |6.3.9600.17521 |
| Frissítés 1.0 |6.3.9600.17491 |

Tekintse át a kibocsátási megjegyzések a StorSimple megoldásban a frissítés telepítése előtt található információkat. További információkért lásd: hogyan [1.2-es frissítés telepítése a StorSimple eszköz](storsimple-install-update-1.md). 

> [!IMPORTANT]
> * Ez a frissítés (beleértve a Windows-frissítések) körülbelül 5 – 10 órát vesz igénybe. 
> * 1.2-es frissítés szoftver, a LSI illesztőprogram és a belső vezérlőprogram-frissítésekre van. Telepítéséhez kövesse az utasításokat a [1.2-es frissítés telepítése a StorSimple eszköz](storsimple-install-update-1.md).
> * Új kiadásokban nem láthatók frissítések azonnal, mert a frissítések fázisokra bontva történő bevezetéséhez végezzük. A néhány nap múlva újra a frissítések keresését, ezek hamarosan elérhető lesz.
> 
> 

## <a name="whats-new-in-update-12"></a>1.2-es frissítés újdonságai
Ezek a funkciók először kiadott frissítés 1., amely csak bizonyos felhasználók számára elérhető történt. A frissítés 1.2-es kiadással a következő új szolgáltatásait és fejlesztéseit a StorSimple-felhasználók a legtöbb jelenik meg:

* **5000-7000-es adatsorozat 8000 sorozat eszközeire áttelepítés** – ebben a kiadásban bevezet egy új áttelepítési szolgáltatás, amely lehetővé teszi a StorSimple 5000-7000-es adatsorozat készülék felhasználók számára az adatok áttelepítése a StorSimple 8000 series fizikai készülék vagy egy virtuális készüléket. Az áttelepítési funkcióját két fő értéknövelő rendelkezik:                                                                  
  
  * **Az üzletmenet folytonossága**, mivel lehetővé teszi az 5000-7000-es adatsorozat készülékek 8000 sorozat készülékek számára a meglévő adatok áttelepítésének.
  * **Továbbfejlesztett szolgáltatás ajánlatok 8000 sorozat készülékek**, hatékony központi felügyelet több készülékek StorSimple Manager szolgáltatással, például jobb osztály hardver és a belső vezérlőprogram, a virtuális készülékek, az adatok mobilitási frissítése és a jövőbeli terv szolgáltatások.
    
    Tekintse meg a [áttelepítési útmutató](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) a további tudnivalókat hogyan telepítheti át a StorSimple 8000 series eszközön való 5000-7000-es adatsorozat. 
* **Az Azure Government portálon rendelkezésre állási** – StorSimple már elérhető az Azure Government portálon. Lásd: hogyan [az Azure Government portálon a StorSimple eszköz üzembe helyezése](storsimple-deployment-walkthrough-gov.md).
* **Egyéb felhőszolgáltatók támogatása** – az egyéb felhőszolgáltatók támogatott Amazon S3, Amazon S3 with RRS, HP és OpenStack (béta).
* **Frissítés a legújabb Storage API-k** – ezzel a kiadással, a StorSimple is frissült a legújabb Azure Storage szolgáltatás API-k. 1 frissítés előtti szoftvert verzióját futtató StorSimple 8000 sorozat eszközeire (kiadásban 0,1, 0,2 és 0,3) az Azure Storage szolgáltatás API-k 2009. július 17 régebbi verzióját használja. Ahogy az a frissített [kapcsolatos tárolási szolgáltatásverziók eltávolításának hirdetmény](http://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx), 1 megjelenésével 2016 augusztusától, amelyet ezen API-k elavulttá válik. Rendkívül fontos, hogy érvényesek-e a StorSimple 8000 Series Update 1 2016 augusztusától 1 előtt. Ha nem sikerül, a StorSimple eszköz leáll, megfelelően működik-e.
* **Támogatja a zóna redundáns tárolás (ZRS)** – a frissítést a legújabb verzióra a Storage API-k, a StorSimple 8000 series zóna redundáns tárolás (ZRS) helyileg redundáns tárolás (LRS) és a georedundáns tárolás (GRS mellett támogatja ). Tekintse meg a [cikk az Azure Storage redundancia beállítások](../storage/common/storage-redundancy.md) zrs-t a részletekért.
* **Kezdeti telepítés és a frissítési élmény fokozott** – ebben a kiadásban a telepítési és frissítési folyamat esett át. A telepítővarázsló a telepítési tovább lett fejlesztve, visszajelzést a felhasználó számára, ha a hálózati konfigurációs és a tűzfal beállításai helytelenek. Hibaelhárítás az eszköz hálózatkezelési további diagnosztikai parancsmagok kapott. Tekintse meg a [telepítési cikk hibaelhárítási](storsimple-troubleshoot-deployment.md) a hibaelhárításhoz használható diagnosztikai parancsmagjai további információt.

## <a name="issues-fixed-in-update-12"></a>A frissítés 1.2 megoldott problémák
A következő táblázat összefoglalja, 1, 1.2-es és 1.1-es frissítésben javított problémák.    

| Nem. | Szolgáltatás | Probléma | A rögzített frissítés | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |A Windows PowerShell-lel |Ha egy felhasználó távolról érhető el a StorSimple eszközt a StorSimple Windows PowerShell használatával, és a telepítővarázsló majd el, egy összeomlás, amint Data 0 IP lett bemeneti történt. A hiba az 1. frissítés most javítását. |1. frissítés |Igen |Igen |
| 2 |Gyári beállítások visszaállítása |Bizonyos esetekben a gépen hajtotta végre a gyári beállítások visszaállítása, ha a StorSimple eszköz akadt vált, és ez az üzenet jelenik meg: **a gyári alaphelyzetbe állítása folyamatban van a (fázis 8)**. Ez történt, ha a CTRL + C billentyűt megnyomva, miközben folyamatban volt a parancsmagot. A hiba most javítását. |1. frissítés |Igen |Nem |
| 3 |Gyári beállítások visszaállítása |Miután egy hibás kettős vezérlő gyári alaphelyzetbe állítása, hozhattak eszközregisztráció folytatásához. Ennek következtében a nem támogatott rendszerben. 1. frissítés egy hibaüzenet jelenik meg, és regisztrációs le van tiltva az eszközön, hogy rendelkezik egy sikertelen vissza a gyári beállításokat. |1. frissítés |Igen |Nem |
| 4 |Gyári beállítások visszaállítása |Bizonyos esetekben a téves pozitív eltérés figyelmeztetések fordultak elő. 1. frissítést futtató eszközök nem lesznek generálva riasztások helytelen eltérés. |1. frissítés |Igen |Nem |
| 5 |Gyári beállítások visszaállítása |A gyári beállítások visszaállítása befejezése előtt megszakadt, az eszköz a megadott helyreállítási módban, és nem engedélyezte a Windows PowerShell a StorSimple eléréséhez. A hiba most javítását. |1. frissítés |Igen |Nem |
| 6 |Vészhelyreállítás |A vész-helyreállítási hiba volt rögzített, amelynek a vész-Helyreállítási sikertelen lesz a céleszközt a biztonsági mentések a felderítés során. |1. frissítés |Igen |Igen |
| 7 |Figyelési LED |Bizonyos esetekben a figyelési LED hátulján készülék található nem jelezte megfelelő állapotáról. A kék LED ki lett kapcsolva. DATA 0 és 1 LED adatok villogó volt, még ha a felületek nem voltak konfigurálva. A probléma megoldását, és figyelési LED most jelzi a megfelelő állapotáról. |1. frissítés |Igen |Nem |
| 8 |Figyelési LED |Bizonyos esetekben a 1. frissítés alkalmazása után a kék világos aktív vezérlőjén ki van kapcsolva és az nehéz lenne azonosítani az aktív vezérlőhöz. A probléma kijavítása a javítás kiadásban. |1.2-es frissítés |Igen |Nem |
| 9 |Hálózati illesztők |A korábbi verziókban nem átirányítható átjáróként konfigurált a StorSimple eszköz sikerült kapcsolat nélkül. Ebben a kiadásban a Data 0 útválasztási metrikáját, új végzett a legalacsonyabb; ezért még akkor is, ha más hálózati illesztők felhő engedélyezve, a felhő származó összes forgalmat az eszközön keresztül lesznek átirányítva Data 0. |1. frissítés |Igen |Igen |
| 10 |Biztonsági másolatok |Frissítés az 1. biztonsági másolatok követően 24 napos okozó hiba a javítás kiadásban frissítés 1.1 kijavítása. |1.1-es frissítés |Igen |Igen |
| 11 |Biztonsági másolatok |A korábbi verziókban programhiba gyenge teljesítményt, a felhőalapú pillanatfelvételek alacsony módosítás aránnyal eredményezett. Ez a hiba kijavítása ebben a kiadásban javítás. |1.2-es frissítés |Igen |Igen |
| 12 |Frissítések |Sikertelen frissítés jelentett és, hogy a tartományvezérlők módba helyreállítási, 1. frissítés hiba a javítás kiadásban kijavítása. |1.2-es frissítés |Igen |Igen |

## <a name="known-issues-in-update-12"></a>A frissítés 1.2 ismert problémák
A következő táblázat összefoglalja az ismert problémákról, ebben a kiadásban.

| Nem. | Szolgáltatás | Probléma | Megjegyzések/megoldás | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Lemez kvórum |Bizonyos ritkán előforduló esetekben ha a legtöbb, a EBOD ház egy 8600 eszköz lemezek megszakadt a kapcsolat nincs lemez kvórum eredményezi majd a tárolókészlet lesz kapcsolva a hálózatról. Offline állapotban maradnak, még akkor is, ha a lemez újracsatlakoztatását. |Szüksége lesz az eszköz újraindul. Ha a probléma továbbra is fennáll, forduljon a Microsoft Support további lépéseket. |Igen |Nem |
| 2 |Helytelen vezérlő azonosítója |A vezérlő helyettesítő végrehajtásakor vezérlő 0 lehet, hogy megjelennek, a vezérlő 1. Tartományvezérlő csere közben, amikor a kép betöltődik a társ-csomópont a vezérlő azonosítója is megjelennek kezdetben, a partner tartományvezérlő azonosítóját. Ritka esetben ez a viselkedés előfordulhat, hogy is látható. a rendszer újraindítását követően. |Nincs felhasználói beavatkozásra szükség. Ez a helyzet magától megoldódik, amikor a vezérlő helyettesítő befejeződése után. |Igen |Nem |
| 3 |Tárfiókok |A Storage szolgáltatás használatával törli a tárfiókot a nem támogatott lehetőséget. Ez a helyzet, amelyben felhasználói nem lehet adatokat beolvasni irányítja. |Igen |Igen | |
| 4 |Eszköz feladatátvétel |A különböző céleszközökre a azonos forráseszközről származó kötettároló több feladatátvétel nem támogatott. Eszköz feladatátvétel az egyetlen elhalt eszközt több eszközre működésképtelenné teszi az adatok tulajdonjoga elvesztése átadja a feladatokat eszköz első kötettárolók. Egy feladatátvétel után ezek kötettárolók jelenik meg, vagy a klasszikus Azure portálon meg eltérően viselkednek. | |Igen |Nem |
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

## <a name="physical-device-updates-in-update-12"></a>A frissítés 1.2 fizikai eszköz frissítése
Ha javítás frissítése egy fizikai eszköz (1. frissítés előtti verziót futtató) 1.2-es alkalmazzák, akkor a szoftververzió 6.3.9600.17584 módosul.

## <a name="controller-and-firmware-updates-in-update-12"></a>A frissítés 1.2-es tartományvezérlő és a belső vezérlőprogram frissítések
Ebben a kiadásban frissíti, az illesztőprogram és az eszközön a lemez belső vezérlőprogramját.

* A SAS-vezérlő frissítéssel kapcsolatos további információkért lásd: [Update 1 LSI SAS-vezérlők, a Microsoft Azure StorSimple készülék](https://support.microsoft.com/kb/3043005). 
* A lemez belső vezérlőprogram frissítéssel kapcsolatos további információkért lásd: [lemez belső vezérlőprogram frissítése 1 Microsoft Azure StorSimple alapplatformjaként](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>1.2-es frissítés a virtuális eszköz frissítése
A frissítés nem alkalmazható a virtuális eszköz. Új virtuális eszközök kell létrehozni. 

## <a name="next-steps"></a>További lépések
* [A saját eszközére telepített frissítés 1.2](storsimple-install-update-1.md).

