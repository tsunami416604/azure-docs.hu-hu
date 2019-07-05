---
title: 'Oktatóanyag: Segítségével az Azure Database Migration Service áttelepítheti a MongoDB az Azure Cosmos DB API a mongodb-hez online |} A Microsoft Docs'
description: Ismerje meg, ha át helyszíni mongodb-hez az Azure Cosmos DB API online mongodb-hez készült Azure Database Migration Service használatával.
services: dms
author: HJToland3
ms.author: jtoland
manager: craigg
ms.reviewer: craigg
ms.service: dms
ms.workload: data-services
ms.custom: mvc, tutorial
ms.topic: article
ms.date: 07/04/2019
ms.openlocfilehash: 17f1b36ba5d5b699cce621db3917ef92654047ff
ms.sourcegitcommit: d2785f020e134c3680ca1c8500aa2c0211aa1e24
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/04/2019
ms.locfileid: "67565579"
---
# <a name="tutorial-migrate-mongodb-to-azure-cosmos-dbs-api-for-mongodb-online-using-dms"></a>Oktatóanyag: Azure Cosmos DB API a mongodb-hez MongoDB át online a DMS használatával

Azure Database Migration Service segítségével online (minimális állásidővel) áttelepítése egy adatbázis egy helyi vagy felhőbeli Azure Cosmos DB API a MongoDB-példány a mongodb-hez.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
>
> * Hozzon létre egy Azure Database Migration Service példányát.
> * Migrálási projekt létrehozása az Azure Database Migration Service használatával.
> * A migrálás futtatása.
> * A migrálás monitorozása.
> * Amikor készen áll az áttelepítés befejezéséhez.

