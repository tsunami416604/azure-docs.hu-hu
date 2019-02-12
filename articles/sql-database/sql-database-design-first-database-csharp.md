---
title: – Az első relációs adatbázis megtervezésével C# – Azure SQL Database |} A Microsoft Docs
description: Ismerje meg, hogy egyetlen Azure SQL Database-adatbázisban az első relációs adatbázis megtervezésével C# ADO.NET használatával.
services: sql-database
ms.service: sql-database
ms.subservice: development
ms.topic: tutorial
author: MightyPen
ms.author: genemi
ms.reviewer: carlrab
manager: craigg-msft
ms.date: 02/08/2019
ms.openlocfilehash: ce46a6b8d4e2bc57625f9202349718dfbaedc660
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "55995683"
---
# <a name="tutorial-design-a-relational-database-in-a-single-database-within-azure-sql-database-cx23-and-adonet"></a>Oktatóanyag: Az Azure SQL Database C belül egy önálló adatbázis relációs adatbázis tervezése&#x23; és az ADO.NET

Az Azure SQL Database egy relációs adatbázis-a-szolgáltatás (DBaaS) a Microsoft cloud (Azure). Ez az oktatóanyag bemutatja, hogyan végezheti el a következőket az Azure Portal, valamint az ADO.NET és a Visual Studio használatával:

> [!div class="checklist"]
> * Hozzon létre egy önálló adatbázis az Azure portal *
> * Az Azure portal használatával kiszolgálószintű IP tűzfalszabály beállítása
> * Csatlakozás az adatbázishoz az ADO.NET és a Visual Studio használatával
> * Táblázatok létrehozása az ADO.NET-tel
> * Adatok beszúrása, frissítése és törlése az ADO.NET-tel
> * Adatok lekérdezése – ADO.NET

