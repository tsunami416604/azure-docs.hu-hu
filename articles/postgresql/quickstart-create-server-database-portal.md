---
title: 'Gyors útmutató: kiszolgáló létrehozása – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló'
description: Ebben a rövid útmutatóban egy Azure Database for PostgreSQL-kiszolgálót fog létrehozni és felügyelni a Azure Portal használatával.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.custom: mvc
ms.topic: quickstart
ms.date: 06/27/2020
ms.openlocfilehash: 9b3e1916301d6346defd6ebeab2f3784882f2d5f
ms.sourcegitcommit: c5021f2095e25750eb34fd0b866adf5d81d56c3a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88798856"
---
# <a name="quickstart-create-an-azure-database-for-postgresql-server-by-using-the-azure-portal"></a>Gyors útmutató: Azure Database for PostgreSQL-kiszolgáló létrehozása a Azure Portal használatával

Az Azure Database for PostgreSQL egy felügyelt szolgáltatás, amely lehetővé teszi magas rendelkezésre állású PostgreSQL-adatbázisok futtatását, felügyeletét és skálázását a felhőben. Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Azure Database for PostgreSQL-kiszolgálót körülbelül öt perc alatt a Azure Portal használatával.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Nyissa meg a webböngészőjét, és lépjen a [portálra](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="create-an-azure-database-for-postgresql-server"></a>Azure-adatbázis létrehozása PostgreSQL-kiszolgálóhoz

Az Azure Database for PostgreSQL-kiszolgáló a [számítási és tárolási erőforrások](./concepts-pricing-tiers.md) konfigurált készletével együtt jön létre. A kiszolgáló egy [Azure-erőforráscsoporton](../azure-resource-manager/management/overview.md) belül jön létre.

Azure Database for PostgreSQL kiszolgáló létrehozása:

1. Válassza az **erőforrás létrehozása** lehetőséget a portál bal felső sarkában.

2. Válassza az **adatbázisok**  >  **Azure Database for PostgreSQL**elemet.

   > [!div class="mx-imgBorder"]
   > ![A menü "Azure Database for PostgreSQL" megjelenítését bemutató képernyőkép.](./media/quickstart-create-database-portal/1-create-database.png)

3. Válassza az **egykiszolgálós** telepítés lehetőséget.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép a Azure Database for PostgreSQL egykiszolgálós telepítési beállításának kiválasztásáról.](./media/quickstart-create-database-portal/select-deployment-option.png)

