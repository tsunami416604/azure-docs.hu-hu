---
title: Az Azure Migrate bemutatása | Microsoft Docs
description: A cikk áttekintést nyújt az Azure Migrate szolgáltatásról.
author: rayne-wiselman
ms.service: azure-migrate
ms.topic: overview
ms.date: 07/11/2019
ms.author: raynew
ms.custom: mvc
ms.openlocfilehash: 03918976935f9071ba2a7951e29195118943a8ef
ms.sourcegitcommit: 670c38d85ef97bf236b45850fd4750e3b98c8899
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/08/2019
ms.locfileid: "68845832"
---
# <a name="about-azure-migrate"></a>Az Azure Migrate bemutatása

Ez a cikk a Azure Migrate gyors áttekintését tartalmazza.

Azure Migrate segítségével áttelepítheti az Azure-ba. A Azure Migrate központosított központot biztosít a helyszíni infrastruktúrák, alkalmazások és az Azure-ba irányuló adatfelderítés,-értékelés és-áttelepítés nyomon követéséhez. A hub Azure-eszközöket biztosít az értékeléshez és az áttelepítéshez, valamint a harmadik féltől származó független szoftvergyártói (ISV) ajánlatokhoz. Az alábbiakat biztosítja:

- **Egységesített áttelepítési platform**: Egyetlen portál használatával elindíthatja, futtathatja és nyomon követheti az Azure-ba való áttelepítést.
- **Eszközök köre**: A Azure Migrate natív eszközöket biztosít, és integrálható más Azure-szolgáltatásokkal, valamint az ISV-eszközökkel. Válassza ki a megfelelő értékelési és áttelepítési eszközöket a szervezeti követelmények alapján. 
- **Azure Migrate kiszolgáló értékelése**: A kiszolgáló-értékelési eszközzel kiértékelheti a helyszíni VMware virtuális gépeket és a Hyper-V virtuális gépeket az Azure-ba való áttelepítéshez.
- **Azure Migrate kiszolgáló áttelepítése**: A kiszolgáló áttelepítési eszközével helyszíni VMware virtuális gépeket, Hyper-V virtuális gépeket, Felhőbeli virtuális gépeket és fizikai kiszolgálókat telepíthet át az Azure-ba.
- **Azure Migrate adatbázis-értékelés**: Helyszíni adatbázisok kiértékelése az Azure-ba való áttelepítéshez.
- **Adatbázis áttelepítése Azure Migrate**: Helyszíni adatbázisok migrálása az Azure-ba.


## <a name="azure-migrate-versions"></a>Azure Migrate verziók

A Azure Migrate szolgáltatásnak két verziója van:

- **Aktuális verzió**: Ezzel a verzióval Azure Migrate projekteket hozhat létre, felderítheti a helyszíni gépeket, és összehangolhatja az értékeléseket és áttelepítéseket. [További](whats-new.md) információ az ebben a verzióban található újdonságokról.
- **Előző verzió**: Ha a Azure Migrate korábbi verzióját használta (csak a helyszíni VMware virtuális gépek felmérése támogatott), akkor most az aktuális verziót kell használnia. Az előző verzió használatával már nem hozhat létre Azure Migrate projekteket, és javasoljuk, hogy ne végezzen új felfedezéseket. A meglévő projektek eléréséhez a Azure Portal > **minden szolgáltatás**területen keressen **Azure Migrate**. A Azure Migrate irányítópulton van egy értesítés és egy hivatkozás a régi Azure Migrate projektek eléréséhez.

## <a name="isv-integration"></a>ISV-integráció

A natív Azure-eszközökön kívül a Azure Migrate számos ISV-ajánlattal is integrálható. Azonosíthatja a szükséges eszközt, és hozzáadhatja egy Azure Migrate projekthez. Központilag követheti az áttelepítési utat a Azure Migrate projektből az Azure-ban és az ISV-eszközökön.

