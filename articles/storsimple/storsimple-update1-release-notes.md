---
title: A StorSimple 8000 Series Update 1.2-es kibocsátási megjegyzései |} A Microsoft Docs
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
ms.openlocfilehash: 11138857e33eec0f854ddb61956ea24c858c49a5
ms.sourcegitcommit: da3459aca32dcdbf6a63ae9186d2ad2ca2295893
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/07/2018
ms.locfileid: "51258986"
---
# <a name="update-12-release-notes-for-your-storsimple-8000-series-device"></a>A StorSimple 8000 sorozatú eszköz kibocsátási megjegyzései 1.2-es frissítés

## <a name="overview"></a>Áttekintés
A következő kiadási megjegyzések az új funkciók ismertetik, és a StorSimple 8000 Series Update 1.2-es a kritikus fontosságú megoldatlan problémák azonosításához. A StorSimple szoftver, illesztőprogram és szerepelnek ebben a kiadásban lemezfirmware-frissítések listáját is tartalmazhatja. 

1.2-es frissítés bármely kiadásban elérhetővé tétel (GA), 0.1-es frissítés, a 0.2 vagy a 0.3-szoftvert futtató StorSimple-eszközre alkalmazhatók. 1.2-es frissítés nem érhető el, ha az eszköz fut, 1. frissítés vagy frissítési 1.1. Ha az eszköz fut a kiadásban elérhetővé tétel (GA), [forduljon a Microsoft Support](storsimple-contact-microsoft-support.md) segítséget nyújtanak a frissítés telepítése.

A következő táblázat felsorolja a megfelelő frissítéseket, 1, 1.1 és 1.2-es eszköz szoftververziók.

| Ha a frissítés futtatása... | Ez az eszköz szoftververzióján. |
| --- | --- |
| 1.2-es frissítés |6.3.9600.17584 |
| 1.1-es frissítés |6.3.9600.17521 |
| 1.0-ás frissítés |6.3.9600.17491 |

Tekintse át a kibocsátási megjegyzések a StorSimple-megoldásban a frissítés telepítése előtt található információkat. További információkért lásd: hogyan [frissítés 1.2 telepítse a StorSimple-eszköz](storsimple-install-update-1.md). 

> [!IMPORTANT]
> * Ez a frissítés (többek között a Windows-frissítések) körülbelül 5 – 10 órát vesz igénybe. 
> * 1.2-es frissítés rendelkezik a szoftver, LSI-illesztőprogram és lemezfirmware-frissítések. Ha telepíteni szeretné, kövesse a [frissítés 1.2 telepítse a StorSimple-eszköz](storsimple-install-update-1.md).
> * Az új kiadásokhoz, akkor előfordulhat, hogy nem jelenik meg frissítések azonnal, mivel egy fázisokra bontva történő bevezetéséhez a frissítések végzünk. Néhány nap alatt újra a frissítések keresését, ezek hamarosan elérhetővé válik.
> 
> 

## <a name="whats-new-in-update-12"></a>1.2-es frissítés újdonságai
Ezek a funkciók először jelentek meg, amely elérhető a felhasználók egy korlátozott csoportja történt Update 1. Az 1.2-es frissítés a kiadástól kezdve a StorSimple-felhasználók a legtöbb jelennének meg a következő új funkciókat és fejlesztéseket:

