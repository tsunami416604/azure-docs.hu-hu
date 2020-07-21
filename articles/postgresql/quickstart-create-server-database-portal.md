---
title: 'Gyors útmutató: kiszolgáló létrehozása – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló'
description: Útmutató egy Azure Database for PostgreSQL-kiszolgáló létrehozásához és kezeléséhez a Azure Portal felhasználói felületének használatával.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/27/2020
ms.openlocfilehash: 9741e00cdcc4907c547ebfcc6f8e7df7a3b32891
ms.sourcegitcommit: 3543d3b4f6c6f496d22ea5f97d8cd2700ac9a481
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2020
ms.locfileid: "86529674"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-in-the-azure-portal"></a>Rövid útmutató: Azure Database for PostgreSQL-kiszolgáló létrehozása az Azure Portalon

Az Azure Database for PostgreSQL egy felügyelt szolgáltatás, amely lehetővé teszi magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, felügyeletét és skálázását a felhőben. Ez a rövid útmutató bemutatja, hogyan hozhat létre nagyjából öt perc alatt egy Azure Database for PostgreSQL-kiszolgálót az Azure Portalon.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Nyissa meg a webböngészőjét, és lépjen a [portálra](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

Az Azure Database for PostgreSQL-kiszolgáló a [számítási és tárolási erőforrások](./concepts-pricing-tiers.md) konfigurált készletével együtt jön létre. A kiszolgáló egy [Azure-erőforráscsoporton](../azure-resource-manager/management/overview.md) belül jön létre.

Azure Database for PostgreSQL-kiszolgáló létrehozásához kövesse az alábbi lépéseket:
1. A portál bal felső sarkában válassza az **erőforrás létrehozása** (+) lehetőséget.

2. Válassza az **adatbázisok**  >  **Azure Database for PostgreSQL**elemet.

   > [!div class="mx-imgBorder"]
   > ![A menü "Azure Database for PostgreSQL"](./media/quickstart-create-database-portal/1-create-database.png)

3. Válassza az **egykiszolgálós** telepítés lehetőséget.

   > [!div class="mx-imgBorder"]
   > ![Azure Database for PostgreSQL kiválasztása – egykiszolgálós üzembe helyezési lehetőség](./media/quickstart-create-database-portal/select-deployment-option.png)

4. Töltse ki az **alapok** űrlapot a következő információkkal:

   > [!div class="mx-imgBorder"]
   > ![Kiszolgáló létrehozása](./media/quickstart-create-database-portal/create-basics.png)

   Beállítás|Ajánlott érték|Leírás
   ---|---|---
   Előfizetés|Az Ön előfizetésének neve|A kiszolgálóhoz használni kívánt Azure-előfizetés. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amely részeként fizet az erőforrásért.
   Erőforráscsoport|*myResourceGroup*| Egy új erőforráscsoport neve vagy egy meglévő az előfizetéséből.
   Kiszolgálónév |*mydemoserver*|Egy egyedi név, amely az Azure Database for PostgreSQL-kiszolgálót azonosítja. A rendszer hozzáfűzi a *postgres.database.azure.com* tartománynevet a megadott kiszolgálónévhez. A kiszolgáló neve csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. Legalább 3, és legfeljebb 63 karakterből állhat.
   Adatforrás | *Nincs* | Válassza a *nincs* lehetőséget, ha teljesen új kiszolgálót szeretne létrehozni. (Ha egy meglévő Azure Database for PostgreSQL-kiszolgáló georedundáns biztonsági mentéséből hoz létre kiszolgálót, válassza a *Biztonsági mentés* lehetőséget).
   Rendszergazdai Felhasználónév |*myadmin*| A kiszolgálóhoz való csatlakozáshoz használt bejelentkezési fiókja. A rendszergazdai bejelentkezési név nem lehet **azure_superuser**, **azure_pg_admin**, **Admin**, **Administrator**, **root**, **Guest**vagy **Public**. Nem kezdődhet **pg_**.
   Jelszó |Az Ön jelszava| Egy új jelszó a kiszolgálói rendszergazdai fiók számára. A jelszó 8–128 karakterből állhat. A jelszónak tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs karakterek, számjegyek (0–9) és nem alfanumerikus karakterek (!, $, #, % stb.).
   Hely|A felhasználókhoz legközelebb eső régió| A felhasználókhoz legközelebb eső hely.
   Verzió|A legújabb főverzió| A legújabb PostgreSQL-főverzió, hacsak nincsenek más konkrét követelmények.
   Számítás + tárolás | **Általános célú**, **5. generációs**, **2 virtuális mag**, **5 GB**, **7 nap**, **Georedundáns** | Az új kiszolgáló számítási, tárolási és biztonsági mentési konfigurációi. Válassza a **kiszolgáló konfigurálása**lehetőséget. Ezután válassza ki a megfelelő díjszabási szintet, további információért lásd: [díjszabás](https://azure.microsoft.com/pricing/details/postgresql/server/). Ha engedélyezni szeretné a kiszolgáló biztonsági mentését a földrajzilag redundáns tárolóban, válassza a **földrajzilag redundáns** **lehetőséget a biztonsági mentési redundancia lehetőségei**közül. Kattintson az **OK** gombra.

   > [!NOTE]
   > Érdemes lehet az alapszintű díjszabást használni, ha a számítási feladathoz elegendő a könnyű számítás és az I/O. Vegye figyelembe, hogy az alapszintű díjszabásban létrehozott kiszolgálók később nem méretezhetők át általános célú vagy a memóriára optimalizált értékre. 
   
5. Válassza a **felülvizsgálat + létrehozás** lehetőséget a beállítások áttekintéséhez. A kiszolgáló üzembe helyezéséhez válassza a **Létrehozás** lehetőséget. A művelet eltarthat néhány percig.

6. Az eszköztáron válassza az **Értesítések** ikont (csengő) az üzembehelyezési folyamat megfigyeléséhez. Az üzembe helyezés befejezése után válassza az **erőforrás** megnyitása lehetőséget a kiszolgáló **Áttekintés** lapján.

A rendszer létrehoz egy üres adatbázist, a **postgres** . **Azure_maintenance** adatbázist is talál, amely a felügyelt szolgáltatás folyamatainak elkülönítésére szolgál a felhasználói műveletektől. **Azure_maintenance** adatbázis nem érhető el.

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása
Alapértelmezés szerint a létrehozott kiszolgáló nem nyilvánosan elérhető, és engedélyt kell adnia az IP-címére. Ha hozzáférést szeretne adni az IP-címhez, lépjen a kiszolgálói erőforráshoz a Azure Portalon, és válassza a **kapcsolatbiztonsági** lehetőséget a kiszolgáló erőforrásának bal oldali menüjében. Ha nem tudja, hogyan találja meg az erőforrását, tekintse [meg az erőforrás megnyitását](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources)ismertető témakört.

> [!div class="mx-imgBorder"]
> ![Kapcsolatbiztonság – Tűzfalszabályok](./media/quickstart-create-database-portal/add-current-ip-firewall.png)
  
Most válassza az **aktuális ügyfél IP-címének hozzáadása** lehetőséget, majd válassza a **Mentés**lehetőséget. További IP-címeket adhat hozzá, vagy megadhat egy IP-címtartományt, amely az adott IP-címekről csatlakozik a kiszolgálóhoz. További információ: [Tűzfalszabályok kezelése](./concepts-firewall-rules.md)
   
> [!NOTE]
> Ellenőrizze, hogy a hálózat engedélyezi-e a kimenő forgalmat az 5432-as porton keresztül, amelyet a Azure Database for PostgreSQL használ a kapcsolódási problémák elkerüléséhez.  

## <a name="connect-to-azure-database-for-postgresql-server-using-psql"></a>Kapcsolódás Azure Database for PostgreSQL kiszolgálóhoz a psql használatával

Használhatja a [psql](http://postgresguide.com/utilities/psql.html) vagy a [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html) -t, amelyek népszerű PostgreSQL-ügyfelek. Ebben a rövid útmutatóban a Azure Portalon belüli [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) psql használatával fogunk kapcsolatot létesíteni.

1. Jegyezze fel a kiszolgáló nevét, a kiszolgáló-rendszergazdai bejelentkezési nevet, a jelszót és az újonnan létrehozott kiszolgáló előfizetés-azonosítóját a kiszolgáló **Áttekintés** szakaszában, ahogy az alábbi képen is látható.

2. Indítsa el Azure Cloud Shell a portálon a lenti képen látható ikon kiválasztásával.

   > [!NOTE]
   > Ha első alkalommal indítja el a Cloud Shell, a rendszer rákérdez, hogy létrehoz egy erőforráscsoportot, egy Storage-fiókot. Ez egy egyszeri lépés, és a rendszer automatikusan csatolja az összes munkamenethez. 

   > [!div class="mx-imgBorder"]
   > ![Azure Cloud Shell megnyitása](media/quickstart-create-database-portal/use-in-cloud-shell.png)

3. Futtassa ezt a parancsot Azure Cloud Shell terminálon. Cserélje le az értékeket a tényleges kiszolgálónévre és a rendszergazda felhasználói bejelentkezési nevére. Használja az üres adatbázis- **postgres** rendszergazdai felhasználóval a következő formátumban: "<admin-username> @ az <servername> alább látható módon Azure Database for PostgreSQL.

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```
 
   Az alábbi módon néz ki a élmény a Cloud Shell terminálon?
   
   ```bash
    Requesting a Cloud Shell.Succeeded.
    Connecting terminal...

    Welcome to Azure Cloud Shell
 
    Type "az" to use Azure CLI
    Type "help" to learn about Cloud Shell

    user@Azure:~$psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
    Password for user myadmin@mydemoserver.postgres.database.azure.com:
    psql (12.2 (Ubuntu 12.2-2.pgdg16.04+1), server 11.6)
    SSL connection (protocol: TLSv1.2, cipher: ECDHE-RSA-AES256-GCM-SHA384, bits: 256, compression: off)
    Type "help" for help.

    postgres=>
    ```
4. Ugyanebben a Azure Cloud Shell terminálban hozzon létre egy adatbázis- **vendéget**
   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. Most, hogy átváltson a kapcsolat az újonnan létrehozott adatbázis- **vendégre**

   ```bash
   \c guest
   ```
6. Írja be `\q` a értéket, majd válassza ki az ENTER billentyűt a psql való kilépéshez. 

## <a name="clean-up-resources"></a>Erőforrások felszabadítása
Sikeresen létrehozott egy Azure Database for PostgreSQL-kiszolgálót egy erőforráscsoporthoz.  Ha nem szeretné, hogy a jövőben is szükség lenne ezekre az erőforrásokra, törölheti őket az erőforráscsoport törlésével vagy csak a PostgreSQL-kiszolgáló törlésével. Az erőforráscsoport törléséhez kövesse az alábbi lépéseket:

1. A Azure Portal keresse meg és válassza ki az **erőforráscsoportok**elemet. 
2. Az erőforráscsoport listán válassza ki az erőforráscsoport nevét.
3. Az erőforráscsoport áttekintés lapján válassza az **erőforráscsoport törlése**elemet.
4. A megerősítő párbeszédpanelen írja be az erőforráscsoport nevét, majd válassza a **Törlés**lehetőséget.

A kiszolgáló törléséhez kattintson a kiszolgáló **Áttekintés** lapján található **delete (Törlés** ) gombra az alábbi ábrán látható módon:
> [!div class="mx-imgBorder"]
> ![Erőforrások törlése](media/quickstart-create-database-portal/12-delete.png)

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./howto-migrate-using-export-and-import.md)
