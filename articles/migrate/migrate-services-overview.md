---
title: Az Azure Migrate bemutatása
description: Ismerkedjen meg a kiszolgáló értékelésével és a Azure Migrate szolgáltatással való áttelepítéssel.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 11/30/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 04e6eddb86d34089db0243ff102d5ebd5ee77e43
ms.sourcegitcommit: 57eb9acf6507d746289efa317a1a5210bd32ca2c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2019
ms.locfileid: "74666120"
---
# <a name="about-azure-migrate"></a>Az Azure Migrate bemutatása

Ez a cikk a Azure Migrate gyors áttekintését tartalmazza.

A Azure Migrate segítségével áttelepítheti a vállalatot a helyszínről az Azure-ba. A Azure Migrate központosított központot biztosít a helyszíni infrastruktúrák, alkalmazások és az Azure-ba irányuló adatfelderítés,-értékelés és-áttelepítés nyomon követéséhez.  Azure Migrate a következőket biztosítja:

- **Egységes áttelepítési platform**: egyetlen portál, amellyel elindíthatja, futtathatja és nyomon követheti az Azure-ba való áttelepítést.
- **Eszközök köre**: az elosztó Azure Migrate eszközök felmérését és áttelepítését, valamint más Azure-szolgáltatásokkal, valamint más eszközökkel és független szoftvergyártói (ISV) ajánlatokkal való integrációt biztosít.
- Számítási **feladatok**: Azure Migrate értékelést és áttelepítést biztosít a következőhöz:
    - **Kiszolgálók**: a kiszolgálók Azure-beli virtuális gépekre való értékeléséhez és áttelepítéséhez Azure Migrate Server assessment, Azure Migrate-kiszolgáló áttelepítését és más eszközöket használjon.
    - **Adatbázisok**: Microsoft-és ISV-eszközök kihasználása helyszíni adatbázisok Azure SQL Database-be vagy felügyelt Azure SQL-példányba történő értékeléséhez és áttelepítéséhez.
    - **Webalkalmazások**: Azure app Service Assistant használatával felméri és áttelepítheti a helyszíni webalkalmazásokat Azure app Servicere.
    - **Virtuális asztalok**: az ISV-eszközök használatával helyszíni virtuális asztali infrastruktúrát (VDI) lehet felmérni és áttelepíteni az Azure-beli Windows rendszerű virtuális asztali környezetbe.
    - **Adatkezelés**: a Azure Data Box termékcsaládot használva gyorsan és költséghatékonyan telepíthet nagy mennyiségű adatátvitelt az Azure-ba.

## <a name="azure-migrate-versions"></a>Azure Migrate verziók

A Azure Migrate szolgáltatásnak jelenleg két verziója van:

- **Aktuális verzió**: ezzel a verzióval Azure Migrate projekteket hozhat létre, felderítheti a helyszíni gépeket, és összehangolhatja az értékeléseket és áttelepítéseket. [További](whats-new.md) információ az ebben a verzióban található újdonságokról.
- **Korábbi verzió**: ha a Azure Migrate korábbi verzióját használta (csak a helyszíni VMWare virtuális gépek felmérése támogatott), akkor most az aktuális verziót kell használnia. Az előző verzió használatával már nem hozhat létre Azure Migrate projekteket, és javasoljuk, hogy ne végezzen új felfedezéseket. A meglévő projektek eléréséhez a Azure Portal > **minden szolgáltatás**területen keressen **Azure Migrate**. A Azure Migrate irányítópulton van egy értesítés és egy hivatkozás a régi Azure Migrate projektek eléréséhez.



## <a name="isv-integration"></a>ISV-integráció

A natív Azure-eszközökön kívül a Azure Migrate számos ISV-ajánlattal is integrálható. 

