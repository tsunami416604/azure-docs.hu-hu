---
title: "A StorSimple 8000 series megoldás áttekintése |} Microsoft Docs"
description: "Ismerteti a StorSimple rétegezéséhez, az eszköz, virtuális eszköz, szolgáltatások és tárolók kezelése, és bemutatja a legfontosabb kifejezések a StorSimple."
services: storsimple
documentationcenter: NA
author: SharS
manager: timlt
editor: 
ms.assetid: 7144d218-db21-4495-88fb-e3b24bbe45d1
ms.service: storsimple
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: TBD
ms.date: 07/10/2017
ms.author: v-sharos@microsoft.com
ms.openlocfilehash: 10f4b6ec29c2d9539fa7fe61c96581755e630822
ms.sourcegitcommit: d41d9049625a7c9fc186ef721b8df4feeb28215f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/02/2017
---
# <a name="storsimple-8000-series-a-hybrid-cloud-storage-solution"></a>A StorSimple 8000 series: hibrid felhő tárolási megoldás
## <a name="overview"></a>Áttekintés
Üdvözli a Microsoft Azure StorSimple, egy integrált tárolási megoldás, amely kezeli a tárolási feladatok elvégzéséről a helyszíni eszközök és a Microsoft Azure felhőalapú tárolást között. StorSimple egy hatékony, költséghatékony és könnyen kezelhető tárolási terület tárolóhálózat (SAN) megoldás, amely kiküszöböli a problémák és a vállalati tárolás és az adatvédelem társított is számos. Azt a saját fejlesztésű StorSimple 8000 series eszközt használ, integrálható a felhőszolgáltatásokkal, és felügyeleti eszközöket biztosít az összes vállalati tároló, beleértve a felhőalapú tárolást zökkenőmentes nézetét. (A Microsoft Azure webhelyen közzétett StorSimple üzembehelyezési információk csak a StorSimple 8000 sorozat eszközeire vonatkozik. Amennyiben a StorSimple 5000/7000-es adatsorozat eszközt használ, [StorSimple súgó](http://onlinehelp.storsimple.com/).)

StorSimple használ [tárolórétegzés](#automatic-storage-tiering) különböző tárolási adathordozón tárolt adatok kezeléséhez. Aktuális munkakészlete a helyszínen tárolt (SSD) a ritkábban használt adatok tárolása merevlemezes (HDD) meghajtók és a felhőbe archiválási adatok fejlesztőre. Ezenkívül a StorSimple a deduplikáció és a tömörítést csökkentésére használ, amely az adatok akkor tárolókapacitást. További információkért látogasson el [a Deduplikáció és a tömörítést](#deduplication-and-compression). Más kulcs használt kifejezések és fogalmak a StorSimple 8000 series dokumentációjának meghatározását, látogasson el [StorSimple terminológia](#storsimple-terminology) Ez a cikk végén.

Tárolók kezelése mellett a StorSimple adatbiztonsági funkciók lehetővé teszik a igény szerinti létrehozásához és ütemezett biztonsági mentések, és majd tárolja őket helyileg vagy a felhőben. Biztonsági másolatok formájában növekményes pillanatképet, ami azt jelenti, hogy azok hozható létre és gyorsan vissza kell venni. Felhőalapú pillanatfelvételek különösen fontos a vész-helyreállítási eljárással lehet, mert cserélje le a másodlagos tárterületre rendszerek (például a szalagos biztonsági mentés), és lehetővé teszik az adatok helyreállítását a datacenter vagy a másodlagos helyek szükség esetén.

![Videó ikonja](./media/storsimple-overview/video_icon.png) A Microsoft Azure StorSimple gyors bevezetéséhez videó megtekintése.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure/StorSimple-Hybrid-Cloud-Storage-Solution/player]

## <a name="why-use-storsimple"></a>StorSimple miért érdemes használni?
A következő táblázat ismerteti a Microsoft Azure StorSimple biztosító kulcs előnyöket nyújtja.

| Szolgáltatás | Előny |
| --- | --- |
| Transzparens integráció |Az iSCSI protokoll használatával adatokat tároló létesítmények láthatatlanul hivatkozásra. Ez biztosítja, hogy az adatközpontban, a felhőben tárolt adatok, vagy a távoli kiszolgálókon úgy tűnik, hogy egyetlen helyen kell tárolni. |
| Alacsonyabb tárolási költségek |Aktuális követelményeinek megfelelő helyi vagy felhőalapú tárolás foglal le, és kiterjeszti a felhőalapú tárolást csak szükség esetén. Azt tovább csökkenti a tárhellyel kapcsolatos követelmények és költségek ugyanazokhoz az adatokhoz (a deduplikáció) redundáns verziói kiküszöbölése révén és tömörítés használatával. |
| Egyszerűsített tárolók kezelése |Konfigurálhatja és kezelheti a tárolt adatok a helyszínen, a távoli kiszolgálón, és a felhőben lévő felügyeleti eszközöket biztosít a rendszer. Emellett kezelheti a biztonsági mentés és visszaállítás funkciókat egy Microsoft Management Console (MMC) beépülő.|
| Továbbfejlesztett vész-helyreállítási és megfelelőség |Nincs szükség a bővített helyreállítási idő. Ehelyett helyreállítja adatok igény szerint. Ez azt jelenti, hogy a normál működés minimális megszakítás folytatása. Emellett beállíthatja a mentési ütemezések és az adatmegőrzés megadásához. |
| Adatok mobilitási |Microsoft Azure felhőszolgáltatások feltöltött adatok elérhetők a helyreállítási és áttelepítési célokból más helyekre. Emellett az StorSimple segítségével konfigurálhatja a StorSimple felhő készülékek a Microsoft Azure-ban futó virtuális gépek (VM). A virtuális gépek virtuális eszközök használhatja vizsgálati vagy helyreállítási célokra tárolt adatok eléréséhez. |
| Az üzletmenet folytonossága |Lehetővé teszi a StorSimple 5000-7000-es adatsorozat az adatok áttelepítése a StorSimple 8000 series eszközre. |
| Az Azure Government portálon rendelkezésre állása |Az Azure Government portálon StorSimple érhető el. További információkért lásd: [a kormányzati portálon a helyszíni StorSimple eszköz üzembe helyezése](storsimple-8000-deployment-walkthrough-gov-u2.md). |
| Adatvédelem és rendelkezésre állás |A StorSimple 8000 series zóna redundáns tárolás (ZRS), helyileg redundáns tárolás (LRS) és a georedundáns tárolás (GRS) mellett támogatja. Tekintse meg [Ez a cikk az Azure Storage redundancia beállításai](https://azure.microsoft.com/documentation/articles/storage-redundancy/) zrs-t a részletekért. |
| Kritikus fontosságú alkalmazások támogatása |StorSimple lehetővé teszi a megfelelő, a helyileg rögzített kötetek azonosítása amely viszont biztosítja, hogy a kritikus alkalmazások által megkövetelt adatok nem rétegzett a felhőbe. Helyileg rögzített kötetek nem tartoznak felhő késések vagy kapcsolódási problémákat. További információ a helyileg rögzített kötetekhez: [kötetek kezelése a StorSimple Device Manager szolgáltatással](storsimple-8000-manage-volumes-u2.md). |
| Alacsony késéssel és nagy teljesítményű |Felhő készülékek előnyeit a magas szintű teljesítmény, kis késleltetésű részeit, a prémium szintű Azure storage hozhat létre. További információ a StorSimple prémium felhő készülékek: [központi telepítése és kezelése az Azure-ban egy StorSimple felhő készülék](storsimple-8000-cloud-appliance-u2.md). |


## <a name="storsimple-components"></a>StorSimple-összetevők
A Microsoft Azure StorSimple megoldáshoz összetevői a következők:

* **A Microsoft Azure StorSimple eszköz** – helyszíni hibrid tárolási tömb, amely tartalmazza az SSD és HDD, redundáns vezérlők és az automatikus feladatátvételi lehetőségeket. A tartományvezérlők kezelése a tároló rétegezésével, elhelyezéséhez jelenleg használt (vagy kiemelt) adatok helyi tárolóban (az eszköz vagy a helyszíni kiszolgálók), miközben ritkábban használt adatok áthelyezését a felhőbe.
* **StorSimple felhő készülék** – más néven a StorSimple virtuális készüléknek, ez az egy architektúrájának replikáló a StorSimple eszköz szoftverének verziójával, és a fizikai hibrid tárolóeszköz legtöbb képességeit. A StorSimple felhő készülék az Azure virtuális gép egyetlen csomópontján fut. Prémium szintű virtuális eszközök, a prémium szintű Azure storage előnyeit, amelyek Update 2 és újabb verziói is.
* **StorSimple Device Manager szolgáltatás** – egy bővítményt, az Azure portál, amely lehetővé teszi a StorSimple eszköz vagy a StorSimple felhő készülék egyetlen webes felhasználói felületen keresztüli kezelését. A StorSimple Device Manager szolgáltatás segítségével létrehozása és szolgáltatások kezelése, és eszközök kezeléséhez, megtekintheti a riasztásokat, kötetek, kezelése és megtekintése és megtekintéséhez és kezeléséhez biztonsági mentési házirendek a biztonságimásolat-katalógus.
* **A Windows PowerShell-lel** –, amelyek segítségével kezelheti a StorSimple eszközt a parancssorból. A Windows PowerShell-lel rendelkezik funkciókat, amelyek lehetővé teszik a StorSimple-eszköz regisztrálása, állítsa be a hálózati illesztő az eszközön, bizonyos típusú frissítések telepítése, az eszköz hibaelhárításához a támogatási munkamenet elérésével, és módosítsa az eszköz állapotát. A StorSimple Windows PowerShell csatlakozás soros konzolon vagy a Windows PowerShell távoli eljáráshívás segítségével érheti el.
* **Az Azure PowerShell StorSimple-parancsmagok** –, amelyek lehetővé teszik a parancssorból szolgáltatásiszint- és áttelepítési feladatok automatizálása a Windows PowerShell-parancsmagok egy gyűjteményét. A StorSimple a Azure PowerShell-parancsmagokkal kapcsolatos további információkért látogasson el a [parancsmag-referencia](/powershell/module/azure/?view=azuresmps-3.7.0#azure).
* **StorSimple Snapshot Manager** – egy MMC beépülő modul által használt kötet csoportok és a Windows kötet árnyékmásolata szolgáltatás alkalmazáskonzisztens biztonsági mentés létrehozásához. Emellett a StorSimple Snapshot Manager segítségével is hozzon létre biztonsági mentési ütemezés és a Klónozás vagy kötetek visszaállítása.
* **A SharePoint StorSimple Adapter** – olyan eszköz, amely a Microsoft Azure StorSimple tárolási és az adatvédelem transzparens módon kiterjeszti a SharePoint-kiszolgálóra halgazdaságok, miközben StorSimple tárolási megtekinthető és kezelhető a SharePoint központi felügyeleti portálról.

Az alábbi ábrán a Microsoft Azure StorSimple architektúrája és összetevői magas szintű áttekintést nyújt.

![StorSimple-architektúra](./media/storsimple-overview/overview-big-picture.png)

Az alábbi szakaszok ismertetik részletesebben ezeket az összetevőket, és azt ismertetik, hogyan a megoldás adatok elrendezése, foglal helyet, és elősegíti a tárolók kezelése és az adatok védelmére. Az utolsó szakaszban biztosít jelentésdefiníciókat néhány a fontos fogalmakat tartalmazza, és a StorSimple-összetevők és a felügyeletük kapcsolatos fogalmak.

## <a name="storsimple-device"></a>StorSimple-eszköz
A Microsoft Azure StorSimple-eszköz a helyszíni hibrid tárolási tömb, amely elsődleges tárolási és a rajta található adatot iSCSI hozzáférést biztosít. A felhőalapú tárolással kommunikációt kezeli, valamint biztosítja a biztonsági és a titkosítás az összes adatot, hogy a Microsoft Azure StorSimple megoldáshoz.

A StorSimple eszköz magában foglalja az SSD és HDD merevlemez-meghajtók, valamint a Feladatátvételi fürtszolgáltatás és automatikus támogatása. Egy megosztott processzor, a megosztott tároló és a két tükrözött vezérlő tartalmaz. Minden vezérlő a következő funkciókkal rendelkezik:

* A fogadó számítógéphez való kapcsolódás
* A helyi hálózat (LAN) való kapcsolódáshoz legfeljebb hat hálózati portok
* Hardver ellenőrzése
* A nem felejtő közvetlen elérésű memória (NVRAM), amely mindaddig megőrzi az adatokat, akkor is, ha tápellátása megszakad
* Fürttámogató frissítés a feladatátvevő fürt kiszolgálóinak a szoftverfrissítések kezeléséhez, hogy a frissítések minimális vagy nincs hatással a szolgáltatás rendelkezésre állása
* Fürtszolgáltatás, mely funkciók, például egy háttér-fürt, a magas rendelkezésre állás biztosítása és minimalizálja a állapotot okoz, amely akkor jelentkezhet, ha egy HDD vagy SSD meghibásodik vagy offline állapotba

Csak egy tartományvezérlő aktív bármikor időben. Ha nem sikerül az aktív vezérlővel, a második tartományvezérlő automatikusan aktívvá válik.

További információkért látogasson el [StorSimple hardverösszetevők és állapot](storsimple-8000-monitor-hardware-status.md).

## <a name="storsimple-cloud-appliance"></a>StorSimple Cloud Appliance
StorSimple hozhat létre egy felhőalapú készülék a architektúra és a fizikai hibrid tárolóeszköz képességeket sorolja. A StorSimple felhő készülék (más néven a StorSimple virtuális készülék) az Azure virtuális gép egyetlen csomópontján fut. (A felhő készülék csak hozhatók létre egy Azure virtuális gépen. Nem hozható létre a StorSimple eszköz vagy egy helyszíni kiszolgálón.)

A felhő készülék a következő funkciókkal rendelkezik:

* Egy fizikai készülék viselkedik, és kínálhat egy iSCSI-illesztő a virtuális gépek a felhőben.
* Felhő készülékek korlátlan számú létrehozása a felhőben, és kapcsolja be- és kikapcsolását szükség szerint.
* Segíthet a helyszíni környezetben vészhelyreállítás, fejlesztési és tesztelési forgatókönyvek szimulálása, és elősegíti az elemszintű lekérési biztonsági másolatból.

A StorSimple felhő készülék a két modell érhető el: a 8010-es eszköz (korábbi nevén az 1100-as modell) és a 8020-as modellt eszköz. A 8010-es eszköz regisztrációját 30 TB-os maximális kapacitását. A 8020 eszköz, amely kihasználja a prémium szintű Azure storage, 64 TB-os maximális kapacitása nem. (A helyi rétegeken, a prémium szintű Azure storage eltárolja SSD meghajtókon mivel standard tárolási eltárolja a HDD.) Vegye figyelembe, hogy kell-e a prémium szintű storage egy prémium szintű Azure storage-fiókot. Prémium szintű storage kapcsolatos további információkért látogasson el [prémium szintű Storage: nagy teljesítményű tárolást Azure virtuális gépek terheléseihez](../virtual-machines/windows/premium-storage.md).

A StorSimple felhő készülék kapcsolatos további információkért látogasson el [központi telepítése és kezelése az Azure-ban egy StorSimple felhő készülék](storsimple-8000-cloud-appliance-u2.md).

## <a name="storsimple-device-manager-service"></a>A StorSimple eszköz kezelő szolgáltatás
A Microsoft Azure StorSimple biztosít egy webes felhasználói felület (a StorSimple eszköz kezelő szolgáltatás), amelynek segítségével központilag kezelheti adatközpont és felhőbeli tárhelyén. A StorSimple Device Manager szolgáltatás segítségével a következő feladatokat:

* A StorSimple eszköz rendszerbeállításainak konfigurálására.
* Konfigurálja, és a StorSimple eszközök biztonsági beállításainak kezelése.
* Felhő hitelesítő adatokat és a tulajdonságok konfigurálása.
* Konfigurálása és kezelése a kiszolgálón köteteket.
* Kötet csoportok konfigurálása.
* Készítsen biztonsági másolatot, és állítsa vissza adatokat.
* Figyelemmel kísérni a teljesítményét.
* Tekintse át a rendszer beállításait, és lehetséges problémák azonosításához.

A StorSimple Device Manager szolgáltatással kivéve azokat, a szükséges állásidő, például a kezdeti beállítás és a frissítések telepítését a rendszer a felügyeleti feladatok végrehajtására.

További információkért látogasson el [felügyelete a StorSimple eszközt a StorSimple Device Manager szolgáltatással](storsimple-8000-manager-service-administration.md).

## <a name="windows-powershell-for-storsimple"></a>A Windows PowerShell-lel
A Windows PowerShell-lel egy parancssori felületet biztosít, amely segítségével létrehozása és kezelése a Microsoft Azure StorSimple szolgáltatás és beállítása és a StorSimple eszközök figyeléséhez. Egy Windows PowerShell-alapú, parancssori felületet, amely tartalmazza a StorSimple eszköz kezelésére szolgáló dedikált parancsmagok. A Windows PowerShell-lel funkciókat, amelyek lehetővé teszik, hogy rendelkezik:

* Eszköz regisztrálása.
* Állítsa be a hálózati illesztő egy eszközön.
* Bizonyos típusú frissítések telepítéséhez.
* Az eszköz hibaelhárításához a támogatási munkamenet elérésével.
* Módosítsa az eszköz állapotát.

Akkor érhessék el a Windows PowerShell StorSimple soros konzolon (állomáson közvetlenül az eszköz csatlakozik) vagy távoli Windows PowerShell távoli eljáráshívás segítségével. Vegye figyelembe, hogy egy Windows PowerShell StorSimple tevékenységek, például az eszközök kezdeti regisztrációja a soros konzol csak végezhető.

További információkért látogasson el [használata a Windows PowerShell-lel az eszköz felügyeletére](storsimple-8000-windows-powershell-administration.md).

## <a name="azure-powershell-storsimple-cmdlets"></a>Az Azure PowerShell StorSimple-parancsmagok
Az Azure PowerShell StorSimple parancsmagok, melyek a Windows PowerShell-parancsmagok, amelyek lehetővé teszik a parancssorból szolgáltatásiszint- és áttelepítési feladatok automatizálására. A StorSimple a Azure PowerShell-parancsmagokkal kapcsolatos további információkért látogasson el a [parancsmag-referencia](/powershell/module/azure/?view=azuresmps-3.7.0).

## <a name="storsimple-snapshot-manager"></a>StorSimple Snapshot Manager
StorSimple Snapshot Manager a Microsoft Management Console (MMC) beépülő modulja, amely egységes, létrehozásához használhatja az időpontban a biztonsági másolatok a helyi és felhőbeli adatát. A beépülő modul a Windows Server-alapú gazdagépen futtatja. A StorSimple Snapshot Manager használhatja:

* Konfigurálhatja, készítsen biztonsági másolatot, és törölje köteteket.
* Kötet csoportokat győződjön meg arról, hogy a biztonsági másolatba mentett adatok alkalmazáskonzisztens.
* Biztonsági házirendek kezelése, hogy az adatok biztonsági mentése egy előre meghatározott ütemezés szerint és a kijelölt helyen tárolni (helyi vagy a felhőben).
* Állítsa vissza a köteteket, és a fájlokat.

Biztonsági mentések pillanatképként, amely csak a változásokat jegyezze fel az utolsó pillanatkép készítése óta, és sokkal kevesebb mint a teljes biztonsági mentés helyet igényel a rendszer rögzíti. Hozzon létre biztonsági mentési ütemezést, vagy azonnali biztonsági másolatok készítése, igény szerint. Ezenkívül használhatja a StorSimple Snapshot Manager adatmegőrzési létrehozásához, hogy a vezérlő hány pillanatképeket a rendszer menti. Ha ezt követően van szüksége a biztonsági másolat, a StorSimple Snapshot Manager lehetővé teszi az adatok helyreállítását a helyi katalógus vagy a felhőalapú pillanatfelvételek választja. 

Ha katasztrófa történik, vagy vissza kell állítania az adatok más okból, a StorSimple Snapshot Manager visszaállítja azt Növekményesen igény szerint. Adatok visszaállítása nem szükséges, hogy leállítja a teljes rendszer állítsa vissza a fájlt, cserélje le a készülék, illetve műveletek áthelyezése egy másik helyre.

További információkért látogasson el [Mi az StorSimple Snapshot Manager?](storsimple-what-is-snapshot-manager.md)

## <a name="storsimple-adapter-for-sharepoint"></a>SharePointhoz készült StorSimple-adapter
A Microsoft Azure StorSimple a SharePoint, a StorSimple tárolási és adatok védelmi szolgáltatások SharePoint kiszolgálófarmok transzparens módon szélességénél választható összetevőként a StorSimple-adaptert tartalmazzon. Az adapter működik egy Blob Storage (RBS) szolgáltatót és az SQL Server RBS szolgáltatás számára, lehetővé téve a biztonsági másolat a Microsoft Azure StorSimple rendszer kiszolgálóra történő áthelyezése a Blobok. Microsoft Azure StorSimple majd tárolja a TÁRFIÓKBAN tárolt adatok helyben vagy a felhőben-használata alapján.

A StorSimple-adaptert SharePoint kezelheti a SharePoint központi felügyeleti portálon találja meg. Következésképpen SharePoint felügyeleti központosított marad, és úgy tűnik, hogy a SharePoint-farm összes tároló.

További információkért látogasson el [SharePoint StorSimple adaptert](storsimple-adapter-for-sharepoint.md). 

## <a name="storage-management-technologies"></a>Felügyeleti tárolótechnológiák
A StorSimple eszköz, a virtuális eszköz és a többi összetevő kívül a Microsoft Azure StorSimple a következő szoftver technológiákat használja adatok gyors eléréséhez és csökkenthető tároló fogyasztása:

* [Automatikus tárolórétegzés](#automatic-storage-tiering) 
* [Dinamikus kiosztás](#thin-provisioning) 
* [A deduplikáció és a tömörítés](#deduplication-and-compression) 

### <a name="automatic-storage-tiering"></a>Automatikus tárolórétegzés
A Microsoft Azure StorSimple automatikusan adatokat logikai rétegek aktuális használatát, kor, és egyéb adatok kapcsolat alapján rendezi. Legtöbb aktív adatok helyileg van tárolva, miközben kevesebb az aktív és inaktív adatok automatikusan át a felhőbe. A következő ábra szemlélteti ezt a tárolási módszert.

![StorSimple tárolási rétegek](./media/storsimple-overview/hcs-data-services-storsimple-components-tiers.png)

Ahhoz, hogy a gyors elérés érdekében, a StorSimple tárolja nagyon aktív (gyakran használt adatok) adatokat SSD meghajtókon a StorSimple eszközt. Alkalmanként használt adatokat tárolja (mozog adatok) a HDD-k, az eszköz vagy az Adatközpont kiszolgálókon. Inaktív adatok, a biztonsági mentési adatok átvitel során, és adatok őrződnek meg az archiválási és megfelelőségi célokra a felhőbe. 

> [!NOTE]
> 2. frissítés vagy újabb verzióját a helyileg rögzített kötet is megadhat, ebben az esetben az adatok továbbra is a helyi eszközön, és nem a felhőbe rétegzett. 


StorSimple állítja be, és újrarendezi az adatokat, és a használati minták tárolási hozzárendelését módosítsa. Például bizonyos adatokat válhatnak kevésbé aktív adott idő alatt. Fokozatosan kevésbé aktív mihelyt áttelepítése az SSD-k HDD, majd a felhőben. Ha ugyanazokat az adatokat ismét aktívvá válik, a tárolóeszköz vissza történő áttelepítés.

A tároló-rétegezési folyamat a következőképpen történik:

1. Egy rendszergazda állít be egy Microsoft Azure felhőalapú társzolgáltatás fiókja.
2. A rendszergazda a soros konzol és a StorSimple Device Manager szolgáltatásfuttatás (az Azure portálon) beállíthatja az eszköz és a fájl kiszolgálót, kötetek és adatok védelme szabályzatokat hoznak létre. A helyszíni gépeket (például a fájlkiszolgálók) a StorSimple eszköz eléréséhez használja az Internet Small Computer System Interface (iSCSI).
3. StorSimple kezdetben az eszköz gyors SSD-rétegen tárolja az adatokat.
4. Az SSD-réteg kapacitásának megközelíti, StorSimple deduplicates és tömöríti a legrégebbi adatblokkok, és helyezi azokat a HDD-réteget.
5. HDD réteg megközelítések kapacitása StorSimple titkosítja a legrégebbi és biztonságosan elküldi azokat a Microsoft Azure storage-fiók HTTPS használatával.
6. A Microsoft Azure hoz létre az adatok több replika az adatközpontban és a távoli adatközpontban, győződjön meg arról, hogy az adatok helyreállítása, ha katasztrófa történik.
7. Ha a fájlkiszolgáló a felhőben tárolt adatokat kér, a StorSimple adja vissza zökkenőmentesen, és tárolja a másolatukat az SSD-rétegen a StorSimple eszköz.

#### <a name="how-storsimple-manages-cloud-data"></a>Hogyan kezeli a StorSimple a felhőbeli adatát

StorSimple ügyféladatok deduplicates a pillanatképek és az elsődleges adatok (állomások írt adatok). Míg a deduplikáció nagy a tárhely hatékonyságát, "Mi az a felhőben található" bonyolult kérdése teszi. A rétegzett elsődleges adatok és a pillanatkép-adatok átfedésben egymással. Az adatok a felhőben egyetlen adattömb rétegzett elsődleges adatként használható, és több pillanatképek is hivatkozhatnak. Minden felhőalapú pillanatfelvétel biztosítja, hogy az időpontban adatok másolatát zárolt kiterjeszti a felhőbe, mindaddig, amíg a pillanatkép törlését.

Adatok csak törlődik a felhőből, amikor nincsenek adatok mutató hivatkozások. Például ha egy felhő-pillanatfelvételt az összes adat, amely a StorSimple eszközt, és ezután törölje az egyes elsődleges adatok vesszük, azt jelenik meg a _elsődleges adatok_ dobja el azonnal. A _felhőalapú adatokat_ viszony – például a rétegzett adatok és a biztonsági mentések ugyanaz marad. Ez azért, mert egy pillanatkép továbbra is hivatkozik a felhőbeli adatát. Pillanatkép törlését követően a felhő (és bármilyen egyéb pillanatkép hivatkozott ugyanazokat az adatokat), fogyasztás elhagyta a felhő. Mielőtt tudjuk eltávolítani a felhőbeli adatát, ellenőrizzük, hogy a pillanatképek nem továbbra is hivatkoznak-e el az adatok. Ez a folyamat _szemétgyűjtés_ és a háttérben szolgáltatásként fut az eszközön. Felhő adatok eltávolítása nincs közvetlen módon történő el az adatok a törlés előtt ellenőrzi a szemétgyűjtési szolgáltatás. A szemétgyűjtés függ a pillanatképek és az összes adat teljes száma. Általában a felhő adatok törlődnek a kisebb, mint hét.


### <a name="thin-provisioning"></a>Dinamikus kiosztás
Dinamikus kiosztás egy virtualizációs technológia, amely rendelkezésre álló tár hosszabb legyen, mint a fizikai erőforrásokat jelenik meg. Ahelyett, hogy elegendő tárhely előre foglalással, StorSimple használja a dinamikus kiosztás aktuális követelményeknek éppen elegendő lemezterületet foglaljon le. Felhőalapú tárolás rugalmas jellege elősegíti ezt a módszert használja, mert StorSimple növelheti vagy csökkentheti a felhőalapú tárolást tartalomkérései változó igényeinek kielégítéséhez.

> [!NOTE]
> Helyileg rögzített kötetek nem vékonyan létesített. Csak helyi kötet lefoglalt tárolót egészében lett kiépítve, a kötet létrehozásakor.


### <a name="deduplication-and-compression"></a>A deduplikáció és a tömörítés
A Microsoft Azure StorSimple további a tárolási követelmények csökkentése érdekében a deduplikáció és az adatok tömörítést alkalmaz.

A deduplikáció összességében jelentősen csökkenti a tárolt adatkészlet redundanciájának kiküszöbölése révén tárolt adatok. Adat változik, StorSimple figyelmen kívül hagyja a nem módosított adatainak és csak a változtatásokat rögzíti. Továbbá a StorSimple csökkenti a tárolt adatok mennyisége azonosításával és a felesleges adatok eltávolítása. 

> [!NOTE]
> A helyileg rögzített kötetekhez adatait nem deduplikált vagy tömörített. Azonban a helyileg rögzített kötetek biztonsági másolatokat deduplikált, és tömöríti.


## <a name="storsimple-workload-summary"></a>StorSimple a számítási feladatok összefoglalása
A támogatott StorSimple munkaterhelések összefoglalását az alábbi táblázatban.

| Forgatókönyv | Számítási feladat | Támogatott | Korlátozások | Verzió |
| --- | --- | --- | --- | --- |
| Együttműködés |Fájlmegosztás |Igen | |Az összes verzió |
| Együttműködés |Elosztott fájlmegosztás |Igen | |Az összes verzió |
| Együttműködés |SharePoint |Igen* |Csak a helyileg rögzített kötetekhez támogatott |Update 2 és újabb verziók |
| Archiválási |Egyszerű fájl archiválása |Igen | |Az összes verzió |
| Virtualizáció |Virtual machines (Virtuális gépek) |Igen* |Csak a helyileg rögzített kötetekhez támogatott |Update 2 és újabb verziók |
| Adatbázis |SQL |Igen* |Csak a helyileg rögzített kötetekhez támogatott |Update 2 és újabb verziók |
| Figyelőrendszer |Figyelőrendszer |Igen* |Támogatott, ha a StorSimple eszköz van kijelölve, csak a munkaterhelés |Update 2 és újabb verziók |
| Biztonsági mentés |Elsődleges cél biztonsági mentése |Igen* |Támogatott, ha a StorSimple eszköz van kijelölve, csak a munkaterhelés |Update 3 és újabb verziók |
| Biztonsági mentés |Másodlagos cél biztonsági mentése |Igen* |Támogatott, ha a StorSimple eszköz van kijelölve, csak a munkaterhelés |Update 3 és újabb verziók |

*Igen &#42; -Megoldás alapelveket és korlátozásokat kell alkalmazni.*

Az alábbi munkaterhelések nem támogatottak az StorSimple 8000 sorozat eszközeire. StorSimple telepíthetők, ha az ilyen terhelések jár beállításai nem támogatottak.

* Orvosi lemezképpel végrehajtott telepítéshez
* Exchange
* VDI
* Oracle
* SAP
* Big Data
* Tartalom terjesztése
* Rendszerindítás SCSI

Az alábbiakban olvashatja a StorSimple támogatott infrastruktúra-elemek listáját.

| Forgatókönyv | Számítási feladat | Támogatott | Korlátozások | Verzió |
| --- | --- | --- | --- | --- |
| Általános kérdések |Express Route |Igen | |Az összes verzió |
| Általános kérdések |FC DataCore |Igen* |Támogatott, ha DataCore SANsymphony |Az összes verzió |
| Általános kérdések |ELOSZTOTT FÁJLRENDSZER REPLIKÁCIÓS SZOLGÁLTATÁSA |Igen* |Csak a helyileg rögzített kötetekhez támogatott |Az összes verzió |
| Általános kérdések |Indexelés |Igen* |A rétegzett kötetek csak metaadatok indexelése támogat (adatok nélkül).<br>A helyileg rögzített kötetekhez a teljes indexelő támogatott. |Az összes verzió |
| Általános kérdések |Víruskereső |Igen* |A rétegzett kötetek csak nyissa meg a vizsgálati és a záró támogatott.<br> A helyileg rögzített kötetekhez teljes ellenőrzés esetén támogatott. |Az összes verzió |

*Igen &#42; -Megoldás alapelveket és korlátozásokat kell alkalmazni.*

Az alábbiakban az egyéb StorSimple megoldások létrehozásához használt szoftverek listáját.

| Munkaterhelésének típusát | A StorSimple használt szoftverek | Támogatott verziók|Megoldási útmutató csatolása| 
| --- | --- | --- | --- |
| Biztonsági mentés célja |Veeam |Veeam v 9-es és újabb verziók |[Biztonsági mentési cél a Veaam StorSimple](storsimple-configure-backup-target-veeam.md)|
| Biztonsági mentés célja |Exec VERITAS biztonsági mentése |Biztonsági mentési Exec 16 és újabb verziók |[A biztonsági mentési Exec biztonsági mentési cél StorSimple](storsimple-configure-backup-target-using-backup-exec.md)|
| Biztonsági mentés célja |VERITAS NetBackup |NetBackup 7.7.x és újabb verziók  |[Biztonsági mentési cél a NetBackup StorSimple](storsimple-configure-backuptarget-netbackup.md)|
| Globális fájlmegosztás <br></br> Együttműködés |Talon  |[A Talon StorSimple](https://www.talonstorage.com/products/fast-deployment-azure-storsimple) | |

## <a name="storsimple-terminology"></a>StorSimple-terminológia
A Microsoft Azure StorSimple megoldásban való telepítése előtt javasoljuk, hogy tekintse át az alábbi szakkifejezéseket és definíciójukat.

### <a name="key-terms-and-definitions"></a>Legfontosabb kifejezések és meghatározások
| Kifejezés (mozaikszó vagy rövidítése) | Leírás |
| --- | --- |
| hozzáférés-vezérlési rekordot (ACR) |Egy olyan rekordot hozzárendelt egy kötetet a Microsoft Azure StorSimple eszközön, amely meghatározza, hogy mely gazdagépek tud hozzá csatlakozni. A meghatározása alapul a iSCSI minősített nevét (IQN) a gazdagépet (a a ACR), amely a StorSimple eszköz csatlakozik. |
| AES-256-RA |A 256 bites Advanced Encryption Standard (AES) algoritmus adatok titkosítására és a felhőben az átvitel során. |
| foglalásiegység-méret (Ausztráliai) |A legkisebb memóriamennyiség, amely kiosztható fel a Windows-fájlrendszer. Ha egy fájl mérete nem egy páros számú többszöröse az a fürt méretét, területnek kell használni ahhoz, hogy a fájl (akár a következő számú többszöröse az a fürt mérete) elveszett lemezterület és a merevlemez töredezettsége. <br>Az Azure StorSimple-köteteket ajánlott Ausztráliai nem 64 KB-os, mert azt jól működik a deduplikációs algoritmusokkal. |
| automatikus tárolórétegzés |Automatikusan áthelyezést kevésbé aktív adatok SSD-k a HDD-k, majd a réteg a felhőben, és a központi felhasználói felületről megadott összes felügyeleti engedélyezésével. |
| Biztonságimásolat-katalógus |Biztonsági mentések, általában használt alkalmazások típusa kapcsolódó gyűjteménye. Ehhez a gyűjteményhez tartozó biztonságimásolat-katalógus panel a StorSimple Device Manager szolgáltatás felhasználói felületén jelennek meg. |
| biztonsági mentési katalógusfájl |A fájlt, amely elérhető a StorSimple Snapshot Manager biztonsági mentési adatbázisban jelenleg tárolt pillanatképek. |
| a biztonsági mentési házirend |A kijelölt kötetek biztonsági másolat típusa és ütemtervet, amely lehetővé teszi egy előre meghatározott ütemezés szerint biztonsági mentések létrehozását. |
| nagyméretű bináris objektumok (BLOB) |Egy adatbázis-kezelő rendszer egyetlen egységként tárolt bináris adatok gyűjteménye. Blobok jellemzően képek, hang-, vagy más multimédiás objektumok, bár egyes esetekben bináris végrehajtható kód egy BLOB tárolja. |
| Challenge Hésshake Authentication Protocol (CHAP) |A kapcsolat létrehozásakor, a jelszó vagy titkos kulcs megosztása társ alapuló társa hitelesítéséhez használt protokoll. A CHAP egyirányú vagy kölcsönös lehet. Az egyirányú CHAP a tároló egy kezdeményező hitelesíti. Kölcsönös CHAP megköveteli, hogy a cél hitelesíteni a kezdeményező és, hogy a kezdeményező hitelesíti a cél. |
| Klónozás |A kötet másolatát. |
| Felhő, egyetlen rétegként (CaaT) |A felhő tárolási, a tároló-architektúra belül egyetlen rétegként integrált, hogy az összes tárolási úgy tűnik, hogy egy vállalati tárolási hálózat része. |
| felhőszolgáltató (CSP) |A szolgáltató a felhőalapú informatika meghatározására szolgáltatások. |
| felhő-pillanatfelvételt |A felhőben tárolt adatok kötet pont időponthoz kötött másolata. Egy felhőalapú pillanatfelvétel megegyezik egy másik, a telephelytől távoli tárolórendszer replikálva pillanatképet. Felhőalapú pillanatfelvételek különösen hasznosak a vész-helyreállítási eljárással. |
| Felhőalapú tárolás titkosítási kulcsa |Jelszó vagy a StorSimple eszköz által küldött az eszköz a felhőre a titkosított adatok eléréséhez használt kulcs. |
| fürttámogató frissítés |A feladatátvevő fürt kiszolgálóinak a szoftverfrissítések kezelése, hogy a frissítések minimális, vagy nincs hatással a szolgáltatás rendelkezésre állása. |
| DataPath |Funkcionális egység összekapcsolt adatfeldolgozási műveleteket gyűjteménye. |
| inaktiválása |Egy állandó művelet, amely a StorSimple eszköz és a hozzárendelt felhő szolgáltatás közötti kapcsolat megszakad. Felhőalapú pillanatfelvételek az eszköz ezen folyamat után továbbra is és klónozott vagy katasztrófa utáni helyreállítás használt. |
| lemez tükrözés |A logikai lemezkötet külön, rögzített replikációs meghajtók valós idejű folyamatos rendelkezésre állás biztosításához. |
| tükrözést |A dinamikus lemezek a logikai lemez kötetek replikációját. |
| a dinamikus lemezek |A kötet lemezformátumot, amely a logikai lemezkezelő (LDM) használatával tárolja, és adatok kezeléséhez több fizikai lemezt. Szabadítson fel helyet adja meg a dinamikus lemezek is javasolt. |
| Kiterjesztett lemezek álló, lemezcsoport (EBOD) ház |A Microsoft Azure StorSimple eszköz extra merevlemez-meghajtóról lemezeket a további tárhely tartalmazó másodlagos ház. |
| FAT-kiépítés |A hagyományos tárolólétesítés mely Storage legyen lefoglalva terület alapján kell várható (és általában túl az aktuális kell). Lásd még: *dinamikus kiosztás*. |
| merevlemez-meghajtó (HDD) |Adatok tárolására forgó egyedi lemezeinek száma általában használó meghajtóra. |
| hibrid felhőalapú tárolásról |Egy tároló-architektúra, amely a helyi és távoli, beleértve a felhőalapú tárolást. |
| Internet Small Computer System Interface (iSCSI) |Az Internet Protocol IP-alapú tárolási hálózati szabvány az adatok tárolási berendezések vagy létesítményekben csatolásának. |
| iSCSI-kezdeményező |A szoftverfrissítési összetevő, amely lehetővé teszi, hogy a gazdagép Windows rendszert futtató egy iSCSI-alapú tárolás külső hálózathoz való kapcsolódáshoz. |
| az iSCSI minősített nevét (IQN) |Az iSCSI-tároló vagy kezdeményező azonosító egyedi név. |
| iSCSI-tároló |Biztosít a központosított iSCSI lemez-alrendszereket a tárolóhálózat szoftverösszetevő. |
| élő archiválási |Egy tárolási módszert alkalmaz, amelyben archiválási adatok érhető el (ez nem tárolja a telephelytől távol szalag, például) folyamatosan. Microsoft Azure StorSimple használ, az élő archiválása. |
| helyileg rögzített kötet |az eszközön található, és soha nem a felhőbe többszintű kötetre. |
| helyi pillanatfelvétel |A Microsoft Azure StorSimple eszközön tárolt adatok kötet pont időponthoz kötött másolata. |
| Microsoft Azure StorSimple |Egy hatékony megoldás, datacenter tárolóeszköz és szoftver, amely lehetővé teszi az informatikai szervezetek kihasználhatják a felhőalapú tárolást, mintha az Adatközpont tárolójában lenne. StorSimple költségek csökkentése során leegyszerűsíti az adatok védelme és kezelése. A megoldás egyesíti az elsődleges tárolási, archiválás, biztonsági mentési és vész-helyreállítási keresztül való zökkenőmentes integrációt biztosítanak a felhőben. SAN tárolás és a felhőbeli adatkezelés egy vállalati szintű platformon kombinálásával a StorSimple eszközök sebességét, az egyszerűség és megbízhatóságának tárolással kapcsolatos összes kell engedélyezni. |
| Teljesítmény- és hűtési modul (PCM) |A StorSimple eszközt a áramforrások és a hűtési ventilátor, ezért a név Power álló- és hűtési modul hardverösszetevők. Az eszköz elsődleges ház két 764W PCMs rendelkezik, mivel a EBOD ház két 580W PCMs. |
| Elsődleges ház |Fő szolgáltatással a StorSimple eszköz, amely tartalmazza az alkalmazás platform tartományvezérlők. |
| A helyreállítási idő célkitűzése (RTO) |A maximális időt, amely egy üzleti vagy rendszer előtt a rendszer arra kell fordított teljes visszaállítása után egy olyan vészhelyzet esetén. |
| soros csatlakozású SCSI (SAS) |Merevlemez-meghajtó (HDD) típusú. |
| szolgáltatásadat-titkosítási kulcs |A kulcs elérhetővé tenni az összes új StorSimple eszköz, amely regisztrálja magát a StorSimple Device Manager szolgáltatásban. A konfigurációs adatokat a StorSimple Device Manager szolgáltatás és az eszköz között továbbított nyilvános kulccsal titkosított, és csak a titkos kulcs segítségével eszközén majd lehet visszafejteni. Szolgáltatásadat-titkosítási kulcs lehetővé teszi, hogy a szolgáltatás a visszafejtéshez a titkos kulcs beszerzése. |
| Szolgáltatásregisztrációs kulcs |A kulcs, amely segít a StorSimple-eszköz regisztrálása a StorSimple Device Manager szolgáltatásban, úgy, hogy az Azure portálon további felügyeleti műveletekhez. |
| Small Computer System Interface (SCSI) |Fizikai számítógépek kapcsolódásához és a közöttük adatok átadására szabványok csoportja. |
| tartós állapotú meghajtót (SSD) |Nincs mozgó részek; tartalmazó lemez Ha például egy USB-meghajtóra. |
| Tárfiók |A tárfiók egy adott felhőre szolgáltató kapcsolódó hozzáférési hitelesítő adatokat. |
| SharePointhoz készült StorSimple-adapter |A Microsoft Azure StorSimple összetevője, amely transzparens módon kiterjeszti a StorSimple tárolási és adatvédelem a SharePoint server farms. |
| A StorSimple eszköz kezelő szolgáltatás |Az Azure portál, amely lehetővé teszi a helyszíni Azure StorSimple és a virtuális eszközök kezelésére a kiterjesztése. |
| StorSimple Snapshot Manager |A Microsoft Management Console (MMC) beépülő kezeléséhez a Microsoft Azure StorSimple biztonsági mentési és helyreállítási műveletek. |
| biztonsági másolatok készítéséhez |Ez a szolgáltatás lehetővé teszi a felhasználó egy interaktív biztonsági mentési kötet érvénybe. Manuális biztonsági másolat figyelésekor az automatikus biztonsági mentés meghatározott házirendjében véve kötet más módja. |
| Dinamikus kiosztás |Optimalizálja a hatékonyságát, amellyel a rendelkezésre álló szabad hely a tárolórendszerek használt módszer. A dinamikus kiosztást, a tároló lefoglalása egy adott időpontban minden felhasználó által igényelt terület alapján több felhasználó használ. Lásd még: *fat kiépítés*. |
| rétegezéséhez |Az aktuális használatát, kor, és egyéb adatok kapcsolat alapuló logikai csoportok adatok elrendezése. StorSimple automatikusan elrendezi a rétegek adatokat. |
| Kötet |Logikai tárolási területek meghajtók formájában jelenik meg. StorSimple-köteteket a gazdagép, beleértve a felderített iSCSI és a StorSimple eszköz által csatlakoztatott kötetek vannak rendelve. |
| kötettároló |A csoportosítás kötetek és a rájuk vonatkozó beállításokat. A StorSimple eszköz minden kötet kötettárolók vannak csoportosítva. Kötet tároló beállítások közé tartoznak a storage-fiókok, a titkosítási beállítások kapcsolódó titkosítási kulcsokat a felhőbe küldött adatokat, és a felhő érintő műveletek felhasznált sávszélesség. |
| kötet csoport |A StorSimple Snapshot Manager, a kötet csoport gyűjteménye készleteiből konfigurált biztonsági mentési feldolgozás megkönnyítése érdekében. |
| Kötet árnyékmásolata szolgáltatás (VSS) |Egy Windows Server operációsrendszer-szolgáltatás, amely elősegíti a alkalmazás konzisztencia által növekményes pillanatképek létrehozásának koordinálására Kötetárnyékmásolat-felismerésre képes alkalmazások kommunikál. VSS biztosítja, hogy az alkalmazások legyenek ideiglenesen inaktív pillanatfelvételeket készít. |
| A Windows PowerShell-lel |A Windows PowerShell-alapú parancssori felület fog működni, és a StorSimple eszköz kezelésére szolgál. Néhány alapvető funkciót, a Windows PowerShell megőrzésével az illesztő felügyelete a StorSimple eszköz körétől további dedikált parancsmagok rendelkezik. |

## <a name="next-steps"></a>Következő lépések
További tudnivalók [StorSimple biztonsági](storsimple-8000-security.md).

