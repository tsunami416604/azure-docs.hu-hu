---
title: "Csatlakozás az Azure Database for MySQL szolgáltatáshoz a MySQL Workbench segítségével | Microsoft Docs"
description: "Ez a rövid útmutató bemutatja, hogy a MySQL Workbench használatával hogyan csatlakozhat az Azure Database for MySQL szolgáltatáshoz, és hogyan kérdezhet le adatokat."
services: mysql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: seanli1988
ms.service: mysql
ms.custom: mvc
ms.topic: quickstart
ms.date: 09/22/2017
ms.openlocfilehash: 024db86b8760c8edb8347679eec6c68ceab3cd35
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-mysql-use-mysql-workbench-to-connect-and-query-data"></a>Azure Database for MySQL: Csatlakozás és adatlekérdezés a MySQL Workbench használatával
Ebben a rövid útmutatóban azt szemléltetjük, hogy miként lehet a MySQL Workbench alkalmazás használatával csatlakozni egy Azure Database for MySQL kiszolgálóhoz. 

## <a name="prerequisites"></a>Előfeltételek
Ebben a rövid útmutatóban a következő útmutatók valamelyikében létrehozott erőforrásokat használunk kiindulási pontként:
- [Azure Database for MySQL kiszolgáló létrehozása az Azure Portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure Database for MySQL kiszolgáló létrehozása az Azure CLI használatával](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-mysql-workbench"></a>A MySQL Workbench telepítése
Töltse le és telepítse a MySQL Workbench alkalmazást a számítógépére [a MySQL webhelyről](https://dev.mysql.com/downloads/workbench/).

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le az Azure Database for MySQL-csatlakozáshoz szükséges kapcsolatadatokat. Ehhez szükség lesz a teljes kiszolgálónévre és bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Az Azure Portal bal oldali menüjében kattintson a **Minden erőforrás** lehetőségre, és keressen rá a létrehozott kiszolgálóra (például **myserver4demo**).

3. Kattintson a kiszolgálónévre.

4. Válassza a kiszolgáló **Tulajdonságok** lapját, és jegyezze fel a **kiszolgálónevet** és a **kiszolgálói rendszergazdai bejelentkezési nevet**.

 ![Azure Database for MySQL-kiszolgáló neve](./media/connect-workbench/1-server-properties-name-login.png)
 
5. Amennyiben elfelejtette a kiszolgáló bejelentkezési adatait, lépjen az **Áttekintés** oldalra, ahol kikeresheti a kiszolgáló rendszergazdájának bejelentkezési nevét, valamint szükség esetén új jelszót kérhet.

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>Csatlakozás a kiszolgálóhoz a MySQL Workbench használatával 
Kapcsolódás az Azure MySQL-kiszolgálóhoz a MySQL Workbench GUI eszköz használatával:

1.  Indítsa el a MySQL Workbench alkalmazást a számítógépen. 

2.  A **Setup New Connection** (Új kapcsolat beállítása) párbeszédpanelen adja meg a következő információkat a **Parameters** (Paraméterek) lapon:

    ![új kapcsolat beállítása](./media/connect-workbench/2-setup-new-connection.png)

    | **Beállítás** | **Ajánlott érték** | **Mező leírása** |
    |---|---|---|
    |   Connection Name (Kapcsolat neve) | Demo Connection | Adjon meg egy címkét a kapcsolathoz. |
    | Connection Method (Csatlakozási módszer) | Standard (TCP/IP) | A Standard (TCP/IP) megfelelő. |
    | Hostname (Gazdanév) | *kiszolgáló neve* | Adja meg azt a kiszolgálónevet, amelyet korábban az Azure Database for MySQL létrehozásakor használt. Az itt látható példakiszolgáló a myserver4demo.mysql.database.azure.com. Használja a teljes tartománynevet (\*.mysql.database.azure.com), ahogyan az a példában látható. Ha nem emlékszik a kiszolgáló nevére, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit.  |
    | Port | 3306 | Az Azure Database for MySQL kiszolgálóhoz való csatlakozáskor mindig a 3306-os portot használja. |
    | Username (Felhasználónév) |  *kiszolgáló-rendszergazdai bejelentkezési név* | Írja be a kiszolgáló-rendszergazdai bejelentkezési felhasználónevet, amelyet korábban az Azure Database for MySQL létrehozásakor adott meg. A példában szereplő felhasználónév a következő: myadmin@myserver4demo. Ha nem emlékszik a felhasználónévre, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit. A formátum *username@servername*.
    | Password (Jelszó) | az ön jelszava | A jelszó mentéséhez kattintson a **Store in Vault...** (Tárolás a tárolóban...) gombra. |

3.   Kattintson a **Test Connection** (Kapcsolat tesztelése) lehetőségre, hogy tesztelje, minden paraméter helyesen lett-e konfigurálva. 

4.   A kapcsolat mentéséhez kattintson az **OK** gombra. 

5.   A **MySQL Connections** (MySQL kapcsolatok) listában kattintson a kiszolgáló csempéjére, majd várja meg a kapcsolat létrejöttét.

        Megnyílik egy új SQL lap egy üres szerkesztővel, ahol beírhatja a lekérdezéseket.
    
        > [!NOTE]
        > Alapértelmezés szerint kötelezően SSL-kapcsolatbiztonságra van szükség az Azure Database for MySQL kiszolgálón. Általában nincs szükség az SSL-tanúsítványok további konfigurációjára ahhoz, hogy a MySQL Workbench csatlakozzon a kiszolgálóhoz. Az SSL-lel kapcsolatos további információkat [az SSL-kapcsolatok alkalmazásban való konfigurálását ismertető, Azure Database for MySQL kiszolgálóra vonatkozó](./howto-configure-ssl.md) cikkben talál.  Ha le kell tiltania az SSL-t, látogasson el az Azure Portalra, és a Kapcsolatbiztonság lapon használja az SSL-kapcsolat kikényszerítése kapcsológombot a letiltásához.

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>Tábla létrehozása, adatok beszúrása, adatok olvasása, adatok frissítése, adatok törlése
1. Másolja és illessze be az SQL-mintakódot egy üres SQL-lapra néhány mintaadat bemutatása érdekében.

    Ez a kód a quickstartdb nevű üres adatbázist hozza létre, majd létrehoz egy inventory nevű mintatáblát. Beilleszt néhány sort, majd beolvassa a sorokat. Egy update utasítással módosítja az adatokat, és ismét beolvassa a sorokat. Végül töröl egy sort, majd ismét beolvassa a sorokat.
    
    ```sql
    -- Create a database
    -- DROP DATABASE IF EXISTS quickstartdb;
    CREATE DATABASE quickstartdb;
    USE quickstartdb;
    
    -- Create a table and insert rows
    DROP TABLE IF EXISTS inventory;
    CREATE TABLE inventory (id serial PRIMARY KEY, name VARCHAR(50), quantity INTEGER);
    INSERT INTO inventory (name, quantity) VALUES ('banana', 150);
    INSERT INTO inventory (name, quantity) VALUES ('orange', 154);
    INSERT INTO inventory (name, quantity) VALUES ('apple', 100);
    
    -- Read
    SELECT * FROM inventory;
    
    -- Update
    UPDATE inventory SET quantity = 200 WHERE id = 1;
    SELECT * FROM inventory;
    
    -- Delete
    DELETE FROM inventory WHERE id = 2;
    SELECT * FROM inventory;
    ```

    A képernyőkép az SQL-példakódot és a futtatás utáni kimenetet mutatja az SQL Workbench-ben.
    
    ![MySQL Workbench SQL lap SQL-mintakód futtatásához](media/connect-workbench/3-workbench-sql-tab.png)

2. A SQL-mintakód futtatásához kattintson az **SQL File** (SQL-fájl) lévő eszköztár villám ikonjára.
3. Figyelje meg az oldal közepén, a **Result Grid** (Eredménytáblázat) szakaszban lévő többlapos eredményeket. 
4. Figyelje meg az oldal alján lévő **Output** (Kimenet) listát. Láthatja az egyes parancsok állapotát. 

A MySQL Workbench használatával csatlakozott az Azure Database for MySQL kiszolgálóhoz, és az SQL nyelv használatával lekérdezte az adatokat.

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./concepts-migrate-import-export.md)
