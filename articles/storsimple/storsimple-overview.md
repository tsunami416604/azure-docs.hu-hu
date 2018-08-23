---
title: A StorSimple 8000-es sorozat megoldásainak áttekintése |} A Microsoft Docs
description: Ismerteti a StorSimple rétegezés, az eszköz, virtuális eszköz, szolgáltatások és tárolók kezelése, és bemutatja a legfontosabb kifejezések a StorSimple.
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: ''
ms.assetid: 7144d218-db21-4495-88fb-e3b24bbe45d1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/10/2017
ms.author: v-sharos@microsoft.com
ms.openlocfilehash: 946b5a568d98367daec0244968b962618f22ae76
ms.sourcegitcommit: 387d7edd387a478db181ca639db8a8e43d0d75f7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2018
ms.locfileid: "42055291"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>A StorSimple 8000 sorozat: a hibrid felhőalapú tárolási megoldás
## <a name="overview"></a>Áttekintés
Üdvözli a Microsoft Azure StorSimple egy integrált tárolási megoldás, amely a helyszíni eszközök és a Microsoft Azure felhőalapú tárolást közötti tárolási feladatokat kezeli. StorSimple egy hatékony, költségkímélő és könnyen felügyelhető terület tárolóhálózat (SAN) tárolómegoldás, amely a problémák és a költségeket, a nagyvállalati adattárolás és -védelem számos költsége kiküszöbölhető a. Azt a jogvédett StorSimple 8000 sorozatú eszköz használja, integrálható a felhőszolgáltatásokkal, és felügyeleti eszközöket biztosít minden vállalati tárhely, beleértve a felhőalapú tárolás áttekinthetőségét. (A Microsoft Azure webhelyen közzétett StorSimple üzembehelyezési információk csak a StorSimple 8000 sorozatú eszközök vonatkozik. Ha egy StorSimple 5000/7000 sorozat eszközt használ, lépjen a [StorSimple súgó](http://onlinehelp.storsimple.com/).)

StorSimple az [tárolórétegzés](#automatic-storage-tiering) különböző adathordozóján tárolt adatok kezeléséhez. A jelenlegi munkakészlet a helyszínen tárolt a tartós állapotú meghajtókkal (SSD-kkel), merevlemezes (HDD) meghajtók rajta ritkábban használt adatokat, és a felhőbe archiválási adatok leküldésekor. Továbbá a StorSimple használatával a deduplikáció és a tömörítést Rövidítse le a tárolási megoldás, amely az adatokat használ fel. További információért ugorjon [Deduplikáció és a tömörítés](#deduplication-and-compression). Más legfontosabb kifejezések és a StorSimple 8000-es sorozat dokumentációban használt fogalmak definícióját, Ugrás [StorSimple terminológia](#storsimple-terminology) Ez a cikk végén található.

Tárolási felügyeleten felül a StorSimple adatvédelmi funkciók engedélyezése hozhat létre igény szerinti és ütemezett biztonsági mentések, és biztonságosan tárolja helyileg vagy a felhőben. Biztonsági mentés növekményes pillanatkép, ami azt jelenti, hogy azok hozhat létre és gyorsan vissza formájában kerül. Felhőbeli pillanatképek adathelyreállítás kritikus része a vész-helyreállítási helyzetekben lehet, mivel azok cserélje le a másodlagos tárhelyen rendszereket (ilyen például a szalagos biztonsági mentés), és lehetővé teszi az adatok helyreállítását az adatközpontban vagy a másodlagos helyek szükség esetén.

![videó ikon](./media/storsimple-overview/video_icon.png) Tekintse meg a videót, amely röviden ismerteti a Microsoft Azure StorSimple.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>A StorSimple miért érdemes használni?
A következő táblázat néhány fontos előnye, hogy a Microsoft Azure StorSimple biztosít.

| Szolgáltatás | Előny |
| --- | --- |
| Transzparens integráció |Használja az iSCSI protokoll alapfeladatokat tárolólétesítmények adatok mutat. Ez biztosítja, hogy az adatközpont, a felhőben tárolt adatok, vagy a távoli kiszolgálókon úgy tűnik, hogy egyetlen helyen kell tárolni. |
| Alacsonyabb tárolási költségek |Foglalja le a megfelelő helyi vagy felhőbeli tárhely aktuális igények figyelembevételével, és kiterjeszti a felhőalapú tárolás csak szükség esetén. Ez tovább csökkenti a tárolási követelmények és költségek ugyanazokat az adatokat (a deduplikáció) redundáns verziói kiküszöbölése révén, és a tömörítéssel. |
| Egyszerűbb |Konfigurálhatja és kezelheti a tárolt adatok a helyszínen, egy távoli kiszolgálóra, és a felhőbeli felügyeleti eszközöket biztosít a rendszer. Emellett biztonsági mentés kezelése, és állítsa vissza a függvények egy Microsoft Management Console (MMC) beépülő modulból.|
| Továbbfejlesztett vész-helyreállítási és megfelelőség |Kiterjesztett helyreállítás ideje nem igényel. Ehelyett azt visszaállítja az adatokat igény szerint. Ez azt jelenti, hogy minimális továbbra is a normál működést. Ezenkívül beállíthatja, adja meg a biztonsági mentési ütemezések és az adatmegőrzés. |
| Rugalmasan áthelyezhető adatok |A Microsoft Azure-felhőszolgáltatások feltöltött adatok más helyekre, helyreállítási és migrálási célokból is elérhetők. A StorSimple segítségével ezenkívül a StorSimple Cloud Appliance konfigurálása a Microsoft Azure-ban futó virtuális gépek (VM). A virtuális gépek virtuális eszközök felhasználhatja tesztelési és helyreállítási célokra tárolt adatok elérésére. |
| Az üzletmenet folytonossága |Lehetővé teszi a StorSimple 5000-7000-es sorozat az adatok áttelepítését egy StorSimple 8000 sorozatú eszköz. |
| Rendelkezésre állás az Azure Government Portalon |A StorSimple az Azure Government Portalon érhető el. További információkért lásd: [központi telepítése a helyszíni StorSimple eszköz a Government Portalon](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Az adatvédelem és a rendelkezésre állás |A StorSimple 8000 sorozat támogatja a Zónaredundáns tárolás (ZRS), helyileg redundáns tárolás (LRS) és a georedundáns tárolás (GRS) felül. Tekintse meg [Ez a cikk az Azure Storage redundanciabeállításai](https://azure.microsoft.com/documentation/articles/storage-redundancy/) ZRS részleteiről. |
| Kritikus fontosságú alkalmazások támogatása |A StorSimple lehetővé teszi, hogy megfelelő, a helyileg rögzített kötetek azonosíthatja Ez viszont biztosítja, hogy a kritikus fontosságú alkalmazásai által igényelt adatok nem rétegzett a felhőbe. A gyors helyi kötetek nem vonatkozik felhőalapú késések vagy kapcsolódási problémák. A gyors helyi kötetek kapcsolatos további információkért lásd: [kötetek kezelése a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manage-volumes-u2.md). |
| Kis késésű és nagy teljesítményű |Felhőalapú berendezések, amelyek kihasználják a nagy teljesítményű, kis késésű funkcióit az Azure premium storage is létrehozhat. A StorSimple prémium szintű felhőalapú berendezések kapcsolatos további információkért lásd: [üzembe helyezése és kezelése az Azure StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>A StorSimple-összetevők
A Microsoft Azure StorSimple megoldás a következő összetevőket tartalmazza:

* **A Microsoft Azure StorSimple-eszköz** – egy helyszíni hibrid tárolási tömb, amely tartalmazza az SSD-kből és merevlemezekből kialakított, redundáns vezérlőket és automatikus feladatátvételi funkciókat együtt. A tartományvezérlők kezelheti a tárolási rétegek, elhelyezése jelenleg használt (vagy forró) adatokat a helyi tárterület (az eszköz vagy a helyszíni kiszolgálók), ritkábban használt adatok a felhőbe való áthelyezés közben.
* **A StorSimple Cloud Appliance** – más néven a StorSimple virtuális készülék, ez az a StorSimple-eszköz, amely replikálja az architektúra egy szoftver verziója és a hibrid fizikai tárolóeszközt a legtöbb képességeit. A StorSimple felhőalapú készülék az Azure virtuális gép egyetlen csomópontján fut. Prémium szintű virtuális eszközök, amely az Azure premium storage előnyeit, 2. frissítést és újabb verziói is.
* **StorSimple-Eszközkezelő szolgáltatás** kiterjesztése az Azure Portal, amely lehetővé teszi, hogy a StorSimple-eszköz vagy a StorSimple felhőalapú készülék egy közös webes felületről kezelheti. A StorSimple-Eszközkezelő szolgáltatás segítségével hozzon létre és a szolgáltatások kezeléséhez, megtekintése és kezelése az eszközök, riasztások megtekintése, kötetek kezelése, és megtekintése és kezelése biztonsági mentési szabályzatok és a biztonságimásolat-katalógus.
* **Storsimple-höz készült Windows PowerShell** – olyan parancssori felületet, amely a StorSimple-eszköz kezeléséhez használhatja. Storsimple-höz készült Windows PowerShell funkcióval rendelkezik, amelyeket lehetővé teszik a StorSimple-eszköz regisztrálása, konfigurálja a hálózati adaptert az eszközön, bizonyos típusú frissítések telepítése, az eszköz hibaelhárítása a támogatási munkamenet elérésével, és az eszköz módosítása állapot. Storsimple-höz készült Windows PowerShell hozzáférhet, a soros konzol csatlakozik, vagy a Windows PowerShell-távelérés használatával.
* **Azure PowerShell StorSimple-parancsmagok** –, amelyek lehetővé teszik, hogy automatizálja a parancssorból a szolgáltatási szint és az áttelepítési feladatokat Windows PowerShell-parancsmagok egy gyűjteményét. Storsimple-höz készült Azure PowerShell-parancsmagokkal kapcsolatos további információkért látogasson el a [parancsmag-referencia](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0#azure).
* **A StorSimple Snapshot Manager** – egy MMC beépülő modul által használt kötet csoportok és a Windows kötet árnyékmásolata szolgáltatás alkalmazásfüggő biztonsági másolatok létrehozásához. Emellett használhatja a StorSimple Snapshot Manager biztonsági mentési ütemezés és a klón létrehozásához, vagy kötetek visszaállítása.
* **A StorSimple Adapter for SharePoint** – olyan eszköz, amely transzparens módon kiterjeszti a Microsoft Azure StorSimple tárolás és adatok védelme a SharePoint Server halgazdaságok közben StorSimple tárolási megtekinthető és kezelhető a a SharePoint központi Felügyeleti portál.

Az alábbi ábrán a Microsoft Azure StorSimple-architektúra és összetevők magas szintű nézetét jeleníti meg.

![A StorSimple-architektúra](./media/storsimple-overview/overview-big-picture.png)

Az alábbi szakaszok ismertetik ezeket az összetevőket nagyobb részletességgel, és a megoldás hogyan rendezi az adatokat, foglalja le a storage, és megkönnyíti a tárolók kezelése és az adatvédelem ismertetik. Az utolsó szakaszban biztosít jelentésdefiníciókat néhány fontos fogalmakat és a StorSimple-összetevők és a felügyeleti kapcsolatos fogalmakat.

## <a name="storsimple-device"></a>StorSimple-eszköz
A Microsoft Azure StorSimple-eszközhöz egy helyszíni hibrid tárolótömböt, amely elsődleges tárolás és a rajta tárolt adatok iSCSI hozzáférést biztosít. A felhőalapú tárolással kommunikációt kezeli, és segít biztosítani, a biztonsági és, hogy a Microsoft Azure StorSimple megoldás az adatok titkosságát.

A StorSimple-eszközt tartalmaz, SSD-kből és merevlemez-meghajtók HDD-k, valamint a fürtözési és az Automatikus feladatátvétel támogatása. Egy megosztott processzor, a megosztott tároló, valamint a két tükrözött tartományvezérlők tartalmazza. Minden vezérlő az alábbi előnyöket nyújtja:

* Kapcsolat egy gazdagépen
* Csatlakozás a helyi hálózati (LAN) legfeljebb hat hálózati portok
* Hardver ellenőrzése
* Nem felejtő közvetlen elérésű memória (NVRAM), amely mindaddig megőrzi az adatokat, még akkor is, ha power megszakad
* Fürttámogató frissítés egy feladatátvevő fürtben lévő kiszolgálók a szoftverfrissítések kezeléséhez, hogy a frissítések minimális, vagy nincs hatással a szolgáltatás rendelkezésre állása
* Fürtszolgáltatás, hogy mely funkciók, például egy háttér-fürthöz, a magas rendelkezésre állás biztosítása, és semmilyen negatív hatások minimalizálása érdekében, hogy okozhat, ha egy HDD vagy SSD meghibásodik vagy offline állapotba

Időben csak egy tartományvezérlő aktív, bármikor. Ha az aktív vezérlő nem sikerül, a második vezérlőn automatikusan aktívvá válik.

További információért ugorjon [StorSimple hardverösszetevők és állapot](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
A StorSimple segítségével architektúráját és funkcióit a fizikai hibrid tárolóeszköz sorolja a felhőalapú berendezés létrehozása. A StorSimple Cloud Appliance eszköz (más néven a virtuális StorSimple-készülék) az Azure virtuális gép egyetlen csomópontján fut. (A felhőalapú berendezés csak lehet létrehozni egy Azure virtuális gépen. Nem hozható létre a StorSimple eszköz vagy egy helyszíni kiszolgálón.)

A felhőalapú berendezés a következő funkciókkal rendelkezik:

* Ez egy fizikai készüléket viselkedik, és a felhőben lévő virtuális gépek iSCSI felületet kínálnak.
* Felhőalapú berendezések korlátlan számú létrehozásához a felhőben, és kapcsolja be- és bekapcsolhatja őket szükség szerint.
* Segíthet a helyszíni környezetekben a vész-helyreállítási, fejlesztési és tesztelési forgatókönyvek szimulálása, és segíthet a biztonsági mentésekből elemszintű előhívásokhoz.

A StorSimple felhőalapú készülék két modellben érhető el: a 8010-es eszköz (korábbi nevén az 1100-as modell) és a 8020-as eszköz. A 8010-es eszköz 30 TB-os maximális kapacitása nem. A 8020-as eszközt, és kihasználja az Azure premium storage kapacitása maximum 64 TB-os. (Helyi szinten az Azure premium storage tárolja az adatokat az SSD-k, míg a standard szintű tárolóban tárolja az adatokat a HDD-k.) Vegye figyelembe, hogy prémium szintű storage használata az Azure premium storage-fiók kell rendelkeznie. Prémium szintű storage szolgáltatással kapcsolatos további információkért lépjen [Premium Storage: nagy teljesítményű tárolási szolgáltatás Azure virtuális gépek számítási feladataihoz](../virtual-machines/windows/premium-storage.md).

A StorSimple felhőalapú készülék kapcsolatos további információkért látogasson el [üzembe helyezése és kezelése az Azure StorSimple Cloud Appliance](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>StorSimple-eszközkezelő szolgáltatás
A Microsoft Azure StorSimple egy webalapú felhasználói felület (a StorSimple-Eszközkezelő szolgáltatás), amely lehetővé teszi, hogy központilag kezelheti az adatközpont és a felhőalapú tárolás biztosítja. A StorSimple-Eszközkezelő szolgáltatás segítségével a következő feladatokat:

* A StorSimple-eszközök rendszerbeállításainak konfigurálására.
* Konfigurálása és kezelése a StorSimple eszköz biztonsági beállításai.
* Felhőhöz tartozó hitelesítő adatok és a tulajdonságok konfigurálása.
* Konfigurálása és kezelése a kötet a kiszolgálón.
* Kötet-csoportok konfigurálása.
* Készítsen biztonsági másolatot, és állítsa vissza az adatokat.
* Teljesítmény figyelése.
* Tekintse át a rendszer beállításait, és azonosíthatja a lehetséges problémákat.

A StorSimple-Eszközkezelő szolgáltatás segítségével az összes felügyeleti feladatot, kivéve azokat, a szükséges állásidő, például a kezdeti beállítás és a frissítések telepítését a rendszer.

További információért ugorjon [a StorSimple-eszköz felügyelete a StorSimple-Eszközkezelő szolgáltatás segítségével](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell-bővítménye
Windows PowerShell-bővítménye olyan parancssori felületet, amely létrehozása és a Microsoft Azure StorSimple szolgáltatás, és állítsa be a eszközök kezelése és figyelése a StorSimple segítségével biztosít. Egy Windows PowerShell-alapú, parancssori felület, amely a StorSimple-eszköz kezelésére szolgáló dedikált parancsokat tartalmaz. Windows PowerShell storsimple-höz készült funkciói, amelyek lehetővé teszik, hogy rendelkezik:

* Eszköz regisztrálása.
* Konfigurálja a hálózati adaptert az eszközön.
* Bizonyos típusú frissítések telepítése.
* Az eszköz hibaelhárítása a támogatási munkamenet elérésével.
* Az Eszközállapot módosítása.

Elérheti Windows PowerShell storsimple-höz készült soros konzolon (a gazdaszámítógép közvetlenül csatlakozik az eszköz) vagy távoli Windows PowerShell-távelérés használatával. Vegye figyelembe, hogy egy Windows PowerShell StorSimple-feladatokhoz, például a kezdeti regisztrációt, csak a soros konzolon végezhető.

További információért ugorjon [használható Windows PowerShell-bővítménye az eszköz felügyeletéhez](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Azure PowerShell StorSimple-parancsmagok
Az Azure PowerShell StorSimple-parancsmagok a Windows PowerShell-parancsmagokkal a parancssorból a szolgáltatási szint és az áttelepítési feladatokat automatizálhat gyűjteményei. Storsimple-höz készült Azure PowerShell-parancsmagokkal kapcsolatos további információkért látogasson el a [parancsmag-referencia](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0).

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
A StorSimple Snapshot Manager egy Microsoft Management Console (MMC) beépülő modulja, amely segítségével létrehozhat egységes, időponthoz – biztonsági másolatok a helyi és felhőbeli adatok. A beépülő modul a Windows Server-alapú gazdagépen futtatja. A StorSimple Snapshot Manager használhatja:

* Konfigurálja, biztonsági mentése és kötetek törlése.
* Konfigurálja a kötet csoportokat, győződjön meg arról, hogy a biztonsági másolatba mentett adatok alkalmazáskonzisztens.
* Biztonsági mentési házirendek kezelése, hogy az adatok biztonsági mentése egy előre meghatározott ütemezés szerint, és a kijelölt helyen tárolja (helyi vagy a felhőben).
* Kötetek és az egyes fájlok visszaállítása.

Biztonsági másolatok pillanatképként, amely csak a változásokat jegyezze fel, mivel a legutolsó pillanatfelvétel idejével bezárólag, és sokkal kevesebb, mint a teljes biztonsági mentések helyet igényel lesznek rögzítve. Hozzon létre biztonsági mentési ütemezéseket, vagy azonnali biztonsági mentés időpontjának, igény szerint. Ezenkívül használhatja a StorSimple Snapshot Manager adatmegőrzési szabályzatok létrehozásához, hogy hány pillanatképek menti a rendszer vezérlő. Ha később szeretne adatokat visszaállítani egy biztonsági mentési és a StorSimple Snapshot Manager lehetővé teszi a helyi katalógus vagy a felhőbeli pillanatképekkel közül választhat. 

Ha katasztrófa történik, vagy valamilyen más okból adatokat vissza kell állítania, a StorSimple Snapshot Manager visszaállítja azt növekményes igény szerint. Adatok visszaállítása nem szükséges, hogy leállítja a teljes rendszer állítsa vissza egy fájlt, cserélje le a berendezések, vagy műveleteket helyez át egy másik helyre.

További információért ugorjon [Mi az a StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>SharePointhoz készült StorSimple-adapter
A Microsoft Azure StorSimple a Sharepointhoz, egy összetevő, amely transzparens módon kiterjeszti a StorSimple tárolási és adatvédelmi funkcióit a SharePoint-kiszolgálófarmok magában foglalja a StorSimple Adapter. Az adapter működik, a Blob Storage (RBS) szolgáltató és az SQL Server RBS szolgáltatás lehetővé teszi, hogy helyezze át a blobokat egy biztonsági másolatot készít a Microsoft Azure StorSimple rendszerbe kiszolgálóra. A Microsoft Azure StorSimple majd tárol a BLOB-adatokat helyben vagy a felhőben, használat alapján.

A StorSimple Adapter for SharePoint felügyelje a SharePoint központi felügyeleti portálon. Ennek következtében központosított marad, a SharePoint felügyeleti, és úgy tűnik, hogy a SharePoint-farm összes tárolót.

További információért ugorjon [StorSimple Adapter for SharePoint](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Tárolási felügyeleti technológiák
A dedikált StorSimple-eszköz, a virtuális eszköz és a más összetevők mellett a Microsoft Azure StorSimple gyors hozzáférést biztosít a adatok és a tárhelyfelhasználás csökkentésében használ a következő szoftver technológiák:

* [Automatikus tárolórétegzés](#automatic-storage-tiering) 
* [A dinamikus kiosztás](#thin-provisioning) 
* [A deduplikáció és a tömörítés](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Automatikus tárolórétegzés
A Microsoft Azure StorSimple automatikusan elrendezése az adatok alapján a jelenlegi felhasználás életkor és más adatokhoz való kapcsolat logikai szinten. Legaktívabb adatok helyileg van tárolva, míg a kevésbé aktív és inaktív adatok automatikus áttelepítése a felhőbe. A következő ábra szemlélteti az adattárolási módszer.

![A StorSimple tárolási rétegek](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

A gyors hozzáférés engedélyezéséhez a StorSimple tárolja nagyon aktív (hideg) adatok az SSD-k a StorSimple-eszköz. Alkalmanként használt adatokat tárolja (meleg adatok) a HDD-k, az eszköz vagy az Adatközpont-kiszolgálójára. Inaktív adatokat, a biztonsági mentési adatokat, áthelyezése és az adatok megőrizve archiválási vagy megfelelőségi okokból a felhőbe. 

> [!NOTE]
> A 2. frissítés vagy újabb verziójú a helyileg rögzített kötet is megadhat, ebben az esetben az adatok a helyi eszközön maradnak, és nem a rétegzett a felhőbe. 


StorSimple állítja be, és újrarendezi az adatokat, és a használati mintákat, tároló-hozzárendelések módosítása. Például bizonyos adatok válhat, kevésbé aktív idővel. Váló fokozatosan kevésbé aktív, áttelepítése az SSD-k HDD, majd a felhőbe. Ha ugyanazokat az adatokat, hogy újra aktívvá válik, térjen vissza a tárolóeszköz áttelepítése.

A storage rétegezési folyamat a következő történik:

1. Egy rendszergazda állít be egy Microsoft Azure felhős társzolgáltatás fiókjába.
2. A rendszergazda használja a soros konzol és a StorSimple-Eszközkezelő szolgáltatás (az Azure Portalon fut) az eszköz- és kiszolgáló konfigurálása a kötetek és adatok alkalmazásvédelmi szabályzatok létrehozására. A helyszíni gépek (például a fájlkiszolgálók) az Internet Small Computer System Interface (iSCSI) használata a StorSimple-eszköz eléréséhez.
3. A StorSimple kezdetben a gyors SSD-rétegen a készülék tárolja az adatokat.
4. Ahogy az SSD-réteg kapacitása megközelíti, a StorSimple adattömbökbe megfelelően és tömöríti a legrégebbi adatblokkok, és áthelyezi őket a HDD-réteget.
5. A HDD szint megközelítések kapacitás StorSimple titkosítja a legrégebbi az adatblokkokat és biztonságosan a Microsoft Azure storage-fiókot a HTTPS-kapcsolaton keresztül küldi őket.
6. A Microsoft az Azure létrehozza az adatok több replika az adatközpontban és a egy távoli adatközpontban annak biztosítása, hogy az adatok helyreállíthatók, ha katasztrófa történik.
7. Amikor a fájlkiszolgáló, a felhőben tárolt adatok, a StorSimple adja vissza zökkenőmentesen, és tárolja a az SSD-rétegen a StorSimple-eszköz.

#### <a name="how-storsimple-manages-cloud-data"></a>Hogyan kezeli a StorSimple a felhőbeli adatok

A StorSimple adattömbökbe megfelelően a vásárlói adatokat összes pillanatképet, és az elsődleges adatok (állomások által írt adatok). Míg a deduplikáció nagyszerűen használható tárolási hatékonyságot, lehetővé teszi, a kérdés, "Mi az a felhőben" bonyolult. Az elsődleges rétegzett adatok és a pillanatkép adatainak átfedésben egymással. Egy olyan adattömb a felhőben tárolt rétegzett elsődleges adatként használható, és is hivatkozhatnak több pillanatképeket. Minden felhőbeli pillanatkép biztosítja, hogy az összes időpontban az adatok másolatát zárolva van a felhőbe, hogy a pillanatkép törléséig.

Adatok csak törlődik a felhőből, amikor is az adatok nem hivatkozik. Például egy felhőbeli pillanatképet az összes adat, amely a StorSimple eszközön, és ezután törölje az egyes elsődleges adatok vessünk láthatjuk lenne a _elsődleges_ dobja el azonnal. A _felhőbeli adatok_ amely magában foglalja a rétegzett adatok és a biztonsági mentések változatlan marad. Ez azért van egy pillanatképet a felhőbeli adatok továbbra is hivatkozik. A felhő után a rendszer törli a pillanatképét (és bármely egyéb pillanatkép, amely a hivatkozott ugyanazokat az adatokat), a felhő felhasználási csepp. Előtt azt távolítsa el a felhőbeli adatok, hogy ellenőrizze, hogy a pillanatképek nem még mindig hivatkoznak az adatokat. Ez a folyamat _szemétgyűjtés_ és a egy háttér-szolgáltatás fut az eszközön. Felhőbeli adatok eltávolítását a lehetőség nem azonnali egyéb hivatkozások az adatok a törlés előtt ellenőrzi a szemétgyűjtési szolgáltatást. Szemétgyűjtési sebességétől függ, pillanatképeket és az összes adat teljes száma. Általában a felhőbeli adatok törlődnek a kevesebb mint egy hét.


### <a name="thin-provisioning"></a>A dinamikus kiosztás
A dinamikus kiosztás egy virtualizációs technológia, amely rendelkezésre álló tár meghaladja a fizikai erőforrásokat jelenik meg. Ahelyett, hogy elegendő tárhely az előzetesen lefoglalását, a StorSimple használja a dinamikus kiosztás aktuális követelményeinek éppen elegendő lemezterületet foglaljon le. Rugalmas jellegének köszönhetően felhőalapú tárolás Ez a megközelítés lehetővé teszi, mert StorSimple növelheti vagy csökkentheti a változó igényekkel összhangban a felhőalapú tárolás.

> [!NOTE]
> A gyors helyi kötetek nem dinamikusan kiosztott. Csak helyi kötet lefoglalt tárolót ebben az esetben van kiépítve, a kötet létrehozásakor.


### <a name="deduplication-and-compression"></a>A deduplikáció és a tömörítés
A Microsoft Azure StorSimple a deduplikáció és az adatok tömörítés használja tovább csökkenti a tárolási követelményeket.

A deduplikáció csökkenti a tárolt adatkészlet redundanciájának kiküszöbölése révén tárolt adatok teljes mennyisége. Adat változik, ahogy a StorSimple figyelmen kívül hagyja a változatlan adatok, és csak a változtatásokat rögzíti. Emellett a StorSimple azonosításával és a felesleges adatok eltávolítása csökkenti a tárolt adatok mennyisége. 

> [!NOTE]
> A gyors helyi kötetek lévő adatok nem deduplikált vagy tömörítve. Azonban helyileg rögzített kötetek biztonsági másolatokat deduplikált, és tömöríti.


## <a name="storsimple-workload-summary"></a>A StorSimple a számítási feladatok összefoglalása
A StorSimple elvégezhető feladatok összefoglalása a lenti táblázatban láthatóak.

| Forgatókönyv | Számítási feladat | Támogatott | Korlátozások | Verzió |
| --- | --- | --- | --- | --- |
| Együttműködés |Fájlmegosztás |Igen | |Az összes verzió |
| Együttműködés |Elosztott fájlmegosztás |Igen | |Az összes verzió |
| Együttműködés |SharePoint |Igen* |Csak a helyileg rögzített kötetekhez támogatott |2. frissítés vagy újabb |
| Archiválás |Egyszerű fájlba az Archiválás |Igen | |Az összes verzió |
| Virtualizáció |Virtual machines (Virtuális gépek) |Igen* |Csak a helyileg rögzített kötetekhez támogatott |2. frissítés vagy újabb |
| Adatbázis |SQL |Igen* |Csak a helyileg rögzített kötetekhez támogatott |2. frissítés vagy újabb |
| Videó felügyelet |Videó felügyelet |Igen* |Támogatott, ha a StorSimple-eszköz csak a számítási feladatok vannak kijelölve |2. frissítés vagy újabb |
| Backup |Elsődleges célhely biztonsági mentés |Igen* |Támogatott, ha a StorSimple-eszköz csak a számítási feladatok vannak kijelölve |3. frissítés vagy újabb |
| Backup |Másodlagos cél biztonsági mentés |Igen* |Támogatott, ha a StorSimple-eszköz csak a számítási feladatok vannak kijelölve |3. frissítés vagy újabb |

*Igen&#42; -megoldás irányelvek és korlátozások kell alkalmazni.*

A StorSimple 8000 sorozatú eszközök nem támogatottak a következő számítási feladatokkal. Ha üzembe helyezte a StorSimple, ezeket a feladatokat eredményez konfigurációja nem támogatott.

* Orvosi lemezképpel végrehajtott telepítéshez
* Exchange
* VDI
* Oracle
* SAP
* Big Data
* Tartalom terjesztése
* Rendszerindítás SCSI

Az alábbiakban olyan infrastruktúra támogatja a StorSimple-összetevőt.

| Forgatókönyv | Számítási feladat | Támogatott | Korlátozások | Verzió |
| --- | --- | --- | --- | --- |
| Általános kérdések |Express Route |Igen | |Az összes verzió |
| Általános kérdések |FC DataCore |Igen* |Támogatott DataCore SANsymphony |Az összes verzió |
| Általános kérdések |DFSR |Igen* |Csak a helyileg rögzített kötetekhez támogatott |Az összes verzió |
| Általános kérdések |Indexelés |Igen* |A rétegzett kötetek csak metaadatokat indexelje támogat (adatok nélkül).<br>A helyileg rögzített kötetekhez teljes indexelő használata támogatott. |Az összes verzió |
| Általános kérdések |Anti-virus |Igen* |Csak nyissa meg a vizsgálatot, zárja be a rétegzett kötetek használata támogatott.<br> A gyors helyi kötetek teljes vizsgálat támogatott. |Az összes verzió |

*Igen&#42; -megoldás irányelvek és korlátozások kell alkalmazni.*

Az alábbiakban olyan egyéb szoftverek, a StorSimple megoldások létrehozásához használt.

| Számítási feladat típusa | A StorSimple használt szoftverek | Támogatott verziók|Megoldási útmutató mutató hivatkozás| 
| --- | --- | --- | --- |
| Biztonsági mentési cél |Veeam |Veeam v 9 és újabb verziók |[StorSimple biztonsági mentési célként Veaam](storsimple-configure-backup-target-veeam.md)|
| Biztonsági mentési cél |VERITAS Backup Execkel |Biztonsági mentési Exec 16 és újabb verziók |[A biztonsági mentési Exec a StorSimple mint biztonsági mentési cél](storsimple-configure-backup-target-using-backup-exec.md)|
| Biztonsági mentési cél |VERITAS Netbackuppal |Netbackuppal 7.7.x és újabb verziók  |[StorSimple biztonsági mentési célként Netbackuppal](storsimple-configure-backuptarget-netbackup.md)|
| Általános fájlmegosztás <br></br> Együttműködés |Talon  |[A Talon StorSimple](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>A StorSimple-terminológia
A Microsoft Azure StorSimple megoldás üzembe helyezése előtt javasoljuk, hogy tekintse át a következő feltételeket és a definíciókat.

### <a name="key-terms-and-definitions"></a>Legfontosabb kifejezések és meghatározások
| Kifejezés (betűszó vagy rövidítése) | Leírás |
| --- | --- |
| hozzáférés-vezérlési rekord (ACR) |Egy rekord, azt a kötetet a Microsoft Azure StorSimple-eszköz, amely meghatározza, hogy mely állomások tudjon hozzá csatlakozni a társított. Meghatározása alapján az iSCSI minősített nevét (IQN) a gazdagépet (a az ACR-REL), amely a StorSimple-eszköz csatlakozik. |
| AES-256 |Egy 256 bites Advanced Encryption Standard (AES) algoritmus, mert az adatok titkosítására helyezi át, és a felhőből. |
| lemezfoglalási egység mérete (AU) |A legkisebb memóriamennyiség, amely kiosztható tárolni egy fájlt a Windows-fájlrendszer. Ha egy fájl mérete nem egy páros számú többszöröse az a fürt méretét, további területnek kell használni, amely tárolja a fájlt (akár a fürtméret tovább többszöröse) elveszett terület és a merevlemez töredezettsége eredményez. <br>A javasolt au-k az Azure StorSimple-köteteket a 64 KB-os azért azt nagyszerűen működik a deduplikációs algoritmusokkal. |
| automatizált tárolórétegzés |Kevésbé aktív adatok automatikusan áthelyezése az SSD-k HDD, majd a felhőben szintre, és ezután ami lehetővé teszi az összes tárolási központi felhasználói felületről. |
| a biztonságimásolat-katalógus |Biztonsági másolatok, általában használt alkalmazások típusa szerint kapcsolódó gyűjteménye. A StorSimple-Eszközkezelő szolgáltatás felhasználói Felületet a biztonságimásolat-katalógus panel jelenik meg ebben a gyűjteményben. |
| a biztonságimásolat-katalógus fájl |Elérhető a StorSimple Snapshot Manager biztonsági mentési adatbázisban jelenleg tárolt pillanatképek listáját tartalmazó fájl. |
| a biztonsági mentési szabályzat |A kijelölt kötetek, a biztonsági mentés típusát és a egy ütemterv, amely lehetővé teszi a biztonsági mentések létrehozását egy előre meghatározott ütemezés szerint. |
| nagyméretű bináris objektumok (Blobok) |Egy adatbázis-kezelő rendszer egyetlen egységként tárolt bináris adatok gyűjteménye. Blobok jellemzően képek, hang vagy más multimédiás objektumok, de néha bináris végrehajtható kód blobként tárolt. |
| Challenge Handshake Authentication Protocol (CHAP) |A társ-kapcsolat, a jelszó vagy titkos kulcs megosztása társ alapján hitelesítéséhez használandó protokoll. A CHAP egyirányú és kölcsönös is lehetnek. Az egyirányú CHAP PROTOKOLLT vagy a cél hitelesíti egy kezdeményező. Kölcsönös CHAP megköveteli, hogy a cél hitelesítést a kezdeményező és, hogy a kezdeményező végzi a hitelesítést a cél. |
| Klónozás |A kötet duplikált másolata. |
| Felhő mint egy csomagja (CaaT) |Felhőalapú tárolás integrált belül a tároló-architektúra egy réteget, hogy az összes tárolási úgy tűnik, hogy egy nagyvállalati tárolási hálózat részét. |
| felhőszolgáltató (CSP) |A szolgáltató felhőszolgáltatások alapkategóriája. |
| felhőbeli pillanatkép |A felhőben tárolt adatmennyiség pont kötött másolata. Felhőbeli pillanatkép megegyezik egy másik, külső helyszínen lévő tárolórendszer replikált pillanatképet. Felhőbeli pillanatképek különösen hasznosak a vész-helyreállítási helyzetekben. |
| felhőalapú tárolás titkosítási kulcsa |Jelszó vagy egy kulcsot a StorSimple-eszköz használja a titkosított adatokat a felhőbe a eszköz által küldött elérésére. |
| fürttámogató frissítés |Szoftverfrissítések egy feladatátvevő fürtben lévő kiszolgálók kezelése, hogy a frissítések minimális, vagy nincs hatással a szolgáltatás rendelkezésre állása. |
| DataPath |Funkcionális egység közötti csatlakoztatott adatokat feldolgozó műveleteket végrehajtó gyűjteménye. |
| inaktiválás |Egy állandó művelet, amely a StorSimple-eszköz és a kapcsolódó felhőalapú szolgáltatás közötti kapcsolat megszakad. Felhőbeli pillanatképekkel az eszköz ezen folyamat után továbbra is, és is klónozta vagy vész-helyreállítási használt. |
| lemez tükrözés |Logikai lemez-kötetek a külön, rögzített replikációs meghajtók valós idejű folyamatos rendelkezésre állás biztosítása érdekében. |
| dinamikus lemez-tükrözés |A dinamikus lemezek a logikai lemez kötet replikálása. |
| a dinamikus lemezek |Egy kötet lemezformátum, amely a logikai lemezkezelő (LDM) használatával tárolhatja és kezelheti az adatokat több fizikai lemezek között. Szabadítson fel helyet adja meg a dinamikus lemezek is javasolt. |
| Kiterjesztett lemez kötegnek (EBOD) ház |A Microsoft Azure StorSimple eszköz, amely tartalmazza a további tárhely külön merevlemez lemezeinek másodlagos ház. |
| FAT-kiépítés |Egy hagyományos tárfiók kiépítésének melyik tárolási hely lefoglalhatók alapján a várható igényeinek (és általában túl az aktuális kell). Lásd még: *dinamikus kiosztás*. |
| merevlemez (HDD) |A meghajtó, amely a rotált egyedi lemezeinek száma általában használja adatok tárolására. |
| a hibrid felhőalapú tárolás |Egy tároló-architektúra, amely a helyi és a külső helyszínen lévő erőforrások, például a felhőalapú tárolást használ. |
| Az Internet Small Computer System Interface (iSCSI) |Egy Internet Protocol IP-alapú tárolási hálózati szabvány kapcsolásának adatok tárolási berendezés vagy üzemeltetnek. |
| az iSCSI-kezdeményező |A szoftverfrissítési összetevő, amely lehetővé teszi egy iSCSI-alapú tárolás külső hálózathoz való csatlakozáshoz Windows rendszerű gazdagépen. |
| az iSCSI minősített nevét (IQN) |ISCSI-tároló vagy kezdeményező azonosító egyedi név. |
| iSCSI-tároló |A szoftverfrissítési összetevő központi iSCSI-tárolóhálózatok kialakítása a lemez-alrendszereket biztosító. |
| élő archiválás |Egy adattárolási módszer, amely archív adatokhoz érhető el (a rendszer nem tárolja adathordozókra szalagon, például) folyamatosan. A Microsoft Azure StorSimple használ élő archiválás. |
| helyileg rögzített kötet |olyan kötetre, amely az eszközön található, és soha nem lesz rétegzett, a felhőbe. |
| helyi pillanatkép |A Microsoft Azure StorSimple eszközön tárolt adatmennyiség pont kötött másolata. |
| Microsoft Azure StorSimple |Egy adatközpontban storsimple készülék és a szoftver, amely lehetővé teszi az informatikai szervezetek, felhőalapú tárolás kihasználhatja az Adatközpont tárolójában, mintha egy hatékony megoldás. A StorSimple megkönnyíti az adatvédelmi és adatkezelési költségek csökkentése mellett. A megoldás egyesíti az elsődleges tárhelyként archiválásra, biztonsági mentési és vész-helyreállítási zökkenőmentes integráció révén a felhőben. SAN tároló és a felhőbeli adatkezelés egy nagyvállalati szintű platform kombinálásával a StorSimple-eszközök engedélyezése sebességét, az egyszerűség és megbízhatóság minden, a storage szolgáltatással kapcsolatos igényeinek. |
| Energia- és hűtési modul (PCM) |A StorSimple-eszköz álló a tápegységek és a hűtési ventilátor, ezért a nevét, a Power és hűtéssel modul hardverösszetevő. Az eszköz elsődleges ház két 764W PCMs van, mivel a EBOD ház két 580W PCMs. |
| Elsődleges ház |Fő lemezház a StorSimple-eszköz, amely tartalmazza az alkalmazás platform tartományvezérlőkön. |
| A helyreállítási időre vonatkozó célkitűzés (RTO) |A legnagyobb, hogy mennyi ideig kell lennie feljegyzi előtt egy üzleti folyamat vagy a rendszer teljes visszaállítása egy vészhelyzetet követően. |
| soros csatlakozású SCSI (SAS) |Merevlemez (HDD) típus. |
| szolgáltatásadat-titkosítási kulcs |Egy kulcs elérhetővé minden olyan új StorSimple-eszköz, amely a StorSimple-Eszközkezelő szolgáltatással regisztrálja. A konfigurációs adatokat a StorSimple-Eszközkezelő szolgáltatás és az eszköz között továbbított nyilvános kulccsal titkosított, és ezután csak a titkos kulcs segítségével az eszköz lehet visszafejteni. Szolgáltatásadat-titkosítási kulcs lehetővé teszi, hogy a szolgáltatás a visszafejtéshez a titkos kulcs beszerzése. |
| Szolgáltatásregisztrációs kulcs |Egy kulcs, amely segít regisztrálni a StorSimple eszközt a StorSimple-Eszközkezelő szolgáltatással, így további felügyeleti műveletek esetében az Azure Portalon megjelenik. |
| Small Computer System Interface (SCSI) |A fizikai számítógépek kapcsolódásához és adatcserével szabványok gyűjteménye. |
| tartós állapotú meghajtót (SSD) |Nincs részek; tartalmazó lemez Ha például egy USB-meghajtóra. |
| tárfiók |Egy adott felhőre szolgáltató a storage-fiókhoz társított hozzáférési hitelesítő adatok egy készletét. |
| SharePointhoz készült StorSimple-adapter |A Microsoft Azure StorSimple összetevője, amely kiterjeszti a StorSimple tárolás és adatok védelme a SharePoint-kiszolgálófarmok transzparens módon. |
| StorSimple-eszközkezelő szolgáltatás |Az Azure Portalon, amely lehetővé teszi, hogy az Azure StorSimple a helyi és virtuális eszközök kezelésére a kiterjesztése. |
| StorSimple Snapshot Manager |A Microsoft Management Console (MMC) beépülő kezeléséhez a Microsoft Azure StorSimple biztonsági mentési és visszaállítási műveletek. |
| biztonsági mentés |Ez a funkció lehetővé teszi, hogy a felhasználót, hogy a kötet egy interaktív biztonsági mentés. Manuális biztonsági másolat egy automatizált biztonsági másolat egy meghatározott szabályzat helyett egy kötet más módja. |
| a dinamikus kiosztás |Módszer, amellyel a rendelkezésre álló lemezterület szerepel a tárolórendszerek hatékonyságának optimalizálása. A dinamikus kiosztást, a tároló lefoglalása egy adott időpontban minden felhasználó által igényelt terület alapján több felhasználó használ. Lásd még: *fat kiépítés*. |
| rétegezést |Aktuális használat életkor és más adatokhoz való kapcsolat alapuló logikai csoportok adatainak rendezése. A StorSimple automatikusan elrendezése adatok szinten. |
| kötet |Logikai tárolási területek meghajtók formájában jelenik meg. StorSimple-köteteket a kötetek csatlakoztatva van a gazdagép, beleértve a felderített iSCSI és a egy StorSimple-eszköz felel meg. |
| kötettároló |Csoportja, kötetek és a rájuk vonatkozó beállításokat. A StorSimple-eszköz minden kötet kötettárolók vannak csoportosítva. Kötet tároló beállítások közé tartozik a storage-fiókok, a titkosítási beállítások a kapcsolódó titkosítási kulcsokat a felhőbe küldött adatok és a felhőt érintő műveletek felhasznált sávszélesség. |
| kötetcsoport |A StorSimple Snapshot Managerben, egy kötetcsoport gyűjteménye kötetek konfigurálni a biztonsági mentés megkönnyítése érdekében. |
| Kötet árnyékmásolata szolgáltatás (VSS) |Egy Windows Server operációsrendszer-szolgáltatás, amely elősegíti a alkalmazáskonzisztencia kommunikálva Kötetárnyékmásolat-felismerésre képes alkalmazások koordinálása a növekményes pillanatképek létrehozása. VSS biztosítja, hogy az alkalmazások legyenek ideiglenesen inaktív pillanatképek készülnek. |
| Windows PowerShell-bővítménye |Windows PowerShell-alapú parancssori felület üzemeltetése és kezelése a StorSimple-eszköz. Miközben a Windows PowerShell alapvető funkciói, ez az interfész rendelkezik további dedikált parancsokat, amelyek felügyelete a StorSimple eszköz irányába. |

## <a name="next-steps"></a>További lépések
Ismerje meg [StorSimple biztonsági](storsimple-8000-security.md).

