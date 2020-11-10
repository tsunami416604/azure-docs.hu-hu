---
title: 'Gyors útmutató: kiszolgáló létrehozása – Azure Portal – Azure Database for MySQL'
description: Ez a cikk lépésről lépésre bemutatja, hogyan hozhat létre egy minta Azure-adatbázist MySQL-kiszolgálóhoz az Azure Portal használatával öt perc alatt.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 11/04/2020
ms.openlocfilehash: eabc077080e16578857f9ba06e6874441dad07ee
ms.sourcegitcommit: 0dcafc8436a0fe3ba12cb82384d6b69c9a6b9536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2020
ms.locfileid: "94425856"
---
# <a name="quickstart-create-an-azure-database-for-mysql-server-in-the-azure-portal"></a>Rövid útmutató: Azure Database for MySQL-kiszolgáló létrehozása a Azure Portal

A MySQL-hez készült Azure Database egy felügyelt szolgáltatás, amellyel a magas rendelkezésre állású MySQL-adatbázisokat futtathatja, kezelheti és skálázhatja a felhőben. Ez a rövid útmutató bemutatja, hogyan hozhat létre egy Azure Database for MySQL egyetlen kiszolgálót, és hogyan csatlakozhat a kiszolgálóhoz a Azure Portal használatával.

