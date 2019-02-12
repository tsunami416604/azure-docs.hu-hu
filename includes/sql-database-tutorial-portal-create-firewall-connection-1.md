---
author: MightyPen
ms.service: sql-database
ms.topic: include
ms.date: 12/10/2018
ms.author: genemi
ms.openlocfilehash: ab31ee82e8035fe888fa70b5796aef2c2b2939b2
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56008523"
---
## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/)

## <a name="create-a-blank-sql-database"></a>Üres SQL-adatbázis létrehozása

Az Azure SQL database megtalálható egy meghatározott készletével [számítási és tárolási erőforrások](../articles/sql-database/sql-database-service-tiers-dtu.md). Az adatbázis működik a egy [Azure-erőforráscsoport](../articles/azure-resource-manager/resource-group-overview.md) és a egy [Azure SQL database logikai kiszolgáló](../articles/sql-database/sql-database-features.md).

Kövesse az alábbi lépéseket egy üres SQL-adatbázis létrehozásához.

1. Kattintson az Azure Portal bal felső sarkában található **Erőforrás létrehozása** gombra.

1. Az a **új** lapon jelölje be **adatbázisok** > **SQL Database**.

   ![üres adatbázis létrehozása](../articles/sql-database/media/sql-database-design-first-database/create-empty-database.png)

