---
title: 'Tervezze meg első, a "C" viszonyítási adatbázisát #'
description: Megtudhatja, hogyan tervezheti meg első, egyazon adatbázisát Azure SQL Database C# nyelven a ADO.NET használatával.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.custom: seo-lt-2019
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
ms.date: 07/29/2019
ms.openlocfilehash: 0f1140bbefc7508666e763fcd4f1a04ba48cdfdd
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75354945"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-cx23-and-adonet"></a>Oktatóanyag: a Azure SQL Database C&#x23; és a ADO.NET-ben található, egyetlen adatbázisban lévő viszonyítási adatbázis tervezése

A Azure SQL Database a Microsoft Cloud (Azure) szolgáltatásban a DBaaS (). Ez az oktatóanyag bemutatja, hogyan végezheti el a következőket az Azure Portal, valamint az ADO.NET és a Visual Studio használatával:

> [!div class="checklist"]
> * Egyetlen adatbázis létrehozása a Azure Portal * használatával
> * Kiszolgálói szintű IP-tűzfalszabály beállítása a Azure Portal használatával
> * Csatlakozás az adatbázishoz az ADO.NET és a Visual Studio használatával
> * Táblázatok létrehozása az ADO.NET-tel
> * Adatok beszúrása, frissítése és törlése az ADO.NET-tel
> * Adatok lekérdezése – ADO.NET

* Ha nem rendelkezik Azure-előfizetéssel, a Kezdés előtt [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) .

> [!TIP]
> A következő Microsoft Learn modul segít megtanulni az ingyenes ASP.NET- [alkalmazások fejlesztését és konfigurálását, amely lekérdezi a Azure SQL Database](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/), beleértve egy egyszerű adatbázis létrehozását is.

## <a name="prerequisites"></a>Előfeltételek