## <a name="prerequisites"></a>Előfeltételek
Azure-előfizetésre van szükség. Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes Azure-fiókot](https://azure.microsoft.com/free/) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-an-azure-database-for-mysql-single-server"></a>Azure Database for MySQL önálló kiszolgáló létrehozása
Lépjen a [Azure Portal](https://portal.azure.com/) egy MySQL egykiszolgálós adatbázis létrehozásához. Keresse meg és válassza ki a *Azure Database for MySQL*.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/find-azure-mysql-in-portal.png" alt-text="Azure Database for MySQL keresése":::

1. Válassza a **Hozzáadás** lehetőséget.

2. A Azure Database for MySQL létrehozása lapon válassza az  **egyetlen kiszolgáló** lehetőséget.
   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/choose-singleserver.png" alt-text="Egyetlen kiszolgáló kiválasztása":::

3. Most adja meg az új egyetlen kiszolgáló alapszintű beállításait.

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/4-create-form.png" alt-text="Kiszolgáló létrehozása űrlap":::

   **Beállítás** | **Ajánlott érték** | **Mező leírása**
   ---|---|---
   Előfizetés | az Ön előfizetése | Válassza ki a kívánt Azure-előfizetést.
   Erőforráscsoport | *myresourcegroup* | Új vagy meglévő erőforráscsoport az előfizetésből.
   A kiszolgáló neve | *mydemoserver* | Adjon meg egy egyedi nevet. A kiszolgálónév csak kisbetűket, számokat és a kötőjel (-) karaktert tartalmazhatja. 3–63 karakter hosszúságú lehet.
   Adatforrás |*Nincs* | Válassza a Nincs lehetőséget egy teljesen új kiszolgáló létrehozásához. Csak akkor válassza a biztonsági mentést, ha egy meglévő kiszolgáló geo-biztonsági másolatából végez visszaállítást.
   Rendszergazdai felhasználónév | *mydemoadmin* | Adja meg a kiszolgáló-rendszergazdai felhasználónevét. Rendszergazdai felhasználónévként nem használható **azure_superuser** , **Admin** , **Administrator** , **root** , **Guest** vagy **Public** .
   Jelszó | az ön jelszava | Új jelszó a kiszolgáló-rendszergazda felhasználó számára. A jelszónak 8 – 128 karakter hosszúnak kell lennie, kis-és nagybetűk, számok és nem alfanumerikus karakterek (!, $, #,% stb.) kombinációjával.
   Hely |a kívánt hely | Válasszon ki egy helyet a legördülő listából.
   Verzió | legújabb főverzió| Használja a legújabb főverziót. [Az összes támogatott verzió](https://docs.microsoft.com/azure/postgresql/concepts-supported-versions) megjelenítése
   Számítás és tárolás | alapértelmezett beállítások használata| Az alapértelmezett díjszabási csomag **4 virtuális mag** és **100 GB** tárhellyel általános célú. A biztonsági másolatok megőrzésének beállítása **7 nap** , földrajzilag redundáns biztonsági mentési lehetőséggel.<br/>Ismerje meg a [díjszabást](https://azure.microsoft.com/pricing/details/mysql/) , és szükség esetén frissítse az alapértelmezett értékeket.

   > [!NOTE]
   > Érdemes lehet az alapszintű díjszabást használni, ha a számítási feladathoz elegendő a könnyű számítás és az I/O. Vegye figyelembe, hogy az alapszintű díjszabásban létrehozott kiszolgálók később nem méretezhetők át általános célú vagy a memóriára optimalizált értékre.

4. Válassza a **felülvizsgálat + létrehozás** lehetőséget a kiszolgáló kiépítéséhez.

5. Várjon, amíg befejeződik a portál oldalának megjelenítése a **központi telepítés befejezéséhez**. Válassza az **Ugrás az erőforráshoz** lehetőséget, hogy az újonnan létrehozott kiszolgáló lapra ugorjon.

   > [!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/deployment-complete.png" alt-text="sikeres üzembe helyezés":::

[Problémák léptek fel? Tudassa velünk](https://aka.ms/mysql-doc-feedback)

## <a name="configure-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály konfigurálása

Alapértelmezés szerint a létrehozott kiszolgáló tűzfallal védett. A kapcsolódáshoz az alábbi lépéseket követve hozzáférést kell biztosítania az IP-címhez:

1. A kiszolgáló erőforrásának bal oldali menüjében lépjen a **Kapcsolódás biztonsága** lehetőségre. Nem tudja, hogyan keresheti meg az erőforrást: [Hogyan lehet erőforrást megnyitni](https://docs.microsoft.com/azure/azure-resource-manager/management/manage-resources-portal#open-resources).

   >[!div class="mx-imgBorder"]
   > :::image type="content" source="./media/quickstart-create-mysql-server-database-using-azure-portal/add-current-ip-firewall.png" alt-text="Kapcsolatbiztonság – Tűzfalszabályok":::

2. Válassza az **aktuális ügyfél IP-címének hozzáadása** lehetőséget, majd a **Mentés** lehetőséget.

   > [!NOTE]
   > Ellenőrizze, hogy a hálózat engedélyezi-e a kimenő forgalmat az 3306-as porton keresztül, amelyet a Azure Database for MySQL használ a kapcsolódási problémák elkerüléséhez.

További IP-címeket adhat hozzá, vagy megadhat egy IP-címtartományt, amely az adott IP-címekről csatlakozik a kiszolgálóhoz. További információ: a [Tűzfalszabályok kezelése Azure Database for MySQL kiszolgálón](./concepts-firewall-rules.md)


[Problémák léptek fel? Tudassa velünk](https://aka.ms/mysql-doc-feedback)

## <a name="connect-to-the-server-with-mysql-command-line-client"></a>Kapcsolódás a kiszolgálóhoz MySQL parancssori ügyféllel
Az [mysql.exe](https://dev.mysql.com/doc/refman/8.0/en/mysql.html) vagy a [MySQL Workbench](./connect-workbench.md) közül választhat, ha a helyi környezetből kíván csatlakozni a kiszolgálóhoz. Ebben a rövid útmutatóban **mysql.exe** fog futni [Azure Cloud Shell](../cloud-shell/overview.md) a kiszolgálóhoz való kapcsolódáshoz.


1. Indítsa el Azure Cloud Shell a portálon a bal felső sarokban található Kiemelt ikonra kattintva. Jegyezze fel a kiszolgáló nevét, a kiszolgáló-rendszergazdai bejelentkezési nevet, a jelszót és az előfizetést az újonnan létrehozott kiszolgálóhoz az **Áttekintés** szakasz az alábbi képen látható módon.

    > [!NOTE]
    > Ha első alkalommal indítja el a Cloud shellt, a rendszer rákérdez, hogy létrehoz egy erőforráscsoportot, egy Storage-fiókot. Ez egy egyszeri lépés, és a rendszer automatikusan csatolja az összes munkamenethez.

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

[Problémák léptek fel? Tudassa velünk](https://aka.ms/mysql-doc-feedback)

## <a name="clean-up-resources"></a>Erőforrások felszabadítása
Sikeresen létrehozott egy Azure Database for MySQL-kiszolgálót egy erőforráscsoporthoz.  Ha nem várható, hogy a jövőben szüksége lenne ezekre az erőforrásokra, törölheti őket az erőforráscsoport törlésével vagy csak a MySQL-kiszolgáló törlésével. Az erőforráscsoport törléséhez kövesse az alábbi lépéseket:
1. A Azure Portal keresse meg és válassza ki az **erőforráscsoportok** elemet.
2. Az erőforráscsoport listán válassza ki az erőforráscsoport nevét.
3. Az erőforráscsoport áttekintés lapján válassza az **erőforráscsoport törlése** elemet.
4. A megerősítő párbeszédpanelen írja be az erőforráscsoport nevét, majd válassza a **Törlés** lehetőséget.

A kiszolgáló törléséhez kattintson a kiszolgáló **Áttekintés** lapján található **delete (Törlés** ) gombra az alábbi ábrán látható módon:
> [!div class="mx-imgBorder"]
> :::image type="content" source="media/quickstart-create-mysql-server-database-using-azure-portal/delete-server.png" alt-text="Erőforrások törlése":::

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
>[PHP-alkalmazás létrehozása Windowson MySQL-sel](../app-service/app-service-web-tutorial-php-mysql.md) <br/>

> [!div class="nextstepaction"]
>[PHP-alkalmazás létrehozása Linuxon a MySQL-vel](../app-service/containers/tutorial-php-mysql-app.md)<br/><br/>

[Nem találja, amit keres? Tudassa velünk.](https://aka.ms/mysql-doc-feedback)