1. Az a **SQL Database** panelen írja be vagy válassza ki a következő értékeket:

   | Beállítás       | Ajánlott érték | Leírás |
   | ------------ | ------------------ | ------------------------------------------------- |
   | **Adatbázis neve** | *yourDatabase* | Érvényes adatbázisnevekkel kapcsolatban lásd: [adatbázis-azonosítókat](/sql/relational-databases/databases/database-identifiers). |
   | **Előfizetés** | *yourSubscription*  | Az előfizetései részleteivel kapcsolatban lásd az [előfizetéseket](https://account.windowsazure.com/Subscriptions) ismertető cikket. |
   | **Erőforráscsoport** | *yourResourceGroup* | Az érvényes erőforráscsoport-nevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
   | **Forrás kiválasztása** | Üres adatbázis | Meghatározza, hogy egy üres adatbázist kell létrehozni. |

   ![Adatbázis létrehozása](../articles/sql-database/media/sql-database-design-first-database/create-database.png)

   1. Válassza ki **kiszolgáló** az új adatbázis-kiszolgáló konfigurálása. Ezután írja be vagy válassza ki a következő értékeket:

      | Beállítás       | Ajánlott érték | Leírás |
      | ------------ | ------------------ | ------------------------------------------------- |
      | **Kiszolgálónév** | Bármely globálisan egyedi név | Az érvényes kiszolgálónevekkel kapcsolatban lásd az [elnevezési szabályokat és korlátozásokat](/azure/architecture/best-practices/naming-conventions) ismertető cikket. |
      | **Kiszolgálói rendszergazdai bejelentkezés** | Bármely érvényes név | Az érvényes bejelentkezési nevekkel kapcsolatban lásd: [adatbázis-azonosítókat](/sql/relational-databases/databases/database-identifiers).|
      | **Jelszó** | Bármely érvényes jelszó | A jelszó legalább 8 karakter hosszúságúnak kell lennie, és kell használnia a karaktereket a következő kategóriák közül legalább háromból: nagybetűs karakterek, kisbetűs karakterek, számok és nem alfanumerikus karakterek. |
      | **Hely** | Bármely érvényes hely | Régiókkal kapcsolatos információkért lásd: [Azure-régiók](https://azure.microsoft.com/regions/). |

      Válassza a **Kiválasztás** lehetőséget

      ![adatbázis-kiszolgáló létrehozása](../articles/sql-database/media/sql-database-design-first-database/create-database-server.png)

   1. Válassza ki **tarifacsomag** a szolgáltatási rétegben, a Dtu-szám és a tárterületméret megadásához. Ismerje meg a dtu-k és tárolási, egyes szolgáltatásszinteken elérhető lehetőségeket.

      A Kiszolgálószint, a Dtu-szám és a tárterületméret kiválasztása, után válassza ki a **alkalmaz**.

   1. Adjon meg egy **rendezést** az üres adatbázis (a jelen oktatóanyag esetében használja az alapértelmezett érték). Rendezés kapcsolatos további információkért lásd: [rendezések](/sql/t-sql/statements/collations).

1. Most, hogy Ön teljesítette a **SQL Database** képernyő, válassza ki **létrehozás** az adatbázis létrehozásához. Ez a lépés is igénybe vehet legfeljebb egy másfél végrehajtásához.

1. Az eszköztáron kattintson az **Értesítések** parancsra az üzembe helyezési folyamat megfigyeléséhez.

     ![értesítés](../articles/sql-database/media/sql-database-design-first-database/notification.png)

## <a name="create-a-firewall-rule"></a>Tűzfalszabály létrehozása

Az SQL database szolgáltatás a kiszolgáló szintjén, hogy a külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgálón lévő adatbázisokhoz hoz létre tűzfalat. Kövesse az alábbi lépéseket követve létrehozhat egy [SQL database kiszolgálószintű tűzfalszabályt](../articles/sql-database/sql-database-firewall-configure.md) az ügyfél IP-cím. Ez a folyamat lehetővé teszi, hogy a külső kapcsolatokat csak az IP-cím a SQL database-tűzfalon keresztül.

> [!NOTE]
> Az SQL database 1433-as porton keresztül kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ha igen, nem lehet csatlakoztatni az Azure SQL database-kiszolgálóhoz, ha a rendszergazda megnyitja az 1433-as porton.

1. Az üzembe helyezés befejezése után válassza ki a **SQL-adatbázisok** a bal oldali menüben válassza ki *yourDatabase* a a **SQL-adatbázisok** lapot. A **áttekintése** oldala az adatbázis megnyitása, amelyen látható a teljes kiszolgálónév (például *yourserver.database.windows.net*) és a további lehetőségeket biztosít.

1. Másolja ki a kiszolgáló és a későbbi lépésekben az adatbázisokhoz való csatlakozáshoz használja a kiszolgáló teljes nevét.

   ![kiszolgáló neve](../articles/sql-database/media/sql-database-design-first-database/server-name.png)

1. Válassza ki **kiszolgálótűzfal beállítása** az eszköztáron. A **tűzfalbeállítások** megnyílik az SQL database-kiszolgálót.

   ![kiszolgálói tűzfalszabály](../articles/sql-database/media/sql-database-design-first-database/server-firewall-rule.png)

   1. Válassza ki **ügyfél IP-cím hozzáadása** az aktuális IP-cím hozzáadása egy új tűzfalszabályt az eszköztáron. A tűzfalszabály az 1433-as portot egy egyedi IP-cím vagy egy IP-címtartomány számára nyithatja meg.

   1. Válassza a **Mentés** elemet. A rendszer létrehoz egy kiszolgálószintű tűzfalszabályt az aktuális IP-címhez, és megnyitja az 1433-as portot a logikai kiszolgálón.

   1. Válassza ki **OK** , majd zárja be a **tűzfalbeállítások** lapot.

Az IP-cím mostantól átadható a tűzfalon keresztül, és mostantól csatlakozhat az SQL database-kiszolgálót, és annak adatbázisaihoz SSMS vagy más választott eszközzel. Ügyeljen arra, akkor a korábban létrehozott a kiszolgálói rendszergazdai fiókkal.

> [!IMPORTANT]
> Alapértelmezés szerint az SQL database-tűzfalon keresztül érhető el az Azure-szolgáltatásokhoz. Válassza ki **OFF** ezen az oldalon letiltása az Azure-szolgáltatásokhoz.
