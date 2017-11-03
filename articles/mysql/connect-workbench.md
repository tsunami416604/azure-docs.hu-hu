---
title: "Azure-adatbázis csatlakoztatni a MySQL a MySQL munkaterület |} Microsoft Docs"
description: "A gyors üzembe helyezés lépései MySQL munkaterület való kapcsolódás és lekérdezés az Azure-adatbázis adatait a MySQL használatára."
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
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="azure-database-for-mysql-use-mysql-workbench-to-connect-and-query-data"></a>MySQL az Azure-adatbázishoz: MySQL-munkaterület használata kapcsolódási és lekérdezési adatok
A gyors üzembe helyezés mutatja be a MySQL munkaterület alkalmazással MySQL egy Azure-adatbázishoz való kapcsolódáshoz. 

## <a name="prerequisites"></a>Előfeltételek
Ebben a rövid útmutatóban a következő útmutatók valamelyikében létrehozott erőforrásokat használunk kiindulási pontként:
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure Portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)
- [Azure-adatbázis létrehozása MySQL-kiszolgálóhoz az Azure CLI használatával](./quickstart-create-mysql-server-database-using-azure-cli.md)

## <a name="install-mysql-workbench"></a>Telepítse a MySQL munkaterület
Töltse le és telepítse a MySQL munkaterület a számítógépen található [a MySQL webhely](https://dev.mysql.com/downloads/workbench/).

## <a name="get-connection-information"></a>Kapcsolatadatok lekérése
Kérje le a MySQL-hez készült Azure Database-hez való csatlakozáshoz szükséges kapcsolatadatokat. Ehhez szükség lesz a teljes kiszolgálónévre és bejelentkezési hitelesítő adatokra.

1. Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

2. Azure-portálon a bal oldali menüben kattintson a **összes erőforrás**, majd keresse meg a létrehozott kiszolgáló (például **myserver4demo**).

3. Kattintson a kiszolgálónévre.

4. Válassza ki a kiszolgálót **tulajdonságok** lapon, majd jegyezze fel a **kiszolgálónév** és **kiszolgálói rendszergazda bejelentkezési név**.

 ![Azure-adatbázis MySQL kiszolgálónév](./media/connect-workbench/1-server-properties-name-login.png)
 
5. Ha elfelejti a kiszolgálói bejelentkezési adatok, navigáljon a **áttekintése** lapon megtekintéséhez a rendszergazdai bejelentkezési nevet, és ha szükséges a jelszó alaphelyzetbe állítása.

## <a name="connect-to-the-server-by-using-mysql-workbench"></a>MySQL-munkaterület használatával kapcsolódni a kiszolgálóhoz 
Csatlakozás Azure MySQL-kiszolgálóhoz a grafikus felhasználói Felülettel eszközzel MySQL munkaterület:

1.  A MySQL munkaterület lkalmazás a számítógépen. 

2.  A **új kapcsolat beállítása** párbeszédpanelen adja meg a következő adatokat a **paraméterek** lapon:

    ![új kapcsolat beállítása](./media/connect-workbench/2-setup-new-connection.png)

    | **Beállítás** | **Ajánlott érték** | **Mező leírása** |
    |---|---|---|
    |   Kapcsolat neve | Bemutató kapcsolat | Adjon meg egy címkét a kapcsolathoz. |
    | Kapcsolati módszer | Standard (TCP/IP) | A Standard (TCP/IP) elégséges. |
    | Gazdanév | *kiszolgáló neve* | Adja meg azt a kiszolgálónevet, amelyet korábban a MySQL-hez készült Azure-adatbázis létrehozásakor használt. Az itt látható példakiszolgáló a myserver4demo.mysql.database.azure.com. Használja a teljes tartománynevet (\*.mysql.database.azure.com), ahogyan az a példában látható. Ha nem emlékszik a kiszolgáló nevére, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit.  |
    | Port | 3306 | A MySQL-hez készült Azure-adatbázishoz való csatlakozáskor mindig a 3306-os portot használja. |
    | Felhasználónév |  *kiszolgáló-rendszergazdai bejelentkezési név* | Írja be a kiszolgáló-rendszergazdai bejelentkezési felhasználónevet, amelyet korábban a MySQL-hez készült Azure-adatbázis létrehozásakor adott meg. A példában szereplő felhasználónév a következő: myadmin@myserver4demo. Ha nem emlékszik a felhasználónévre, a kapcsolati adatok lekéréséhez kövesse az előző szakasz lépéseit. A formátum *username@servername*.
    | Jelszó | az ön jelszava | Kattintson a **Store tárolóban...**  gombra kattintva mentse a jelszót. |

3.   Kattintson a **Kapcsolat tesztelése** lehetőségre, hogy tesztelje, minden paraméter helyesen lett-e konfigurálva. 

4.   Kattintson a **OK** a kapcsolat mentéséhez. 

5.   A lista tartalmazza a **MySQL kapcsolatok**, és kattintson a kiszolgáló megfelelő csempére, majd várja meg a kapcsolatot a.

        Új SQL lapon nyílik meg egy üres szerkesztő, ahová beírhatja a lekérdezéseket.
    
        > [!NOTE]
        > Alapértelmezés szerint az SSL-kapcsolat biztonsági szükséges, és a MySQL-kiszolgálóhoz tartozó Azure-adatbázis érvényes. Általában nem SSL-tanúsítványokkal nincs szükség további konfigurációra a MySQL-munkaterület a kiszolgálóhoz való csatlakozás. Az SSL további információkért lásd: [konfigurálása az SSL-kapcsolatot az alkalmazásokhoz való biztonságos kapcsolódás Azure-adatbázis a MySQL](./howto-configure-ssl.md).  Ha le kell tiltania az SSL, látogasson el az Azure-portálon, és a kapcsolat biztonsági lap a kényszerítéséhez SSL-kapcsolat váltógomb letiltásához kattintson.

## <a name="create-a-table-insert-data-read-data-update-data-delete-data"></a>Hozzon létre egy táblát, adatokat beszúrni, olvassa el az adatok, adatainak frissítése, adatok törlése
1. Másolja, és a mintakódot az SQL mutatja be a mintaadatokat egy üres SQL fülre.

    Ez a kód egy quickstartdb nevű üres adatbázist hoz létre, és majd a készlet nevű minta táblázat létrehozása. Ezután néhány sor beilleszti beolvassa a sorokat. Módosítja az adatokat az update utasításban a, és olvassa be újra a sorokat. Végül azt töröl egy sort, és ezután beolvassa újra a sorokat.
    
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

    A képernyőfelvételen látható az SQL-kódot SQL munkaterület és a kimeneti után futott.
    
    ![MySQL munkaterület SQL lapon SQL példakód futtatása](media/connect-workbench/3-workbench-sql-tab.png)

2. Mintát szeretné futtatni az SQL-kódot, kattintson az eszköztár lightening bolt ikonra a **SQL fájl** fülre.
3. Figyelje meg, a három lapokra eredményez a **eredmény rács** szakasz az oldal közepén. 
4. Figyelje meg a **kimeneti** lista az oldal alján. Minden parancs állapota látható. 

Most csatlakozott az Azure Database a MySQL MySQL munkaterület használatával, és a felhasználói adatokat az SQL-nyelv használatával.

## <a name="next-steps"></a>Következő lépések
> [!div class="nextstepaction"]
> [Adatbázis migrálása exportálással és importálással](./concepts-migrate-import-export.md)
