---
title: fájl belefoglalása
description: fájl belefoglalása
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.author: estfan
ms.custom: include file
ms.date: 05/15/2018
ms.openlocfilehash: aa1001661d8fe03855e1a28b882f674bee3606b2
ms.sourcegitcommit: 9dec0358e5da3ceb0d0e9e234615456c850550f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/14/2019
ms.locfileid: "72311955"
---
* Ha Azure SQL Database használ, kövesse a [Kapcsolódás a Azure SQL Databasehoz](#connect-azure-sql-db)című témakör lépéseit.

* Ha SQL Server használ, kövesse a [Kapcsolódás a SQL Serverhoz](#connect-sql-server)című témakör lépéseit.

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Kapcsolódás Azure SQL Databasehoz

Ha az SQL-eseményindító vagy-művelet megkérdezi a kapcsolati adatokat, kövesse az alábbi lépéseket, amelyek az eseményindítók és a műveletek esetében is működnek.

1. A **kapcsolatok neve**mezőben hozzon létre egy nevet a kapcsolatok számára.

1. A **SQL Server neve**alatt válassza ki az Azure SQL Servert. Amikor megjelenik a **SQL Database neve** lista, válassza ki az adatbázist. Adja meg az Azure SQL Server-kiszolgáló felhasználónevét és jelszavát.

   Ezeket az információkat vagy a Azure Portal az SQL Database-tulajdonságok vagy a kapcsolatok karakterlánca alatt is megtalálhatja:

   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![Azure SQL Database-kapcsolatok létrehozása](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

1. A kapcsolat létrehozása után folytassa az SQL- [trigger hozzáadásával](#add-sql-trigger) vagy [egy SQL-művelet hozzáadásával](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Csatlakozás az SQL Serverhez

Ha az SQL-eseményindító vagy-művelet megkérdezi a kapcsolati adatokat, kövesse az alábbi lépéseket, amelyek az eseményindítók és a műveletek esetében is működnek. A Kezdés előtt azonban győződjön meg arról, hogy már [beállította a helyszíni adatátjárót](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection). Ellenkező esetben az átjáró nem jelenik meg az átjárók listájában a kapcsolódás létrehozásakor.

1. A **kapcsolatok neve**mezőben hozzon létre egy nevet a kapcsolatok számára.

1. Az trigger vagy művelet területen válassza a **kapcsolat helyszíni adatátjárón keresztül** lehetőséget, hogy megjelenjenek az SQL Server beállításai.

1. Az **SQL Server-név** és az **SQL-adatbázis neve**mezőben adja meg az SQL-kiszolgáló és a-adatbázis nevét. A **Felhasználónév** és a **jelszó**mezőben adja meg a kiszolgáló felhasználónevét és jelszavát.

   A következő információk is megtalálhatók a kapcsolatok karakterláncában:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![SQL Server-kapcsolatok létrehozása](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Ha az SQL Server Windows vagy alapszintű hitelesítést használ, válassza ki a **hitelesítési típust**.

1. Az **átjárók**területen válassza ki a korábban létrehozott helyszíni adatátjáróhoz társított Azure-előfizetést, és válassza ki a helyszíni adatátjáró nevét.

   Ha az átjáró nem jelenik meg a listában, ellenőrizze, hogy megfelelően [állította-e be az átjárót](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   ![SQL Server-kapcsolatok létrehozása befejeződött](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

1. A kapcsolat létrehozása után folytassa az SQL- [trigger hozzáadásával](#add-sql-trigger) vagy az [SQL-művelet hozzáadásával](#add-sql-action).
