---
title: 'Oktatóanyag: A MongoDB áttelepítése online a MongoDB-hoz felelős Azure Cosmos DB API-ba'
titleSuffix: Azure Database Migration Service
description: Ismerje meg, hogyan migrálhat a helyszíni MongoDB-ról a MongoDB-hoz készült Azure Cosmos DB API for MongoDB online az Azure Database Migration Service használatával.
services: dms
author: pochiraju
ms.author: rajpo
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: seo-lt-2019
ms.topic: article
ms.date: 09/25/2019
ms.openlocfilehash: 66375d83dca4edef17919e3b493d5e45be37cc40
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78255622"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms"></a>Oktatóanyag: A MongoDB áttelepítése az Azure Cosmos DB API-jába a MongoDB-hoz online a DMS használatával

Az Azure Database Migration Service segítségével online (minimális állásidő) áttelepítést végezhet a MongoDB helyszíni vagy felhőbeli példányából az Azure Cosmos DB MongoDB-hoz készült API-jába.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * Hozzon létre egy Azure Database Migration Service-példányt.
> * Hozzon létre egy áttelepítési projektet az Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * A migrálás monitorozása.
> * Fejezze be az áttelepítést, ha készen áll.

Ebben az oktatóanyagban egy Azure-virtuális gépen üzemeltetett MongoDB-adatkészletet telepít át az Azure Cosmos DB MongoDB-hoz elérhető API-jára minimális állásidő mellett az Azure Database Migration Service használatával. Ha még nincs beállítva MongoDB-forrás, olvassa el a [MongoDB telepítése és konfigurálása Windows virtuális gépen az Azure-ban című témakört.](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb)

> [!NOTE]
> Az Azure Database Migration Service online áttelepítés végrehajtásához létre kell adnia egy példányt a prémium díjszabási szint alapján.

> [!IMPORTANT]
> Az optimális áttelepítési élmény érdekében a Microsoft azt javasolja, hogy hozzon létre egy példányt az Azure Database Migration Service ugyanabban az Azure-régióban, mint a cél-adatbázis. Az adatok régiók vagy földrajzi területek közötti áthelyezése lelassíthatja az áttelepítési folyamatot.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk a MongoDB-ból az Azure Cosmos DB MongoDB-hoz készült API-jához való online áttelepítést ismerteti. Offline áttelepítésesetén a [MongoDB áttelepítése az Azure Cosmos DB MongoDB-hoz elérhető NST API-jába a DMS használatával.](tutorial-mongodb-cosmos-db.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* [Hajtsa végre az áttelepítés előtti](../cosmos-db/mongodb-pre-migration.md) lépéseket, például az átviteli igény becslését, a partíciókulcs kiválasztását és az indexelési házirendet.
* [Hozzon létre egy Azure Cosmos DB API-t a MongoDB-fiókhoz.](https://ms.portal.azure.com/#create/Microsoft.DocumentDB)
* Hozzon létre egy Microsoft Azure virtuális hálózatot az Azure adatbázis-áttelepítési szolgáltatáshoz az Azure Resource Manager telepítési modelljével, amely az [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction) vagy a [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways)használatával biztosít helyek közötti kapcsolatot a helyszíni forráskiszolgálókhoz.

    > [!NOTE]
    > A virtuális hálózat beállítása során, ha az ExpressRoute szolgáltatást hálózati társviszony-létesítéssel használja a Microsofthoz, adja hozzá a következő [szolgáltatásvégpontokat](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) ahhoz az alhálózathoz, amelyben a szolgáltatás ki lesz építve:
    >
    > * Cél adatbázis-végpont (például SQL-végpont, Cosmos DB-végpont és így tovább)
    > * Tárolási végpont
    > * Szolgáltatásbusz végpontja
    >
    > Erre a konfigurációra azért van szükség, mert az Azure Database Migration Service nem rendelkezik internetkapcsolattal.

* Győződjön meg arról, hogy a virtuális hálózati biztonsági csoport (NSG) szabályai nem tiltják le a következő kommunikációs portokat: 53, 443, 445, 9354 és 10000-20000. A virtuális hálózati NSG-forgalom szűrésével kapcsolatos további részleteket a [Hálózati forgalom szűrése hálózati biztonsági csoportokkal című témakörben olvashat.](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg)
* Nyissa meg a Windows tűzfalat, hogy az Azure Database Migration Service hozzáférhessen a forrás MongoDB-kiszolgálóhoz, amely alapértelmezés szerint a 27017-es TCP-port.
* Ha a forrásadatbázis(ok) előtt tűzfalberendezést használ, előfordulhat, hogy tűzfalszabályokat kell hozzáadnia ahhoz, hogy az Azure Database Migration Service hozzáférhessen a forrásadatbázis(ok)hoz az áttelepítéshez.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-mongodb-to-cosmosdb-online/portal-select-subscription1.png)

2. Válassza ki azt az előfizetést, amelyben létre szeretné hozni az Azure Database Migration Service példányát, majd válassza **az Erőforrás-szolgáltatók**lehetőséget.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-mongodb-to-cosmosdb-online/portal-select-resource-provider.png)

