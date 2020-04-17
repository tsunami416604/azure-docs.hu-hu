---
title: Az Azure Migrate bemutatása
description: További információ az Azure Áttelepítés szolgáltatásról.
ms.topic: overview
ms.date: 04/15/2020
ms.custom: mvc
ms.openlocfilehash: aa999b874b6e8b0075ab905a72ff04f03cb39ac7
ms.sourcegitcommit: 31ef5e4d21aa889756fa72b857ca173db727f2c3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81537899"
---
# <a name="about-azure-migrate"></a>Az Azure Migrate bemutatása

Ez a cikk az Azure Migrate szolgáltatás gyors áttekintését tartalmazza.

Az Azure Migrate egy központi központot biztosít a helyszíni kiszolgálók, infrastruktúra, alkalmazások és adatok Azure-ba való értékeléséhez és áttelepítéséhez. Az Azure Migrate a következő szolgáltatásokat nyújtja:

- **Egységes áttelepítési platform:** Egyetlen portál az Azure-ba való migrálási út elindításához, futtatásához és nyomon követéséhez.
- **Eszközök köre**: Az értékeléshez és a migrációhoz szükséges eszközök széles választéka. Az eszközök közé tartozik az Azure Migrate: Server Assessment és az Azure Migrate: Server Migration. Az Azure Migrate integrálható más Azure-szolgáltatásokkal, valamint más eszközökkel és független szoftverszállítói (ISV) ajánlatokkal.
- **Értékelés és áttelepítés:** Az Azure Áttelepítési központban felmérheti és áttelepítheti:
    - **Kiszolgálók:** A helyszíni kiszolgálók felmérése és áttelepítése az Azure virtuális gépekre.
    - **Adatbázisok:** Értékelje és telepítse át a helyszíni adatbázisokat az Azure SQL DB-be vagy az Azure SQL felügyelt példányba.
    - **Webalkalmazások:** Értékelje és telepítse át a helyszíni webalkalmazásokat az Azure App Service szolgáltatásba az Azure App Service Assistant használatával.
    - **Virtuális asztalok:** Értékelje és telepítse át a helyszíni virtuális asztali infrastruktúrát (VDI) az Azure-beli Windows virtuális asztalra.
    - **Adatok:** Az Azure Data Box-termékek használatával gyorsan és költséghatékonyan migrálnagy mennyiségű adatot az Azure-ba. 


## <a name="integrated-tools"></a>Integrált eszközök

Az Azure Áttelepítési központ ezeket az eszközöket tartalmazza.