4. Töltse ki az **alapismeretek** űrlapot a következő információkkal.

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép, amely az alapvető beállítások lapot mutatja egyetlen kiszolgáló létrehozásához.](./media/quickstart-create-database-portal/create-basics.png)

   Beállítás|Ajánlott érték|Leírás
   ---|---|---
   Előfizetés|Az Ön előfizetésének neve|A kiszolgálóhoz használni kívánt Azure-előfizetés. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amely részeként fizet az erőforrásért.
   Erőforráscsoport|*myResourceGroup*| Egy új erőforráscsoport neve vagy egy meglévő az előfizetéséből.
   Kiszolgálónév |*mydemoserver*|Egy egyedi név, amely az Azure Database for PostgreSQL-kiszolgálót azonosítja. A rendszer hozzáfűzi a *postgres.database.Azure.com* tartománynevet a megadott kiszolgálónévhez. A kiszolgáló neve csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. Az értéknek 3 – 63 karaktert kell tartalmaznia.
   Adatforrás | **Nincs** | Válassza a **nincs** lehetőséget, ha teljesen új kiszolgálót szeretne létrehozni. (Akkor válassza a **biztonsági mentés** lehetőséget, ha egy meglévő Azure Database for PostgreSQL-kiszolgáló geo biztonsági másolatából hozta létre a kiszolgálót.)
   Rendszergazdai Felhasználónév |*myadmin*| A kiszolgálóhoz való csatlakozáshoz használt bejelentkezési fiókja. A rendszergazdai bejelentkezési név nem lehet **azure_superuser**, **azure_pg_admin**, **Admin**, **Administrator**, **root**, **Guest**vagy **Public**. Nem kezdődhet **pg_**.
   Jelszó |Az Ön jelszava| Egy új jelszó a kiszolgálói rendszergazdai fiók számára. 8 – 128 karaktert tartalmazhat a következő kategóriák közül legalább háromból: angol nagybetűs karakterek, angol kisbetűs karakterek, számok (0 – 9) és nem alfanumerikus karakterek (például!, $, #,%).
   Hely|A felhasználókhoz legközelebb eső régió| A felhasználókhoz legközelebb eső hely.
   Verzió|A legújabb főverzió| A legújabb PostgreSQL-főverzió, hacsak nincsenek más konkrét követelmények.
   Számítás + tárolás | **Általános célú**, **5. generációs**, **2 virtuális mag**, **5 GB**, **7 nap**, **Georedundáns** | Az új kiszolgáló számítási, tárolási és biztonsági mentési konfigurációi. Válassza a **kiszolgáló konfigurálása**lehetőséget. Ezután válassza ki a megfelelő árképzési szintet. További információkért tekintse meg a [díjszabás részleteit](https://azure.microsoft.com/pricing/details/postgresql/server/). Ha engedélyezni szeretné a kiszolgáló biztonsági mentését a földrajzilag redundáns tárolóban, válassza a **földrajzilag redundáns** **lehetőséget a biztonsági mentési redundancia lehetőségei**közül. Kattintson az **OK** gombra.

   > [!NOTE]
   > Érdemes lehet az alapszintű díjszabást használni, ha a számítási feladathoz elegendő a könnyű számítás és az I/O. Vegye figyelembe, hogy az alapszintű díjszabásban létrehozott kiszolgálók később nem méretezhetők át általános célú vagy a memóriára optimalizált értékre. 
   
5. Válassza a **felülvizsgálat + létrehozás** lehetőséget a beállítások áttekintéséhez. A kiszolgáló üzembe helyezéséhez válassza a **Létrehozás** lehetőséget. A művelet eltarthat néhány percig.

6. Az eszköztáron válassza az **Értesítések** ikont (csengő) az üzembehelyezési folyamat megfigyeléséhez. Az üzembe helyezés befejezése után válassza az **erőforrás** megnyitása lehetőséget a kiszolgáló **áttekintő** oldalának megnyitásához.

A rendszer létrehoz egy üres adatbázist, a **postgres**. Emellett olyan **azure_maintenance** -adatbázist is talál, amely a felügyelt szolgáltatás folyamatainak elkülönítésére szolgál a felhasználói műveletekben. A **azure_maintenance** adatbázis nem érhető el.

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása
Alapértelmezés szerint a létrehozott kiszolgáló nem érhető el nyilvánosan. Engedélyeket kell adnia az IP-címéhez. Nyissa meg a kiszolgálói erőforrást a Azure Portalban, és válassza a **kapcsolatbiztonsági** lehetőséget a kiszolgáló erőforrásának bal oldali menüjében. Ha nem tudja, hogyan találja meg az erőforrását, tekintse [meg az erőforrások megnyitása](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources)című témakört.

> [!div class="mx-imgBorder"]
> ![A kapcsolatbiztonsági szabályokra vonatkozó tűzfalszabályok bemutató képernyőkép.](./media/quickstart-create-database-portal/add-current-ip-firewall.png)
  
Válassza az **aktuális ügyfél IP-címének hozzáadása**lehetőséget, majd válassza a **Mentés**lehetőséget. További IP-címeket adhat hozzá, vagy megadhat egy IP-címtartományt, amely az IP-címekről a kiszolgálóhoz csatlakozik. További információ: [Tűzfalszabályok a Azure Database for PostgreSQL](./concepts-firewall-rules.md).
   
> [!NOTE]
> A kapcsolódási problémák elkerüléséhez ellenőrizze, hogy a hálózat engedélyezi-e a kimenő forgalmat az 5432-as porton keresztül. Azure Database for PostgreSQL a portot használja.  

## <a name="connect-to-azure-database-for-postgresql-server-by-using-psql"></a>Kapcsolódás Azure Database for PostgreSQL kiszolgálóhoz a psql használatával

Használhatja a [psql](http://postgresguide.com/utilities/psql.html) vagy a [pgAdmin](https://www.pgadmin.org/docs/pgadmin4/latest/connecting.html), amelyek népszerű PostgreSQL-ügyfelek. Ebben a rövid útmutatóban a Azure Portalon belüli [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) psql használatával fogunk kapcsolatot létesíteni.

1. Jegyezze fel a kiszolgáló nevét, a kiszolgáló-rendszergazdai bejelentkezési nevet, a jelszót és az újonnan létrehozott kiszolgáló előfizetés-AZONOSÍTÓját a kiszolgáló **Áttekintés** szakaszában.

2. Nyissa meg Azure Cloud Shell a portálon a bal felső sarokban található ikon kiválasztásával.

   > [!NOTE]
   > Ha első alkalommal nyit meg Cloud Shell, megjelenik egy erőforráscsoport és egy Storage-fiók létrehozásának kérése. Ez egy egyszeri lépés, és a rendszer automatikusan csatolja az összes munkamenethez. 

   > [!div class="mx-imgBorder"]
   > ![Képernyőkép, amely megjeleníti a kiszolgáló adatait és a Azure Cloud Shell megnyitásának ikonját.](media/quickstart-create-database-portal/use-in-cloud-shell.png)

3. Futtassa a következő parancsot a Azure Cloud Shell terminálon. Cserélje le az értékeket a tényleges kiszolgálónévre és a rendszergazda felhasználói bejelentkezési nevére. A következő formátumban használja az üres adatbázis- **postgres** a rendszergazda felhasználóval: `<admin-username>@<servername>` .

   ```azurecli-interactive
   psql --host=mydemoserver.postgres.database.azure.com --port=5432 --username=myadmin@mydemoserver --dbname=postgres
   ```
 
   A tapasztalatok a Cloud Shell terminálon láthatók:
   
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
4. Ugyanebben a Azure Cloud Shell terminálban hozzon létre egy **vendég**nevű adatbázist.

   ```bash
   postgres=> CREATE DATABASE guest;
   ```

5. Kapcsolja a kapcsolatokat az újonnan létrehozott **vendég** adatbázishoz.

   ```bash
   \c guest
   ```
6. Írja be `\q` a értéket, majd válassza ki az ENTER billentyűt a psql bezárásához. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Sikeresen létrehozott egy Azure Database for PostgreSQL-kiszolgálót egy erőforráscsoporthoz. Ha nem szeretné, hogy a jövőben is szükség lenne ezekre az erőforrásokra, törölheti őket az erőforráscsoport vagy a PostgreSQL-kiszolgáló törlésével. 

Az erőforráscsoport törlése:

1. A Azure Portal keresse meg és válassza ki az **erőforráscsoportok**elemet. 
2. Az erőforráscsoport listán válassza ki az erőforráscsoport nevét.
3. Az erőforráscsoport **Áttekintés** lapján válassza az **erőforráscsoport törlése**elemet.
4. A megerősítő párbeszédpanelen adja meg az erőforráscsoport nevét, majd válassza a **Törlés**lehetőséget.

A kiszolgáló törléséhez kattintson a **Törlés** gombra a kiszolgáló **Áttekintés** lapján:

> [!div class="mx-imgBorder"]
> ![A kiszolgáló törlésére szolgáló gombot megjelenítő képernyőkép.](media/quickstart-create-database-portal/12-delete.png)

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálás és importálás használatával](./howto-migrate-using-export-and-import.md)
