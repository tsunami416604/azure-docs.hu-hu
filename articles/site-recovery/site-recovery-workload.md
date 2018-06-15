---
title: Milyen számítási feladatokat tud védeni az Azure Site Recovery? | Microsoft Docs
description: Azokat a számítási feladatokat ismerteti, amelyeket meg lehet védeni a vészhelyreállítás Azure Site Recovery szolgáltatással történő használatával.
services: site-recovery
documentationcenter: ''
author: rayne-wiselman
manager: cfreeman
editor: ''
ms.assetid: 4953948f-26c0-4699-8fe7-59d3bfc1d3da
ms.service: site-recovery
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: storage-backup-recovery
ms.date: 05/02/2018
ms.author: raynew
ms.openlocfilehash: 4d6d3b776995233f3729a76788504678dc8979d7
ms.sourcegitcommit: 870d372785ffa8ca46346f4dfe215f245931dae1
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/08/2018
ms.locfileid: "33894068"
---
# <a name="what-workloads-can-you-protect-with-azure-site-recovery"></a>Milyen számítási feladatokat tud védeni az Azure Site Recovery?

Ez a cikk az [Azure Site Recovery](site-recovery-overview.md) szolgáltatással replikálható számítási feladatokat és alkalmazásokat írja le.



## <a name="overview"></a>Áttekintés

A szervezeteknek szükségük van egy üzletmenet-folytonossági és vészhelyreállítási (BCDR) stratégiára, amely meghatározza, hogy hogyan tudnak a számítási feladatok és az adatok biztonságban és üzemben maradni a tervezett és nem tervezett leállások során, illetve hogy miként lehet a lehető leggyorsabban visszaállni a normál működésre.

Az Azure Site Recovery segít a BCDR-stratégia kidolgozásában. A Site Recovery használatával alkalmazásbarát replikációt végezhet a felhőbe vagy egy másodlagos helyre. Függetlenül attól, hogy alkalmazásai Windows- vagy Linux-alapúak, illetve hogy fizikai, VMware- vagy Hyper-V-kiszolgálón futnak, a Site Recovery koordinálja a replikációt, elvégzi a vészhelyreállítás tesztelését, valamint feladatátvételt és -visszavételt hajt végre.

A Site Recovery számos Microsoft-alkalmazással (például SharePoint, Exchange, Dynamics, SQL Server és Active Directory) együttműködik. A Microsoft továbbá szorosan együttműködik az olyan vezető szállítókkal, mint az Oracle, a SAP vagy a Red Hat. A replikációs megoldásokat alkalmazásonként szabhatja testre.

## <a name="why-use-site-recovery-for-application-replication"></a>Miért előnyös a Site Recovery használata az alkalmazásreplikációhoz?

A Site Recovery az alábbi módokon járul hozzá az alkalmazásszintű védelemhez és helyreállításhoz:

* Alkalmazásfüggetlen, így egy támogatott gépen futó bármilyen számítási feladatok replikációját biztosítja.
* Közel szinkron replikációt biztosít, amelynek esetében a helyreállítási időkorlát mindössze 30 másodperc is lehet, így megfelel a legtöbb kritikus fontosságú üzleti alkalmazás igényeinek.
* Alkalmazáskonzisztens pillanatképeket rögzít egy- vagy többszintű alkalmazásokról.
* Együttműködik az SQL Server AlwaysOn szolgáltatással, és számos más alkalmazásszintű replikációs technológiát is képes felhasználni (például AD-replikáció, SQL AlwaysOn, Exchange adatbázis-elérhetőségi csoportok (DAG) és Oracle Data Guard).
* Külső szkripteket és manuális műveleteket is tartalmazó, rugalmas helyreállítási tervekkel rendelkezik, amelyekkel egész alkalmazáscsoportokat állíthat helyre egyetlen kattintással.
* A Site Recovery és az Azure fejlett hálózatkezelési funkciói leegyszerűsítik az alkalmazáshálózati követelményeket, ideértve az IP-címek lefoglalását, a terheléselosztók konfigurálását, valamint az Azure Traffic Manager integrációját, ami alacsony helyreállítási időre vonatkozó célkitűzéssel rendelkező hálózatváltást garantál.
* A szolgáltatás gazdag, éles használatra kész és alkalmazásspecifikus parancsprogramokat tartalmazó automatizációs kódtárat tartalmaz, amely letölthető, és beépíthető a helyreállítási tervekbe.