3. Keresse meg az áttelepítést, majd jobbra a **Microsoft.DataMigration**programtól, és válassza a **Regisztráció**lehetőséget.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-mongodb-to-cosmosdb-online/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki azt a helyet, ahol létre szeretné hozni az Azure Database Migration Service példányát.

5. Jelöljön ki egy meglévő virtuális hálózatot, vagy hozzon létre egy újat.

   A virtuális hálózat hozzáférést biztosít az Azure Database Migration Service számára a forrás MongoDB-példányhoz és a cél Azure Cosmos DB-fiókhoz.

   A virtuális hálózat Azure Portalon való létrehozásáról további információt a Virtuális hálózat létrehozása az Azure Portal használatával című témakörben [talál.](https://aka.ms/DMSVnet)

6. Válasszon ki egy termékváltozatot a prémium szintű tarifacsomagból.

    > [!NOTE]
    > Az online áttelepítések csak a prémium szint használata esetén támogatottak. További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![Az Azure Database Migration Service összes példányának megkeresése](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. Az **Azure Database Migration Services** képernyőn keresse meg a létrehozott Azure Database Migration Service-példány nevét, majd válassza ki a példányt.

    Az Azure Database Migration szolgáltatáspéldányát az Azure Portal keresési ablaktáblájából is felderítheti.

    ![A Keresés ablaktábla használata az Azure Portalon](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. Válassza a + **Új migrálási projekt** lehetőséget.

4. Az **Új áttelepítési projekt** képernyőn adja meg a projekt nevét, a **Forráskiszolgáló típusa** mezőben válassza a **MongoDB (Célkiszolgáló** **típusa** szövegmező) mezőben a **CosmosDB (MongoDB API)** lehetőséget, majd a **Tevékenység típusának kiválasztása**területen válassza az Online **adatáttelepítés [előzetes]** lehetőséget.

    ![Adatbázis-áttelepítési szolgáltatás projekt létrehozása](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5. Válassza a **Mentés**lehetőséget, majd a **Tevékenység létrehozása és futtatása** lehetőséget a projekt létrehozásához és az áttelepítési tevékenység futtatásához.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. A **Forrás részletei** képernyőn adja meg a forrás MongoDB-kiszolgáló kapcsolatának részleteit.

   > [!IMPORTANT]
   > Az Azure Database Migration Service nem támogatja az Azure Cosmos DB forrásként.

    A forráshoz három mód van:
   * **Normál mód**, amely elfogadja a teljesen minősített tartománynevet vagy IP-címet, portszámot és kapcsolati hitelesítő adatokat.
   * **Kapcsolati karakterlánc mód**, amely a [Kapcsolati karakterlánc URI-formátum](https://docs.mongodb.com/manual/reference/connection-string/)című cikkben leírtak szerint fogad el egy MongoDB-kapcsolati karakterláncot.
   * **Az Azure storage-ból származó adatok,** amely elfogadja a blob tároló SAS URL-címet. Válassza ki **a Blob bson-memóriaképeket tartalmaz,** ha a blob tároló BSON dumps által a MongoDB [bsondump eszköz](https://docs.mongodb.com/manual/reference/program/bsondump/)által termelt, és törölje a jelet, ha a tároló JSON-fájlokat tartalmaz.

     Ha ezt a lehetőséget választja, győződjön meg arról, hogy a tárfiók kapcsolati karakterlánca a következő formátumban jelenik meg:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Emellett az Azure Storage-ban a típusmemória-kiírási adatok alapján tartsa szem előtt a következő részleteket.

     * BSON-memóriaképek esetén a blobtárolóban lévő adatoknak bsondump formátumúnak kell lenniük, így az adatfájlok a collection.bson formátumú adatbázisokat tartalmazó mappákba kerülnek. A metaadatfájlokat (ha vannak ilyenek) a .metadata.json *formátumgyűjtemény*használatával kell elnevezni.

     * JSON-memóriaképek esetén a blobtárolóban lévő fájlokat a tartalmazó adatbázisokról elnevezett mappákba kell helyezni. Az adatfájlokat minden adatbázismappában el kell helyezni egy "data" nevű almappába, és a .json *formátumgyűjtemény*használatával kell elnevezni. A metaadatfájlokat (ha vannak ilyenek) a "metaadatok" nevű almappába kell helyezni, és ugyanazzal a formátummal, a .json *gyűjteménysel*kell elnevezni. A metaadatfájloknak ugyanabban a formátumban kell lenniük, mint a MongoDB bsondump eszköz.

    > [!IMPORTANT]
    > A mongo kiszolgálón nem érdemes önaláírt tanúsítványt használni. Ha azonban használ ilyent, csatlakozzon a kiszolgálóhoz **a kapcsolati karakterlánc mód** használatával, és győződjön meg arról, hogy a kapcsolati karakterlánc ""
    >
    >```
    >&sslVerifyCertificate=false
    >```

    Az IP-cím olyan helyzetekben használható, amikor a DNS-névfeloldás nem lehetséges.

   ![Forrás adatainak megadása](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. Kattintson a **Mentés** gombra.

   > [!NOTE]
   > A forráskiszolgáló címének az elsődleges címnek kell lennie, ha a forrás egy replikakészlet, és az útválasztó, ha a forrás egy szilánkos MongoDB-fürt. Szilánkos MongoDB-fürt esetén az Azure Database Migration Service-nek képesnek kell lennie a fürt egyes szegmenseihez csatlakozni, amelyek több gépen is megkell nyitniuk a tűzfalat.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. A **migrálási cél részletei** képernyőn adja meg a kapcsolat részleteit a cél Azure Cosmos DB-fiók, amely az előre kiépített Azure Cosmos DB API-t MongoDB-fiók, amelyre a MongoDB-adatok áttelepítése.

    ![Cél adatainak megadása](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. Kattintson a **Mentés** gombra.

## <a name="map-to-target-databases"></a>Leképezés céladatbázisokra

1. A **Céladatbázisok leképezése** képernyőn rendelje hozzá a forrást és az áttelepítés céladatbázisát.

   Ha a céladatbázis ugyanazt az adatbázisnevet tartalmazza, mint a forrásadatbázis, az Azure Database Migration Service alapértelmezés szerint kiválasztja a céladatbázist.

   Ha a **Create** karakterlánc jelenik meg az adatbázis neve mellett, azt jelzi, hogy az Azure Database Migration Service nem találta meg a céladatbázist, és a szolgáltatás létrehozza az adatbázist.

   Az áttelepítés ezen pontján, ha megosztási átviteli értéket szeretne az adatbázisban, adjon meg egy átviteli ru-t. A Cosmos DB-ben az adatbázis szintjén vagy az egyes gyűjtemények egyenként is kiépítheti az átviteli szintet. Az átviteli értéket [kérelemegységekben](https://docs.microsoft.com/azure/cosmos-db/request-units) (RT) mérik. További információ az [Azure Cosmos DB díjszabásáról.](https://azure.microsoft.com/pricing/details/cosmos-db/)

   ![Leképezés céladatbázisokra](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. Kattintson a **Mentés** gombra.

3. A **Gyűjtemény beállítás képernyőn** bontsa ki a gyűjtemények listáját, majd tekintse át az áttelepítendő gyűjtemények listáját.

   Az Azure Database Migration Service automatikusan kiválasztja a forrás MongoDB-példányon található összes olyan gyűjteményt, amely nem létezik a cél Azure Cosmos DB-fiókban. Ha újra ki szeretné telepíteni azokat a gyűjteményeket, amelyek már tartalmaznak adatokat, explicit módon ki kell választania a képernyőn megjelenő gyűjteményeket.

   Megadhatja, hogy hány RT-t kíván használni a gyűjteményeket. A legtöbb esetben 500 (1000 minimum szilánkos gyűjtemények) és 4000 közötti érték elegendőnek kell lennie. Az Azure Database Migration Service a gyűjtemény mérete alapján intelligens alapértelmezett értékeket javasol.

    > [!NOTE]
    > Az adatbázis-áttelepítés és -gyűjtés párhuzamosan az Azure Database Migration Service több példányait, ha szükséges, a futtatás felgyorsítása érdekében.

   Egy szegmenskulcsot is megadhat az [Azure Cosmos DB particionálásának](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) előnyeinek kihasználásához az optimális méretezhetőség érdekében. Ügyeljen arra, hogy tekintse át a [shard/partíciókulcs kiválasztásával kapcsolatos gyakorlati tanácsokat.](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey) Ha nem rendelkezik partíciókat, mindig használhatja **_id** a szegmens kulcs a jobb átviteli.

   ![Gyűjtemények tábláinak kijelölése](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. Kattintson a **Mentés** gombra.

5. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét.

    ![Áttelepítés összefoglalása](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

   Megjelenik az áttelepítési tevékenység ablak, és megjelenik a tevékenység **állapota.**

   ![Tevékenység állapota](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>A migrálás monitorozása

* Az áttelepítési tevékenység képernyőn válassza a **Frissítés** lehetőséget a kijelző frissítéséhez, amíg az áttelepítés **állapota** visszajátszásként nem jelenik **meg.**

   > [!NOTE]
   > A Tevékenység kiválasztásával megismerheti az adatbázis- és gyűjteményszintű áttelepítési mutatók részleteit.

   ![Tevékenység állapota visszajátszás](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Adatok ellenőrzése a Cosmos DB-ban

1. Módosítsa a forrás MongoDB adatbázisát.
2. Csatlakozzon a COSMOS DB-hez, és ellenőrizze, hogy az adatok replikálódnak-e a forrás MongoDB-kiszolgálóról.

    ![Tevékenység állapota visszajátszás](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)

## <a name="complete-the-migration"></a>Az áttelepítés befejezése

* Miután a forrásból származó összes dokumentum elérhető a COSMOS DB célon, válassza az áttelepítési tevékenység helyi menüjének **Befejezés** parancsát az áttelepítés befejezéséhez.

    Ez a művelet befejezi az összes függőben lévő módosítás visszajátszását, és befejezi az áttelepítést.

    ![Tevékenység állapota visszajátszás](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="post-migration-optimization"></a>Áttelepítés utáni optimalizálás

Miután áttelepítette a MongoDB-adatbázisban tárolt adatokat az Azure Cosmos DB MongoDB-hoz kapcsolódó API-jába, csatlakozhat az Azure Cosmos DB-hez, és kezelheti az adatokat. Az áttelepítés utáni optimalizálási lépéseket is végrehajthat, például optimalizálhatja az indexelési szabályzatot, frissítheti az alapértelmezett konzisztenciaszintet, vagy konfigurálhatja a globális disztribúciót az Azure Cosmos DB-fiókjához. További információt az [áttelepítés utáni optimalizálásról](../cosmos-db/mongodb-post-migration.md) szóló cikkben talál.

## <a name="additional-resources"></a>További források

* [Cosmos DB szolgáltatás információ](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>További lépések

* Tekintse át az áttelepítési útmutatót a Microsoft [adatbázis-áttelepítési útmutatójában](https://datamigration.microsoft.com/)található további forgatókönyvekért.