**ISV** | **Funkció**
--- | ---
[Cloudamize](https://www.cloudamize.com/platform) | Értékelés
[Eszköz 42](https://docs.device42.com/) | Értékelés
[Turbonomic](https://learn.turbonomic.com/azure-migrate-portal-free-trial) | Értékelés
[UnifyCloud](https://www.cloudatlasinc.com/cloudrecon/) | Értékelés
[A kölcsönzés technológiája](https://www.corenttech.com/AzureMigrate/) | Értékelés és Migrálás
[Carbonite](https://www.carbonite.com/globalassets/files/datasheets/carb-migrate4azure-microsoft-ds.pdf) | Migrate (Áttelepítés)

### <a name="selecting-an-isv-tool"></a>ISV-eszköz kiválasztása

Miután hozzáadta az ISV-eszközt egy Azure Migrate projekthez, a licenc beszerzésével vagy az ingyenes próbaverzióra való feliratkozással megkezdheti az eszközt az ISV-szabályzatnak megfelelően. Minden eszközön lehetőség van a Azure Migratehoz való kapcsolódásra. Az eszköz Azure Migrate való összekapcsolásához kövesse az eszköz útmutatásait és dokumentációját. Az ISV-eszközök licencelése az ISV licencelési modellel összhangban történik.

## <a name="azure-migrate-server-assessment"></a>Azure Migrate kiszolgáló értékelése

Azure Migrate Server Assessment a helyszíni VMware virtuális gépeket és a Hyper-V virtuális gépeket az Azure-ba való Migrálás során felfedi és értékeli. Ez segít a következők azonosításában:

- **Azure-készültség:** Vizsgálja meg, hogy a helyszíni gépek készen állnak-e az Azure-ba való áttelepítésre.
- **Az Azure méretezése:** A Migrálás után megbecsülheti az Azure-beli virtuális gépek méretét.
- **Azure-költségbecslés:** A helyszíni kiszolgálók Azure-beli futtatásával kapcsolatos költségek becslése.
- **Függőségi vizualizáció:** Azonosítsa a kiszolgálók közötti függőségeket, és a legjobb módszer a függő kiszolgálók Azure-ba való áthelyezésére. 

A kiszolgáló értékelése egy, a helyszínen üzembe helyezett, egyszerűsített berendezést használ, és regisztrálja magát a kiszolgáló értékelésével.

- A készülék felfedi a helyszíni gépeket, csatlakozik a kiszolgáló értékeléséhez, és folyamatosan küldi a metaadatokat és a teljesítménnyel kapcsolatos adatokat Azure Migrate.
- A felderítés ügynök nélküli. Nem kell telepíteni a felderített virtuális gépeket.
- A gépek felderítése után azokat a csoportokat kell összegyűjtenie, amelyek jellemzően az áttelepíteni kívánt virtuális gépekből állnak.
- Hozzon létre egy értékelést a csoport számára. Ezután elemezheti az értékelést, hogy kiderítse az áttelepítési stratégiát.

## <a name="azure-migrate-server-migration"></a>Azure Migrate kiszolgáló áttelepítése

A Azure Migrate-kiszolgáló áttelepítése a helyszíni VMware virtuális gépek, Hyper-V virtuális gépek, fizikai kiszolgálók, más virtualizált gépek és nyilvános Felhőbeli virtuális gépek áttelepítését teszi lehetővé az Azure-ba. A gépeket a kiértékelésük után vagy értékelés nélkül is áttelepítheti. 

## <a name="azure-migrate-database-assessment"></a>Adatbázis-értékelés Azure Migrate

A Azure Migrate a Data Migration Assistant (DMA) szolgáltatással integrálható a helyszíni SQL Server adatbázisok áttelepítésére az Azure SQL DB-be, az Azure SQL felügyelt példányaiba vagy SQL Server-t futtató Azure-beli virtuális gépekre. A DMA információt nyújt az áttelepítéssel kapcsolatos lehetséges blokkolási problémákról. Azonosítja a nem támogatott funkciókat, valamint az áttelepítés után kihasználható új funkciókat, és segít az adatbázis-áttelepítés megfelelő elérési útjának azonosításában. [További információk](https://docs.microsoft.com/sql/dma/dma-overview?view=sql-server-2017).


## <a name="azure-migrate-database-migration"></a>Adatbázis-áttelepítés Azure Migrate

A Azure Migrate a Azure Database Migration Service (DMS) szolgáltatással integrálható a helyszíni adatbázisok Azure-ba való migrálása érdekében. A DMS használatával helyszíni adatbázisokat telepíthet át az SQL, az Azure SQL DB és az Azure SQL felügyelt példányain futó Azure virtuális gépekre. [További információk](https://docs.microsoft.com/azure/dms/dms-overview).

## <a name="web-app-assessment-and-migration"></a>Webalkalmazások felmérése és áttelepítése

Az Azure Migrate központban a helyszíni webalkalmazásokat az Azure-ba is felhasználhatja és áttelepítheti.

- **Webes alkalmazások felmérése online**: A Azure App Service Migration Assistant segítségével kihasználhatja a helyszíni webhelyeket Azure App Servicere való áttelepítéshez.
- Webalkalmazások migrálása: Telepítse át a .NET-és PHP-webalkalmazásokat az Azure-ba Azure App Service Migration Assistant használatával.

[Részletek](https://appmigration.microsoft.com/)

## <a name="offline-data-migration"></a>Offline adatok migrálása

A Data Box offline termékcsalád használatával nagy mennyiségű adatátvitelt helyezhet át az Azure-ba. [További információ](https://docs.microsoft.com/azure/databox/)

## <a name="next-steps"></a>További lépések

- Próbálja ki az oktatóanyagokat a [VMWare virtuális gépek](tutorial-assess-vmware.md) és a [Hyper-V virtuális gépek](tutorial-assess-hyper-v.md)értékeléséhez.
- [Itt talál további információkat](https://azure.microsoft.com/pricing/details/azure-migrate/) az Azure Migrate díjszabásáról.
- Tekintse meg az Azure Migrate használatával kapcsolatban felmerülő [gyakori kérdéseket](resources-faq.md).
