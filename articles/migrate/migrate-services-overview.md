---
title: Az Azure Migrate bemutatása
description: Ismerkedjen meg a Azure Migrate szolgáltatással.
ms.topic: overview
ms.date: 12/29/2019
ms.custom: mvc
ms.openlocfilehash: c336b2f95a17896edcc7eaad2b2586c6859804f9
ms.sourcegitcommit: 7b25c9981b52c385af77feb022825c1be6ff55bf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/13/2020
ms.locfileid: "79240127"
---
# <a name="about-azure-migrate"></a>Az Azure Migrate bemutatása

Ez a cikk a Azure Migrate szolgáltatás gyors áttekintését tartalmazza.

A Azure Migrate használatával migrálhat az Azure-ba. A Azure Migrate központosított központot biztosít a helyszíni infrastruktúrák, alkalmazások és az Azure-ba irányuló adatkezeléshez, valamint a következő funkciókkal:

- **Egységes áttelepítési platform**: egyetlen portál, amellyel elindíthatja, futtathatja és nyomon követheti az Azure-ba való áttelepítést.
- **Eszközök köre**: számos eszköz az értékeléshez és az áttelepítéshez. A hub Azure Migratet tartalmaz: kiszolgáló-Értékelés és Azure Migrate: kiszolgáló áttelepítése. Integrálható más Azure-szolgáltatásokkal, valamint más eszközökkel és független szoftvergyártói (ISV) ajánlatokkal.
- **Értékelés és áttelepítés**: az Azure Migrate központban értékelheti és áttelepítheti a következőket:
    - **Kiszolgálók**: helyszíni kiszolgálók felmérése és migrálása az Azure-beli virtuális gépekre.
    - **Adatbázisok**: helyszíni adatbázisok felmérése és migrálása az Azure SQL db-be vagy az Azure SQL felügyelt példányaira.
    - **Webalkalmazások**: a Azure app Service Assistant használatával felméri és áttelepítheti a helyszíni webalkalmazásokat Azure app Serviceba.
    - **Virtuális asztalok**: a helyszíni virtuális asztali infrastruktúra (VDI) felmérése és migrálása az Azure-beli Windows rendszerű virtuális asztali környezetbe.
    - **Adatforgalom**: nagy mennyiségű, gyorsan és költséghatékonyan telepíthető Azure-beli adatforgalom a Azure Data Box termékek használatával. 


## <a name="integrated-tools"></a>Integrált eszközök

Az Azure Migrate hub a következő eszközöket biztosítja.

