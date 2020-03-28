---
title: 'Az első c relációs adatbázis tervezése #'
description: Ismerje meg, hogyan tervezheti meg első relációs adatbázisát egyetlen adatbázisban az Azure SQL Database-ben C# használatával ADO.NET használatával.
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
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75354945"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-cx23-and-adonet"></a>Oktatóanyag: Tervezzen relációs adatbázist egyetlen adatbázisban az Azure SQL Database C&#x23; és ADO.NET

Az Azure SQL Database egy relációs adatbázis-szolgáltatásként (DBaaS) a Microsoft Cloud (Azure). Ez az oktatóanyag bemutatja, hogyan végezheti el a következőket az Azure Portal, valamint az ADO.NET és a Visual Studio használatával:

> [!div class="checklist"]
> * Egyetlen adatbázis létrehozása az Azure Portalon*
> * Kiszolgálószintű IP-tűzfalszabály beállítása az Azure Portal használatával
> * Csatlakozás az adatbázishoz az ADO.NET és a Visual Studio használatával
> * Táblázatok létrehozása az ADO.NET-tel
> * Adatok beszúrása, frissítése és törlése az ADO.NET-tel
> * Adatok lekérdezése – ADO.NET

*Ha nem rendelkezik Azure-előfizetéssel, a kezdés előtt [hozzon létre egy ingyenes fiókot.](https://azure.microsoft.com/free/)

> [!TIP]
> A következő Microsoft Learn modul segítségével ingyenesen [megtudhatja, hogyan fejleszthet és konfigurálhat egy ASP.NET alkalmazást, amely lekérdezi az Azure SQL-adatbázist,](https://docs.microsoft.com/learn/modules/develop-app-that-queries-azure-sql/)beleértve egy egyszerű adatbázis létrehozását is.

## <a name="prerequisites"></a>Előfeltételek

A [Visual Studio 2019-es](https://www.visualstudio.com/downloads/) vagy újabb verzióinak telepítése.

## <a name="create-a-blank-single-database"></a>Üres önálló adatbázis létrehozása

Az Azure SQL Database egyetlen adatbázisa jön létre a számítási és tárolási erőforrások meghatározott készletével. Az adatbázis egy [Azure-erőforráscsoporton](../azure-resource-manager/management/overview.md) belül jön létre, és [egy adatbázis-kiszolgáló](sql-database-servers.md)használatával történik.

Az üres adatbázis létrehozásához kövesse az alábbi lépéseket.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Az **Új** oldalon válassza az **Adatbázisok** elemet az Azure Marketplace szakaszban, majd kattintson az **SQL Database** elemre a **Kiemelt** szakaszban.

   ![üres adatbázis létrehozása](./media/sql-database-design-first-database/create-empty-database.png)

3. Töltse ki az **SQL Database** űrlapot a következő információkkal, ahogy az az előző képen látható:

    | Beállítás       | Ajánlott érték | Leírás |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Adatbázis neve** | *yourDatabase* | Az érvényes adatbázisneveket az [Adatbázis-azonosítók](/sql/relational-databases/databases/database-identifiers). |
    | **Előfizetés** | *az előfizetés*  | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
    | **Erőforráscsoport** | *yourResourceGroup* | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. |
    | **Forrás kijelölése** | Üres adatbázis | Meghatározza, hogy egy üres adatbázist kell létrehozni. |

4. Kattintson a **Kiszolgáló** gombra meglévő adatbázis-kiszolgáló használatához, vagy hozzon létre és konfiguráljon egy új adatbázis-kiszolgálót. Válasszon ki egy meglévő kiszolgálót, vagy kattintson **az Új kiszolgáló létrehozása** gombra, és töltse ki az Új **kiszolgáló** űrlapot a következő információkkal:

    | Beállítás       | Ajánlott érték | Leírás |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Kiszolgáló neve** | Bármely globálisan egyedi név | Az érvényes kiszolgálónevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/resource-naming) ismertető cikket. |
    | **Kiszolgálórendszergazdai bejelentkezés** | Bármely érvényes név | Az érvényes bejelentkezési nevekről az [Adatbázis-azonosítók](/sql/relational-databases/databases/database-identifiers)oldalon van. |
    | **Jelszó** | Bármely érvényes jelszó | A jelszónak legalább nyolc karakterből kell rendelkeznie, és a következő kategóriák közül három karaktert kell használnia: nagybetűs karakterek, kisbetűs karakterek, számok és nem alfanumerikus karakterek. |
    | **Helyen** | Bármely érvényes hely | A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket. |

    ![adatbázis-kiszolgáló létrehozása](./media/sql-database-design-first-database/create-database-server.png)

5. Kattintson a **Kiválasztás** gombra.
6. Kattintson a **Tarifacsomag** parancsra a szolgáltatási szint, a DTU-k vagy virtuális magok száma és a tárterületméret megadásához. A DIT-ek/virtuális magok és a szolgáltatásszintekhez elérhető tárhelyek számát is megismerheti.

    Miután kiválasztotta a szolgáltatási szintet, a DT-k vagy a virtuális magok számát és a tárterület mennyiségét, kattintson az **Alkalmaz**gombra.

7. Adja meg az üres adatbázis **illesztését** (ebben az oktatóanyagban használja az alapértelmezett értéket). A rendezésekkel kapcsolatos további információkért lásd: [Rendezések](/sql/t-sql/statements/collations)

8. Most, hogy kitöltötte az **SQL Database** űrlapot, kattintson a **Létrehozás** gombra az egyetlen adatbázis kiépítéséhez. Ez a lépés eltarthat néhány percig.

9. Az eszköztáron kattintson az **Értesítések** parancsra az üzembe helyezési folyamat megfigyeléséhez.

   ![értesítés](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>Kiszolgálószintű IP-tűzfalszabály létrehozása

Az SQL Database szolgáltatás létrehoz egy IP-tűzfalat a kiszolgáló szintjén. Ez a tűzfal megakadályozza, hogy külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz és a kiszolgáló bármely adatbázisához, kivéve, ha a tűzfalszabály engedélyezi az IP-címüket a tűzfalon keresztül. Az egyetlen adatbázishoz való külső csatlakozás engedélyezéséhez először hozzá kell adnia egy IP-tűzfalszabályt az IP-címhez (vagy AZ IP-címtartományhoz). Sql [Database-kiszolgálószintű IP-tűzfalszabály](sql-database-firewall-configure.md)létrehozásához kövesse az alábbi lépéseket.

> [!IMPORTANT]
> Az SQL Database szolgáltatás az 1433-as porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni ehhez a szolgáltatáshoz, előfordulhat, hogy a hálózat tűzfala nem engedélyezi az 1433-as porton keresztüli kimenő forgalmat. Ha igen, csak akkor csatlakozhat egyetlen adatbázishoz, ha a rendszergazda megnyitja az 1433-as portot.

1. A telepítés befejezése után kattintson a bal oldali menü **SQL-adatbázisok parancsára,** majd *az SQL-adatbázisok* lapon az Adatbázis **parancsra.** Megnyílik az adatbázis áttekintő lapja, amely a teljesen minősített **kiszolgálónevet** (például *yourserver.database.windows.net)* jeleníti meg, és további konfigurációs lehetőségeket biztosít.

2. Másolja a teljesen minősített kiszolgálónevet a kiszolgálóhoz és az adatbázisokhoz való csatlakozáshoz az SQL Server Management Studio kiszolgálóról.

   ![kiszolgáló neve](./media/sql-database-design-first-database/server-name.png)

3. Kattintson a **Kiszolgálótűzfal beállítása** lehetőségre az eszköztáron. Megnyílik az SQL-adatbáziskiszolgálóhoz tartozó **Tűzfalbeállítások** oldal.

   ![kiszolgálószintű IP-tűzfal szabály](./media/sql-database-design-first-database/server-firewall-rule.png)

4. Kattintson az **eszköztár Ügyfél IP-cím hozzáadása** gombjára, ha az aktuális IP-címet hozzá szeretné adni egy új IP-tűzfalszabályhoz. Az IP-tűzfalszabály egyetlen IP-címhez vagy IP-címtartományhoz is megnyithatja az 1433-as portot.

5. Kattintson a **Mentés** gombra. Az SQL Database-kiszolgálón az aktuális 1433-as IP-címmegnyitásához kiszolgálószintű IP-tűzfalszabály jön létre.

6. Kattintson az **OK** gombra, majd zárja be a **Tűzfalbeállítások** lapot.

Az IP-cím most már áthaladhat az IP tűzfalon. Most már csatlakozhat az egyetlen adatbázishoz az SQL Server Management Studio vagy más választott eszköz használatával. Ügyeljen arra, hogy a korábban létrehozott kiszolgálói rendszergazdai fiókot használja.

> [!IMPORTANT]
> Alapértelmezés szerint az SQL Database IP tűzfalon keresztüli hozzáférés engedélyezve van az összes Azure-szolgáltatáshoz. Kattintson a **KI** gombra ezen az oldalon az összes Azure-szolgáltatás hozzáférésének letiltásához.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban olyan alapvető adatbázis-feladatokat ismertel meg, mint például adatbázis és táblák létrehozása, az adatbázishoz való csatlakozás, az adatok betöltése és lekérdezések futtatása. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adatbázis létrehozása
> * Tűzfalszabály beállítása
> * Csatlakozás az adatbázishoz a [Visual Studio és a C#](sql-database-connect-query-dotnet-visual-studio.md) használatával
> * Táblázatok létrehozása
> * Adatok beszúrása, frissítése, törlése és lekérdezése

Az adatáttelepítésről a következő oktatóanyagra való továbbjutáshoz.

> [!div class="nextstepaction"]
> [SQL Server migrálása felügyelt Azure SQL Database-példányra kapcsolat nélküli üzemmódban, a DMS használatával](../dms/tutorial-sql-server-to-azure-sql.md)