**ISV** | **Funkció**
--- | ---
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Kiszolgálók migrálása
[Cloudamize](https://www.cloudamize.com/platform) | Kiszolgálók értékelése
[A kölcsönzés technológiája](https://www.corenttech.com/AzureMigrate/) | Kiszolgálók értékelése és migrálása
[Eszköz 42](https://docs.device42.com/) | Kiszolgálók értékelése
[Tóparti](https://go.microsoft.com/fwlink/?linkid=2104908) | VDI értékelése
[RackWare](https://www.rackwareinc.com/cloud-migration) | Kiszolgálók migrálása
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Kiszolgálók értékelése
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Kiszolgálók és adatbázisok értékelése

## <a name="azure-tool-integration"></a>Azure-eszközök integrációja

A táblázat összefoglalja a Azure Migrateba integrált egyéb eszközöket.

**Eszköz** | **Részletek**
--- | ---
Azure Migrate: kiszolgáló értékelése | Kiszolgálók értékelése
Azure Migrate: kiszolgáló áttelepítése | Kiszolgálók migrálása
Adatbázis-Migration Assistant (DMA) | Adatbázisok felmérése
Database Migration Service (DMS) | Adatbázisok migrálása
Movere | Kiszolgálók értékelése
Webalkalmazás-Migration Assistant | Webalkalmazások felmérése és migrálása



### <a name="selecting-a-tool"></a>Eszköz kiválasztása

Azonosítsa a szükséges eszközt, és vegye fel egy Azure Migrate projektbe.

- Ha ISV-eszközt vagy Mozgatót ad hozzá:
    - Első lépésként szerezze be a licencet, vagy regisztráljon az ingyenes próbaverzióra az eszköz házirendjének megfelelően. Az eszközök licencelése az ISV vagy az eszköz licencelési modelljének megfelelően történik.
    - Minden eszközön lehetőség van a Azure Migratehoz való kapcsolódásra. Az eszköz Azure Migrate való összekapcsolásához kövesse az eszköz útmutatásait és dokumentációját.
- Központilag nyomon követheti az áttelepítési utat a Azure Migrate projekten belül, az Azure-ban és más eszközökön.



## <a name="azure-migrate-server-assessment-tool"></a>Azure Migrate Server Assessment eszköz

Azure Migrate: a kiszolgáló-értékelő eszköz felfedi és felméri a helyszíni VMware virtuális gépeket, a Hyper-V virtuális gépeket és a fizikai kiszolgálókat az Azure-ba való Migrálás során. Ez segít a következők azonosításában:

- **Azure-készültség:** Vizsgálja meg, hogy a helyszíni gépek készen állnak-e az Azure-ba való áttelepítésre.
- Az **Azure méretezése:** Az Azure-beli virtuális gépek becsült mérete az áttelepítés után.
- **Azure-költségbecslés:** A helyszíni kiszolgálók Azure-ban való futtatásának becsült költségei.
- **Függőségi vizualizáció:** A kiszolgálók közötti függőségek (ha a függőségi vizualizáció engedélyezve van), valamint a függő kiszolgálók Azure-ba való áthelyezésének optimális módjai.

A kiszolgáló értékelése egy, a helyszínen üzembe helyezett, egyszerűsített berendezést használ, és regisztrálja magát a kiszolgáló értékelésével.

- A készülék felfedi a helyszíni gépeket.
- A kiszolgáló értékeléséhez csatlakozik, és folyamatosan küldi el a gépek metaadatait és teljesítményadatait a Azure Migrate.
- A készülék felderítése ügynök nélkül történik. Semmit nem kell telepíteni a felderített gépekre.
- A felderítést követően a felderített gépeket csoportokba gyűjti. Általában össze kell gyűjtenie azokat a gépeket, amelyeket át szeretne telepíteni.
- Hozzon létre egy értékelést a csoport számára. Ezt követően elemezheti az értékelést, hogy kiderítse az áttelepítési stratégiát.

## <a name="azure-migrate-server-migration-tool"></a>Azure Migrate Server Migration eszköz

Azure Migrate: a kiszolgálói áttelepítési eszköz segítségével helyszíni VMware virtuális gépeket, Hyper-V virtuális gépeket, fizikai kiszolgálókat, más virtualizált számítógépeket és nyilvános Felhőbeli virtuális gépeket telepíthet át az Azure-ba. A gépeket a kiértékelésük után vagy értékelés nélkül is áttelepítheti.


## <a name="database-migration-assistant"></a>Adatbázis Migration Assistant

A Azure Migrate a Microsoft Data Migration Assistant (DMA) szolgáltatással integrálható a helyszíni SQL Server adatbázisok áttelepítésére az Azure SQL DB-be, az Azure SQL felügyelt példányaiba vagy SQL Server-t futtató Azure-beli virtuális gépekre. A DMA információt nyújt az áttelepítéssel kapcsolatos lehetséges blokkolási problémákról. Azonosítja a nem támogatott funkciókat, valamint az áttelepítés után kihasználható új funkciókat, és segít az adatbázis-áttelepítés megfelelő elérési útjának azonosításában. [További információk](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).

## <a name="database-migration-service"></a>Database Migration Service

A Azure Migrate a Azure Database Migration Service (DMS) szolgáltatással integrálható a helyszíni adatbázisok Azure-ba való migrálása érdekében. A DMS használatával helyszíni adatbázisokat telepíthet át az SQL, az Azure SQL DB és az Azure SQL felügyelt példányain futó Azure virtuális gépekre. [További információk](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="movere"></a>Movere

 
A mozgató egy SaaS-platform, amely az üzleti intelligencia növelésével teszi biztonságosabbá a teljes informatikai környezeteket egyetlen nap alatt. Ahogy a szervezet fejleszti, megváltoztatja és digitálisan optimalizálja a megoldást, a megoldás biztosítja a vállalatok számára, hogy a platformtól, az alkalmazástól és a földrajztól függetlenül a környezet láthatóságával és ellenőrzésével rendelkezzenek. A [beszerzést](https://azure.microsoft.com/blog/microsoft-acquires-movere-to-help-customers-unlock-cloud-innovation-with-seamless-migration-tools/) a Microsoft szerezte be, és már nem önálló ajánlatként árusítjuk.  A mozgató szolgáltatás a Microsoft Solution Assessment és a Cloud Economics programon keresztül érhető el. [További](https://www.movere.io) információ a mozgató szolgáltatásról. Ha kérdése van, küldje el a következőt: movereq@microsoft.com vagy forduljon a Microsoft képviselőjéhez.

Javasoljuk, hogy tekintse meg a Azure Migrate, a beépített áttelepítési szolgáltatást is. Azure Migrate egy központi központot biztosít a felhőbe való Migrálás egyszerűbbé tételéhez. A Központ átfogó támogatást nyújt a különböző számítási feladatokhoz, beleértve a fizikai és virtuális kiszolgálókat, az adatbázisokat és az alkalmazásokat. A teljes körű láthatósággal könnyen nyomon követhető a felderítés, az értékelés és az áttelepítés folyamata. A beépített Azure-és partner ISV-eszközökkel Azure Migrate is számos funkcióval rendelkezik, beleértve a virtuális és a fizikai kiszolgálók felderítését, a teljesítmény-alapú megfelelő méretezést, a költségmegtakarítást, az importálási alapú értékeléseket és az ügynök nélküli alkalmazást függőség elemzése. Ha szakértői segítségre van szüksége az első lépésekhez, a Microsoft az [Azure szakértői által felügyelt](https://azure.microsoft.com/partners) szolgáltatót is felhasználva végigvezeti Önt az utazáson. Tekintse meg a [Azure Migrate webhelyét](https://azure.microsoft.com/services/azure-migrate/). 
 

## <a name="web-app-migration-assistant"></a>Webalkalmazás-Migration Assistant

A Azure Migrate a Azure App Service Migration Assistant integrálódik. Az Azure Migrate hub segítségével a következő módon elemezheti és áttelepítheti a helyszíni webalkalmazásokat az Azure-ba:

- **Webalkalmazások felmérése online**: Azure app Service Migration Assistant használatával kivizsgálhatja a helyszíni webhelyeket Azure app Servicere való áttelepítéshez.
- **Webalkalmazások migrálása**: .net-és PHP-alapú webalkalmazások migrálása az Azure-ba Azure app Service Migration Assistant használatával.

[További](https://appmigration.microsoft.com/) információ a segédről.



## <a name="offline-data-migration"></a>Offline adatáttelepítés

A Azure Data Box termékek használatával a nagy mennyiségű adatmennyiséget az Azure-ba helyezheti át. [További információ](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>Következő lépések

- Próbálja ki az oktatóanyagokat a [VMWare virtuális gépek](tutorial-assess-vmware.md) és a [Hyper-V virtuális gépek](tutorial-assess-hyper-v.md)értékeléséhez.
- [Itt talál további információkat](https://azure.microsoft.com/pricing/details/azure-migrate/) az Azure Migrate díjszabásáról.
- Tekintse meg az Azure Migrate használatával kapcsolatban felmerülő [gyakori kérdéseket](resources-faq.md).