A [Visual Studio 2019](https://www.visualstudio.com/downloads/) -es vagy újabb verziójának telepítése.

## <a name="create-a-blank-single-database"></a>Üres önálló adatbázis létrehozása

Azure SQL Databaseben egyetlen adatbázis jön létre a számítási és tárolási erőforrások egy meghatározott készletével. Az adatbázis egy [Azure-erőforráscsoport](../azure-resource-manager/management/overview.md) keretén belül jön létre, és egy [adatbázis-kiszolgáló](sql-database-servers.md)használatával van kezelve.

Az alábbi lépéseket követve hozzon létre egy üres önálló adatbázist.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Az **Új** oldalon válassza az **Adatbázisok** elemet az Azure Marketplace szakaszban, majd kattintson az **SQL Database** elemre a **Kiemelt** szakaszban.

   ![üres adatbázis létrehozása](./media/sql-database-design-first-database/create-empty-database.png)

3. Töltse ki a **SQL Database** űrlapot a következő információkkal az előző képen látható módon:

    | Beállítás       | Ajánlott érték | Leírás |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Adatbázis neve** | *yourDatabase* | Az érvényes adatbázis-nevekkel kapcsolatban lásd: [adatbázis-azonosítók](/sql/relational-databases/databases/database-identifiers). |
    | **Előfizetés** | *yourSubscription*  | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
    | **Erőforráscsoport** | *yourResourceGroup* | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. |
    | **Forrás kiválasztása** | Üres adatbázis | Meghatározza, hogy egy üres adatbázist kell létrehozni. |

4. Kattintson a **kiszolgáló** elemre egy meglévő adatbázis-kiszolgáló használatához, vagy hozzon létre és konfiguráljon egy új adatbázis-kiszolgálót. Válasszon ki egy meglévő kiszolgálót, vagy kattintson az **új kiszolgáló létrehozása** lehetőségre, és töltse ki az **új kiszolgáló** űrlapot a következő információkkal:

    | Beállítás       | Ajánlott érték | Leírás |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Kiszolgáló neve** | Bármely globálisan egyedi név | Az érvényes kiszolgálónevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. |
    | **Kiszolgáló-rendszergazdai bejelentkezés** | Bármely érvényes név | Az érvényes bejelentkezési nevekért lásd: [adatbázis-azonosítók](/sql/relational-databases/databases/database-identifiers). |
    | **Jelszó** | Bármely érvényes jelszó | A jelszónak legalább nyolc karakterből kell állnia, és tartalmaznia kell karaktereket a következő kategóriák közül legalább háromból: nagybetűs karakterek, kisbetűk, számok és nem alfanumerikus karakterek. |
    | **Hely** | Bármely érvényes hely | A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket. |

    ![adatbázis-kiszolgáló létrehozása](./media/sql-database-design-first-database/create-database-server.png)

5. Kattintson a **Kiválasztás** gombra.
6. Kattintson a **Tarifacsomag** parancsra a szolgáltatási szint, a DTU-k vagy virtuális magok száma és a tárterületméret megadásához. Megvizsgálhatja az egyes szolgáltatási szintek számára elérhető DTU/virtuális mag és tárhelyek számát.

    Miután kiválasztotta a szolgáltatási szintet, a DTU vagy a virtuális mag számát, valamint a tárterület méretét, kattintson az **alkalmaz**gombra.

7. Adja meg az üres adatbázis **rendezését** (ebben az oktatóanyagban használja az alapértelmezett értéket). A rendezésekkel kapcsolatos további információkért lásd: [Rendezések](/sql/t-sql/statements/collations)

8. Most, hogy végrehajtotta a **SQL Database** űrlapot, kattintson a **Létrehozás** gombra az önálló adatbázis kiépítéséhez. Ez a lépés néhány percet is igénybe vehet.

9. Az eszköztáron kattintson az **Értesítések** parancsra az üzembe helyezési folyamat megfigyeléséhez.

   ![értesítés](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Kiszolgálói szintű IP-tűzfalszabály létrehozása

A SQL Database szolgáltatás egy IP-tűzfalat hoz létre a kiszolgáló szintjén. Ez a tűzfal megakadályozza, hogy a külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz és a kiszolgálón lévő adatbázisokhoz, kivéve, ha egy tűzfalszabály engedélyezi az IP-címet a tűzfalon keresztül. Az önálló adatbázis külső kapcsolatainak engedélyezéséhez először hozzá kell adnia egy IP-tűzfalszabály-szabályt az IP-címéhez (vagy IP-címtartományt). Az alábbi lépéseket követve hozzon létre egy [SQL Database kiszolgálói szintű IP-tűzfalszabály](sql-database-firewall-configure.md)létrehozását.

> [!IMPORTANT]
> Az SQL Database szolgáltatás a 1433-as porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni a szolgáltatáshoz, előfordulhat, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ha igen, nem tud csatlakozni az önálló adatbázishoz, ha a rendszergazda megnyitja a 1433-es portot.

1. Az üzembe helyezés befejezése után kattintson az **SQL-adatbázisok** elemre a bal oldali menüben, majd kattintson a *yourDatabase* elemre az **SQL-adatbázisok** lapon. Megnyílik az adatbázis áttekintő lapja, amely megjeleníti a teljes **kiszolgálónevet** (például *YourServer.database.Windows.net*), és további konfigurálási lehetőségeket biztosít.

2. Másolja ezt a teljes kiszolgálónevet, hogy a kiszolgálóhoz és az adatbázisokhoz való kapcsolódáshoz SQL Server Management Studio.

   ![kiszolgáló neve](./media/sql-database-design-first-database/server-name.png)

3. Kattintson a **Kiszolgálótűzfal beállítása** lehetőségre az eszköztáron. Megnyílik az SQL-adatbáziskiszolgálóhoz tartozó **Tűzfalbeállítások** oldal.

   ![kiszolgálói szintű IP-tűzfalszabály](./media/sql-database-design-first-database/server-firewall-rule.png)

4. Kattintson az **ügyfél IP-** címének hozzáadása elemre az eszköztáron, és adja hozzá az aktuális IP-címet egy új IP-tűzfalszabály-szabályhoz. Az IP-tűzfalszabály az 1433-as portot egyetlen IP-cím vagy IP-címtartomány számára is megnyithatja.

5. Kattintson a **Save** (Mentés) gombra. A rendszer létrehoz egy kiszolgálói szintű IP-tűzfalszabály-szabályt az aktuális IP-címhez, amely az 1433-es portot nyitja meg a SQL Database kiszolgálón.

6. Kattintson az **OK** gombra, majd zárja be a **Tűzfalbeállítások** lapot.

Az IP-cím mostantól átadható az IP-tűzfalon. Most már csatlakozhat az önálló adatbázishoz SQL Server Management Studio vagy egy tetszőleges eszköz használatával. Ügyeljen arra, hogy a korábban létrehozott kiszolgálói rendszergazdai fiókot használja.

> [!IMPORTANT]
> Alapértelmezés szerint a SQL Database IP-tűzfalon keresztüli hozzáférés engedélyezve van az összes Azure-szolgáltatáshoz. Kattintson a **KI** gombra ezen az oldalon az összes Azure-szolgáltatás hozzáférésének letiltásához.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte az adatbázis alapvető feladatait, például az adatbázisok és táblák létrehozását, az adatbázishoz való kapcsolódást, az adatok betöltését és a lekérdezések futtatását. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adatbázis létrehozása
> * Tűzfalszabály beállítása
> * Csatlakozás az adatbázishoz a [Visual Studio és a C#](sql-database-connect-query-dotnet-visual-studio.md) használatával
> * Táblázatok létrehozása
> * Az adatbeszúrási, frissítési, törlési és lekérdezési művelet

Folytassa a következő oktatóanyaggal, amely az adatáttelepítés megismerését mutatja be.

> [!div class="nextstepaction"]
> [SQL Server migrálása felügyelt Azure SQL Database-példányra kapcsolat nélküli üzemmódban, a DMS használatával](../dms/tutorial-sql-server-to-azure-sql.md)
