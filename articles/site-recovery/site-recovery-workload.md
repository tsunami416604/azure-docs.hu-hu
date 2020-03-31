---
title: A helyszíni alkalmazások vész-helyreállítási szolgáltatása az Azure Site Recovery szolgáltatással
description: Azokat a számítási feladatokat ismerteti, amelyeket meg lehet védeni a vészhelyreállítás Azure Site Recovery szolgáltatással történő használatával.
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 2b901425a0020c0ccc7b834ee36d965910028018
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062844"
---
# <a name="about-disaster-recovery-for-on-premises-apps"></a>Információk a helyszíni alkalmazások vészhelyreállításáról

Ez a cikk ismerteti a helyszíni számítási feladatok és alkalmazások, amelyek et az [Azure Site Recovery](site-recovery-overview.md) szolgáltatással megvédheti a vészhelyreállításhoz.

## <a name="overview"></a>Áttekintés

A szervezeteknek üzletmenet-folytonossági és vész-helyreállítási (BCDR) stratégiára van szükségük a számítási feladatok és adatok biztonságos és elérhető vétlen helyének megőrzéséhez a tervezett és nem tervezett állásidő alatt. És, vissza a rendszeres munkakörülmények között.

Az Azure Site Recovery segít a BCDR-stratégia kidolgozásában. A Site Recovery használatával alkalmazásbarát replikációt végezhet a felhőbe vagy egy másodlagos helyre. A Site Recovery segítségével kezelheti a replikációt, végrehajthatja a vész-helyreállítási tesztelést, valamint futtathatja a feladatátvételeket és a feladat-visszavételeket. Az alkalmazások futtathatók Windows vagy Linux alapú számítógépeken, fizikai kiszolgálókon, VMware-en vagy Hyper-V-n.

A Site Recovery integrálható a Microsoft alkalmazásaival, például a SharePointtal, az Exchange-szel, a Dynamics-szal, az SQL Serverrel és az Active Directoryval. A Microsoft szorosan együttműködik a vezető gyártókkal, például az Oracle-lel, az SAP-val és a Red Hat-tal. A replikációs megoldásokat alkalmazásonként szabhatja testre.

## <a name="why-use-site-recovery-for-application-replication"></a>Miért előnyös a Site Recovery használata az alkalmazásreplikációhoz?

A Site Recovery az alábbi módokon járul hozzá az alkalmazásszintű védelemhez és helyreállításhoz:

- Alkalmazásfüggetlen, és replikációt biztosít a támogatott gépen futó munkaterhelések számára.
- Közel szinkron replikáció, a helyreállítási pont céljai (RPO) akár 30 másodpercig is megfelelni ük a legkritikusabb üzleti alkalmazások igényeinek.
- Alkalmazáskonzisztens pillanatképeket rögzít egy- vagy többszintű alkalmazásokról.
- Integráció az SQL Server AlwaysOn kiszolgálóval, valamint partnerség más alkalmazásszintű replikációs technológiákkal. Például az Active Directory replikáció, az SQL AlwaysOn és az Exchange adatbázis rendelkezésre állási csoportjai (DAGs).
- Rugalmas helyreállítási tervek, amelyek lehetővé teszik egy teljes alkalmazásverem egyetlen kattintással történő helyreállítását, valamint külső parancsfájlok és manuális műveletek belefoglalását a tervbe.
- Speciális hálózatkezelés a Site Recovery és az Azure területén az alkalmazáshálózati követelmények egyszerűsítése érdekében. A hálózatkezelés, például az IP-címek lefoglalása, a terheléselosztás konfigurálása és az Azure Traffic Managerrel való integráció alacsony helyreállítási idejű célkitűzések (RTO) hálózati átkapcsolásesetén.
- A szolgáltatás gazdag, éles használatra kész és alkalmazásspecifikus parancsprogramokat tartalmazó automatizációs kódtárat tartalmaz, amely letölthető, és beépíthető a helyreállítási tervekbe.

## <a name="workload-summary"></a>A számítási feladatok összefoglalása

A Site Recovery a támogatott gépeken futó bármilyen alkalmazást képes replikálni. Az alábbi táblázatban megadott alkalmazások további tesztelését követően a termékcsoportokkal együttműködve.

