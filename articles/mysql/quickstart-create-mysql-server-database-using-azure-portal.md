---
title: 'Gyors útmutató: kiszolgáló létrehozása – Azure Portal – Azure Database for MySQL'
description: Ez a cikk lépésről lépésre bemutatja, hogyan hozhat létre egy minta Azure-adatbázist MySQL-kiszolgálóhoz az Azure Portal használatával öt perc alatt.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 7/15/2020
ms.openlocfilehash: cdddd9a90911499421351adf0f41ef90f0e2f9a5
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906555"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-in-the-azure-portal"></a>Rövid útmutató: Azure Database for MySQL-kiszolgáló létrehozása a Azure Portal

A MySQL-hez készült Azure Database egy felügyelt szolgáltatás, amellyel a magas rendelkezésre állású MySQL-adatbázisokat futtathatja, kezelheti és skálázhatja a felhőben. Ez a rövid útmutató bemutatja, hogyan hozhat létre nagyjából öt perc alatt egy Azure Database for MySQL-kiszolgálót az Azure Portal használatával.  

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra
Nyissa meg a webböngészőjét, és keresse fel az [Azure Portalt](https://portal.azure.com/). Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez. Az alapértelmezett nézet a szolgáltatási irányítópult.

## <a name="create-an-azure-database-for-mysql-server"></a>Azure-adatbázis létrehozása MySQL-kiszolgálóhoz
A MySQL-kiszolgálóhoz való Azure-adatbázist [számítási és tárolási erőforrások](./concepts-compute-unit-and-storage.md) egy meghatározott készletével együtt fogja létrehozni. A kiszolgálót egy [Azure-erőforráscsoporton](../azure-resource-manager/management/overview.md) belül hozza létre.

Kövesse az alábbi lépéseket egy, a MySQL-kiszolgálóhoz készült Azure-adatbázis létrehozásához:

1. A portál bal felső sarkában válassza az **erőforrás létrehozása** (+) lehetőséget.

2. Válassza az **adatbázisok**  >  **Azure Database for MySQL**elemet. Megadhatja a **MySQL** kifejezést is a keresőmezőbe a szolgáltatás megtalálásához.

  
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/2_navigate-to-mysql.png" alt-text="Az Azure Database for MySQL menüpont":::

3. Adja meg az alábbi adatokat az új kiszolgálóűrlapon:
    
>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Kiszolgáló létrehozása űrlap":::

**Beállítás** | **Ajánlott érték** | **Mező leírása** 
---|---|---
Előfizetés | Az Ön előfizetése | Válassza ki a kiszolgálóhoz használni kívánt Azure-előfizetést. Ha több előfizetéssel rendelkezik, válassza ki azt az előfizetést, amely részeként fizet az erőforrásért.
Erőforráscsoport | *myresourcegroup* | Adjon meg egy új vagy egy létező erőforráscsoportnevet. Az erőforráscsoport használható az egyetlen projekthez tartozó függőségek rendszerezéséhez.
Kiszolgálónév | Egyedi kiszolgálónév | Adjon meg egy egyedi nevet, amely azonosítja a Azure Database for MySQL-kiszolgálót. Például: "mysqldbserver". A kiszolgáló neve csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. 3–63 karakter hosszúságú lehet.
Adatforrás |*Nincs* | Válassza a *Nincs* lehetőséget egy teljesen új kiszolgáló létrehozásához. (Akkor válassza a *biztonsági mentés* lehetőséget, ha egy meglévő Azure Database for MySQL-kiszolgáló geo biztonsági másolatából hozta létre a kiszolgálót.
Kiszolgáló-rendszergazdai bejelentkezés | myadmin | Adjon meg egy felhasználónevet a kiszolgáló rendszergazdájához. Rendszergazdai felhasználónévként nem használható **azure_superuser**, **Admin**, **Administrator**, **root**, **Guest**vagy **Public** .
Jelszó | *Az Ön választása* | Adjon meg új jelszót a kiszolgálói rendszergazdai fiók számára. A jelszónak 8 – 128 karakter hosszúnak kell lennie, kis-és nagybetűk, számok és nem alfanumerikus karakterek (!, $, #,% stb.) kombinációjával.
Jelszó megerősítése | *Az Ön választása*| Erősítse meg a rendszergazdafiók jelszavát.
Hely | *A felhasználókhoz legközelebb eső régió*| Válassza ki a felhasználókhoz vagy a többi Azure-alkalmazásához legközelebb eső helyet.
Verzió | *A legújabb főverzió*| A legújabb főverzió (ha nincsenek más verzió használatát megkövetelő egyedi igényei).
Számítás és tárolás | **Általános célú**, **5. generációs**, **2 virtuális mag**, **5 GB**, **7 nap**, **Georedundáns** |Az új kiszolgáló számítási, tárolási és biztonsági mentési konfigurációi. Válassza a **kiszolgáló konfigurálása**lehetőséget. Ezután válassza ki a megfelelő díjszabási szintet, és további információért tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/mysql/). Ha engedélyezni szeretné a kiszolgáló biztonsági mentését a földrajzilag redundáns tárolóban, válassza a **földrajzilag redundáns** **lehetőséget a biztonsági mentési redundancia lehetőségei**közül. Válassza az **OK** lehetőséget.

   > [!NOTE]
   > Érdemes lehet az alapszintű díjszabást használni, ha a számítási feladathoz elegendő a könnyű számítás és az I/O. Vegye figyelembe, hogy az alapszintű díjszabásban létrehozott kiszolgálók később nem méretezhetők át általános célú vagy a memóriára optimalizált értékre. 

4. Válassza a **felülvizsgálat + létrehozás** lehetőséget a kiszolgáló kiépítéséhez. Az üzembe helyezés akár 20 percet is igénybe vehet.
   
5. Az eszköztáron válassza az **Értesítések** elemet (a harang ikont) az üzembehelyezési folyamat nyomon követéséhez.
   
Alapértelmezés szerint a következő adatbázisok jönnek létre a kiszolgáló alatt: **information_schema**, **mysql**, **performance_schema** és **sys**.

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása
Alapértelmezés szerint a létrehozott kiszolgáló tűzfallal védett, és nem érhető el nyilvánosan. Ha hozzáférést szeretne adni az IP-címhez, lépjen a kiszolgálói erőforráshoz a Azure Portal, és válassza a **kapcsolatbiztonsági** lehetőséget a kiszolgáló erőforrásának bal oldali menüjében. Nem tudja, hogyan keresheti meg az erőforrást: [Hogyan lehet erőforrást megnyitni](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources).

>[!div class="mx-imgBorder"]
> :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Kapcsolatbiztonság – Tűzfalszabályok":::
   
Most válassza az **aktuális ügyfél IP-címének hozzáadása** lehetőséget, majd válassza a **Mentés**lehetőséget. További IP-címeket adhat hozzá, vagy megadhat egy IP-címtartományt, amely az adott IP-címekről csatlakozik a kiszolgálóhoz. További információ: a [Tűzfalszabályok kezelése Azure Database for MySQL kiszolgálón](./concepts-firewall-rules.md)

> [!NOTE]
> Ellenőrizze, hogy a hálózat engedélyezi-e a kimenő forgalmat az 3306-as porton keresztül, amelyet a Azure Database for MySQL használ a kapcsolódási problémák elkerüléséhez.  

## <a name="connect-to-azure-database-for-mysql-server-using-mysql-command-line-client"></a>Kapcsolódás Azure Database for MySQL kiszolgálóhoz a MySQL parancssori ügyfélprogram használatával
Az [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) vagy a [MySQL Workbench](./connect-workbench.md) közül választhat, ha a helyi környezetből kíván csatlakozni a kiszolgálóhoz. Ebben a rövid útmutatóban **mysql.exe** fog futni [Azure Cloud Shell](https://docs.microsoft.com/azure/cloud-shell/overview) a kiszolgálóhoz való kapcsolódáshoz.

1. Indítsa el Azure Cloud Shell a portálon a bal felső sarokban található Kiemelt ikonra kattintva. Jegyezze fel a kiszolgáló nevét, a kiszolgáló-rendszergazdai bejelentkezési nevet, a jelszót és az előfizetést az újonnan létrehozott kiszolgálóhoz az **Áttekintés** szakasz az alábbi képen látható módon.

    >[!NOTE]
    >Ha első alkalommal indítja el a Cloud shellt, a rendszer rákérdez, hogy létrehoz egy erőforráscsoportot, egy Storage-fiókot. Ez egy egyszeri lépés, és a rendszer automatikusan csatolja az összes munkamenethez. 

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/use-in-cloud-shell.png" alt-text="Portál teljes nézete Cloud Shell":::
2. Futtassa ezt a parancsot Azure Cloud Shell terminálon. Cserélje le az értékeket a tényleges kiszolgálónévre és a rendszergazda felhasználói bejelentkezési nevére. A rendszergazdai felhasználónévhez az alább látható módon kell megadni a "@ \<servername> "-t Azure Database for MySQL  

  ```azurecli-interactive
  mysql --host=mydemoserver.mysql.database.azure.com --user=myadmin@mydemoserver -p 
  ```

  Az alábbi módon néz ki a élmény a Cloud Shell terminálon?
  ```
  Requesting a Cloud Shell.Succeeded.
  Connecting terminal...

  Welcome to Azure Cloud Shell

  Type "az" to use Azure CLI
  Type "help" to learn about Cloud Shell

  user@Azure:~$mysql -h mydemoserver.mysql.database.azure.com -u myadmin@mydemoserver -p
  Enter password:
  Welcome to the MySQL monitor.  Commands end with ; or \g.
  Your MySQL connection id is 64796
  Server version: 5.6.42.0 Source distribution

  Copyright (c) 2000, 2020, Oracle and/or its affiliates. All rights reserved.

  Oracle is a registered trademark of Oracle Corporation and/or its
  affiliates. Other names may be trademarks of their respective
  owners.

  Type 'help;' or '\h' for help. Type '\c' to clear the current input statement.
  mysql>
  ```
3. Ugyanebben a Azure Cloud Shell terminálban hozzon létre egy adatbázis- **vendéget** 
  ```
  mysql> CREATE DATABASE guest;
  Query OK, 1 row affected (0.27 sec)
  ```
4. Váltás az adatbázis **vendégére**
  ```
  mysql> USE guest;
  Database changed 
  ```
5. Írja be ```quit``` a értéket, majd válassza ki az ENTER billentyűt a MySQL kilépéséhez.   

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Sikeresen létrehozott egy Azure Database for MySQL-kiszolgálót egy erőforráscsoporthoz.  Ha nem várható, hogy a jövőben szüksége lenne ezekre az erőforrásokra, törölheti őket az erőforráscsoport törlésével vagy csak a MySQL-kiszolgáló törlésével. Az erőforráscsoport törléséhez kövesse az alábbi lépéseket:
1. A Azure Portal keresse meg és válassza ki az **erőforráscsoportok**elemet. 
2. Az erőforráscsoport listán válassza ki az erőforráscsoport nevét.
3. Az erőforráscsoport áttekintés lapján válassza az **erőforráscsoport törlése**elemet.
4. A megerősítő párbeszédpanelen írja be az erőforráscsoport nevét, majd válassza a **Törlés**lehetőséget.

A kiszolgáló törléséhez kattintson a kiszolgáló **Áttekintés** lapján található **delete (Törlés** ) gombra az alábbi ábrán látható módon:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Erőforrások törlése":::

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
>[Php-alkalmazás létrehozása Windowson MySQL](../app-service/app-service-web-tutorial-php-mysql.md) 
> -sel [Php-alkalmazás létrehozása Linuxon a MySQL](../app-service/containers/tutorial-php-mysql-app.md) 
> -vel [Java-alapú Spring app létrehozása MySQL](https://docs.microsoft.com/azure/developer/java/spring-framework/spring-app-service-e2e?tabs=bash) -sel