## <a name="workload-summary"></a>A számítási feladatok összefoglalása
A Site Recovery a támogatott gépeken futó bármilyen alkalmazást képes replikálni. Ezenfelül a termékekért felelős csoportokkal együttműködésben további, alkalmazásspecifikus teszteket is végrehajtottunk.

| **Számítási feladat** |**Virtuális gépek replikálása az Azure-ba** |**Hyper-V virtuális gépek replikálása másodlagos helyre** | **Hyper-V virtuális gépek replikálása az Azure-ba** | **VMware virtuális gépek replikálása másodlagos helyre** | **VMware virtuális gépek replikálása az Azure-ba** |
| --- | --- | --- | --- | --- |---|
| Active Directory, DNS |I |I |I |I |I|
| Webalkalmazások (IIS, SQL) |I |I |I |I |I|
| System Center Operations Manager |I |I |I |I |I|
| SharePoint |I |I |I |I |I|
| SAP<br/><br/>Replikálás SAP-helyről az Azure-ba nem fürtözött rendszer esetén |I (a Microsoft által végzett tesztek alapján) |I (a Microsoft által végzett tesztek alapján) |I (a Microsoft által végzett tesztek alapján) |I (a Microsoft által végzett tesztek alapján) |I (a Microsoft által végzett tesztek alapján)|
| Exchange (nem DAG) |I |I |I |I |I|
| Távoli asztal/VDI |I |I |I |I |I|
| Linux (operációs rendszer és alkalmazások) |I (a Microsoft által végzett tesztek alapján) |I (a Microsoft által végzett tesztek alapján) |I (a Microsoft által végzett tesztek alapján) |I (a Microsoft által végzett tesztek alapján) |I (a Microsoft által végzett tesztek alapján)|
| Dynamics AX |I |I |I |I |I|
| Oracle |I (a Microsoft által végzett tesztek alapján) |I (a Microsoft által végzett tesztek alapján) |I (a Microsoft által végzett tesztek alapján) |I (a Microsoft által végzett tesztek alapján) |I (a Microsoft által végzett tesztek alapján)|
| Windows fájlkiszolgáló |I |I |I |I |I|
| Citrix XenApp és XenDesktop |I|N/A |I |N/A |I |

## <a name="replicate-active-directory-and-dns"></a>Active Directory és DNS replikálása
A legtöbb vállalati alkalmazás számára elengedhetetlen az Active Directory- és DNS-infrastruktúra használata. A vészhelyreállítás során a számítási feladatok és az alkalmazások helyreállítása előtt ezeket az infrastruktúra-összetevőket meg kell védenie és helyre kell állítania.

A Site Recovery segítséglével teljesen automatizált vészhelyreállítási tervet hozhat létre az Active Directoryhoz és a DNS-hez. Ha például egy elsődleges helyről szeretné egy másodlagos helyre átadni a SharePointot és az SAP-t, akkor beállíthat egy olyan helyreállítási tervet, amely először az Active Directoryt adja át, majd beállíthat egy másik, alkalmazásspecifikus helyreállítási tervet is, amely az Active Directoryra támaszkodó egyéb alkalmazásokat adja át.

[Itt részletesebben tájékozódhat](site-recovery-active-directory.md) az Active Directory és a DNS védelméről.