* **5000 – 7000 sorozatú 8000 sorozatú eszközök áttelepítés** – ebben a kiadásban új áttelepítési funkciója, amely lehetővé teszi a StorSimple 5000-7000-es sorozat készülék felhasználók számára az adatok áttelepítését egy StorSimple 8000 sorozatú fizikai készülék és a egy virtuális vezet be. berendezés. A migrálási szolgáltatás két fő értéknövelő rendelkezik:                                                                  
  
  * **Üzletmenet-folytonossági**, 5000 – 7000 sorozatú készülékek a 8000-es sorozatú készülékek a meglévő adatok áttelepítésének engedélyezésével.
  * **A 8000-es sorozatú készülékek a szolgáltatás által kínált továbbfejlesztett**, hatékony központi felügyelet több berendezések keresztül a StorSimple Manager szolgáltatás, például jobb az osztály a hardver, és frissítette a belső vezérlőprogram, a virtuális készülékek, rugalmasan áthelyezhető adatok, és a szolgáltatások a jövőbeli ütemterv részét képezi.
    
    Tekintse meg a [áttelepítési útmutató](https://gallery.technet.microsoft.com/Azure-StorSimple-50007000-c1a0460b) a részletek történő áttelepítés egy StorSimple 8000 sorozatú eszközre 5000 – 7000 sorozatú. 
* **Az Azure Government Portalon rendelkezésre állási** – StorSimple már elérhető az Azure Government Portalon. Lásd: hogyan [az Azure Government Portalon a StorSimple eszköz üzembe helyezése](storsimple-deployment-walkthrough-gov.md).
* **Más felhőszolgáltatók támogatása** – a más felhőszolgáltatók támogatott az Amazon S3, Amazon S3 with RRS, HP és OpenStack (bétaverzió).
* **Frissítés a legújabb Storage API-k** – ebben a kiadásban a StorSimple frissítve lett, hogy a legújabb Azure Storage szolgáltatás API-k. 1. frissítés előtti szoftvert verzióit futtató StorSimple 8000 sorozatú eszközök (verzió, a 0.1, 0.2-es és 0,3) az Azure Storage szolgáltatás API-k 2009. július 17-nál régebbi verzióját használja. Az a frissített [tárolási szolgáltatásverziók eltávolításának közleményét](https://blogs.msdn.com/b/windowsazurestorage/archive/2015/10/19/microsoft-azure-storage-service-version-removal-update-extension-to-2016.aspx), a 2016. augusztus 1-én elavulnak ezen API-k. Rendkívül fontos, hogy érvényesek-e a StorSimple 8000 Series Update 1 2016. augusztus 1. előtt. Ehhez nem, ha leáll a StorSimple-eszközök megfelelően működik-e.
* **A Zónaredundáns Társzolgáltatási (ZRS) támogatják** – a frissítést a legújabb verzióra a Storage API-k, a StorSimple 8000 sorozat támogatni fogja a Zónaredundáns Társzolgáltatási (ZRS) helyileg redundáns tárolás (LRS) és a georedundáns tárolás (GRS mellett ). Ebben [a cikk az Azure Storage redundanciabeállításai](../storage/common/storage-redundancy.md) ZRS részleteiről.
* **Továbbfejlesztett kezdeti telepítési és frissítési élmény** – ebben a kiadásban a telepítési és frissítési folyamat fokozott. A telepítési folyamatot a telepítővarázsló javult a visszajelzés a felhasználónak, ha a hálózati konfigurációt és a tűzfal beállításai helytelenek. További diagnosztikai parancsmag, az eszköz hálózatkezelési hibaelhárítás adtak meg. Tekintse meg a [hibaelhárítási cikk üzembe helyezési](storsimple-troubleshoot-deployment.md) további információ az új, a hibakeresés diagnosztikai parancsmagokat.

## <a name="issues-fixed-in-update-12"></a>Az 1.2-es frissítés megoldott problémák
Az alábbi táblázat összefoglalja az 1.2-es, 1.1-es és 1-frissítésekben kijavított problémák.    

| Nem. | Szolgáltatás | Probléma | Kijavítva a frissítés | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Windows PowerShell-bővítménye |Ha egy felhasználó távolról elérhető a StorSimple-eszköz storsimple-höz készült Windows PowerShell használatával, és majd a varázsló elindítása, egy összeomlási, amint Data 0 IP volt bemeneti történt. 1. frissítés most javítja ezt a hibát. |1. frissítés |Igen |Igen |
| 2 |Gyári beállítások visszaállítása |Bizonyos esetekben hajtotta végre a gyári beállítások visszaállítása, ha a StorSimple eszköz elakadt vált, és ez az üzenet jelenik meg: **gyári alaphelyzetbe állítása folyamatban van (8. fázis)**. Ez azért történt, miközben folyamatban volt a parancsmag lenyomása CTRL + C billentyűkombinációt. Most már rögzített ezt a hibát. |1. frissítés |Igen |Nem |
| 3 |Gyári beállítások visszaállítása |Egy hibás kettős vezérlők gyári beállítások visszaállítása, miután a is engedélyezett folytatásához az eszközök regisztrációját. Ennek következtében nem támogatott rendszerben. Az 1. frissítésben egy hibaüzenet jelenik meg, és a regisztráció le van tiltva az eszközön, hogy rendelkezik egy sikertelen gyári beállítások visszaállítása. |1. frissítés |Igen |Nem |
| 4 |Gyári beállítások visszaállítása |Bizonyos esetekben hamis pozitív eltérés riasztásokat is következik be. 1. frissítést futtató eszközök már nem lesznek generálva riasztások helytelen eltérés. |1. frissítés |Igen |Nem |
| 5 |Gyári beállítások visszaállítása |Gyári beállítások visszaállítása befejezése előtt megszakadt, ha az eszköz a megadott helyreállítási módban, és nem engedélyezte a storsimple-höz készült Windows PowerShell eléréséhez. Most már rögzített ezt a hibát. |1. frissítés |Igen |Nem |
| 6 |Vészhelyreállítás |A vész-helyreállítási hiba rendszerriasztások mechanizmusában viselkedésmintáit a DR sikertelen lesz a biztonsági mentések a céleszközön a felderítés során. |1. frissítés |Igen |Igen |
| 7 |Figyelési LED-EK |Bizonyos esetekben a figyelési LED-ek hátulján berendezés található fejeződött nem megfelelő állapotát jelzi. A kék LED ki lett kapcsolva. DATA 0 és 1 LED-ek adatok villogó lett, még ha ezeken a felületeken is nincs konfigurálva. A probléma megoldódott, és figyelési LED-ek már megfelelő állapotát jelzi. |1. frissítés |Igen |Nem |
| 8 |Figyelési LED-EK |Bizonyos esetekben a 1. frissítés alkalmazása után az aktív vezérlőn a kék világos ki van kapcsolva így az nehéz az aktív vezérlő megkeresése. Ez a hiba elhárítása a patch-kiadásban. |1.2-es frissítés |Igen |Nem |
| 9 |Hálózati illesztők |A korábbi verziókban nem átirányítható átjáróként konfigurált StorSimple-eszköz kapcsolat nélküli sikerült módra. Ebben a kiadásban a Data 0 útválasztási metrikát lett végzett a legalacsonyabb; ezért akkor is, ha más hálózati adapterek a felhőben, a az eszközre érkező minden felhő az adat 0-n keresztül történik. |1. frissítés |Igen |Igen |
| 10 |Biztonsági másolatok |Egy hiba, az Update 1, ami miatt a biztonsági mentések sikertelenségét 24 nap után megoldottuk a patch kiadásban 1.1-es frissítés. |1.1-es frissítés |Igen |Igen |
| 11 |Biztonsági másolatok |Egy hiba a korábbi verziókban a felhőbeli pillanatképekkel kevéssé aránnyal gyenge teljesítményt eredményezett. A hiba a javítás kiadás megoldották. |1.2-es frissítés |Igen |Igen |
| 12 |Frissítések |A patch kiadás tartalmaz hibát jelentett meghiúsult frissítést és miatt lépnek helyreállítási módba, a vezérlő 1. frissítés megoldották. |1.2-es frissítés |Igen |Igen |

## <a name="known-issues-in-update-12"></a>Ismert problémák az 1.2-es frissítés
Az alábbi táblázat összefoglalja az ismert problémákról, ebben a kiadásban.

| Nem. | Szolgáltatás | Probléma | Megjegyzések és megoldás | Fizikai eszköz vonatkozik | Virtuális eszköz vonatkozik |
| --- | --- | --- | --- | --- | --- |
| 1 |Lemez kvórum |Ritka esetekben a többsége a EBOD ház-8600-as eszköz a lemezek le vannak választva, nincs lemez kvórum, ami akkor a tárolókészlet lesz offline állapotban van. Offline állapotban marad, akkor is, ha a lemez újracsatlakoztatását. |Indítsa újra az eszközt kell. Ha a probléma tartósan fennáll, forduljon a Microsoft Support a következő lépéseket. |Igen |Nem |
| 2 |Nem megfelelő vezérlő azonosítója |A vezérlő helyettesítő hajtja végre, amikor 0. vezérlő előfordulhat, hogy megjelenjen 1. vezérlő. Csere, a vezérlő során a kép betöltésekor a társ-csomópontból a vezérlő azonosító konferenciateremként jelenjen meg kezdetben a partner-tartományvezérlő-azonosító néven Ritka esetekben a rendszer újraindítását követően is ezt a viselkedést is látható. |Nincs szükség felhasználói beavatkozásra. Ez a helyzet megoldódik, a vezérlő cseréje befejeződése után. |Igen |Nem |
| 3 |Tárfiókok |A Storage szolgáltatás törli a storage-fiók használata nem támogatott forgatókönyv. Ez a helyzet, amelyben a felhasználói adatokat nem lehet beolvasni vezet. |Igen |Igen | |
| 4 |Eszköz-feladatátvétel |A kötettároló a azonos forráseszközről származó különböző céleszközökre több lehetségessé nem támogatott. Eszköz feladatátvételi egyetlen kézbesíthetetlen eszközről több eszközre fogja elérhetővé tenni a kötettároló feladatátvétele az eszköz első elveszíti az adatok tulajdonjoga. A feladatátvétel után ezek kötettárolók jelennek meg, illetve eltérően viselkednek, megtekintheti őket a klasszikus Azure portálon. | |Igen |Nem |
| 5 |Telepítés |StorSimple Adapter for SharePoint telepítési, során meg kell adnia egy eszköz IP-cím ahhoz, hogy a telepítés sikeres befejezéséhez. | |Igen |Nem |
| 6 |Webproxy |-E a webproxy konfigurálása a megadott protokoll HTTPS-t, az eszköz-to-service kommunikációs hatással lesz, és kerül, hogy az eszköz offline állapotban van. Támogatási csomag is jön létre, a folyamat, jelentős erőforrásokat az eszközön. |Győződjön meg arról, hogy a webalkalmazás-proxy URL-címe van-e a HTTP protokoll megadott. További információ: [Configure web proxy for your device](storsimple-configure-web-proxy.md) (Webproxy beállítása az eszközhöz). |Igen |Nem |
| 7 |Webproxy |Ha konfigurálja, és engedélyezze a webalkalmazás-proxy egy regisztrált eszközön, majd kell indítania az aktív vezérlőn az eszközön. | |Igen |Nem |
| 8 |Felhőalapú nagy késést és nagy i/o-munkaterhelés |A StorSimple eszköz (sorrendben másodperc) nagyon magas felhőalapú késéseket és nagy i/o-munkaterhelés ütközik, amikor az eszköz köteteihez egy csökkentett teljesítményű állapotba kerülnek, és az i/o "az eszköz nem áll készen" hiba miatt sikertelenek lehetnek. |Szüksége lesz, manuálisan indítsa újra a eszközvezérlők vagy helyreállíthatja az ebben a helyzetben eszköz feladatátvételt hajt végre. |Igen |Nem |
| 9 |Azure PowerShell |A StorSimple parancsmag használatakor **Get-AzureStorSimpleStorageAccountCredential &#124; Select-Object - először a 1 - Wait** jelölje ki az első objektumot, így hozhat létre egy új **VolumeContainer** objektum, a parancsmag az összes található objektumokat adja vissza. |Használó zárójelek között a következő parancsmagot: **(Get-Azure-StorSimpleStorageAccountCredential) &#124; Select-Object - First 1 - Wait** |Igen |Igen |
| 10 |Migrálás |Több kötettárolóba továbbítódnak az áttelepítéshez, a DRÓN legfrissebb biztonsági mentés esetén csak az első kötettároló esetén pontosak. Az első 4 biztonsági másolatok az első kötettároló áttelepítésük párhuzamos áttelepítés fogja elindítani. |Azt javasoljuk, hogy egyszerre egy kötettárolót át. |Igen |Nem |
| 11 |Migrálás |A visszaállítás után kötetek nem adódnak hozzá a biztonsági mentési szabályzat vagy a virtuális lemez csoport. |Ezen kötetek hozzáadása a biztonsági mentési szabályzatot a biztonsági másolatok létrehozásához kell. |Igen |Igen |
| 12 |Migrálás |Az áttelepítés befejezése után az 5000/7000 sorozat eszköz nem kell elérni az áttelepített adatokat tárolókat. |Azt javasoljuk, hogy az áttelepítés befejezése és véglegesített után törölje az áttelepített adatokat tárolókat. |Igen |Nem |
| 13 |Klónozás és Vészhelyreállítás |1. frissítést futtató StorSimple-eszköz nem klónozza vagy vészhelyreállításra 1 frissítés előtti szoftvert futtató eszközre. |Update 1, hogy ezeket a műveleteket a céleszköz frissíteni kell |Igen |Igen |
| 14 |Migrálás |Konfiguráció biztonsági másolata az áttelepítéshez 5000 – 7000 sorozatú eszköz sikertelen lehet, ha vannak a kötet-csoportokhoz társított kötetek a. |Nincs társított köteteket a kötet üres csoportok törlése, és ismételje meg a konfiguráció biztonsági másolata. |Igen |Nem |

## <a name="physical-device-updates-in-update-12"></a>Fizikai eszköz frissítése az 1.2-es frissítés
Ha javítás frissítése 1.2-es (1. frissítés előtti verziót futtató) fizikai eszköz az alkalmazott, akkor a szoftververzió 6.3.9600.17584 változik.

## <a name="controller-and-firmware-updates-in-update-12"></a>Az 1.2-es frissítés a vezérlő- és belsővezérlőprogram-frissítések
Ebben a kiadásban az illesztőprogram és az eszközön a lemezfirmware frissíti.

* A SAS-vezérlő frissítéssel kapcsolatos további információkért lásd: [Update 1 LSI SAS-vezérlők a Microsoft Azure StorSimple-készülék](https://support.microsoft.com/kb/3043005). 
* A lemezfirmware-frissítés kapcsolatos további információkért lásd: [lemezfirmware Update 1 a Microsoft Azure StorSimple-készülék](https://support.microsoft.com/kb/3063416).

## <a name="virtual-device-updates-in-update-12"></a>1.2-es frissítés a virtuális eszköz frissítése
Ez a frissítés a virtuális eszközre nem alkalmazható. Új virtuális eszközök kell létrehozni. 

## <a name="next-steps"></a>További lépések
* [1.2-es frissítés telepítése az eszközön](storsimple-install-update-1.md).