| **Munkateher** |**Virtuális gépek replikálása az Azure-ba** |**Hyper-V virtuális gépek replikálása másodlagos helyre** | **Hyper-V virtuális gépek replikálása az Azure-ba** | **VMware virtuális gépek replikálása másodlagos helyre** | **VMware virtuális gépek replikálása az Azure-ba** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |Igen |Igen |Igen |Igen |Igen|
| Webalkalmazások (IIS, SQL) |Igen |Igen |Igen |Igen |Igen|
| System Center Operations Manager |Igen |Igen |Igen |Igen |Igen|
| SharePoint |Igen |Igen |Igen |Igen |Igen|
| SAP<br/><br/>Replikálás SAP-helyről az Azure-ba nem fürtözött rendszer esetén |Igen (a Microsoft tesztelte) |Igen (a Microsoft tesztelte) |Igen (a Microsoft tesztelte) |Igen (a Microsoft tesztelte) |Igen (a Microsoft tesztelte)|
| Exchange (nem DAG) |Igen |Igen |Igen |Igen |Igen|
| Távoli asztal/VDI |Igen |Igen |Igen |Igen |Igen|
| Linux (operációs rendszer és alkalmazások) |Igen (a Microsoft tesztelte) |Igen (a Microsoft tesztelte) |Igen (a Microsoft tesztelte) |Igen (a Microsoft tesztelte) |Igen (a Microsoft tesztelte)|
| Dynamics AX |Igen |Igen |Igen |Igen |Igen|
| Windows fájlkiszolgáló |Igen |Igen |Igen |Igen |Igen|
| Citrix XenApp és XenDesktop |Igen|N/A |Igen |N/A |Igen |

## <a name="replicate-active-directory-and-dns"></a>Active Directory és DNS replikálása

A legtöbb vállalati alkalmazás számára elengedhetetlen az Active Directory- és DNS-infrastruktúra használata. A vész-helyreállítási során meg kell védenie és helyre kell állítania ezeket az infrastruktúra-összetevőket, mielőtt helyreállítaná a számítási feladatokat és az alkalmazásokat.

A Site Recovery segítséglével teljesen automatizált vészhelyreállítási tervet hozhat létre az Active Directoryhoz és a DNS-hez. Ha például egy elsődleges helyről egy másodlagos helyre szeretné átkerülni a SharePointot és az SAP-t, beállíthat egy helyreállítási tervet, amely először az Active Directory taséját veszi át. Ezután egy további alkalmazásspecifikus helyreállítási tervvel nem felel meg az Active Directoryt használó többi alkalmazás.

[További információ](site-recovery-active-directory.md) az Active Directory és a DNS vészutáni helyreállításáról.

## <a name="protect-sql-server"></a>Az SQL Server védelme

Az SQL Server adatszolgáltatások alapja számos üzleti alkalmazás egy helyszíni adatközpontban. A Site Recovery az SQL Server HA/DR technológiáival használható az SQL Servert használó többrétegű vállalati alkalmazások védelmére.

A Site Recovery a következőket biztosítja:

- Egyszerű és költséghatékony vészhelyreállítási megoldás az SQL Serverhez. Az SQL Server önálló kiszolgálói és fürtjei több verziójának és kiadásának replikálása az Azure-ba vagy egy másodlagos helyre.
- Integrálható SQL AlwaysOn rendelkezésre állási csoportokkal, az Azure Site Recovery helyreállítási tervek feladatátvételének és feladat-visszavételének kezeléséhez.
- Végpontok közötti helyreállítási tervek az alkalmazás összes rétege számára, ideértve az SQL Server-adatbázisokat is.
- Az SQL Server méretezése a site recovery-vel a csúcsterhelésekhez, az Azure-ban nagyobb IaaS virtuálisgép-méretekre _való felszakítással._
- Az SQL Server vészhelyreállítási funkcióinak egyszerű tesztelése. Feladatátvételi tesztek segítségével elemezheti az adatokat és megfelelőségi ellenőrzést futtathat az éles környezet befolyásolása nélkül.

[További információ](site-recovery-sql.md) az SQL-kiszolgáló vészutáni helyreállításáról.

## <a name="protect-sharepoint"></a>A SharePoint védelme

Az Azure Site Recovery szolgáltatással az alábbi módokon biztosíthatja a SharePoint-környezetek védelmét:

- Használata esetén nem szükséges költséges készenléti kiszolgálófarmot kiépítenie a vészhelyreállításhoz. A Site Recovery segítségével replikálhatja a teljes farmot (web-, alkalmazás- és adatbázisszinteket) az Azure-ba vagy egy másodlagos helyre.
- Leegyszerűsíti az alkalmazások üzembe helyezését és felügyeletét. Az elsődleges helyre telepített frissítések et a rendszer automatikusan replikálja. A frissítések egy másodlagos helyen lévő farm feladatátvétele és helyreállítása után érhetők el. Csökkenti a felügyeleti összetettséget és a készenléti gazdaság naprakészen tartásával kapcsolatos költségeket.
- Leegyszerűsíti a SharePoint-alkalmazások fejlesztését és tesztelését, mivel segítségével az éleshez hasonló, igény szerinti replikakörnyezet hozható létre a teszteléshez és a hibakereséshez.
- Megkönnyíti a felhőre való átállást, mivel a SharePoint üzemelő példányok a Site Recovery segítségével is áttelepíthetők az Azure-ba.