Ha a nem rendelkezik Azure-előfizetéssel, [hozzon létre egy ingyenes fiókot](https://azure.microsoft.com/free/) megkezdése előtt.

## <a name="prerequisites"></a>Előfeltételek

Egy telepített [Visual Studio 2017](https://www.visualstudio.com/downloads/)

## <a name="create-a-blank-single-database"></a>Üres önálló adatbázis létrehozása

Egy Azure SQL Database-adatbázis számítási és tárolási erőforrások egy meghatározott készletével együtt jön létre. Az adatbázis létrejön egy [Azure-erőforráscsoport](../azure-resource-manager/resource-group-overview.md) és használatával kezelhetők egy [adatbázis-kiszolgáló](sql-database-servers.md).

Kövesse az alábbi lépéseket egy üres önálló adatbázis létrehozása.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.
2. Az **Új** oldalon válassza az **Adatbázisok** elemet az Azure Marketplace szakaszban, majd kattintson az **SQL Database** elemre a **Kiemelt** szakaszban.

   ![üres adatbázis létrehozása](./media/sql-database-design-first-database/create-empty-database.png)

3. Töltse ki a **SQL Database** űrlapon az alábbi információkkal az előző képen látható módon:

    | Beállítás       | Ajánlott érték | Leírás |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Adatbázis neve** | *yourDatabase* | Érvényes adatbázisnevekkel kapcsolatban lásd: [adatbázis-azonosítókat](/sql/relational-databases/databases/database-identifiers). |
    | **Előfizetés** | *yourSubscription*  | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
    | **Erőforráscsoport** | *yourResourceGroup* | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
    | **Forrás kiválasztása** | Üres adatbázis | Meghatározza, hogy egy üres adatbázist kell létrehozni. |

4. Kattintson a **kiszolgáló** használja a meglévő adatbázis-kiszolgáló és a egy új adatbázis-kiszolgáló konfigurálása és létrehozása. Válasszon egy meglévő kiszolgálót, vagy kattintson a **hozzon létre egy új kiszolgálót** , és töltse ki a **új kiszolgáló** űrlapján az alábbi adatokat:

    | Beállítás       | Ajánlott érték | Leírás |
    | ------------ | ------------------ | ------------------------------------------------- |
    | **Kiszolgálónév** | Bármely globálisan egyedi név | Az érvényes kiszolgálónevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
    | **Kiszolgálói rendszergazdai bejelentkezés** | Bármely érvényes név | Az érvényes bejelentkezési nevekkel kapcsolatban lásd: [adatbázis-azonosítókat](/sql/relational-databases/databases/database-identifiers). |
    | **Jelszó** | Bármely érvényes jelszó | A jelszó legalább nyolc karakter hosszúságúnak kell lennie, és kell használnia a karaktereket a következő kategóriák közül legalább háromból: nagybetűs karakterek, kisbetűs karakterek, számok és nem alfanumerikus karakterek. |
    | **Hely** | Bármely érvényes hely | A régiókkal kapcsolatos információkért lásd [az Azure régióit](https://azure.microsoft.com/regions/) ismertető cikket. |

    ![adatbázis-kiszolgáló létrehozása](./media/sql-database-design-first-database/create-database-server.png)

5. Kattintson a **Kiválasztás** gombra.
6. Kattintson a **Tarifacsomag** parancsra a szolgáltatásszint, a DTU-k vagy virtuális magok száma és a tárterületméret megadásához. Előfordulhat, hogy fedezze fel a lehetőségeket a dtu-k/virtuális magok és a storage, amely az egyes szolgáltatásszinteken elérhető.

    A szolgáltatási rétegben kiválasztása után a dtu-k vagy virtuális magok számát, és a tárterületméret kattintson **alkalmaz**.

7. Adjon meg egy **rendezést** az üres adatbázis (a jelen oktatóanyag esetében használja az alapértelmezett érték). A rendezésekkel kapcsolatos további információkért lásd: [Rendezések](/sql/t-sql/statements/collations)

8. Most, hogy Ön teljesítette a **SQL Database** alkotnak, kattintson a **létrehozás** az egyetlen adatbázis létrehozásához. Ez a lépés több percet is igénybe vehet.

9. Az eszköztáron kattintson az **Értesítések** parancsra az üzembe helyezési folyamat megfigyeléséhez.

   ![értesítés](./media/sql-database-design-first-database/notification.png)

## <a name="create-a-server-level-ip-firewall-rule"></a>IP-kiszolgálószintű tűzfalszabály létrehozása

Az SQL Database szolgáltatás a kiszolgáló szintjén hoz létre egy IP-tűzfalon. Ez a tűzfal megakadályozza, hogy külső alkalmazások és eszközök csatlakozzanak a kiszolgáló és a kiszolgálón lévő adatbázisokhoz, kivéve, ha egy tűzfalszabály lehetővé teszi, hogy azok a tűzfalon keresztül IP. Ahhoz, hogy a külső kapcsolatokat az egyetlen adatbázishoz, először hozzá kell adnia egy IP-tűzfalszabály, az IP-cím (vagy IP-címtartomány). Kövesse az alábbi lépéseket követve létrehozhat egy [SQL Database kiszolgálószintű IP-tűzfalszabály](sql-database-firewall-configure.md).

> [!IMPORTANT]
> Az SQL Database szolgáltatás 1433-as porton keresztül kommunikál. Ha ez a szolgáltatás a vállalati hálózaton belülről csatlakozni próbál, a hálózati tűzfal előfordulhat, hogy nem engedélyezett a kimenő forgalmat az 1433-as porton keresztül. Ha igen, nem lehet csatlakoztatni az egyetlen adatbázishoz, kivéve, ha a rendszergazda megnyitja az 1433-as porton.

1. Az üzembe helyezés befejezése után kattintson a **SQL-adatbázisok** az bal oldali menüben, majd kattintson a *yourDatabase* a a **SQL-adatbázisok** lapot. Megnyílik az adatbázis áttekintő oldala, amelyen látható a teljes **kiszolgálónév** (például *yourserver.database.windows.net*) és a további lehetőségeket biztosít.

2. Másolja ki a teljes kiszolgálónevet az SQL Server Management Studióval csatlakozhat a kiszolgáló és az adatbázisokhoz való használatra.

   ![kiszolgáló neve](./media/sql-database-design-first-database/server-name.png)

3. Kattintson a **Kiszolgálótűzfal beállítása** lehetőségre az eszköztáron. Megnyílik az SQL Database kiszolgálóhoz tartozó **Tűzfalbeállítások** oldal.

   ![kiszolgálószintű IP-tűzfalszabály](./media/sql-database-design-first-database/server-firewall-rule.png)

4. Kattintson a **ügyfél IP-cím hozzáadása** az aktuális IP-cím hozzáadása egy új IP-tűzfalszabály az eszköztáron. Az IP-tűzfalszabály egyetlen IP-címet vagy egy IP-címtartományt az 1433-as port is megnyithatja.

5. Kattintson a **Save** (Mentés) gombra. Egy IP-kiszolgálószintű tűzfalszabályt az aktuális IP-címhez, az SQL Database-kiszolgálóhoz az 1433-as port megnyitása jön létre.

6. Kattintson az **OK** gombra, majd zárja be a **Tűzfalbeállítások** lapot.

Az IP-cím már továbbíthatja az IP-tűzfalon keresztül. Mostantól csatlakozhat az SQL Server Management Studio vagy más választott eszközzel, egyetlen adatbázis. Ügyeljen arra, használja a korábban létrehozott kiszolgálói rendszergazdai fiókkal.

> [!IMPORTANT]
> Alapértelmezés szerint az SQL Database IP-tűzfalon keresztül érhető el az Azure-szolgáltatásokhoz. Kattintson a **KI** gombra ezen az oldalon az összes Azure-szolgáltatás hozzáférésének letiltásához.

[!INCLUDE [sql-database-csharp-adonet-create-query-2](../../includes/sql-database-csharp-adonet-create-query-2.md)]

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban megismerte az alapvető adatbázis-feladatok például adatbázisok és táblák létrehozása, csatlakozzon az adatbázishoz, adatok betöltése és lekérdezéseket futtathat. Megismerte, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Adatbázis létrehozása
> * Tűzfalszabály beállítása
> * Csatlakozás az adatbázishoz a [Visual Studio és a C#](sql-database-connect-query-dotnet-visual-studio.md) használatával
> * Táblázatok létrehozása
> * INSERT, update, delete és adatokat kérdezhet le

Folytassa a következő oktatóanyaggal adatok migrálása.

> [!div class="nextstepaction"]
> [SQL Server migrálása felügyelt Azure SQL Database-példányra kapcsolat nélküli üzemmódban, a DMS használatával](../dms/tutorial-sql-server-to-azure-sql.md)
