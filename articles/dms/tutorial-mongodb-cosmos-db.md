---
title: 'Oktatóanyag: Segítségével az Azure Database Migration Service áttelepítheti a MongoDB az Azure Cosmos DB API a mongodb-hez offline |} A Microsoft Docs'
description: Ismerje meg, ha át helyszíni mongodb-hez az Azure Cosmos DB API a mongodb-hez offline az Azure Database Migration Service használatával.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 03/12/2019
ms.openlocfilehash: 62acab231455b19d37b0800ead172950b5ee0378
ms.sourcegitcommit: 5839af386c5a2ad46aaaeb90a13065ef94e61e74
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/19/2019
ms.locfileid: "58176276"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-offline-using-dms"></a>Oktatóanyag: MongoDB át az Azure Cosmos DB API a mongodb-hez offline a DMS használatával
Az Azure Database Migration Service használatával történő offline áttelepítéshez (egyszeri) az adatbázisok egy helyszíni vagy felhőbeli Azure Cosmos DB API a MongoDB-példány a mongodb-hez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Egy Azure Database Migration Service-példány létrehozása.
> * Migrálási projekt létrehozása az Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * A migrálás monitorozása.

Ebben az oktatóanyagban egy adatkészletet, a mongodb-hez az Azure virtuális gépként az Azure Cosmos DB API üzemeltetett mongodb-hez készült Azure Database Migration Service használatával telepít át. Ha egy MongoDB-forráshoz, állítsa be a már nem rendelkezik, tekintse meg a cikket [telepítése és konfigurálása a mongodb-hez az Azure-beli Windows virtuális gép](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

## <a name="prerequisites"></a>Előfeltételek
Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:
- [Hozzon létre egy Azure Cosmos DB API a MongoDB-fiókhoz](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
- Hozzon létre egy Azure Virtual Network (VNET) használatával az Azure Resource Manager üzembe helyezési modell, amely lehetővé teszi a helyek közötti kapcsolatot a helyszíni adatforrás-kiszolgálók használatával vagy az Azure Database Migration Service [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

    > [!NOTE]
    > Virtuális hálózathoz a telepítés során, ha az ExpressRoute hálózati a Microsoft társviszony-létesítés használja, hozzá a következő szolgáltatás [végpontok](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) az alhálózathoz, amelyben üzembe fogja helyezni a szolgáltatást:
    > - Cél adatbázis végpont (például SQL-végpont, Cosmos-DB végpont, és így tovább)
    > - Storage-végpont
    > - Service bus-végpont
    >
    > Ez a konfiguráció szükség, mert az Azure Database Migration Service nem rendelkezik internetkapcsolattal.

- Győződjön meg arról, hogy a virtuális hálózatok közötti hálózati biztonsági csoport szabályai nem blokkolják a következő kommunikációs portokat: 443-as, 53-as és 9354-es, 445-ös, valamint a 12000. További részletek az Azure VNET NSG-forgalom szűréséről: [Hálózati forgalom szűrése hálózati biztonsági csoportokkal](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
- Nyissa meg a Windows tűzfalat az Azure Database Migration Service a forrás MongoDB-kiszolgáló, amely alapértelmezés szerint a TCP-port 27017 eléréséhez.
- Ha tűzfalkészüléket használ a forrásadatbázis(ok) előtt, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia annak engedélyezéséhez, hogy az Azure Database Migration Service a migrálás céljából hozzáférhessen a forrásadatbázis(ok)hoz.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása
1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-mongodb-to-cosmosdb/portal-select-subscription1.png)
       
2. Válassza ki azt az előfizetést, amelyen az Azure Database Migration Service példányát létre szeretné hozni, majd válassza az **Erőforrás-szolgáltatók** elemet.
 
    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-mongodb-to-cosmosdb/portal-select-resource-provider.png)
    
3.  Keressen a „migration” kifejezésre, majd a **Microsoft.DataMigration** jobb oldalán válassza a **Regisztrálás** elemet.
 
    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-mongodb-to-cosmosdb/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Példány létrehozása
1.  Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-mongodb-to-cosmosdb/portal-marketplace.png)

2.  Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.
 
    ![Azure Database Migration Service-példány létrehozása](media/tutorial-mongodb-to-cosmosdb/dms-create1.png)
  
3.  **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, amelyen az Azure Database Migration Service példányát létre szeretné hozni. 

5. Válasszon ki egy meglévő virtuális hálózatot (VNET), vagy hozzon létre egy újat.

    A virtuális hálózat az Azure Database Migration Service a forrás MongoDB-példányban, és a cél Azure Cosmos DB-fiók hozzáférést biztosít.

    További információk a virtuális hálózatok létrehozásáról az Azure Portallal: [Virtuális hálózat létrehozása az Azure Portallal](https://aka.ms/DMSVnet).

6. Válasszon tarifacsomagot.

    További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    Ha a megfelelő Azure Database Migration Service-csomag kiválasztása segítségre van szüksége, tekintse meg a javaslatok, az ebben a blogbejegyzésben [Itt](https://go.microsoft.com/fwlink/?linkid=861067).  

     ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-mongodb-to-cosmosdb/dms-settings2.png)

7.  A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása
A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.
 
      ![Az Azure Database Migration Service minden példányának megkeresése](media/tutorial-mongodb-to-cosmosdb/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service-példány nevét, és válassza ki ezt a példányt.

3. Válassza a + **Új migrálási projekt** lehetőséget.

4. Az a **új migrálási projekt** lapon adjon meg egy nevet a projektnek a **forráskiszolgáló típusa** szövegbeviteli mezőben válasszon ki **MongoDB**, a a **célkiszolgáló típusa**  szövegbeviteli mezőben válasszon ki **CosmosDB (MongoDB API-t)**, majd **válassza ki a tevékenység típusát**válassza **Offline adatok migrálása**. 

    ![Database Migration Service-projekt létrehozása](media/tutorial-mongodb-to-cosmosdb/dms-create-project.png)

5.  Válassza a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához és a migrálási művelet lefuttatásához.

## <a name="specify-source-details"></a>Forrás adatainak megadása
1. Az a **forrás részletei** képernyőn, adja meg a forráskiszolgáló MongoDB kapcsolati adatait.
    
   Kapcsolati karakterlánc mód használata is, és adjon meg egy fájl blobtárolóba, amelyben a gyűjtemény adatait szeretné áttelepíteni már kiírt helyét.

   > [!NOTE]
   > Az Azure Database Migration Service migrálhatja bson vagy json-dokumentumokat az Azure Cosmos DB API a MongoDB-gyűjteményt.
    
   Amikor a DNS-névfeloldás nem lehetséges, az IP-címet is használhatja.

   ![Forrás adatainak megadása](media/tutorial-mongodb-to-cosmosdb/dms-specify-source.png)

2. Kattintson a **Mentés** gombra.

## <a name="specify-target-details"></a>Cél adatainak megadása
1. Az a **Migrálási cél részletei** képernyőn, adja meg a cél Azure Cosmos DB-fiókot, amely az előre kiépített Azure Cosmos DB API a MongoDB-fiókhoz, amelyhez a MongoDB-adatait migráláshoz kapcsolati adatait.

    ![Cél adatainak megadása](media/tutorial-mongodb-to-cosmosdb/dms-specify-target.png)

2. Kattintson a **Mentés** gombra.

## <a name="map-to-target-databases"></a>Leképezés céladatbázisokra
1. Az a **leképezés céladatbázisokra** képernyőn, és leképezheti a forrás- és a céladatbázis az áttelepítéshez.

    Ha a céladatbázis neve megegyezik a forrásadatbázis nevével, az Azure Database Migration Service alapértelmezés szerint a céladatbázist választja ki.

    Ha a karakterlánc **létrehozás** jelenik meg az adatbázis neve mellett azt jelzi, hogy az Azure Database Migration Service nem találta meg a céladatbázist, és a szolgáltatás az adatbázist hozza létre az Ön számára.

    Ezen a ponton a migrálás lehetősége [kiépítése átviteli](https://docs.microsoft.com/azure/cosmos-db/set-throughput). A Cosmos DB az adatbázis szintjén vagy külön-külön az egyes gyűjtemények átviteli telepíthet. Átviteli sebesség mérése a [kérelemegység](https://docs.microsoft.com/azure/cosmos-db/request-units) (RU-k). Tudjon meg többet [Azure Cosmos DB díjszabása](https://azure.microsoft.com/pricing/details/cosmos-db/).

    ![Leképezés céladatbázisokra](media/tutorial-mongodb-to-cosmosdb/dms-map-target-databases.png)

2. Kattintson a **Mentés** gombra.
3. Az a **gyűjteménybeállítás** képernyőn, majd tekintse át a gyűjteményeket, amelyek áttelepíthetők, bontsa ki a gyűjtemények listázása.

    Vegye figyelembe, hogy az Azure Database Migration Service automatikusan kiválasztja az összes gyűjteményt, amely a MongoDB forráspéldányon létezik, amely még nem léteznek a cél Azure Cosmos DB-fiókot. Ha szeretné a gyűjteményeket, amelyek már tartalmazzák az adatok áttelepítéséhez, explicit módon jelölje ki ezt a panelt a gyűjteményeket szeretne.

    A gyűjtemények használni kívánt RUs mennyisége is megadhat. Az Azure Database Migration Service intelligens alapértelmezett beállítások a gyűjtemény mérete alapján javasol.

    Azt is megadhatja a szegmenskulcs kihasználásához [az Azure Cosmos DB particionálási](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) optimális méretezhetőségre. Ne feledje el áttekinteni a [ajánlott eljárások a szilánkleképezés-/ partíciókulcs kiválasztása](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey).

    ![Gyűjtemények táblák kijelölése](media/tutorial-mongodb-to-cosmosdb/dms-collection-setting.png)

4. Kattintson a **Mentés** gombra.

5. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét.

    ![A migrálás összegzése](media/tutorial-mongodb-to-cosmosdb/dms-migration-summary.png)

## <a name="run-the-migration"></a>A migrálás futtatása
- Válassza a **Migrálás futtatása** lehetőséget.

    A migrálási tevékenység ablak megjelenik, és a **állapot** , de a tevékenység **nem indult el**.

    ![Tevékenységállapot](media/tutorial-mongodb-to-cosmosdb/dms-activity-status.png)

## <a name="monitor-the-migration"></a>A migrálás monitorozása
- A migrálás műveletének ablakában válassza a **Frissítés** lehetőséget a megjelenítés frissítéséhez addig, amíg a migrálás **Állapota** át nem vált **Befejezve** értékre.

   > [!NOTE]
   > Kiválaszthatja, hogy a tevékenységet, hogy a részletek a gyűjtemény - és adatbázisszintű áttelepítési mérőszámokat.

    ![Befejeződött a tevékenység állapota](media/tutorial-mongodb-to-cosmosdb/dms-activity-completed.png)

## <a name="verify-data-in-cosmos-db"></a>Ellenőrizze az adatokat a Cosmos DB-ben

- Az áttelepítés befejezése után ellenőrizheti, győződjön meg arról, hogy a gyűjteményeknek sikeresen lett migrálva az Azure Cosmos DB-fiókot.

    ![Befejeződött a tevékenység állapota](media/tutorial-mongodb-to-cosmosdb/dms-cosmosdb-data-explorer.png)

## <a name="additional-resources"></a>További források

 * [A cosmos DB szolgáltatás adatai](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>További lépések
- Tekintse át a migrálási útmutató segítséget nyújt a Microsoft további forgatókönyvek [adatbázis-Migrálási útmutató](https://datamigration.microsoft.com/).