**Eszköz** | **Értékelés/Migrálás** | **Részletek**
--- | --- | ---
**Azure Migrate: kiszolgáló értékelése** | Kiszolgálók értékelése | A helyszíni VMware virtuális gépek, Hyper-V virtuális gépek és fizikai kiszolgálók felderítése és kiértékelése annak megállapításához, hogy készen állnak-e az Azure-ba való áttelepítésre.
**Azure Migrate: kiszolgáló áttelepítése** | Kiszolgálók migrálása | A VMware virtuális gépek, a Hyper-V virtuális gépek, a fizikai kiszolgálók, valamint az egyéb virtualizált gépek és a nyilvános felhőalapú virtuális gépek migrálása az Azure-ba. 
**Adatbázis-Migration Assistant (DMA)** | A helyszíni SQL Server adatbázisok kiértékelésével áttelepíthetők az Azure SQL DB-be, az Azure SQL felügyelt példányaira vagy SQL Server rendszert futtató Azure-beli virtuális gépekre. | A DMA információt nyújt az áttelepítéssel kapcsolatos lehetséges blokkolási problémákról. Azonosítja a nem támogatott funkciókat, valamint az áttelepítés után kihasználható új funkciókat, és segít az adatbázis-áttelepítés megfelelő elérési útjának azonosításában. [További információk](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).
**Database Migration Service (DMS)** | Helyszíni adatbázisok migrálása az SQL, az Azure SQL DB és az Azure SQL felügyelt példányain futó Azure virtuális gépekre. | [További](https://docs.microsoft.com/azure/dms/dms-overview) információ a DMS-ről.
**Movere** | Kiszolgálók értékelése | [További](#movere) információ a mozgató szolgáltatásról.
**Webalkalmazás-Migration Assistant** | Helyszíni webalkalmazások felmérése és migrálása az Azure-ba. |  A Azure App Service Migration Assistant segítségével kivizsgálhatja a helyszíni webhelyeket Azure App Servicere való áttelepítéshez.<br/><br/> Telepítse át a .NET-és PHP-webalkalmazásokat az Azure-ba Azure App Service Migration Assistant használatával. [További](https://appmigration.microsoft.com/) információ a segédről.
**Azure Data Box** | Offline adatáttelepítés. | A Azure Data Box termékek használatával az Azure-ba irányuló nagy mennyiségű adattal is át lehet helyezni. [További információk](https://docs.microsoft.com/azure/databox/).

## <a name="isv-integration"></a>ISV-integráció

Azure Migrate számos ISV-ajánlattal integrálható. 

**ISV** | **Funkció**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Kiszolgálók migrálása
[Cloudamize](https://www.cloudamize.com/platform) | Kiszolgálók értékelése
[A kölcsönzés technológiája](https://www.corenttech.com/AzureMigrate/) | Kiszolgálók értékelése és migrálása
[Eszköz 42](https://docs.device42.com/) | Kiszolgálók értékelése
[Lakeside](https://go.microsoft.com/fwlink/?linkid=2104908) | VDI értékelése
[RackWare](https://go.microsoft.com/fwlink/?linkid=2102735) | Kiszolgálók migrálása
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Kiszolgálók értékelése
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Kiszolgálók és adatbázisok értékelése


## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate: kiszolgáló-Assessment eszköz

A Azure Migrate: a kiszolgáló-értékelő eszköz felkeresi és kiértékeli a helyszíni VMware virtuális gépeket, a Hyper-V virtuális gépeket és a fizikai kiszolgálókat az Azure-ba való Migrálás során. Ez segít a következők azonosításában:

- **Azure-készültség:** Vizsgálja meg, hogy a helyszíni gépek készen állnak-e az Azure-ba való áttelepítésre.
- Az **Azure méretezése:** Az Azure-beli virtuális gépek becsült mérete az áttelepítés után.
- **Azure-költségbecslés:** A helyszíni kiszolgálók Azure-ban való futtatásának becsült költségei.
- **Függőségi vizualizáció:** Ha a kiszolgálói értékelést a függőségi vizualizációval használja, hatékonyan azonosíthatja a kiszolgálók közötti függőségeket, valamint a függő kiszolgálók Azure-ba való áthelyezésének optimális módjait.

A kiszolgáló értékelése egy, a helyszínen üzembe helyezett, egyszerűsített berendezést használ, és regisztrálja magát a kiszolgáló értékelésével.

- A készülék egy fizikai kiszolgálón vagy virtuális gépen fut, és könnyen telepíthető egy letöltött sablonnal.
- A készülék felfedi a helyszíni gépeket, és folyamatosan küldi a gépek metaadatait és teljesítményadatait a Azure Migrate.
- A készülék felderítése ügynök nélkül történik. Semmit nem kell telepíteni a felderített gépekre.
- A felderítést követően a felderített gépeket csoportokba gyűjti, és felméri a csoportok áttelepítését.


## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate: kiszolgáló áttelepítési eszköze

A Azure Migrate: a kiszolgálói áttelepítési eszköz segítségével helyszíni VMware virtuális gépeket, Hyper-V virtuális gépeket, fizikai kiszolgálókat, más virtualizált gépeket és nyilvános Felhőbeli virtuális gépeket telepíthet át az Azure-ba. A gépeket a kiértékelése után telepítheti át, vagy áttelepítheti őket értékelés nélkül.


## <a name="select-a-tool"></a>Eszköz kiválasztása

Az Azure Migrate központban kiválaszthatja az értékeléshez használni kívánt eszközt, és hozzáadhatja egy Azure Migrate projekthez. Ha ISV-eszközt vagy Mozgatót ad hozzá:

- Első lépésként szerezze be a licencet, vagy regisztráljon az ingyenes próbaverzióra az eszköz utasításainak megfelelően. Az eszközök licencelését az ISV/Tool határozza meg. 
- Minden eszközön lehetőség van a Azure Migratehoz való kapcsolódásra. A kapcsolódáshoz kövesse az utasításokat.
- Az áttelepítési útvonal nyomon követése a Azure Migrate projekten belül minden eszközön.


## <a name="movere"></a>Movere

A mozgató egy SaaS-platform, amely az üzleti intelligencia növelésével teszi biztonságosabbá a teljes informatikai környezeteket egyetlen nap alatt. Ahogy a szervezet fejleszti, megváltoztatja és digitálisan optimalizálja a megoldást, a megoldás biztosítja a vállalatok számára, hogy a platformtól, az alkalmazástól és a földrajztól függetlenül a környezet láthatóságával és ellenőrzésével rendelkezzenek. A [beszerzést](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) a Microsoft szerezte be, és már nem önálló ajánlatként árusítjuk.  A mozgató szolgáltatás a Microsoft Solution Assessment és a Cloud Economics programon keresztül érhető el. [További](https://www.movere.io) információ a mozgató szolgáltatásról. Ha kérdése van, küldje el a következőt: movereq@microsoft.com vagy forduljon a Microsoft képviselőjéhez.

Javasoljuk, hogy tekintse meg a Azure Migrate, a beépített áttelepítési szolgáltatást is. Azure Migrate egy központi központot biztosít a felhőbe való Migrálás egyszerűbbé tételéhez. A Központ átfogó támogatást nyújt a különböző számítási feladatokhoz, beleértve a fizikai és virtuális kiszolgálókat, az adatbázisokat és az alkalmazásokat. A teljes körű láthatósággal könnyen nyomon követhető a felderítés, az értékelés és az áttelepítés folyamata. A beépített Azure-és partner ISV-eszközökkel Azure Migrate is számos funkcióval rendelkezik, beleértve a virtuális és a fizikai kiszolgálók felderítését, a teljesítmény-alapú megfelelő méretezést, a költségmegtakarítást, az importálási alapú értékeléseket és az ügynök nélküli alkalmazást függőség elemzése. Ha szakértői segítségre van szüksége az első lépésekhez, a Microsoft az [Azure szakértői által felügyelt](https://azure.microsoft.com/partners) szolgáltatót is felhasználva végigvezeti Önt az utazáson. Tekintse meg a [Azure Migrate webhelyét](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="azure-migrate-versions"></a>Azure Migrate verziók

A Azure Migrate szolgáltatásnak két verziója van:

- **Aktuális verzió**: ezzel a verzióval Azure Migrate projekteket hozhat létre, felderítheti a helyszíni gépeket, és összehangolhatja az értékeléseket és áttelepítéseket. [További](whats-new.md) információ az ebben a verzióban található újdonságokról.
- **Korábbi verzió**: ha a Azure Migrate korábbi verzióját használta (csak a helyszíni VMWare virtuális gépek felmérése támogatott), akkor most az aktuális verziót kell használnia. Az előző verzió használatával már nem hozhat létre Azure Migrate projekteket, és javasoljuk, hogy ne végezzen új felfedezéseket. A meglévő projektek eléréséhez keresse meg és válassza a **Azure Migrate**lehetőséget a Azure Portal. A **Azure Migrate** irányítópulton van egy értesítés és egy hivatkozás a régi Azure Migrate projektek eléréséhez.



## <a name="next-steps"></a>Következő lépések

- Próbálja ki az oktatóanyagokat a [VMWare virtuális gépek](tutorial-prepare-vmware.md), a [Hyper-V virtuális gépek](tutorial-prepare-hyper-v.md)és a [fizikai kiszolgálók](tutorial-prepare-physical.md)értékeléséhez.
- Tekintse meg az Azure Migrate használatával kapcsolatban felmerülő [gyakori kérdéseket](resources-faq.md).
