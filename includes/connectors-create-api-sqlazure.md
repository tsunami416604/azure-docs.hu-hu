---
title: fájl belefoglalása
description: fájl belefoglalása
services: logic-apps
author: ecfan
ms.service: logic-apps
ms.topic: include
ms.date: 05/15/2018
ms.author: estfan
ms.custom: include file
ms.openlocfilehash: da03c5247b8ebe0a3305b08a05d661264497663f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179372"
---
* Ha az Azure SQL Database használata esetén kövesse a [csatlakozhat az Azure SQL Database](#connect-azure-sql-db). 

* Ha az SQL Servert használ, kövesse a [csatlakozhat az SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Csatlakozás az Azure SQL Database

1. Ha az SQL-eseményindítót vagy műveletet kéri a kapcsolati adatokat, kövesse az alábbi lépéseket:

   1. Hozzon létre a kapcsolat nevét.

   2. Válassza ki az SQL Servert, és válassza ki az adatbázis. 

      Az adatbázisok listája csak az SQL-kiszolgáló kiválasztása után jelenik meg.
 
   3. Adja meg a felhasználónevet és jelszót a kiszolgálóhoz.

      Találja meg ezeket az információkat az Azure Portalon vagy az SQL database tulajdonságainál vagy a kapcsolati karakterlánc: 
      
      "Felhasználói azonosító = <*felhasználónév*>"
      <br>
      "Jelszó = <*yourPassword*>"

   Ez a példa bemutatja a kapcsolatadatokat az eseményindító, de ezek a lépések műveletek túl.

   ![Azure SQL Database-kapcsolat létrehozása](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)
   <br>
   Csillag (*) azt jelzi, hogy szükséges értékeket.

   | Tulajdonság | Érték | Részletek | 
   |----------|-------|---------| 
   | Kapcsolat neve | <*my-sql-connection*> | A kapcsolat neve | 
   | SQL-kiszolgáló neve | <*my-sql-server*> | Az SQL Server neve |
   | SQL-adatbázis neve | <*my-sql-database*>  | Az SQL-adatbázis neve | 
   | Felhasználónév | <*my-sql-username*> | A felhasználónév, az adatbázis eléréséhez |
   | Jelszó | <*my-sql-password*> | A jelszót az adatbázis eléréséhez | 
   |||| 

2. Ha elkészült, kattintson a **Létrehozás** gombra.

3. Miután létrehozta a kapcsolatot, folytassa [hozzáadása SQL eseményindító](#add-sql-trigger) vagy [hozzáadása SQL művelet](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Csatlakozás az SQL Serverhez

Az átjáró kiválasztása előtt ellenőrizze, hogy Ön már [állítsa be a data gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection). Ezzel a módszerrel az átjáró listájában jelenik meg az átjárók a kapcsolat létrehozásakor.

1. Ha az SQL-eseményindítót vagy műveletet kéri a kapcsolati adatokat, kövesse az alábbi lépéseket:

   1. Válassza az eseményindítót vagy műveletet **kapcsolódás helyszíni adatátjárón keresztül** úgy, hogy az SQL server beállítások jelennek meg.

   2. Hozzon létre a kapcsolat nevét.

   3. Adja meg az SQL server-címét, majd adja meg az adatbázis nevét.
   
      Ez az információ található a kapcsolati karakterlánc: 
      
      * "Server=<*yourServerAddress*>"
      * "Adatbázis = <*yourDatabaseName*>"

   4. Adja meg a felhasználónevet és jelszót a kiszolgálóhoz.

      Ez az információ található a kapcsolati karakterlánc: 
      
      * "Felhasználói azonosító = <*felhasználónév*>"
      * "Jelszó = <*yourPassword*>"

   5. Ha az SQL server Windows vagy az alapszintű hitelesítést használ, válassza ki a hitelesítési típus.

   6. Válassza ki a helyszíni adatátjárót, korábban létrehozott nevét.
   
      Ha az átjáró nem jelenik meg a listában, ellenőrizze, hogy megfelelően [az átjáró beállításához](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   Ez a példa bemutatja a kapcsolatadatokat az eseményindító, de ezek a lépések műveletek túl.

   ![SQL Server közötti kapcsolat létrehozása](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)
   <br>
   Csillag (*) azt jelzi, hogy szükséges értékeket.

   | Tulajdonság | Érték | Részletek | 
   |----------|-------|---------| 
   | Csatlakozás helyszíni átjárón keresztül | A beállítás először az SQL Server-beállítások. | | 
   | Kapcsolat neve | <*my-sql-connection*> | A kapcsolat neve | 
   | SQL-kiszolgáló neve | <*my-sql-server*> | Az SQL Server neve |
   | SQL-adatbázis neve | <*my-sql-database*>  | Az SQL-adatbázis neve |
   | Felhasználónév | <*my-sql-username*> | A felhasználónév, az adatbázis eléréséhez |
   | Jelszó | <*my-sql-password*> | A jelszót az adatbázis eléréséhez | 
   | Hitelesítés típusa | Windows- vagy alapszintű | Nem kötelező: Az SQL server által használt hitelesítési típus | 
   | Átjárók | <*my-data-gateway*> | A helyszíni adatátjáró neve | 
   |||| 

2. Ha elkészült, kattintson a **Létrehozás** gombra. 

3. Miután létrehozta a kapcsolatot, folytassa [hozzáadása SQL eseményindító](#add-sql-trigger) vagy [hozzáadása SQL művelet](#add-sql-action).