**Eszköz** | **Felmérés/áttelepítés** | **Részletek**
--- | --- | ---
**Azure Áttelepítés:Kiszolgálófelmérés** | Értékelje a kiszolgálókat. | Fedezze fel és értékelje a helyszíni VMware virtuális gépeket, hyper-v-virtuális gépeket és fizikai kiszolgálókat az Azure-ba való migrálásra való felkészülés során.
**Azure áttelepítése:Kiszolgáló áttelepítése** | Kiszolgálók áttelepítése. | Telepítse a VMware virtuális gépeket, a Hyper-V virtuális gépeket, a fizikai kiszolgálókat, más virtualizált gépeket és a nyilvános felhőbeli virtuális gépeket az Azure-ba. 
**Adatbázis-áttelepítési segéd (DMA)** | Felmérheti a helyszíni SQL Server-adatbázisokat az Azure SQL DB-re, az Azure SQL felügyelt példányra vagy az SQL Servert futtató Azure virtuális gépekre való áttelepítéshez. | A DMA segít az áttelepítés lehetséges blokkolási problémáinak azonosításában. Azonosítja a nem támogatott szolgáltatásokat, az áttelepítés után hasznosnak jogosult új szolgáltatásokat, és segít azonosítani az adatbázis-áttelepítés helyes útvonalát. [További információ](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**Adatbázis-áttelepítési szolgáltatás (DMS)** | Telepítse át a helyszíni adatbázisokat az SQL, Az Azure SQL DB és az Azure SQL felügyelt példányokat futtató Azure virtuális gépekre. | [További információ](https://docs.microsoft.com/azure/dms/dms-overview) a DMS-ről.
**Movere** | Értékelje a kiszolgálókat. | [További információ](#movere) a Movere-ról.
**Webalkalmazás áttelepítési segédje** | Értékelje és migrálja a helyszíni webalkalmazásokat az Azure-ba. |  Az Azure App Service-áttelepítési segéd segítségével felmérheti a helyszíni webhelyeket az Azure App Service-be való áttelepítéshez.<br/><br/> Az Asszisztens segítségével .NET és PHP webalkalmazásokat telepítheti át az Azure-ba. [További információ](https://appmigration.microsoft.com/) az Azure App Service-áttelepítési segédről.
**Azure Data Box** | Kapcsolat nélküli adatáttelepítés. | Azure Data Box-termékekkel nagy mennyiségű adatot helyezhet át offline állapotba az Azure-ba. [További információ](https://docs.microsoft.com/azure/databox/).

> [!NOTE]
> Ha az Azure Government szolgáltatásban van, a külső integrált eszközök és az isv-ajánlatok nem küldhetnek adatokat az Azure Migrate-projekteknek. Az eszközöket egymástól függetlenül is használhatja.

## <a name="isv-integration"></a>IsV integráció

Az Azure Migrate számos isv-ajánlattal integrálható. 

**Isv**    | **Szolgáltatás**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Kiszolgálók áttelepítése
[Cloudamize](https://www.cloudamize.com/platform) | Kiszolgálók felmérése
[Corent Technology](https://www.corenttech.com/AzureMigrate/) | Kiszolgálók felmérése és áttelepítése
[Eszköz 42](https://docs.device42.com/) | Kiszolgálók felmérése
[Tóparti](https://go.microsoft.com/fwlink/?linkid=2104908) | Értékelje a VDI-t
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Kiszolgálók áttelepítése
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Kiszolgálók felmérése
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Kiszolgálók és adatbázisok felmérése


## <a name="azure-migrate-server-assessment-tool"></a>Az Azure Áttelepítési kiszolgáló értékelése eszköz

Az Azure Migrate:Server Assessment eszköz felderíti és felméri a helyszíni VMware virtuális gépeket, a Hyper-V VM-eket és a fizikai kiszolgálókat az Azure-ba való migráláshoz. Az eszköz a következőket teszi:

- **Az Azure-ra való felkészültség:** Felméri, hogy a helyszíni gépek készen állnak-e az Azure-ba való áttelepítésre.
- **Az Azure méretezése:** Az Azure-beli virtuális gépek mérete az áttelepítés után.
- **Az Azure költségbecslése:** A helyszíni kiszolgálók Azure-beli futtatásának becsült költségei.
- **Függőségi elemzés:** Ha a kiszolgálói felmérést [függőségelemzéssel](concepts-dependency-visualization.md)használja, hatékonyan azonosíthatja a kiszolgálók közötti függőségeket, és optimalizálhatja a függő kiszolgálók Azure-ba való áthelyezésére vonatkozó stratégiákat.


A Server Assessment egy könnyű [Azure Migrate-berendezést](migrate-appliance.md) használ, amelyet a helyszínen telepít.

- A készülék virtuális gépen vagy fizikai kiszolgálón fut. Könnyen telepítheti egy letöltött sablon használatával.
- A készülék felderíti a helyszíni gépeket, és folyamatosan elküldi a gép metaadatait és teljesítményadatait az Azure Migrate szolgáltatásnak.
- A készülék felderítése ügynök nélküli. Semmi sincs telepítve a felfedezett gépeken.
- A készülék felderítése után csoportokba gyűjti a felderített gépeket, és egy csoportra vonatkozó értékeléseket futtat.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate Server Migration eszköz

Az Azure Migrate:Server Migration eszköz segítségével áttelepítheti a helyszíni VMware virtuális gépeket, a Hyper-V virtuális gépeket, a fizikai kiszolgálókat, más virtualizált gépeket és a nyilvános felhőbeli virtuális gépeket az Azure-ba. A gépek et a felmérés után, vagy áttelepíteni őket értékelés nélkül. 

A VMware virtuális gépek ügynök nélküli áttelepítése és a Hyper-V virtuális gépek áttelepítése esetén a Kiszolgálóáttelepítés egy Azure Migrate-berendezést használ, amelyet a helyszínen telepít. A készülék akkor is használatos, ha beállítja a kiszolgáló felmérését, és azt az előző szakasz ismerteti.


## <a name="selecting-assessmentmigration-tools"></a>Értékelési/áttelepítési eszközök kiválasztása

Az Azure Migrate hubban kiválaszthatja az értékeléshez vagy áttelepítéshez használni kívánt eszközt, és hozzáadhatja egy Azure Migrate projekthez. Ha független eszközegyesítő eszközt vagy Movere eszközt ad hozzá:

- A kezdéshez szerezzen licencet, vagy regisztráljon egy ingyenes próbaverzióra, az eszköz utasításainak megfelelően. Az eszközök licencelését a független eszköz és az eszköz határozza meg. 
- Az egyes eszközökben van egy lehetőség az Azure Migrate való csatlakozáshoz. A csatlakozáshoz kövesse az eszköz utasításait.
- Az azure-áttelepítési projekten belül, az összes eszközön nyomon követheti az áttelepítési útvonalat.


## <a name="movere"></a>Movere

A Movere egy SaaS platform, amely egy nap alatt teljes informatikai környezetek pontos bemutatásával növeli az üzleti intelligenciát. A szervezetek növekedésével, módosításával és digitális optimalizálásával a megoldás biztosítja a vállalatok számára azt a bizalmat, amely a környezetük láthatóságához és vezérléséhez szükséges, platformtól, alkalmazástól vagy földrajzi elhelyezkedéstől függetlenül. A Movere-t a Microsoft [vásárolta meg,](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) és már nem értékesítik önálló ajánlatként.  A Movere a Microsoft Solution Assessment and Cloud Economics Programs programon keresztül érhető el. [További információ](https://www.movere.io) a Movere-ról. 

Javasoljuk, hogy tekintse meg az Azure Migrate beépített áttelepítési szolgáltatásunkat is. Az Azure Migrate egy központi központot biztosít a felhőbe való migrálás egyszerűsítéséhez. A hub átfogó támogatást nyújt a különböző számítási feladatokhoz, beleértve a fizikai és virtuális kiszolgálókat, adatbázisokat és alkalmazásokat. A teljes körű láthatóság megkönnyíti az előrehaladás nyomon követését a felderítés, az értékelés és az áttelepítés során. Az Azure és a beépített partner független hálózatfizető eszközökkel az Azure Migrate számos szolgáltatással is rendelkezik, beleértve a virtuális és fizikai kiszolgálófelderítést, a teljesítményalapú jobb méretezést, a költségtervezést, az importálásalapú értékeléseket és az ügynök nélküli alkalmazásfüggőség-elemzést. Ha az első lépésekhez szakértői segítségre van szüksége, a Microsoft képzett [Azure Expert Managed Service Provider-t](https://azure.microsoft.com/partners) használ, hogy végigvezesse Önt az utazás során. Tekintse meg az [Azure Migrate webhelyet.](https://azure.microsoft.com/services/azure-migrate/) 
 

## <a name="azure-migrate-versions"></a>Az Azure Áttelepítés verziói

Az Azure Migrate szolgáltatásnak két verziója van:

- **Jelenlegi verzió:** Ezzel a verzióval azure-áttelepítési projekteket hozhat létre, helyszíni gépeket fedezhet fel, és vezényelhet értékeléseket és áttelepítéseket. [További információ](whats-new.md) a jelen verzió újdonságairól.
- **Előző verzió:** Ha az Azure Migrate előző verzióját használta (csak a helyszíni VMware virtuális gépek felmérése támogatott), most használja az aktuális verziót. Már nem hozhat létre Azure Migrate projekteket az előző verzióval, és azt javasoljuk, hogy ne hajtson végre új felderítéseket. A meglévő projektek eléréséhez az Azure Portalon keresse meg és válassza az **Azure Áttelepítése**lehetőséget. Az **Azure Migrate** irányítópulton van egy értesítés és egy hivatkozás a régi Azure Migrate-projektek eléréséhez.



## <a name="next-steps"></a>További lépések

- Próbálja ki oktatóanyagainkat a [VMware VM-ek,](tutorial-prepare-vmware.md) [hyper-v vm-ek](tutorial-prepare-hyper-v.md)vagy [fizikai kiszolgálók értékeléséhez.](tutorial-prepare-physical.md)
- Tekintse meg az Azure Migrate használatával kapcsolatban felmerülő [gyakori kérdéseket](resources-faq.md).
