---
title: Az Azure Migrate bemutatása | Microsoft Docs
description: A cikk áttekintést nyújt az Azure Migrate szolgáltatásról.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 10/22/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: def0832898170e0a278c403349eab1bd89050a57
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73498752"
---
# <a name="about-azure-migrate"></a>Az Azure Migrate bemutatása

Ez a cikk a Azure Migrate gyors áttekintését tartalmazza.

Azure Migrate segítségével áttelepítheti a helyszínről az Azure-ba. A Azure Migrate központosított központot biztosít a helyszíni infrastruktúrák, alkalmazások és az Azure-ba irányuló adatfelderítés,-értékelés és-áttelepítés nyomon követéséhez. A hub az Azure-eszközöket és-szolgáltatásokat biztosítja az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártók (ISV) ajánlatokhoz. Azure Migrate a következőket biztosítja:

- **Egységes áttelepítési platform**: egyetlen portál, amellyel elindíthatja, futtathatja és nyomon követheti az Azure-ba való áttelepítést.
- **Eszközök köre**: natív eszközök és integráció más Azure-szolgáltatásokkal, valamint az ISV-eszközökkel. Válassza ki a megfelelő értékelési és áttelepítési eszközöket a szervezeti követelmények alapján.
- **Munkaterhelések**: a Azure Migrate értékelési és áttelepítési eszközöket biztosít a következőhöz:
    - **Kiszolgálók**: a Microsoft eszközeinek vagy ISV-eszközeinek használata a kiszolgálók Azure-beli virtuális gépekre történő kiértékeléséhez és áttelepítéséhez.
    - **Adatbázisok**: a Microsoft és az ISV eszközök kihasználása helyszíni adatbázisok értékeléséhez és áttelepítéséhez az Azure SQL db vagy az Azure SQL felügyelt példányain.
    - **Webalkalmazások**: Azure app Service Assistant használatával felméri és áttelepítheti a helyszíni webalkalmazásokat Azure app Servicere.
    - **Virtuális asztalok**: az ISV-eszközök használatával helyszíni virtuális asztali infrastruktúrát (VDI) lehet felmérni és áttelepíteni az Azure-beli Windows rendszerű virtuális asztali környezetbe.
    - **Adatkezelés**: a Azure Data Box termékcsaládot használva gyorsan és költséghatékonyan telepítheti át az Azure-ba irányuló adatátvitelt.

## <a name="azure-migrate-versions"></a>Azure Migrate verziók

A Azure Migrate szolgáltatásnak jelenleg két verziója van:

- **Aktuális verzió**: ezzel a verzióval Azure Migrate projekteket hozhat létre, felderítheti a helyszíni gépeket, és összehangolhatja az értékeléseket és áttelepítéseket. [További](whats-new.md) információ az ebben a verzióban található újdonságokról.
- **Korábbi verzió**: ha a Azure Migrate korábbi verzióját használta (csak a helyszíni VMWare virtuális gépek felmérése támogatott), akkor most az aktuális verziót kell használnia. Az előző verzió használatával már nem hozhat létre Azure Migrate projekteket, és javasoljuk, hogy ne végezzen új felfedezéseket. A meglévő projektek eléréséhez a Azure Portal > **minden szolgáltatás**területen keressen **Azure Migrate**. A Azure Migrate irányítópulton van egy értesítés és egy hivatkozás a régi Azure Migrate projektek eléréséhez.

## <a name="isv-integration"></a>ISV-integráció

A natív Microsoft-eszközökön kívül a Azure Migrate számos ISV-ajánlattal is integrálható. 

**ISV** | **Funkció**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Értékelés
[Eszköz 42](https://docs.device42.com/) | Értékelés
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Értékelés
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Értékelés
[A kölcsönzés technológiája](https://www.corenttech.com/AzureMigrate/) | Értékelés és Migrálás
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrálás
[Tóparti](https://go.microsoft.com/fwlink/?linkid=2104908) | Értékelés



### <a name="selecting-an-isv-tool"></a>ISV-eszköz kiválasztása

Azonosíthatja a szükséges eszközt, és hozzáadhatja egy Azure Migrate projekthez.

- Az ISV-eszközök hozzáadása után megkezdheti a licenc beszerzését vagy az ingyenes próbaverzió regisztrálását az ISV-szabályzatnak megfelelően. Az ISV-eszközök licencelése az ISV licencelési modellel összhangban történik.
- Minden eszközön lehetőség van a Azure Migratehoz való kapcsolódásra. Az eszköz Azure Migrate. L-vel való összekapcsolásához kövesse az eszköz útmutatásait és dokumentációját.
- Központilag nyomon követheti az áttelepítési utat a Azure Migrate projekten belül, az Azure-ban és az ISV-eszközökön.


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


## <a name="database-assessment"></a>Adatbázis-Értékelés

A Azure Migrate a Microsoft Data Migration Assistant (DMA) szolgáltatással integrálható a helyszíni SQL Server adatbázisok áttelepítésére az Azure SQL DB-be, az Azure SQL felügyelt példányaiba vagy SQL Server-t futtató Azure-beli virtuális gépekre. A DMA információt nyújt az áttelepítéssel kapcsolatos lehetséges blokkolási problémákról. Azonosítja a nem támogatott funkciókat, valamint az áttelepítés után kihasználható új funkciókat, és segít az adatbázis-áttelepítés megfelelő elérési útjának azonosításában. [Részletek](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).

## <a name="database-migration"></a>Adatbázis-áttelepítés

A Azure Migrate a Azure Database Migration Service (DMS) szolgáltatással integrálható a helyszíni adatbázisok Azure-ba való migrálása érdekében. A DMS használatával helyszíni adatbázisokat telepíthet át az SQL, az Azure SQL DB és az Azure SQL felügyelt példányain futó Azure virtuális gépekre. [Részletek](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="web-app-migration"></a>Webalkalmazás migrálása

A Azure Migrate a Azure App Service Migration Assistant integrálódik. Az Azure Migrate hub segítségével a következő módon elemezheti és áttelepítheti a helyszíni webalkalmazásokat az Azure-ba:

- **Webalkalmazások felmérése online**: Azure app Service Migration Assistant használatával kivizsgálhatja a helyszíni webhelyeket Azure app Servicere való áttelepítéshez.
- **Webalkalmazások migrálása**: .net-és PHP-alapú webalkalmazások migrálása az Azure-ba Azure app Service Migration Assistant használatával.

[További](https://appmigration.microsoft.com/) információ a segédről.

## <a name="offline-data-migration"></a>Offline adatáttelepítés

A Azure Data Box termékek használatával a nagy mennyiségű adatmennyiséget az Azure-ba helyezheti át. [Részletek](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>További lépések

- Próbálja ki az oktatóanyagokat a [VMWare virtuális gépek](tutorial-assess-vmware.md) és a [Hyper-V virtuális gépek](tutorial-assess-hyper-v.md)értékeléséhez.
- [Itt talál további információkat](https://azure.microsoft.com/pricing/details/azure-migrate/) az Azure Migrate díjszabásáról.
- Tekintse meg az Azure Migrate használatával kapcsolatban felmerülő [gyakori kérdéseket](resources-faq.md).