[További információ](site-recovery-sharepoint.md) a SharePoint vészutáni helyreállításáról.

## <a name="protect-dynamics-ax"></a>A Dynamics AX védelme

Az Azure Site Recovery szolgáltatással az alábbi módokon biztosíthatja a Dynamics AX ERP-megoldás védelmét:

- A teljes Dynamics AX-környezet (webes és AOS-szintek, adatbázisszintek, SharePoint) azure-ba vagy másodlagos helyre való replikációjának kezelése.
- Leegyszerűsíti a Dynamics AX üzemelő példányok felhőbe (Azure-ba) való áttelepítését.
- Leegyszerűsíti a Dynamics AX-alkalmazások fejlesztését és tesztelését, mivel segítségével az éleshez hasonló, igény szerinti másolat hozható létre a teszteléshez és a hibakereséshez.

[További információ](site-recovery-dynamicsax.md) a dinamikus AX vészutáni helyreállításáról.

## <a name="protect-remote-desktop-services"></a>Távoli asztali szolgáltatások védelme

A Távoli asztali szolgáltatások (RDS) lehetővé teszi a virtuális asztali infrastruktúrát (VDI), a munkamenet-alapú asztalokat és az alkalmazásokat, amelyek lehetővé teszik a felhasználók számára, hogy bárhol dolgozzanak.

Az Azure Site Recovery segítségével replikálhatja a következő szolgáltatásokat:

- Felügyelt vagy nem felügyelt készletes virtuális asztalok replikálása egy másodlagos helyre.
- Távoli alkalmazások és munkamenetek replikálása egy másodlagos helyre vagy az Azure-ba.

Az alábbi táblázat a replikációs beállításokat mutatja be:

| **Távoli asztali szolgáltatások** |**Virtuális gépek replikálása az Azure-ba** | **Hyper-V virtuális gépek replikálása másodlagos helyre** | **Hyper-V virtuális gépek replikálása az Azure-ba** | **VMware virtuális gépek replikálása másodlagos helyre** | **VMware virtuális gépek replikálása az Azure-ba** | **Fizikai kiszolgálók replikálása egy másodlagos helyre** | **Fizikai kiszolgálók replikálása az Azure-ba** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Készletbe vont virtuális asztalok (nem felügyelt)** |Nem|Igen |Nem |Igen |Nem |Igen |Nem |
| **Készletbe vont virtuális asztalok (felügyelt, UPD nélkül)** |Nem|Igen |Nem |Igen |Nem |Igen |Nem |
| **Távoli alkalmazások és asztali munkamenetek (UPD nélkül)** |Igen|Igen |Igen |Igen |Igen |Igen |Igen |

[További információ](/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure) az RDS vészutáni helyreállításáról.

## <a name="protect-exchange"></a>Az Exchange védelme

A Site Recovery szolgáltatással az alábbi módokon biztosíthatja az Exchange-környezetek védelmét:

- A kisméretű Exchange-környezetek, például az önálló kiszolgálók esetén a Site Recovery képes az Azure-ba vagy egy másodlagos helyre történő replikálásra és feladatátvitelre.
- Nagyobb méretű környezetek esetén a Site Recovery integrálható az Exchange DAG-kkal.
- Vállalati környezetben az Exchange-vészhelyreállításhoz az Exchange DAG-k használatát javasoljuk.  A Site Recovery helyreállítási tervekbe a DAG-k is bevehetők a helyek közötti DAG feladatátvitelhez.

Ha többet szeretne megtudni az Exchange vész-helyreállítási lehetőségéről, olvassa el az [Exchange DAG-ek](/Exchange/high-availability/database-availability-groups/database-availability-groups) és [az Exchange vész-helyreállítási .](/Exchange/high-availability/disaster-recovery/disaster-recovery)

## <a name="protect-sap"></a>Az SAP védelme

A Site Recovery szolgáltatással az alábbi módokon biztosíthatja az SAP védelmét:

