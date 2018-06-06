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
ms.openlocfilehash: 013e230aa7e096f6a90ed7cf9e93a44fbdeb3bd6
ms.sourcegitcommit: 59fffec8043c3da2fcf31ca5036a55bbd62e519c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/04/2018
ms.locfileid: "34678265"
---
* Ha az Azure SQL Database használata esetén kövesse a [csatlakozás az Azure SQL Database](#connect-azure-sql-db). 

* Ha az SQL Server használata esetén kövesse a [csatlakozás az SQL Server](#connect-sql-server).

<a name="connect-azure-sql-db"></a>

### <a name="connect-to-azure-sql-database"></a>Csatlakozás az Azure SQL Database

1. Ha az SQL eseményindító vagy művelet kéri a kapcsolati adatokat, kövesse az alábbi lépéseket:

   1. Hozzon létre a kapcsolat nevét.

   2. Válassza ki az SQL Servert, majd válassza ki az adatbázist. 

      Az adatbázisok listája jelenik meg, csak az SQL server kiválasztása után.
 
   3. Adja meg a felhasználónevet és jelszót a kiszolgálóhoz.

      Található ezeket az információkat az Azure portálon vagy az SQL-adatbázis tulajdonságok, illetve a kapcsolati karakterlánc: 
      
      "Felhasználóazonosító = <*felhasználónév*>"
      <br>
      "Jelszó = <*yourPassword*>"

   Ez a példa bemutatja a kapcsolati adatok eseményindító, de ezek a lépések műveletekhez túl.

   ![Azure SQL adatbázis-kapcsolat létrehozása](./media/connectors-create-api-sqlazure/azure-sql-database-create-connection.png)
   <br>
   Csillag (*) azt jelzi, hogy szükséges értékeket.

   | Tulajdonság | Érték | Részletek | 
   |----------|-------|---------| 
   | Kapcsolat neve | <*saját sql-kapcsolat*> | A kapcsolat neve | 
   | SQL-kiszolgáló neve | <*sql-kiszolgáló*> | Az SQL server nevét |
   | SQL-adatbázis neve | <*saját sql-adatbázis*>  | Az SQL-adatbázis neve | 
   | Felhasználónév | <*saját sql-felhasználónév*> | A felhasználó nevét az adatbázis elérése közben |
   | Jelszó | <*saját sql-jelszó*> | Az adatbázis eléréséhez szükséges jelszót | 
   |||| 

2. Ha elkészült, kattintson a **Létrehozás** gombra.

3. Miután létrehozta a kapcsolatot, folytassa [hozzáadása SQL eseményindító](#add-sql-trigger) vagy [hozzáadása SQL művelet](#add-sql-action).

<a name="connect-sql-server"></a>

### <a name="connect-to-sql-server"></a>Csatlakozás az SQL Serverhez

Az átjáró kiválasztása előtt győződjön meg arról, hogy Ön már [állítsa be az Ön data gateway](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection). Ily módon az átjáró listájában jelenik meg az átjáró a kapcsolat létrehozásakor.

1. Ha az SQL eseményindító vagy művelet kéri a kapcsolati adatokat, kövesse az alábbi lépéseket:

   1. Jelölje ki az eseményindító vagy művelet **keresztül, a helyszíni adatátjáró** , hogy az SQL server beállítások jelennek meg.

   2. Hozzon létre a kapcsolat nevét.

   3. Adjon meg a címet az SQL server számára, majd adja meg az adatbázis nevét.
   
      Ezt az információt találja meg a kapcsolati karakterlánc: 
      
      * "Server = <*yourServerAddress*>"
      * "Database = <*yourDatabaseName*>"

   4. Adja meg a felhasználónevet és jelszót a kiszolgálóhoz.

      Ezt az információt találja meg a kapcsolati karakterlánc: 
      
      * "Felhasználóazonosító = <*felhasználónév*>"
      * "Jelszó = <*yourPassword*>"

   5. Ha az SQL server Windows vagy az egyszerű hitelesítést használ, válassza ki a hitelesítés típusát.

   6. Válassza ki a helyszíni data gateway, korábban létrehozott nevét.
   
      Ha az átjáró nem található a listában, akkor ellenőrizze, hogy helyesen [az átjáró beállítását](https://docs.microsoft.com/azure/logic-apps/logic-apps-gateway-connection).

   Ez a példa bemutatja a kapcsolati adatok eseményindító, de ezek a lépések műveletekhez túl.

   ![SQL Server-kapcsolat létrehozása](./media/connectors-create-api-sqlazure/sql-server-create-connection.png)
   <br>
   Csillag (*) azt jelzi, hogy szükséges értékeket.

   | Tulajdonság | Érték | Részletek | 
   |----------|-------|---------| 
   | Csatlakozás a helyi átjáró | A beállítás először az SQL Server-beállítások. | | 
   | Kapcsolat neve | <*saját sql-kapcsolat*> | A kapcsolat neve | 
   | SQL-kiszolgáló neve | <*sql-kiszolgáló*> | Az SQL server nevét |
   | SQL-adatbázis neve | <*saját sql-adatbázis*>  | Az SQL-adatbázis neve |
   | Felhasználónév | <*saját sql-felhasználónév*> | A felhasználó nevét az adatbázis elérése közben |
   | Jelszó | <*saját sql-jelszó*> | Az adatbázis eléréséhez szükséges jelszót | 
   | Hitelesítés típusa | A Windows vagy Basic | Választható lehetőség: A hitelesítési típus a SQL server által használt | 
   | Átjárók | <*saját adatátjáró*> | A helyszíni data gateway nevét | 
   |||| 

2. Ha elkészült, kattintson a **Létrehozás** gombra. 

3. Miután létrehozta a kapcsolatot, folytassa [hozzáadása SQL eseményindító](#add-sql-trigger) vagy [hozzáadása SQL művelet](#add-sql-action).