## <a name="protect-sql-server"></a>Az SQL Server védelme
Az SQL Server adatszolgáltatási alapot nyújt az üzleti alkalmazások helyi adatközpontban működő adatszolgáltatásai számára.  A Site Recovery és az SQL Server HA/DR technológiák együtt is használhatók az SQL Servert használó többszintű vállalati alkalmazások védelmére. A Site Recovery a következőket biztosítja:

* Egyszerű és költséghatékony vészhelyreállítási megoldás az SQL Serverhez. Az SQL Server önálló kiszolgálói és fürtjei több verziójának és kiadásának replikálása az Azure-ba vagy egy másodlagos helyre.  
* Integrálható SQL AlwaysOn rendelkezésre állási csoportokkal, az Azure Site Recovery helyreállítási tervek feladatátvételének és feladat-visszavételének kezeléséhez.
* Végpontok közötti helyreállítási tervek az alkalmazás összes rétege számára, ideértve az SQL Server-adatbázisokat is.
* Magas terhelésekhez méretezhető SQL Server Site Recovery-vel az Azure nagyobb méretű IaaS virtuális gépekkel történő teljesítménynöveléssel.
* Az SQL Server vészhelyreállítási funkcióinak egyszerű tesztelése. Feladatátvételi tesztek segítségével elemezheti az adatokat és megfelelőségi ellenőrzést futtathat az éles környezet befolyásolása nélkül.

[Itt részletesebben tájékozódhat](site-recovery-sql.md) az SQL Server védelméről.

## <a name="protect-sharepoint"></a>A SharePoint védelme
Az Azure Site Recovery szolgáltatással az alábbi módokon biztosíthatja a SharePoint-környezetek védelmét:

* Használata esetén nem szükséges költséges készenléti kiszolgálófarmot kiépítenie a vészhelyreállításhoz. A Site Recovery segítségével a farm egésze (a webes, az alkalmazás- és az adatbázisréteg) replikálható az Azure-ba vagy egy másodlagos helyre.
* Leegyszerűsíti az alkalmazások üzembe helyezését és felügyeletét. A rendszer automatikusan replikálja az elsődleges helyekre telepített frissítéseket, így azok a farm feladatátvételét és helyreállítását követően azonnal elérhetőek lesznek a másodlagos helyen. Csökkenti a felügyelet összetettségét, valamint a készenléti farm naprakészen tartásával kapcsolatos költségeket.
* Leegyszerűsíti a SharePoint-alkalmazások fejlesztését és tesztelését, mivel segítségével az éleshez hasonló, igény szerinti replikakörnyezet hozható létre a teszteléshez és a hibakereséshez.
* Megkönnyíti a felhőre való átállást, mivel a SharePoint üzemelő példányok a Site Recovery segítségével is áttelepíthetők az Azure-ba.

[Itt részletesebben olvashat](site-recovery-sharepoint.md) a SharePoint védelméről.

## <a name="protect-dynamics-ax"></a>A Dynamics AX védelme
Az Azure Site Recovery szolgáltatással az alábbi módokon biztosíthatja a Dynamics AX ERP-megoldás védelmét:

* Replikálja a teljes Dynamics AX-környezetet (webes és AOS-szintek, adatbázis szintek, SharePoint) az Azure-ba, vagy egy másodlagos helyre.
* Leegyszerűsíti a Dynamics AX üzemelő példányok felhőbe (Azure-ba) való áttelepítését.
* Leegyszerűsíti a Dynamics AX-alkalmazások fejlesztését és tesztelését, mivel segítségével az éleshez hasonló, igény szerinti másolat hozható létre a teszteléshez és a hibakereséshez.

[Itt részletesebben tájékozódhat](site-recovery-dynamicsax.md) a Dynamic AX védelméről.

## <a name="protect-rds"></a>A távoli asztali szolgáltatások védelme
A távoli asztali szolgáltatások (RDS) lehetővé teszik a virtuális asztali infrastruktúra (VDI), illetve a munkamenet-alapú asztal és alkalmazások használatát, így a felhasználók bárhonnan dolgozhatnak. Az Azure Site Recovery segítségével:

* Felügyelt vagy nem felügyelt, készletbe vont virtuális asztalait másodlagos helyre, távoli alkalmazásait és munkameneteit pedig másodlagos helyre vagy az Azure-ba replikálhatja.

* A következőket replikálhatja:

| **RDS** |**Virtuális gépek replikálása az Azure-ba** | **Hyper-V virtuális gépek replikálása másodlagos helyre** | **Hyper-V virtuális gépek replikálása az Azure-ba** | **VMware virtuális gépek replikálása másodlagos helyre** | **VMware virtuális gépek replikálása az Azure-ba** | **Fizikai kiszolgálók replikálása másodlagos helyre** | **Fizikai kiszolgálók replikálása az Azure-ba** |
|---| --- | --- | --- | --- | --- | --- | --- |
| **Készletezett virtuális asztal (nem kezelt)** |Nem|Igen |Nem |Igen |Nem |Igen |Nem |
| **Készletezett virtuális asztal (kezelt, UPD nélküli)** |Nem|Igen |Nem |Igen |Nem |Igen |Nem |
| **Távoli alkalmazások és asztali munkamenetek (UPD nélkül)** |Igen|Igen |Igen |Igen |Igen |Igen |Igen |

[Vészhelyreállítás beállítása a távoli asztali szolgáltatásokhoz (RDS) az Azure Site Recovery használatával](https://docs.microsoft.com/windows-server/remote/remote-desktop-services/rds-disaster-recovery-with-azure).

[Itt részletes tájékoztatást olvashat](https://gallery.technet.microsoft.com/Remote-Desktop-DR-Solution-bdf6ddcb) az RDS védelméről.

## <a name="protect-exchange"></a>Az Exchange védelme
A Site Recovery szolgáltatással az alábbi módokon biztosíthatja az Exchange-környezetek védelmét:

* A kisméretű Exchange-környezetek, például az önálló kiszolgálók esetén a Site Recovery képes az Azure-ba vagy egy másodlagos helyre történő replikálásra és feladatátvitelre.
* Nagyobb méretű környezetek esetén a Site Recovery integrálható az Exchange DAG-kkal.
* Vállalati környezetben az Exchange-vészhelyreállításhoz az Exchange DAG-k használatát javasoljuk.  A Site Recovery helyreállítási tervekbe a DAG-k is bevehetők a helyek közötti DAG feladatátvitelhez.

[További információk](https://gallery.technet.microsoft.com/Exchange-DR-Solution-using-11a7dcb6) az Exchange védelméről.

## <a name="protect-sap"></a>Az SAP védelme
A Site Recovery szolgáltatással az alábbi módokon biztosíthatja az SAP védelmét:

* Lehetővé teszi a helyszínen futó SAP NetWeaver és nem NetWeaver éles alkalmazások védelmét az összetevők az Azure-ba való replikálásával.
* Lehetővé teszi az Azure-t futtató SAP NetWeaver és nem NetWeaver éles alkalmazások védelmét az összetevők egy másik Azure-adatközpontba való replikálásával.
* Megkönnyíti a felhőre való áttelepítést, mivel az SAP üzemelő példányok a Site Recovery segítségével is áttelepíthetők az Azure-ba.
* Leegyszerűsíti az SAP projektek frissítését, tesztelését és prototípuskészítését, mivel a segítségével egy igény szerinti éles klón hozható létre az SAP alkalmazások teszteléséhez.

[Itt részletesen tájékozódhat](site-recovery-sap.md) az SAP védelméről.

## <a name="protect-iis"></a>Az IIS védelme
A Site Recovery szolgáltatással az alábbi módokon biztosíthatja az üzemelő IIS-példány védelmét:

Az Azure Site Recovery úgy gondoskodik a vészhelyreállításról, hogy a környezet kritikus összetevőit replikálja egy offline távoli helyre, vagy egy, a Microsoft Azure-hoz hasonló nyilvános felhőbe. Mivel a rendszer a webkiszolgálóval és az adatbázissal együtt replikálja a virtuális gépeket a helyreállítási helyen, nincs szükség a konfigurációs fájlok vagy tanúsítványok külön biztonsági mentésére. A feladatátvétel után módosult környezeti változóktól függő alkalmazásleképezések és -kötések a vészhelyreállítás tervekbe integrált szkriptekkel frissíthetők. A virtuális gépek csak feladatátvétel esetén kerülnek a helyreállítási helyre. Az Azure Site Recovery a végpontok közötti feladatátvétel összehangolását is elősegíti az alábbi képességek biztosításával:

-   A virtuális gépek leállításának és indításának sorrendbe állítása a különböző szinteken.
-   Szkriptek hozzáadása az alkalmazásfüggőségek és -kötések frissítésének biztosításához a virtuális gépek elindítása után. A szkriptek a DNS-kiszolgáló frissítésére is használhatók, hogy a helyreállítási helyre mutasson.
-   IP-címek lefoglalása a virtuális gépekhez a feladatátvétel előtt az elsődleges és a helyreállítási hálózat leképezésével, és olyan szkriptek használata, amelyeket feladatátvétel után nem kell frissíteni.
-   Egykattintásos feladatátvétel lehetővé tétele a webkiszolgálók több webalkalmazása számára. Ezzel áttekinthetővé tehetők a vészhelyzet esetén végrehajtandó műveletek.
-   Lehetőség a helyreállítási tervek tesztelésére egy DR-részletezések számára elkülönített környezetben.

[Itt részletesen tájékozódhat](https://aka.ms/asr-iis) az IIS webfarm védelméről.

## <a name="protect-citrix-xenapp-and-xendesktop"></a>A Citrix XenApp és a XenDesktop védelme
A Site Recovery szolgáltatással az alábbi módokon biztosíthatja az üzemelő Citrix XenApp- és XenDesktop-példány védelmét:

* Az üzemelő Citrix XenApp- és XenDesktop-példányok védelmének engedélyezése a különféle környezeti szintek (például AD DNS-kiszolgáló, SQL Database-kiszolgáló, Citrix kézbesítési vezérlő, StoreFront-kiszolgáló, XenApp Master (VDA), Citrix XenApp licenckiszolgáló) az Azure-ba való replikálásával történik.
* Megkönnyíti a felhőre való áttelepítést, mivel az üzemelő Citrix XenApp- és XenDesktop-példányok a Site Recovery segítségével is áttelepíthetők az Azure-ba.
* Leegyszerűsíti a Citrix XenApp-/XenDesktop-fejlesztést és -tesztelést, mivel segítségével az éleshez hasonló, igény szerinti másolat hozható létre az alkalmazások teszteléséhez és a hibakereséséhez.
* Ez a megoldás kizárólag a Windows Server operációs rendszer virtuális asztali környezeteire alkalmazható, az ügyfelek virtuális asztali környezetei esetében nem, mivel azok licencelése az Azure-ban még nem támogatott.
[Itt részletesen tájékozódhat](https://azure.microsoft.com/pricing/licensing-faq/) az ügyfél/kiszolgáló asztali környezeteinek Azure-ban történő licenceléséről.

[Itt részletesen tájékozódhat](site-recovery-citrix-xenapp-and-xendesktop.md) az üzemelő Citrix XenApp- és XenDesktop-példányok védelméről. Másik lehetőségként megtekintheti [a Citrix tanulmányát](https://aka.ms/citrix-xenapp-xendesktop-with-asr), amely ugyanezt részletezi.

## <a name="next-steps"></a>További lépések

[Első lépések:](azure-to-azure-quickstart.md) Azure-beli virtuális gép replikálása.