- Lehetővé teszi a helyszínen futó SAP NetWeaver és nem NetWeaver éles alkalmazások védelmét az összetevők az Azure-ba való replikálásával.
- Lehetővé teszi az Azure-t futtató SAP NetWeaver és nem NetWeaver éles alkalmazások védelmét az összetevők egy másik Azure-adatközpontba való replikálásával.
- Megkönnyíti a felhőre való áttelepítést, mivel az SAP üzemelő példányok a Site Recovery segítségével is áttelepíthetők az Azure-ba.
- Leegyszerűsíti az SAP projektek frissítését, tesztelését és prototípuskészítését, mivel a segítségével egy igény szerinti éles klón hozható létre az SAP alkalmazások teszteléséhez.

[További információ](site-recovery-sap.md) az SAP vész-helyreállítási.

## <a name="protect-internet-information-services"></a>Internetes információs szolgáltatások védelme

A Site Recovery segítségével megvédheti az Internet Information Services (IIS) telepítését az alábbiak szerint:

Az Azure Site Recovery úgy gondoskodik a vészhelyreállításról, hogy a környezet kritikus összetevőit replikálja egy offline távoli helyre, vagy egy, a Microsoft Azure-hoz hasonló nyilvános felhőbe. Mivel a webkiszolgálóval és az adatbázissal rendelkező virtuális gépek replikálódnak a helyreállítási helyre, nincs szükség külön biztonsági másolatra a konfigurációs fájlok vagy tanúsítványok számára. A feladatátvétel után módosult környezeti változóktól függő alkalmazásleképezések és -kötések a vészhelyreállítás tervekbe integrált szkriptekkel frissíthetők. A virtuális gépek csak feladatátvétel során kerülnek előadakövetkező helyre a helyreállítási helyen. Az Azure Site Recovery a következő képességekkel is segít a teljes körű feladatátvétel koordinálásában:

- A virtuális gépek leállításának és indításának sorrendbe állítása a különböző szinteken.
- Parancsfájlok hozzáadása az alkalmazásfüggőségek és kötések frissítésekének engedélyezéséhez a virtuális gépeken az indításután. A szkriptek a DNS-kiszolgáló frissítésére is használhatók, hogy a helyreállítási helyre mutasson.
- IP-címeket rendeljen le a virtuális gépekhez a feladatátvétel előtt az elsődleges és helyreállítási hálózatok leképezésével, és használjon olyan parancsfájlokat, amelyeket nem kell frissíteni a feladatátvétel után.
- Több webalkalmazás egykattintásos feladatátvételi lehetősége, amely kiküszöböli a katasztrófa során a zavart.
- Lehetőség a helyreállítási tervek tesztelésére egy DR-részletezések számára elkülönített környezetben.

[További információ](site-recovery-iis.md) az IIS vész-helyreállítási helyzetéről.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>A Citrix XenApp és a XenDesktop védelme

A Site Recovery szolgáltatással az alábbi módokon biztosíthatja az üzemelő Citrix XenApp- és XenDesktop-példány védelmét:

- Engedélyezze a Citrix XenApp és a XenDesktop központi telepítésének védelmét. Replikálja a különböző telepítési rétegeket az Azure-ba: Active Directory, DNS-kiszolgáló, SQL adatbázis-kiszolgáló, Citrix kézbesítési vezérlő, StoreFront-kiszolgáló, XenApp Master (VDA), Citrix XenApp licenckiszolgáló.
- Megkönnyíti a felhőre való áttelepítést, mivel az üzemelő Citrix XenApp- és XenDesktop-példányok a Site Recovery segítségével is áttelepíthetők az Azure-ba.
- Leegyszerűsíti a Citrix XenApp-/XenDesktop-fejlesztést és -tesztelést, mivel segítségével az éleshez hasonló, igény szerinti másolat hozható létre az alkalmazások teszteléséhez és a hibakereséséhez.
- Ez a megoldás csak a Windows Server virtuális asztalokra vonatkozik, az ügyfélvirtuális asztalokra nem. Az ügyfél virtuális asztalai még nem támogatottak az Azure-ban. [Itt részletesen tájékozódhat](https://azure.microsoft.com/pricing/licensing-faq/) az ügyfél/kiszolgáló asztali környezeteinek Azure-ban történő licenceléséről.

[További információ](site-recovery-citrix-xenapp-and-xendesktop.md) a Citrix XenApp és a XenDesktop telepítések vészutáni helyreállításáról. Vagy hivatkozhat a [Citrix tanulmányra](https://aka.ms/citrix-xenapp-xendesktop-with-asr).

## <a name="next-steps"></a>További lépések

[További információ](azure-to-azure-quickstart.md) az Azure-beli virtuális gépek vész-helyreállítási.
