---
ms.service: logic-apps
author: ecfan
ms.author: estfan
ms.topic: include
ms.date: 11/08/2019
ms.openlocfilehash: ea0ae1b1527aa1f527c8ac8fbcd3b4e4f6b6fe2f
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74789196"
---
* Ha Azure SQL Database-t használ, kövesse a Csatlakozás az [Azure SQL-adatbázishoz](#connect-azure-sql-db)című csoportban leírt lépéseket.

* SQL Server használata esetén kövesse a Csatlakozás az [SQL Server kiszolgálóhoz](#connect-sql-server)című csoportlépéseit.

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Csatlakozás az Azure SQL-adatbázishoz

Amikor az SQL-eseményindító vagy művelet kéri a kapcsolatadatait, kövesse az alábbi lépéseket, amelyek az eseményindítók és a műveletek esetén is működnek.

1. A **Kapcsolatnév mezőbe**hozza létre a kapcsolat nevét.

1. Az **SQL Server-név csoportban**válassza ki az Azure SQL-kiszolgálót. Amikor megjelenik az **SQL Adatbázis neve** lista, jelölje ki az adatbázist. Adja meg a felhasználónevet és a jelszót az Azure SQL-kiszolgálóhoz.

   Ezeket az információkat az Azure Portalon az SQL-adatbázis tulajdonságai vagy a kapcsolati karakterlánc alatt is megtalálhatja:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Kapcsolat létrehozása az Azure SQL Database-rel](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

1. A kapcsolat létrehozása után folytassa [az SQL-eseményindító hozzáadása](#add-sql-trigger) vagy [SQL-művelet hozzáadása című művelettel.](#add-sql-action)

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Csatlakozás az SQL Serverhez

Amikor az SQL-eseményindító vagy művelet kéri a kapcsolatadatait, kövesse az alábbi lépéseket, amelyek az eseményindítók és a műveletek esetén is működnek. Olyan esetekben, amelyek [megkövetelik,](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install) hogy telepítse a helyszíni adatátjáró egy helyi számítógépre, és [hozzon létre az Azure-adatátjáró-erőforrás,](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)győződjön meg arról, hogy ezeket a követelményeket először. Ellenkező esetben az átjáró-erőforrás nem jelenik meg az átjárók listájában a kapcsolat létrehozásakor.

A Windows-hitelesítés használata az SQL Server-összekötővel [integrációs szolgáltatási környezetben (ISE)](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview)is, használja az összekötő nem ISE-verzióját és a helyszíni adatátjárót. Az ISE-címkével ellátott verzió nem támogatja a Windows-hitelesítést.

1. A **Kapcsolatnév mezőbe**hozza létre a kapcsolat nevét.

1. Az eseményindítóban vagy műveletben válassza a **Csatlakozás a helyszíni adatátjárón keresztül** lehetőséget, hogy az SQL-kiszolgáló beállításai megjelenjenek.

1. Az **SQL-kiszolgáló neve** és **az SQL-adatbázis neve**esetén adja meg az SQL-kiszolgáló címét és az adatbázis nevét. A **Felhasználónév** és **jelszó**mezőbe válassza ki a kiszolgáló felhasználónevét és jelszavát.

   Ezeket az információkat a kapcsolati karakterláncban is megtalálhatja:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Kapcsolat létrehozása az SQL Server kiszolgálóval](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Ha az SQL-kiszolgáló Windows vagy egyszerű hitelesítést használ, válassza a **Hitelesítés típusát**.

1. Az **Átjárók**csoportban válassza ki a korábban létrehozott helyszíni adatátjáróhoz társított Azure-előfizetést, és válassza ki a helyszíni adatátjáró nevét.

   Ha az átjáró nem jelenik meg a listában, ellenőrizze, hogy helyesen [állította-e be az átjárót.](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection)

   ![Befejeződött az SQL Server-kapcsolat létrehozása](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

1. A kapcsolat létrehozása után folytassa az [SQL-eseményindító hozzáadása](#add-sql-trigger) vagy [az SQL hozzáadása művelettel.](#add-sql-action)
