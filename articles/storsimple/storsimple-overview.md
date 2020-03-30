---
title: StorSimple 8000 sorozatú megoldás áttekintés | Microsoft dokumentumok
description: Ismerteti a StorSimple rétegezés, az eszköz, a virtuális eszköz, a szolgáltatások és a tárolási felügyelet, és bemutatja a StorSimple által használt kulcskifejezéseket.
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
ms.author: timlt
ms.openlocfilehash: 2a6650cac975c575415a329361da00d4fbfcaa9d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "68965117"
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>StorSimple 8000 sorozat: hibrid felhőalapú tárolási megoldás

[!INCLUDE [storsimple-8000-eol-banner](../../includes/storsimple-8000-eol-banner.md)]

## <a name="overview"></a>Áttekintés
Üdvözli a Microsoft Azure StorSimple, egy integrált tárolási megoldás, amely a helyszíni eszközök és a Microsoft Azure felhőalapú tárhelye közötti tárolási feladatokat kezeli. A StorSimple egy hatékony, költséghatékony és könnyen kezelhető tárolóhálózat (SAN) megoldás, amely kiküszöböli a vállalati tárolással és adatvédelemmel kapcsolatos számos problémát és költséget. A jogvédett StorSimple 8000 sorozatú eszközt használja, integrálható a felhőszolgáltatásokkal, és olyan eszközkészletet kínál, amellyel egyszerűen megjeleníthető minden vállalati tárhely, többek között a felhőbeli tárhely is. (A StorSimple telepítési információk közzé a Microsoft Azure webhelycsak a StorSimple 8000 sorozatú eszközök csak. Ha StorSimple 5000/7000 sorozatú eszközt használ, látogasson el a [StorSimple súgójába.)](http://onlinehelp.storsimple.com/)

A StorSimple [tárolási rétegezést](#automatic-storage-tiering) használ a tárolt adatok különböző adathordozók közötti kezeléséhez. Az aktuális munkakészlet a helyszínen található ssd-meghajtókon, a ritkábban használt adatok a merevlemez-meghajtókon (HDD-k) tárolódnak, az archiválási adatok pedig a felhőbe kerül. Ezenkívül a StorSimple deduplikációt és tömörítést használ az adatok által felhasznált tárterület csökkentése érdekében. További információ: [Deduplikáció és tömörítés](#deduplication-and-compression). A StorSimple 8000 sorozat dokumentációjában használt egyéb kulcsfontosságú kifejezések és fogalmak meghatározásához a cikk végén található [StorSimple terminológia](#storsimple-terminology) című részt.

A tárhelykezelés mellett a StorSimple adatvédelmi funkciók lehetővé teszik az igény szerinti és ütemezett biztonsági mentések létrehozását, majd azok helyi vagy felhőbeli tárolását. A biztonsági mentések növekményes pillanatképek formájában készülnek, ami azt jelenti, hogy gyorsan létrehozhatók és visszaállíthatók. A felhőbeli pillanatképek kritikus fontosságúak lehetnek a vész-helyreállítási forgatókönyvekben, mivel helyettesítik a másodlagos tárolórendszereket (például a szalagos biztonsági mentést), és lehetővé teszik az adatok visszaállítását az adatközpontba vagy szükség esetén alternatív helyekre.

![videó ikon](./media/storsimple-overview/video_icon.png) Tekintse meg a Microsoft Azure StorSimple rövid bemutatását bemutató videót.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>Miért válassza partnerünket, a(z) StorSimple-t?
Az alábbi táblázat a Microsoft Azure StorSimple által nyújtott legfontosabb előnyöket ismerteti.

| Szolgáltatás | Előny |
| --- | --- |
| Átlátható integráció |Az iSCSI protokollt használja az adattároló létesítmények láthatatlan összekapcsolására. Ez biztosítja, hogy a felhőben, az adatközpontban vagy a távoli kiszolgálókon tárolt adatok úgy tűnik, hogy egyetlen helyen tárolódnak. |
| Csökkentett tárolási költségek |Elegendő helyi vagy felhőalapú tárhelyet foglal le az aktuális igényeknek megfelelően, és csak szükség esetén bővíti a felhőalapú tárhelyet. Tovább csökkenti a tárolási követelményeket és a költségeket azáltal, hogy megszünteti az azonos adatok redundáns verzióit (deduplikáció) és tömörítést használ. |
| Egyszerűsített tároláskezelés |Rendszerfelügyeleti eszközöket biztosít a helyszínen, a távoli kiszolgálón és a felhőben tárolt adatok konfigurálásához és kezeléséhez. Ezenkívül a biztonsági mentési és visszaállítási funkciókat a Microsoft Management Console (MMC) beépülő modulból is kezelheti.|
| Továbbfejlesztett katasztrófa-helyreállítás és megfelelőség |Nem igényel hosszabb helyreállítási időt. Ehelyett visszaállítja az adatokat, ahogy szükség van rájuk. Ez azt jelenti, hogy a normál működés minimális megszakítással folytatódhat. Ezenkívül konfigurálhatja a biztonsági mentési ütemezések és adatmegőrzési beállításokat. |
| Az adatok mobilitása |A Microsoft Azure felhőszolgáltatásaiba feltöltött adatok helyreállítási és áttelepítési célból más helyekről is elérhetők. Emellett a StorSimple segítségével konfigurálhatja a StorSimple felhőalapú készülékeket a Microsoft Azure-ban futó virtuális gépeken (VM-ek). A virtuális gépek ezután virtuális eszközök használatával érhetik el a tárolt adatokat tesztelési vagy helyreállítási célokra. |
| Az üzletmenet folytonossága |Lehetővé teszi a StorSimple 5000-7000 sorozatú felhasználók számára, hogy adataikat egy StorSimple 8000 sorozatú eszközre telepítsék. |
| Elérhetőség az Azure Government Portálon |A StorSimple az Azure Government Portalon érhető el. További információ: [Deploy your on-premises StorSimple device in the Government Portal](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Adatvédelem és elérhetőség |A StorSimple 8000 sorozat támogatja a zone redundáns tárolás (ZRS), amellett, hogy a helyileg redundáns tárolás (LRS) és a georedundáns tárolás (GRS). A ZRS-részletekért tekintse meg [ezt a cikket az Azure Storage redundancia-beállításairól.](https://azure.microsoft.com/documentation/articles/storage-redundancy/) |
| A kritikus fontosságú alkalmazások támogatása |A StorSimple lehetővé teszi a megfelelő kötetek helyileg rögzítettként való azonosítását, ami viszont biztosítja, hogy a kritikus alkalmazások által igényelt adatok ne legyenek rétegezve a felhőbe. A helyileg rögzített kötetek nem tartoznak a felhőbeli késések vagy a kapcsolódási problémák. A helyileg rögzített kötetekről a [Kötetek kezelésével a StorSimple Eszközkezelő szolgáltatás használata című témakörben talál](storsimple-8000-manage-volumes-u2.md)további információt. |
| Alacsony késleltetés és nagy teljesítmény |Olyan felhőalapú berendezéseket hozhat létre, amelyek kihasználják az Azure prémium szintű storage nagy teljesítményű, alacsony késésű funkcióit. A StorSimple prémium szintű felhőalapú készülékeiről a [StorSimple felhőalapú berendezés telepítése és kezelése az Azure-ban](storsimple-8000-cloud-appliance-u2.md)című témakörben talál további információt. |


## <a name="storsimple-components"></a>StorSimple összetevők
A Microsoft Azure StorSimple megoldás a következő összetevőket tartalmazza:

* **Microsoft Azure StorSimple eszköz** – egy helyszíni hibrid tárolótömb, amely SSD-ket és HDD-ket, valamint redundáns vezérlőket és automatikus feladatátvételi képességeket tartalmaz. A vezérlők kezelik a tárolási rétegezést, a jelenleg használt (vagy gyakori elérésű) adatokat a helyi tárolóban (az eszközön vagy a helyszíni kiszolgálókon), miközben a ritkábban használt adatokat a felhőbe helyezik.
* **StorSimple Cloud Appliance** – más néven a StorSimple virtuális berendezés, ez egy szoftver változata a StorSimple eszköz, amely replikálja az architektúra és a legtöbb képességeit a fizikai hibrid tárolóeszköz. A StorSimple cloud appliance egyetlen csomóponton fut egy Azure virtuális gépen. Prémium szintű virtuális eszközök, amelyek kihasználják az Azure prémium szintű tárhelyét, elérhetők a 2.
* **StorSimple Device Manager szolgáltatás** – az Azure Portal kiterjesztése, amely lehetővé teszi a StorSimple-eszköz vagy a StorSimple cloud appliance egyetlen webes felületről történő kezelését. A StorSimple Eszközkezelő szolgáltatással szolgáltatásokat hozhat létre és kezelhet, megtekintheti és kezelheti az eszközöket, megtekintheti a riasztásokat, kezelheti a köteteket, valamint megtekintheti és kezelheti a biztonsági mentési házirendeket és a biztonsági mentési katalógust.
* **Windows PowerShell storsimple-** parancssori felület, amely a StorSimple eszköz kezelésére használható. A Windows PowerShell for StorSimple olyan funkciókkal rendelkezik, amelyek lehetővé teszik a StorSimple eszköz regisztrálását, a hálózati felület konfigurálását az eszközön, bizonyos típusú frissítések telepítését, az eszköz hibaelhárítását a támogatási munkamenet elérésével, és az eszköz módosítását Állami. A Windows PowerShell for StorSimple a soros konzolhoz való csatlakozással vagy a Windows PowerShell-táv-ellenőrzés használatával érhető el.
* **Azure PowerShell StorSimple parancsmagok** – Windows PowerShell-parancsmagok gyűjteménye, amelyek lehetővé teszik a szolgáltatásszintű és áttelepítési feladatok automatizálását a parancssorból. Az Azure PowerShell-parancsmagok storSimple, látogasson el a [parancsmag referencia.](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0#azure)
* **StorSimple Snapshot Manager** – egy MMC beépülő modul, amely kötetcsoportokat és a Windows kötet árnyékmásolata szolgáltatást használ alkalmazáskonzisztens biztonsági mentések létrehozásához. Emellett a StorSimple Snapshot Manager segítségével biztonsági mentési ütemezéseket hozhat létre, és klónozhat vagy visszaállíthat köteteket.
* **StorSimple Adapter for SharePoint** – egy olyan eszköz, amely transzparens módon kiterjeszti a Microsoft Azure StorSimple tárhelyet és adatvédelmet a SharePoint Server-farmokra, miközben a StorSimple tárhelyet láthatóvá és kezelhetővé teszi a SharePoint központi felügyeleti portáljáról.

Az alábbi ábra a Microsoft Azure StorSimple architektúrájának és összetevőinek magas szintű megtekintését mutatja be.

![StorSimple architektúra](./media/storsimple-overview/overview-big-picture.png)

A következő szakaszok részletesebben ismertetik ezeket az összetevőket, és ismertetik, hogy a megoldás hogyan rendezi el az adatokat, hogyan osztja ki a tárolást, és hogyan könnyíti meg a tárolás kezelést és az adatvédelmet. Az utolsó szakasz definíciókat tartalmaz a StorSimple összetevőivel és azok kezelésével kapcsolatos fontos kifejezések és fogalmak némelyikéhez.

## <a name="storsimple-device"></a>StorSimple eszköz
A Microsoft Azure StorSimple eszköz egy helyszíni hibrid tárolótömb, amely elsődleges tároló- és iSCSI-hozzáférést biztosít a rajta tárolt adatokhoz. Kezeli a felhőalapú tárterülettel való kommunikációt, és segít a Microsoft Azure StorSimple-megoldásban tárolt összes adat biztonságának és titkosságának biztosításában.

A StorSimple eszköz tartalmaz SSD-ket és merevlemez-meghajtókat HDD-ket, valamint támogatja a fürtözést és az automatikus feladatátvételt. Megosztott processzort, megosztott tárolót és két tükrözött vezérlőt tartalmaz. Minden vezérlő a következőket biztosítja:

* Csatlakozás gazdaszámítógéphez
* Legfeljebb hat hálózati port a helyi hálózathoz való csatlakozáshoz
* Hardverfigyelés
* Nem felejtő véletlen hozzáférésű memória (NVRAM), amely akkor is megőrzi az információkat, ha az áramellátás megszakad
* Fürtalapú frissítés a feladatátvevő fürt kiszolgálóin lévő szoftverfrissítések kezeléséhez, hogy a frissítések minimális vagy semmilyen hatással legyenek a szolgáltatás rendelkezésre állására
* Fürtszolgáltatás, amely háttérfürtként működik, magas rendelkezésre állást biztosít, és minimálisra csökkentve azokat a káros hatásokat, amelyek akkor fordulhatnak elő, ha egy HDD vagy SSD meghibásodik vagy offline állapotba kerül

Egyszerre csak egy vezérlő aktív. Ha az aktív vezérlő meghibásodik, a második vezérlő automatikusan aktívvá válik.

További információ: [StorSimple hardverösszetevők és állapot](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>StorSimple felhőberendezés
A StorSimple segítségével létrehozhat egy felhőalapú berendezést, amely replikálja a fizikai hibrid tárolóeszköz architektúráját és képességeit. A StorSimple cloud appliance (más néven a StorSimple virtuális berendezés) egyetlen csomóponton fut egy Azure virtuális gépen. (A felhőalapú berendezés csak azure-beli virtuális gépen hozható létre. StorSimple eszközön vagy helyszíni kiszolgálón nem hozhat létre.

A felhőalapú készülék a következő funkciókkal rendelkezik:

* Úgy viselkedik, mint egy fizikai készülék, és iSCSI-felületet kínál a felhőben lévő virtuális gépekszámára.
* Korlátlan számú felhőalapú készüléket hozhat létre a felhőben, és szükség szerint be- és kikapcsolhatja őket.
* Ez segíthet szimulálni a helyszíni környezetekben a vész-helyreállítási, fejlesztési és tesztelési forgatókönyvek, és segíthet az elemszintű lekérés a biztonsági mentések.

A StorSimple Cloud Appliance két modellben érhető el: a 8010-es eszközben (korábbi nevén 1100-as modell) és a 8020-as eszközön. A 8010-es készülék maximális kapacitása 30 TB. A 8020-as eszköz, amely kihasználja az Azure prémium szintű tárhelyét, maximális kapacitása 64 TB. (A helyi szinteken az Azure prémium szintű tárhelye az SSD-k adatait tárolja, míg a szabványos tárolók on HDD-k adatait.) Vegye figyelembe, hogy a prémium szintű storage használatához prémium szintű tárfiókkal kell rendelkeznie.

A StorSimple felhőalapú berendezésről további információt a [StorSimple felhőalapú berendezés telepítése és kezelése az Azure-ban](storsimple-8000-cloud-appliance-u2.md)című részben talál.

## <a name="storsimple-device-manager-service"></a>StorSimple-eszközkezelő szolgáltatás
A Microsoft Azure StorSimple webalapú felhasználói felületet (a StorSimple Eszközkezelő szolgáltatást) biztosít, amely lehetővé teszi az adatközpontok és a felhőalapú tárolás központi kezelését. A StorSimple Eszközkezelő szolgáltatással a következő feladatokat hajthatja végre:

* Konfigurálja a StorSimple eszközök rendszerbeállításait.
* A StorSimple eszközök biztonsági beállításainak konfigurálása és kezelése.
* Konfigurálja a felhőhitelesítő adatokat és -tulajdonságokat.
* Kötetek konfigurálása és kezelése a kiszolgálón.
* Kötetcsoportok konfigurálása.
* Biztonsági másolatot és visszaállításhoz.
* A teljesítmény figyelése.
* Tekintse át a rendszerbeállításokat, és azonosítsa a lehetséges problémákat.

A StorSimple Eszközkezelő szolgáltatás sal minden felügyeleti feladatot elvégezhet, kivéve azokat, amelyek rendszerleállási időt igényelnek, például a frissítések kezdeti beállítását és telepítését.

További információt a [StorSimple Eszközkezelő szolgáltatás használata a StorSimple-eszköz felügyeletéhez](storsimple-8000-manager-service-administration.md)című területen talál.

## <a name="windows-powershell-for-storsimple"></a>Windows PowerShell storsimple-hez
A Windows PowerShell for StorSimple parancssori felületet biztosít, amely segítségével létrehozhatja és kezelheti a Microsoft Azure StorSimple szolgáltatást, és beállíthatja és figyelheti a StorSimple-eszközöket. Ez egy Windows PowerShell-alapú, parancssori felület, amely dedikált parancsmagokat tartalmaz a StorSimple-eszköz kezeléséhez. A Windows PowerShell for StorSimple olyan szolgáltatásokat tartalmaz, amelyek lehetővé teszik a következőket:

* Regisztráljon egy eszközt.
* Konfigurálja a hálózati adaptert egy eszközön.
* Bizonyos típusú frissítések telepítése.
* Hibaelhárítás az eszköz ről a támogatási munkamenet elérésével.
* Módosítsa az eszköz állapotát.

A Windows PowerShell for StorSimple egy soros konzolról (az eszközhöz közvetlenül csatlakoztatott gazdaszámítógépen) vagy távolról is elérheti a Windows PowerShell-távirányításhasználatával. Vegye figyelembe, hogy egyes Windows PowerShell StorSimple feladatokhoz, például a kezdeti eszközregisztrációhoz, csak a soros konzolon hajthatók végre.

További információt a [Windows PowerShell használata a StorSimple számára az eszköz felügyeletéhez](storsimple-8000-windows-powershell-administration.md)című területen talál.

## <a name="azure-powershell-storsimple-cmdlets"></a>Azure PowerShell StorSimple parancsmagok
Az Azure PowerShell StorSimple parancsmagok Windows PowerShell-parancsmagok gyűjteménye, amelyek lehetővé teszik a szolgáltatásszintű és áttelepítési feladatok automatizálását a parancssorból. Az Azure PowerShell-parancsmagok storSimple, látogasson el a [parancsmag referencia.](/powershell/module/servicemanagement/azure/?view=azuresmps-3.7.0)

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
A StorSimple Snapshot Manager egy Microsoft Management Console (MMC) beépülő modul, amelynek segítségével konzisztens, időponthoz közvetlenül megfelelő biztonsági másolatokat hozhat létre a helyi és felhőalapú adatokról. A beépülő modul Windows Server-alapú gazdagépen fut. A StorSimple Snapshot Manager segítségével a következőkre használható:

* Kötetek konfigurálása, biztonsági és törlése.
* A kötetcsoportok konfigurálásával győződjön meg arról, hogy a biztonsági másolatok adatai alkalmazáskonzisztensek.
* A biztonsági mentési szabályzatok kezelése úgy, hogy az adatokról egy előre meghatározott ütemezés szerint készüljön, és egy kijelölt helyen (helyileg vagy a felhőben) tároljon.
* Kötetek és egyes fájlok visszaállítása.

A biztonsági mentések pillanatképekként készülnek, amelyek csak az utolsó pillanatkép készítése óta végrehajtott módosításokat rögzítik, és sokkal kevesebb tárhelyet igényelnek, mint a teljes biztonsági mentések. Biztonsági mentési ütemezéseket hozhat létre, vagy szükség szerint azonnali biztonsági mentést készíthet. Emellett a StorSimple Snapshot Manager használatával adatmegőrzési szabályzatokat hozhat létre, amelyek szabályozzák, hogy hány pillanatkép kerül mentésre. Ha ezt követően szüksége van az adatok visszaállítására egy biztonsági mentésből, storSimple Snapshot Manager lehetővé teszi, hogy válasszon a helyi vagy felhőbeli pillanatképek katalógusából. 

Ha egy katasztrófa bekövetkezik, vagy ha más okból kell visszaállítania az adatokat, a StorSimple Snapshot Manager szükség szerint fokozatosan állítja vissza az adatokat. Az adatok visszaállításához nem szükséges a teljes rendszer leállítása a fájl visszaállítása, a berendezések cseréje vagy a műveletek másik helyre való áthelyezése közben.

További információért látogasson el [a Mi a StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>SharePointhoz készült StorSimple-adapter
A Microsoft Azure StorSimple tartalmazza a StorSimple adaptert a SharePointhoz, egy opcionális összetevőt, amely transzparens módon kiterjeszti a StorSimple tárhely- és adatvédelmi szolgáltatásait a SharePoint-kiszolgálófarmokra. Az adapter együttműködik egy távoli blobtároló (RBS) szolgáltatóval és az SQL Server RBS szolgáltatásával, így a BLOB-k áthelyezhető a Microsoft Azure StorSimple rendszer által biztonsági másolatot készítő kiszolgálóra. A Microsoft Azure StorSimple ezután a használat alapján helyileg vagy a felhőben tárolja a BLOB-adatokat.

A SharePoint-alapú StorSimple adapter kezelése a SharePoint központi felügyeleti portáljáról történik. Ennek következtében a SharePoint-felügyelet továbbra is központosított, és úgy tűnik, hogy az összes tárterület a SharePoint-farmban található.

További információt a [StorSimple Adapter for SharePoint](storsimple-adapter-for-sharepoint.md)című webhely című részében talál. 

## <a name="storage-management-technologies"></a>Tárolási kezelési technológiák
A dedikált StorSimple-eszköz, virtuális eszköz és egyéb összetevők mellett a Microsoft Azure StorSimple a következő szoftvertechnológiákat használja az adatok gyors eléréséhez és a tárhelyfelhasználás csökkentéséhez:

* [Automatikus tárolási rétegezés](#automatic-storage-tiering) 
* [Dinamikus kiosztás](#thin-provisioning) 
* [Deduplikáció és tömörítés](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Automatikus tárolási rétegezés
A Microsoft Azure StorSimple automatikusan elrendezi az adatokat logikai szinteken az aktuális használat, az életkor és a más adatokkal való kapcsolat alapján. A legaktívabb adatok helyileg tárolódnak, míg a kevésbé aktív és inaktív adatok automatikusan átkerülnek a felhőbe. Az alábbi ábra bemutatja ezt a tárolási megközelítést.

![StorSimple tárolási szintek](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

A gyors hozzáférés érdekében a StorSimple nagyon aktív adatokat (gyors adatadatokat) tárol a SsD-ken a StorSimple-eszközön. Tárolja az adatokat, amelyek alkalmanként használt (meleg adatok) a HDD-k az eszközön vagy a szerverek az adatközpontban. Az inaktív adatokat, a biztonsági mentési adatokat és az archiválási vagy megfelelőségi célokra megőrzött adatokat a felhőbe helyezi át. 

> [!NOTE]
> A 2-es vagy újabb frissítésben megadhatja a kötethelyileg rögzítettként, amely esetben az adatok a helyi eszközön maradnak, és nem rétegzettak a felhőben. 


A StorSimple a használati minták változásával módosítja és átrendezi az adat- és tárolási hozzárendeléseket. Előfordulhat például, hogy egyes információk idővel kevésbé aktívak. Mivel fokozatosan kevésbé aktív, az SSD-kről hdd-kre, majd a felhőbe kerül. Ha ugyanazok az adatok ismét aktívvá válnak, a rendszer visszatelepíti a tárolóeszközre.

A tárolási rétegezési folyamat a következőképpen történik:

1. Egy rendszergazda beállít egy Microsoft Azure felhőalapú tárfiókot.
2. A rendszergazda a soros konzol és a StorSimple Eszközkezelő szolgáltatás (az Azure Portalon futó) konfigurálása az eszköz és a fájlkiszolgáló, kötetek létrehozása és adatvédelmi szabályzatok. A helyszíni gépek (például a fájlkiszolgálók) az internetkis számítógéprendszer-illesztő (iSCSI) segítségével érik el a StorSimple eszközt.
3. Kezdetben a StorSimple az eszköz gyors SSD-rétegén tárolja az adatokat.
4. Ahogy az SSD-réteg megközelíti a kapacitást, a StorSimple duplikálja és tömöríti a legrégebbi adatblokkokat, és áthelyezi őket a HDD-szintre.
5. Ahogy a HDD-szint megközelíti a kapacitást, a StorSimple titkosítja a legrégebbi adatblokkokat, és biztonságosan elküldi azokat a Microsoft Azure tárfiókba HTTPS-kapcsolaton keresztül.
6. A Microsoft Azure az adatok több replikáját hozza létre az adatközpontban és egy távoli adatközpontban, biztosítva, hogy az adatok katasztrófa esetén helyreállíthatók legyenek.
7. Amikor a fájlkiszolgáló a felhőben tárolt adatokat kér, a StorSimple zökkenőmentesen adja vissza azadatokat, és tárolja a StorSimple-eszköz SSD-szintjén tárolt példányt.

#### <a name="how-storsimple-manages-cloud-data"></a>Hogyan kezeli a StorSimple a felhőadatokat?

A StorSimple duplikálja az ügyféladatokat az összes pillanatkép és az elsődleges adatok (a gazdagépek által írt adatok) között. Bár a deduplikáció nagyszerű a tárolási hatékonyság szempontjából, bonyolulttá teszi a "mi van a felhőben" kérdését. A rétegzett elsődleges adatok és a pillanatkép-adatok átfedésben vannak egymással. A felhőben lévő egyetlen adattömb rétegzett elsődleges adatként is használható, és több pillanatkép is hivatkozhat rá. Minden felhőbeli pillanatkép biztosítja, hogy az összes időpontban megadott adatok egy-egy példányát a felhőbe, amíg a pillanatkép törlődik.

Az adatok csak akkor törlődnek a felhőből, ha nincsenek hivatkozások az adatokra. Például ha egy felhőbeli pillanatképet az összes adatot, amely a StorSimple-eszköz, majd töröl néhány elsődleges adatokat, azt látjuk, az _elsődleges adatok_ csökkenése azonnal. A _felhőalapú adatok,_ amely tartalmazza a rétegzett adatok és a biztonsági mentések, ugyanaz marad. Ennek az az oka, hogy van egy pillanatkép továbbra is hivatkozva a felhőbeli adatok. A felhőbeli pillanatkép törlése után (és minden más pillanatkép, amely ugyanarra az adatra hivatkozott), a felhőfelhasználás csökken. A felhőadatok eltávolítása előtt ellenőrizzük, hogy egyetlen pillanatkép sem hivatkozik-e még ezekre az adatokra. Ezt a folyamatot _szemétgyűjtésnek nevezik,_ és az eszközön futó háttérszolgáltatás. A felhőalapú adatok eltávolítása nem azonnali, mivel a szemétgyűjtési szolgáltatás a törlés előtt ellenőrzi az adatokra vonatkozó egyéb hivatkozásokat. A szemétgyűjtés sebessége a pillanatképek teljes számától és az összes adattól függ. A felhőadatok általában kevesebb mint egy hét alatt törlődnek.


### <a name="thin-provisioning"></a>Dinamikus kiosztás
A vékony kiépítés egy virtualizációs technológia, amelyben a rendelkezésre álló tárterület láthatóan meghaladja a fizikai erőforrásokat. Ahelyett, hogy elegendő tárhelyet előre lefoglalna, a StorSimple vékony kiépítést használ, hogy csak elegendő helyet foglaljon le az aktuális igényeknek megfelelően. A felhőalapú tárolás rugalmas jellege megkönnyíti ezt a megközelítést, mivel a StorSimple növelheti vagy csökkentheti a felhőalapú tárolást a változó igények nek megfelelően.

> [!NOTE]
> A helyileg rögzített kötetek nincsenek vékonyan kiépítve. A csak helyi kötethez lefoglalt tárterület a kötet létrehozásakor teljes egészében ki van építve.


### <a name="deduplication-and-compression"></a>Deduplikáció és tömörítés
A Microsoft Azure StorSimple deduplikációt és adattömörítést használ a tárolási igények további csökkentése érdekében.

A deduplikáció csökkenti a tárolt adatok teljes mennyiségét azáltal, hogy kiküszöböli a redundanciát a tárolt adatkészletben. Az információ változásakor a StorSimple figyelmen kívül hagyja a változatlan adatokat, és csak a módosításokat rögzíti. Emellett a StorSimple csökkenti a tárolt adatok mennyiségét a felesleges információk azonosításával és eltávolításával. 

> [!NOTE]
> A helyileg rögzített köteteken lévő adatok nem deduplikált adják vagy nem tömörítik. A helyileg rögzített kötetek biztonsági másolatai azonban deduplikálódnak és tömörítettek lesznek.


## <a name="storsimple-workload-summary"></a>StorSimple munkaterhelés összegzése
A támogatott StorSimple számítási feladatok összegzése az alábbiakban táblázatba.

| Forgatókönyv | Számítási feladat | Támogatott | Korlátozások | Verzió |
| --- | --- | --- | --- | --- |
| Együttműködés |Fájlmegosztás |Igen | |Az összes verzió |
| Együttműködés |Elosztott fájlmegosztás |Igen | |Az összes verzió |
| Együttműködés |SharePoint |Igen* |Csak helyileg rögzített kötetekkel támogatott |2. és újabb frissítés |
| Levéltári |Egyszerű fájlarchiválás |Igen | |Az összes verzió |
| Virtualizáció |Virtuális gépek |Igen* |Csak helyileg rögzített kötetekkel támogatott |2. és újabb frissítés |
| Adatbázis |SQL |Igen* |Csak helyileg rögzített kötetekkel támogatott |2. és újabb frissítés |
| Videó-megfigyelés |Videó-megfigyelés |Igen* |Akkor támogatott, ha a StorSimple eszköz csak ehhez a számítási feladathoz van leszentelve |2. és újabb frissítés |
| Backup |Elsődleges cél biztonsági mentése |Igen* |Akkor támogatott, ha a StorSimple eszköz csak ehhez a számítási feladathoz van leszentelve |3. és újabb frissítés |
| Backup |Másodlagos cél biztonsági mentése |Igen* |Akkor támogatott, ha a StorSimple eszköz csak ehhez a számítási feladathoz van leszentelve |3. és újabb frissítés |

*Igen&#42; – Megoldási irányelveket és korlátozásokat kell alkalmazni.*

A StorSimple 8000 sorozatú eszközök nem támogatják a következő számítási feladatokat. Ha a StorSimple-en van telepítve, ezek a számítási feladatok nem támogatott konfigurációt eredményeznek.

* Orvosi képalkotás
* Exchange
* Vdi
* Oracle
* SAP
* Big Data
* Tartalom terjesztése
* Rendszerindítás az SCSI-ből

Az alábbiakban a StorSimple által támogatott infrastruktúra-összetevők listája látható.

| Forgatókönyv | Számítási feladat | Támogatott | Korlátozások | Verzió |
| --- | --- | --- | --- | --- |
| Általános kérdések |Express Route |Igen | |Az összes verzió |
| Általános kérdések |DataCore FC |Igen* |A DataCore SANsymphony-val támogatott |Az összes verzió |
| Általános kérdések |DFSR |Igen* |Csak helyileg rögzített kötetekkel támogatott |Az összes verzió |
| Általános kérdések |Indexelés |Igen* |Rétegzett kötetek esetén csak a metaadat-indexelés támogatott (nincs adat).<br>Helyileg rögzített kötetek esetén a teljes indexelés támogatott. |Az összes verzió |
| Általános kérdések |Víruskereső |Igen* |Rétegzett kötetek esetén csak a megnyitáskor és a bezáráskor ivizsgálat támogatott.<br> Helyileg rögzített kötetek esetén a teljes vizsgálat támogatott. |Az összes verzió |

*Igen&#42; – Megoldási irányelveket és korlátozásokat kell alkalmazni.*

Az alábbiakban a StorSimple-lel használt egyéb szoftverek listája látható a megoldások létrehozásához.

| Munkaterhelés típusa | A StorSimple-szel használt szoftver | Támogatott verziók|Hivatkozás a megoldásútmutatóhoz| 
| --- | --- | --- | --- |
| Biztonsági mentési cél |Veeam között |Veeam v 9 és újabb |[StorSimple tartalék célpontként veaam](storsimple-configure-backup-target-veeam.md)|
| Biztonsági mentési cél |Veritas Biztonsági Másolat Exec |Backup Exec 16 és újabb |[StorSimple, mint egy biztonsági mentési cél Backup Exec](storsimple-configure-backup-target-using-backup-exec.md)|
| Biztonsági mentési cél |Veritas NetBackup |NetBackup 7.7.x és újabb verziók  |[StorSimple tartalék célként a NetBackup segítségével](storsimple-configure-backuptarget-netbackup.md)|
| Globális fájlmegosztás <br></br> Együttműködés |Talon  |[StorSimple talonnal](https://www.talonstorage.com/products/archive/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>StorSimple terminológia
A Microsoft Azure StorSimple-megoldás üzembe helyezése előtt azt javasoljuk, hogy tekintse át az alábbi feltételeket és definíciókat.

### <a name="key-terms-and-definitions"></a>Kulcsfontosságú kifejezések és fogalommeghatározások
| Kifejezés (betűszó vagy rövidítés) | Leírás |
| --- | --- |
| hozzáférés-vezérlési rekord (ACR) |A Microsoft Azure StorSimple-eszköz egy kötetéhez társított rekord, amely meghatározza, hogy mely állomások csatlakozhatnak hozzá. A meghatározás a StorSimple-eszközhöz csatlakozó állomások (ACR-ben található) iSCSI minősített nevén (IQN) alapul. |
| AES-256 |256 bites Advanced Encryption Standard (AES) algoritmus az adatok titkosítására a felhőbe és a felhőből való áthelyezéskor. |
| elosztási egység mérete (AUS) |A Windows fájlrendszerben lévő fájlok tárolására lefoglalható legkisebb lemezterület. Ha a fájlméret nem a fürtméret többszöröse, akkor a fájl (a fürtméret következő többszöröséig) további helyet kell tárolni, ami a merevlemez területének és töredezettségének csökkenését eredményezi. <br>Az Ajánlott AUS az Azure StorSimple kötetek 64 KB, mert jól működik a deduplikációs algoritmusok. |
| automatikus tárolási rétegezés |A kevésbé aktív adatok automatikus áthelyezése az SSD-kről a HDD-kre, majd a felhő egyik szintjére, majd lehetővé teszi az összes tárkezelés központi felhasználói felületről történő kezelését. |
| katalógus biztonsági mentése |A biztonsági mentések gyűjteménye, általában a használt alkalmazástípussal kapcsolatos. Ez a gyűjtemény a StorSimple Eszközkezelő szolgáltatás felhasználói felületének biztonsági másolata panelen jelenik meg. |
| katalógusfájl biztonsági mentése |A StorSimple Snapshot Manager biztonsági mentési adatbázisában jelenleg tárolt elérhető pillanatképek listáját tartalmazó fájl. |
| biztonsági mentési házirend |Kötetek, a biztonsági mentés típusa és a menetrend, amely lehetővé teszi, hogy egy előre meghatározott ütemezés szerint biztonsági másolatot készítsen. |
| bináris nagy objektumok (BLOBs) |Az adatbázis-kezelő rendszerben egyetlen entitásként tárolt bináris adatok gyűjteménye. A BLOB-k általában képek, hangvagy más multimédiás objektumok, bár néha a bináris végrehajtható kód BLOB-ként van tárolva. |
| Challenge Handshake Authentication Protocol (CHAP) |A kapcsolat társának hitelesítésére használt protokoll, amely a jelszót vagy titkos kulcsot megosztó társon alapul. Chap lehet egyirányú vagy kölcsönös. Az egyirányú CHAP segítségével a cél hitelesíti a kezdeményezőt. A Mutual CHAP megköveteli, hogy a cél hitelesítse a kezdeményezőt, és hogy a kezdeményező hitelesítse a célt. |
| clone |Kötet másolata. |
| Felhő mint szint (CaaT) |A tárolási architektúrán belüli rétegként integrált felhőalapú tárhely, így úgy tűnik, hogy az összes tárterület egy vállalati tárolóhálózat része. |
| felhőszolgáltató (CSP) |Felhőalapú számítástechnikai szolgáltatások nyújtója. |
| felhőbeli pillanatkép |A felhőben tárolt kötetadatok időponthoz legközelebb ido-másolata. A felhőbeli pillanatkép egy másik, telephelyen kívüli tárolórendszeren replikált pillanatképnek felel meg. A felhőbeli pillanatképek különösen hasznosak a vész-helyreállítási forgatókönyvekben. |
| felhőalapú tárolási titkosítási kulcs |A storSimple-eszköz által használt jelszó vagy kulcs az eszköz által a felhőbe küldött titkosított adatok eléréséhez. |
| fürtbarát frissítés |Szoftverfrissítések kezelése a feladatátvevő fürt kiszolgálóin, hogy a frissítések minimális vagy semmilyen hatással legyenek a szolgáltatás rendelkezésre állására. |
| datapath (adatelérési út) |Olyan funkcionális egységek gyűjteménye, amelyek összekapcsolt adatfeldolgozási műveleteket hajtanak végre. |
| inaktiválás |Állandó művelet, amely megszakítja a kapcsolatot a StorSimple eszköz és a kapcsolódó felhőszolgáltatás között. Az eszköz felhőbeli pillanatképei a folyamat után is megmaradnak, és klónozhatók vagy vész-helyreállítási célokra használhatók. |
| lemeztükrözés |Logikai lemezkötetek replikálása különböző merevlemezeken valós időben a folyamatos rendelkezésre állás biztosítása érdekében. |
| dinamikus lemeztükrözés |Logikai lemezkötetek replikációja dinamikus lemezeken. |
| dinamikus lemezek |Olyan lemezkötet-formátum, amely a Logikai lemezkezelő (LDM) segítségével tárolja és kezeli az adatokat több fizikai lemezen. A dinamikus lemezek bővíthetők, hogy több szabad területet biztosítsanak. |
| Kiterjesztett csomó lemezek (EBOD) ház |A Microsoft Azure StorSimple-eszköz másodlagos tárolója, amely további tárolóhelyhez további merevlemezeket tartalmaz. |
| zsírellátás |Hagyományos tárolókiépítés, amelyben a tárolóhely a várható igények alapján van lefoglalva (és általában meghaladja a jelenlegi igényeket). Lásd még: *vékony kiépítés*. |
| merevlemez-meghajtó (HDD) |Olyan meghajtó, amely forgó korongokat használ az adatok tárolására. |
| hibrid felhőalapú tárolás |Olyan tárolási architektúra, amely helyi és külső erőforrásokat használ, beleértve a felhőalapú tárolást is. |
| ISCSI internetkis számítógép-felület (iSCSI) |IP-alapú tárolóhálózati szabvány az adattároló berendezések vagy létesítmények összekapcsolására. |
| iSCSI-kezdeményező |Olyan szoftverösszetevő, amely lehetővé teszi, hogy a Windows rendszert futtató gazdaszámítógép külső iSCSI-alapú tárolóhálózathoz csatlakozzon. |
| iSCSI minősített név (IQN) |ISCSI-tárolót vagy kezdeményezőt azonosító egyedi név. |
| iSCSI-tároló |Olyan szoftverösszetevő, amely központi iSCSI-lemezalrendszereket biztosít a tárolóhálózatokban. |
| élő archiválás |Olyan tárolási megközelítés, amelyben az archiválási adatok mindig elérhetők (például nem a helyszínen kívül tárolják őket). A Microsoft Azure StorSimple élő archiválást használ. |
| helyileg rögzített kötet |olyan kötetet, amely az eszközön található, és soha nem rétegzett a felhőbe. |
| helyi pillanatkép |A Microsoft Azure StorSimple eszközön tárolt kötetadatok időponthoz szerzett másolata. |
| Microsoft Azure StorSimple |Hatékony megoldás, amely egy adatközpont-tárolóberendezésből és -szoftverből áll, amely lehetővé teszi az informatikai szervezetek számára, hogy a felhőalapú tárolást úgy használják ki, mintha adatközponti tároló lenne. A StorSimple leegyszerűsíti az adatvédelmet és az adatkezelést, miközben csökkenti a költségeket. A megoldás egyesíti az elsődleges tárolást, az archiválást, a biztonsági mentést és a vészhelyreállítást (DR) a felhővel való zökkenőmentes integráció révén. A SAN-tárolás és a felhőalapú adatkezelés nagyvállalati szintű platformon történő kombinálásával a StorSimple eszközök lehetővé teszik a sebességet, az egyszerűséget és a megbízhatóságot minden tárolással kapcsolatos igény nek. |
| Teljesítmény- és hűtőmodul (PCM) |Hardver alkatrészek a StorSimple eszköz, amely a tápegységek és a hűtőventilátor, innen a név Power and Cooling modul. Az eszköz elsődleges burkolata két 764 W-os PCM-vel rendelkezik, míg az EBOD ház két 580 W-os PCM-vel rendelkezik. |
| elsődleges ház |Az alkalmazásplatform-vezérlőket tartalmazó StorSimple-eszköz fő burkolata. |
| helyreállítási idő célkitűzés (RTO) |Az a maximális időtartam, amelyet az üzleti folyamat vagy rendszer katasztrófa utáni teljes visszaállítása előtt el kell fordítani. |
| soros csatlakoztatott SCSI (SAS) |Egyfajta merevlemez-meghajtó (HDD). |
| szolgáltatásadat-titkosítási kulcs |A kulcs elérhetővé minden új StorSimple eszköz, amely regisztrálja a StorSimple Device Manager szolgáltatás. A StorSimple Eszközkezelő szolgáltatás és az eszköz között átvitt konfigurációs adatok nyilvános kulccsal vannak titkosítva, és ezután csak az eszközön fejthetők vissza személyes kulccsal. A szolgáltatás adattitkosítási kulcsa lehetővé teszi, hogy a szolgáltatás beszerezze ezt a személyes kulcsot a visszafejtéshez. |
| szolgáltatás regisztrációs kulcsa |Egy kulcs, amely segít regisztrálni a StorSimple-eszközt a StorSimple Eszközkezelő szolgáltatással, hogy az megjelenjen az Azure Portalon a további felügyeleti műveletekhez. |
| Kis számítógépes rendszerinterfész (SCSI) |A számítógépek fizikai csatlakoztatására és az adatok között történő továbbítására vonatkozó szabványok készlete. |
| SSD-meghajtó (SSD) |Mozgó alkatrészeket nem tartalmazó lemez; például egy flash meghajtót. |
| tárfiók |Egy adott felhőszolgáltató tárfiókjához kapcsolódó hozzáférési hitelesítő adatok készlete. |
| SharePointhoz készült StorSimple-adapter |Egy Microsoft Azure StorSimple-összetevő, amely transzparens módon kiterjeszti a StorSimple tárhelyet és adatvédelmet a SharePoint-kiszolgálófarmokra. |
| StorSimple-eszközkezelő szolgáltatás |Az Azure Portal egy bővítménye, amely lehetővé teszi az Azure StorSimple helyszíni és virtuális eszközök kezelését. |
| StorSimple Snapshot Manager |Microsoft Management Console (MMC) beépülő modul a Microsoft Azure StorSimple biztonsági mentési és visszaállítási műveleteinek kezeléséhez. |
| biztonsági mentés |Olyan szolgáltatás, amely lehetővé teszi a felhasználó számára, hogy interaktív biztonsági másolatot készítsen egy kötetről. Ez egy másik módja a kötet manuális biztonsági mentésének, szemben az automatikus biztonsági mentés nek egy meghatározott házirenden keresztül történő. |
| vékony kiépítés |A rendelkezésre álló tárolóhely tárolási rendszerekben való alkalmazásának hatékonyságának optimalizálására szolgáló módszer. A vékony kiépítés, a tárterület több felhasználó között van lefoglalva az egyes felhasználók által egy adott időpontban szükséges minimális terület alapján. Lásd még: *zsírkiépítés*. |
| rétegezés |Adatok elrendezése logikai csoportosításokba az aktuális használat, az életkor és más adatokkal való kapcsolat alapján. A StorSimple automatikusan elrendezi az adatokat a szintekben. |
| Kötet |Meghajtók formájában bemutatott logikai tárolóterületek. A StorSimple kötetek megfelelnek a gazdagép által csatlakoztatott kötetek, beleértve az iSCSI és a StorSimple-eszköz használatával felderített köteteket is. |
| kötettároló |Kötetek és a rájuk vonatkozó beállítások csoportosítása. A StorSimple-eszköz összes kötete kötettárolókba van csoportosítva. A kötettároló-beállítások közé tartoznak a tárfiókok, a felhőbe küldött adatok titkosítási beállításai a társított titkosítási kulcsokkal, valamint a felhőt érintő műveletekhez felhasznált sávszélesség. |
| kötetcsoport |A StorSimple Snapshot Manager kötetcsoport kötetek gyűjteménye a biztonsági mentés feldolgozását megkönnyítő kötetek gyűjteménye. |
| Kötet árnyékmásolata szolgáltatás (VSS) |Olyan Windows Server operációsrendszer-szolgáltatás, amely a VSS-t támogató alkalmazásokkal való kommunikációval megkönnyíti az alkalmazások konzisztenciáját a növekményes pillanatképek létrehozásának koordinálása érdekében. A VSS biztosítja, hogy az alkalmazások ideiglenesen inaktívak legyenek a pillanatképek készítésekekekekekekekénél. |
| Windows PowerShell storsimple-hez |A StorSimple-eszköz működtetéséhez és kezeléséhez használt Windows PowerShell-alapú parancssori felület. A Windows PowerShell néhány alapvető képességének fenntartása mellett ez a felület további dedikált parancsmagokkal rendelkezik, amelyek a StorSimple-eszközök kezelésére irányulnak. |

## <a name="next-steps"></a>További lépések
További információ a [StorSimple biztonságáról.](storsimple-8000-security.md)

