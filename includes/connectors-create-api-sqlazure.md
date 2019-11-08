---
title: fájl belefoglalása
description: fájl belefoglalása
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.author: estfan
ms.custom: include file
ms.date: 11/08/2019
ms.openlocfilehash: 0be29f6f541aa58e57eb665ebaf29e35f42865e4
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73826317"
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

   ![Azure SQL Databasehoz való kapcsolódás létrehozása](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)

1. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

1. A kapcsolat létrehozása után folytassa az SQL- [trigger hozzáadásával](#add-sql-trigger) vagy [egy SQL-művelet hozzáadásával](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Csatlakozás az SQL Serverhez

Ha az SQL-eseményindító vagy-művelet megkérdezi a kapcsolati adatokat, kövesse az alábbi lépéseket, amelyek az eseményindítók és a műveletek esetében is működnek. Olyan forgatókönyvek esetében, amelyekhez a helyszíni [adatátjárót](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-install) egy helyi számítógépre kell telepíteni, és [létre kell hozni az Azure-beli adatátjáró erőforrást](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection), először el kell végeznie ezeket a követelményeket. Ellenkező esetben az átjáró-erőforrás nem jelenik meg az átjárók listájában a kapcsolódás létrehozásakor.

Azt is megteheti, hogy a Windows-hitelesítést a SQL Server-összekötővel együtt szeretné használni egy [Integration Service-környezetben (ISE)](https://docs.microsoft.com/azure/logic-apps/connect-virtual-network-vnet-isolated-environment-overview), használja az összekötő nem ISE verzióját és a helyszíni adatátjárót. Az ISE-címkével ellátott verzió nem támogatja a Windows-hitelesítést.

1. A **kapcsolatok neve**mezőben hozzon létre egy nevet a kapcsolatok számára.

1. Az trigger vagy művelet területen válassza a **kapcsolat helyszíni adatátjárón keresztül** lehetőséget, hogy megjelenjenek az SQL Server beállításai.

1. Az **SQL Server-név** és az **SQL-adatbázis neve**mezőben adja meg az SQL-kiszolgáló és a-adatbázis nevét. A **Felhasználónév** és a **jelszó**mezőben adja meg a kiszolgáló felhasználónevét és jelszavát.

   A következő információk is megtalálhatók a kapcsolatok karakterláncában:

   * `Server=<your-server-address>`
   * `Database=<your-database-name>`
   * `User ID=<your-user-name>`
   * `Password=<your-password>`

   ![SQL Serverhoz való kapcsolódás létrehozása](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)

1. Ha az SQL Server Windows vagy alapszintű hitelesítést használ, válassza ki a **hitelesítési típust**.

1. Az **átjárók**területen válassza ki a korábban létrehozott helyszíni adatátjáróhoz társított Azure-előfizetést, és válassza ki a helyszíni adatátjáró nevét.

   Ha az átjáró nem jelenik meg a listában, ellenőrizze, hogy megfelelően [állította-e be az átjárót](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   ![SQL Server-kapcsolatok létrehozása befejeződött](./media/connectors-create-api-sqlazure/sql-server-create-connection-complete.png)

1. Amikor elkészült, válassza a **Létrehozás** lehetőséget.

1. A kapcsolat létrehozása után folytassa az SQL- [trigger hozzáadásával](#add-sql-trigger) vagy az [SQL-művelet hozzáadásával](#add-sql-action).