Ebben az oktatóanyagban egy adatkészletet, a mongodb-hez az Azure virtuális gépként az Azure Cosmos DB API a mongodb-hez minimális állásidővel által üzemeltetett Azure Database Migration Service segítségével telepít át. Ha egy MongoDB-forráshoz, állítsa be a már nem rendelkezik, tekintse meg a cikket [telepítése és konfigurálása a mongodb-hez az Azure-beli Windows virtuális gép](https://docs.microsoft.com/azure/virtual-machines/windows/install-mongodb).

> [!NOTE]
> Azure Database Migration Service egy online migrálás végrehajtásához használatához hozzon létre egy példányt prémium tarifacsomagban alapján.

> [!IMPORTANT]
> Egy optimális migrálási folyamatba, a Microsoft azt javasolja, létrehozása az Azure Database Migration Service példányát és a céladatbázis Azure ugyanabban a régióban. Adatok áthelyezése a különböző régiók és földrajzi területek számára lelassíthatja a migrálási folyamatot.

[!INCLUDE [online-offline](../../includes/database-migration-service-offline-online.md)]

Ez a cikk ismerteti egy online migrálás MongoDB-ből az Azure Cosmos DB API a mongodb-hez. Offline áttelepítés, lásd: [MongoDB Migrálása az Azure Cosmos DB MongoDB API-kapcsolat nélküli módban a DMS használatával](tutorial-mongodb-cosmos-db.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

* [Hajtsa végre az áttelepítés előtti](../cosmos-db/mongodb-pre-migration.md) lépéseket, például az átviteli sebesség, egy partíciókulcsot és az indexelési házirendet válassza becslése.
* [Hozzon létre egy Azure Cosmos DB API a MongoDB-fiókhoz](https://ms.portal.azure.com/#create/Microsoft.DocumentDB).
* Az Azure Resource Manager üzembe helyezési modell, amely biztosítja, hogy a helyek közötti kapcsolatot a helyszíni adatforrás-kiszolgálók használatával hozzon létre egy Azure virtuális hálózaton (VNet) az Azure Database Migration Service [ExpressRoute](https://docs.microsoft.com/azure/expressroute/expressroute-introduction)vagy [VPN](https://docs.microsoft.com/azure/vpn-gateway/vpn-gateway-about-vpngateways).

    > [!NOTE]
    > Virtuális hálózathoz a telepítés során, ha az ExpressRoute hálózati a Microsoft társviszony-létesítés használja, hozzá a következő szolgáltatás [végpontok](https://docs.microsoft.com/azure/virtual-network/virtual-network-service-endpoints-overview) az alhálózathoz, amelyben üzembe fogja helyezni a szolgáltatást:
    >
    > * Cél adatbázis végpont (például SQL-végpont, Cosmos-DB végpont, és így tovább)
    > * Storage-végpont
    > * Service bus-végpont
    >
    > Ez a konfiguráció szükség, mert az Azure Database Migration Service nem rendelkezik internetkapcsolattal.

* Győződjön meg arról, hogy a virtuális hálózatok közötti hálózati biztonsági csoport (NSG) szabályai nem blokkolják a következő kommunikációs portokat: 53, 443, 445-ös, 9354 és a 10000 – 20000. Az Azure VNet NSG-forgalom szűrése további részletekért tekintse meg a cikket [hálózati biztonsági csoportokkal a hálózati forgalom szűrése](https://docs.microsoft.com/azure/virtual-network/virtual-networks-nsg).
* Nyissa meg a Windows tűzfalat az Azure Database Migration Service a forrás MongoDB-kiszolgáló, amely alapértelmezés szerint a TCP-port 27017 eléréséhez.
* A forrásadatbázis (ok) elé egy tűzfalkészülék használata esetén előfordulhat, hogy hozzá kell tűzfalszabályokban, Azure Database Migration Service az áttelepítéshez a forrásadatbázis (ok) eléréséhez.

## <a name="register-the-microsoftdatamigration-resource-provider"></a>A Microsoft.DataMigration erőforrás-szolgáltató regisztrálása

1. Jelentkezzen be az Azure Portalra, és válassza a **Minden szolgáltatás**, majd az **Előfizetések** elemet.

   ![Portál-előfizetések megtekintése](media/tutorial-mongodb-to-cosmosdb-online/portal-select-subscription1.png)

2. Válassza ki az előfizetést, amelyben az Azure Database Migration Service példányát létre, és válassza ki a kívánt **erőforrás-szolgáltatók**.

    ![Erőforrás-szolgáltatók megtekintése](media/tutorial-mongodb-to-cosmosdb-online/portal-select-resource-provider.png)

3. Keressen a „migration” kifejezésre, majd a **Microsoft.DataMigration** jobb oldalán válassza a **Regisztrálás** elemet.

    ![Erőforrás-szolgáltató regisztrálása](media/tutorial-mongodb-to-cosmosdb-online/portal-register-resource-provider.png)    

## <a name="create-an-instance"></a>Példány létrehozása

1. Az Azure Portalon válassza a + **Erőforrás létrehozása** lehetőséget, keresse meg az Azure Database Migration Service-t, és a legördülő menüben válassza ki az **Azure Database Migration Service**-t.

    ![Azure Piactér](media/tutorial-mongodb-to-cosmosdb-online/portal-marketplace.png)

2. Az **Azure Database Migration Service** képernyőn válassza a **Létrehozás** lehetőséget.

    ![Azure Database Migration Service-példány létrehozása](media/tutorial-mongodb-to-cosmosdb-online/dms-create1.png)
  
3. **A migrálási szolgáltatás létrehozása** képernyőn adja meg a szolgáltatás, az előfizetés és egy új vagy meglévő erőforráscsoport nevét.

4. Válassza ki a helyet, ahol az Azure Database Migration Service példányát létre szeretné.

5. Válasszon ki egy meglévő Vnetet, vagy hozzon létre egy újat.

   A virtuális hálózat az Azure Database Migration Service a forrás MongoDB-példányban, és a cél Azure Cosmos DB-fiók hozzáférést biztosít.

   Virtuális hálózat létrehozása az Azure Portalon kapcsolatos további információkért tekintse meg a cikket [hozzon létre egy virtuális hálózatot az Azure portal használatával](https://aka.ms/DMSVnet).

6. Válassza ki a Termékváltozat a prémium tarifacsomag.

    > [!NOTE]
    > Csak a prémium szint használatakor online áttelepítéseket támogatja. További tájékoztatás a költségekről és a tarifacsomagokról a [díjszabási lapon](https://aka.ms/dms-pricing) olvasható.

    ![Az Azure Database Migration Service-példány beállításainak konfigurálása](media/tutorial-mongodb-to-cosmosdb-online/dms-settings3.png)

7. A szolgáltatás létrehozásához válassza a **Létrehozás** lehetőséget.

## <a name="create-a-migration-project"></a>Migrálási projekt létrehozása

A szolgáltatás létrejötte után keresse meg azt az Azure Portalon, nyissa meg, és hozzon létre egy új migrálási projektet.

1. Az Azure Portalon válassza a **Minden szolgáltatás** lehetőséget, keresse meg az Azure Database Migration Service-t, majd válassza ki az **Azure Database Migration Servicest**.

    ![Keresse meg az Azure Database Migration Service összes példánya](media/tutorial-mongodb-to-cosmosdb-online/dms-search.png)

2. Az a **Azure Database Migration Service** képernyőn, keresse meg a nevet, Azure Database Migration Service-példány létrehozott, majd válassza ki a példányt.

    Felfedezheti azt is megteheti, hogy a keresési ablaktáblán, az Azure Portalon Azure Database Migration service-példányát.

    ![A keresési ablak használatával az Azure Portalon](media/tutorial-mongodb-to-cosmosdb-online/dms-search-portal.png)

3. Válassza a + **Új migrálási projekt** lehetőséget.

4. Az a **új migrálási projekt** lapon adjon meg egy nevet a projektnek a **forráskiszolgáló típusa** szövegbeviteli mezőben válasszon ki **MongoDB**, a a **célkiszolgáló típusa**  szövegbeviteli mezőben válasszon ki **CosmosDB (MongoDB API-t)** , majd **válassza ki a tevékenység típusát**válassza **Online adatáttelepítés [előzetes verzió]** .

    ![Database Migration Service-projekt létrehozása](media/tutorial-mongodb-to-cosmosdb-online/dms-create-project1.png)

5. Válassza ki **mentése**, majd válassza ki **létrehozása és a futási tevékenységet** a projekt létrehozása és futtatása a migrálási tevékenységet.

## <a name="specify-source-details"></a>Forrás adatainak megadása

1. Az a **forrás részletei** képernyőn, adja meg a forráskiszolgáló MongoDB kapcsolati adatait.

    Van egy forrás csatlakozni három mód:
   * **Normál módú**, amely fogad egy teljesen minősített tartománynevét vagy IP-cím, Port száma és kapcsolat hitelesítő adatait.
   * **Kapcsolati karakterlánc mód**, amely fogad egy MongoDB-kapcsolati karakterláncot, a cikkben leírtak szerint [kapcsolati URI karakterláncként](https://docs.mongodb.com/manual/reference/connection-string/).
   * **Az Azure storage-ból adatokat**, amely elfogadja a blobtároló SAS URL-címet. Válassza ki **Blob tartalmazza a BSON memóriaképek** rendelkezik-e a blob-tároló a MongoDB által előállított BSON memóriaképek [bsondump eszköz](https://docs.mongodb.com/manual/reference/program/bsondump/), és a kijelölését, ha a tároló a JSON-fájlokat tartalmazza.

     Ha ezt a lehetőséget választja, lehet, hogy a tárfiók kapcsolati sztringje megjelenik-e a következő formátumban:

     ```
     https://blobnameurl/container?SASKEY
     ```

     Emellett az Azure BLOB storage típusú memóriakép információk alapján, tartsa a következő részletességi szem előtt.

     * A BSON memóriaképek blob-tárolóban lévő adatok bsondump formátumúnak kell lennie, hogy adatfájlokat a formátum collection.bson tartalmazó adatbázisai elnevezett mappákba kerülnek. Metaadat-fájlok (ha van ilyen) a következő formátumban kell elnevezni *gyűjtemény*. metadata.json.

     * JSON-memóriaképek blob-tárolóban lévő fájlokat tartalmazó adatbázisok elnevezett mappákba kell elhelyezni. Minden adatbázis mappában található fájlokat kell helyezni egy almappát "adatok" néven, és elnevezett, a következő formátumban *gyűjtemény*.json. Metaadat-fájlok (ha vannak) kell helyezni egy almappát "metaadatok" nevű, és használja ugyanazt a formátumot, nevű *gyűjtemény*.json. A metaadatok fájlok ugyanebben a formátumban kell lennie, mert a MongoDB bsondump eszköz állítja elő.

    > [!IMPORTANT]
    > Önaláírt tanúsítvány használata a mongo-kiszolgálón nem ajánlott. Azonban ha egy használja, kérjük lépjen kapcsolatba a kiszolgálóra történő **kapcsolati karakterlánc mód** , és győződjön meg arról, hogy rendelkezik-e a kapcsolati karakterlánc ""
    >
    >```
    >&sslVerifyCertificate=false
    >```

    Olyan esetekben, mely DNS-névfeloldás nem lehetséges az IP-címet is használhatja.

   ![Forrás adatainak megadása](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-source1.png)

2. Kattintson a **Mentés** gombra.

   > [!NOTE]
   > A forráskiszolgáló címét az elsődleges címének kell lennie, ha a forrás egy replikakészlethez, és az útválasztó, ha a forrás egy horizontálisan skálázott MongoDB-fürttel. Szilánkokra osztott MongoDB-fürttel az Azure Database Migration Service tud csatlakozni a fürt, amelyre szükség lehet további számítógépeken a tűzfal megnyitása után az egyes szegmensek kell lennie.

## <a name="specify-target-details"></a>Cél adatainak megadása

1. Az a **Migrálási cél részletei** képernyőn, adja meg a cél Azure Cosmos DB-fiókot, amely az előre kiépített Azure Cosmos DB API a MongoDB-fiókhoz, amelyhez a MongoDB-adatait migráláshoz kapcsolati adatait.

    ![Cél adatainak megadása](media/tutorial-mongodb-to-cosmosdb-online/dms-specify-target1.png)

2. Kattintson a **Mentés** gombra.

## <a name="map-to-target-databases"></a>Leképezés céladatbázisokra

1. Az a **leképezés céladatbázisokra** képernyőn, és leképezheti a forrás- és a céladatbázis az áttelepítéshez.

   Ha a céladatbázis adatbázis neve megegyezik a forrás-adatbázis, Azure Database Migration Service alapértelmezés szerint választja ki a céladatbázisban.

   Ha a karakterlánc **létrehozás** jelenik meg az adatbázis neve mellett azt jelzi, hogy az Azure Database Migration Service nem találta meg a céladatbázist, és a szolgáltatás az adatbázist hozza létre.

   Ezen a ponton a migrálás, ha azt szeretné megosztani az adatbázis átviteli sebességet, adjon meg egy átviteli RU. A Cosmos DB az adatbázis szintjén vagy külön-külön az egyes gyűjtemények átviteli telepíthet. Átviteli sebesség mérése a [kérelemegység](https://docs.microsoft.com/azure/cosmos-db/request-units) (RU-k). Tudjon meg többet [Azure Cosmos DB díjszabása](https://azure.microsoft.com/pricing/details/cosmos-db/).

   ![Leképezés céladatbázisokra](media/tutorial-mongodb-to-cosmosdb-online/dms-map-target-databases1.png)

2. Kattintson a **Mentés** gombra.

3. Az a **gyűjteménybeállítás** képernyőn, majd tekintse át a gyűjteményeket, amelyek áttelepíthetők, bontsa ki a gyűjtemények listázása.

   Azure Database Migration Service automatikus összes gyűjteményt, amely a MongoDB forráspéldányon létezik, amely még nem léteznek a cél Azure Cosmos DB-fiókot választja ki. Ha szeretné a gyűjteményeket, amelyek már tartalmazzák az adatok áttelepítéséhez, explicit módon válassza ki a gyűjtemények ezen a képernyőn szeretné.

   Megadhatja a gyűjteményeket szeretne összeállítani kívánt fenntartott egységek számát. A legtöbb esetben az 500-as (1000 legalább a horizontálisan skálázott gyűjteményekben) és 4000 közötti értéket elegendőnek kell lennie. Azure Database Migration Service intelligens alapértelmezett beállítások a gyűjtemény mérete alapján javasol.

    > [!NOTE]
    > Hajtsa végre az adatbázis-migrálás és -gyűjteményt párhuzamosan több példány, Azure Database Migration Service használata, ha szükséges, a Futtatás felgyorsítására.

   Azt is megadhatja a szegmenskulcs kihasználásához [az Azure Cosmos DB particionálási](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview) optimális méretezhetőségre. Ne feledje el áttekinteni a [ajánlott eljárások a szilánkleképezés-/ partíciókulcs kiválasztása](https://docs.microsoft.com/azure/cosmos-db/partitioning-overview#choose-partitionkey). Ha nem rendelkezik egy partíciókulcsot, mindig használhatja **_azonosítója** szegmenskulcsként nagyobb átviteli sebességet.

   ![Gyűjtemények táblák kijelölése](media/tutorial-mongodb-to-cosmosdb-online/dms-collection-setting1.png)

4. Kattintson a **Mentés** gombra.

5. **A migrálás összegzése** képernyő **Tevékenység neve** szövegbeviteli mezőjében adja meg a migrálási tevékenység nevét.

    ![A migrálás összegzése](media/tutorial-mongodb-to-cosmosdb-online/dms-migration-summary1.png)

## <a name="run-the-migration"></a>A migrálás futtatása

* Válassza a **Migrálás futtatása** lehetőséget.

   A migrálási tevékenység ablak megjelenik, és a **állapot** a tevékenység megjelenik.

   ![A tevékenység állapota](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-status1.png)

## <a name="monitor-the-migration"></a>A migrálás monitorozása

* A migrálási tevékenység képernyőn válassza ki a **frissítése** amíg megjelenítheti a képernyőn a **állapot** az áttelepítés állapota **Replaying**.

   > [!NOTE]
   > Kiválaszthatja, hogy a tevékenységet, hogy a részletek a gyűjtemény - és adatbázisszintű áttelepítési mérőszámokat.

   ![Tevékenység állapota visszajátszása](media/tutorial-mongodb-to-cosmosdb-online/dms-activity-replaying.png)

## <a name="verify-data-in-cosmos-db"></a>Ellenőrizze az adatokat a Cosmos DB-ben

1. Módosíthatja a forrás MongoDB-adatbázis.
2. Csatlakozás COSMOS DB-győződjön meg arról, ha a rendszer replikálja az az adatokat a forráskiszolgálóról a mongodb-hez.

    ![Tevékenység állapota visszajátszása](media/tutorial-mongodb-to-cosmosdb-online/dms-verify-data.png)

## <a name="complete-the-migration"></a>A migrálás befejezése

* Után minden dokumentum a forrásból a COSMOS DB cél érhetők el, válassza ki a **Befejezés** az áttelepítés befejezéséhez a migrálási tevékenység helyi menüből.

    Ez a művelet befejezéséhez a függőben lévő módosítások visszajátszásával és az áttelepítés befejezéséhez.

    ![Tevékenység állapota visszajátszása](media/tutorial-mongodb-to-cosmosdb-online/dms-finish-migration.png)

## <a name="post-migration-optimization"></a>Áttelepítés utáni optimalizálása

A mongodb-hez készült Azure Cosmos DB API a MongoDB-adatbázisban tárolt adatok, az áttelepítés után csatlakozhat az Azure Cosmos DB, és az adatok kezeléséhez. Mint az indexelési házirendet optimalizálása további optimalizálás az áttelepítést követő lépések végrehajtását kérhetik, frissítse az alapértelmezett konzisztenciaszint, vagy globális terjesztés konfigurálása az Azure Cosmos DB-fiók. További információkért lásd: a [áttelepítés utáni optimalizálási](../cosmos-db/mongodb-post-migration.md) cikk.

## <a name="additional-resources"></a>További források

* [A cosmos DB szolgáltatás adatai](https://azure.microsoft.com/services/cosmos-db/)

## <a name="next-steps"></a>További lépések

* Tekintse át a migrálási útmutató segítséget nyújt a Microsoft további forgatókönyvek [adatbázis-Migrálási útmutató](https://datamigration.microsoft.com/).
