---
title: "Azure Portal: SQL-adatbázis létrehozása | Microsoft Docs"
description: "Ismerje meg, hogyan hozhat létre SQL Database logikai kiszolgálót, kiszolgálószintű tűzfalszabályokat és adatbázisokat az Azure Portal használatával. Megtanulhatja azt is, hogyan kérdezhet le egy SQL-adatbázist az Azure Portal használatával."
keywords: "oktatóanyag az SQL Database használatához, SQL-adatbázis létrehozása"
services: sql-database
documentationcenter: 
author: CarlRabeler
manager: jhubbard
editor: 
ms.assetid: aeb8c4c3-6ae2-45f7-b2c3-fa13e3752eed
ms.service: sql-database
ms.custom: quick start
ms.workload: data-management
ms.tgt_pltfrm: portal
ms.devlang: na
ms.topic: hero-article
ms.date: 03/13/2017
ms.author: carlrab
translationtype: Human Translation
ms.sourcegitcommit: 0d8472cb3b0d891d2b184621d62830d1ccd5e2e7
ms.openlocfilehash: 1c087f3ecbdd6956b50a8ef9b7e81340f83ff0cf
ms.lasthandoff: 03/21/2017


---
# <a name="create-and-query-a-single-azure-sql-database-in-the-azure-portal"></a>Önálló Azure SQL-adatbázis létrehozása és lekérdezése az Azure Portalon

Az Azure SQL-adatbázisok létrehozhatók az Azure Portal segítségével. Ez a módszer egy böngészőalapú felhasználói felületet biztosít az Azure SQL-adatbázisok, valamint az összes kapcsolódó Azure-erőforrás létrehozásához.

## <a name="log-in-to-the-azure-portal"></a>Jelentkezzen be az Azure portálra.

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-a-sql-database"></a>SQL-adatbázis létrehozása

Az Azure SQL-adatbázis [számítási és tárolási erőforrások](sql-database-service-tiers.md) egy meghatározott készletével együtt jön létre. Az adatbázis egy [Azure-erőforráscsoporton](../azure-resource-manager/resource-group-overview.md) belül egy [Azure SQL Database logikai kiszolgálón](sql-database-features.md) jön létre. 

Kövesse az alábbi lépéseket az Adventure Works LT mintaadatokat tartalmazó SQL-adatbázis létrehozásához. 

1. Kattintson az Azure Portal bal felső sarkában található **Új** gombra.

2. Az **Új** panelen válassza az **Adatbázisok** lehetőséget, majd az **Adatbázisok** panelen válassza az **SQL Database** lehetőséget.

3. Töltse ki az SQL Database űrlapját a szükséges információkkal: 
   - Adatbázis neve: Adjon meg egy adatbázisnevet
   - Előfizetés: Válassza ki az előfizetést
   - Erőforráscsoport: Válasszon ki egy újat vagy egy meglévőt
   - Forrás: Válassza a **Minta (AdventureWorksLT)** lehetőséget
   - Kiszolgáló: Hozzon létre egy új kiszolgálót (a **Kiszolgáló** nevének globálisan egyedinek kell lennie)
   - Rugalmas készlet: A gyors üzembe helyezéshez válassza a **Most nem** lehetőséget
   - Tarifacsomag: Válassza a **20 DTU** egységet, illetve a **250** GB tárhelyet
   - Rendezés: A mintaadatbázis importálásakor nem módosíthatja ezt az értéket 
   - Rögzítés az irányítópulton: Jelölje be ezt a jelölőnégyzetet

      ![Adatbázis létrehozása](./media/sql-database-get-started/create-database-s1.png)

4. Amikor végzett, kattintson a **Létrehozás** gombra. Az üzembe helyezés eltarthat néhány percig.
5. Miután az SQL-adatbázis üzembe helyezése befejeződött, válassza ki az **SQL-adatbázisok** elemet az irányítópulton, vagy az **SQL-adatbázisok** elemet a bal oldali menüben, majd kattintson az új adatbázisra az **SQL-adatbázisok** oldalon. Megnyílik az adatbázis áttekintő oldala, amelyen látható a teljes kiszolgálónév (például **mynewserver20170313.database.windows.net**), valamint a további konfigurálható beállítások.

      ![new-sql database](./media/sql-database-get-started/new-database-s1-overview.png) 

## <a name="create-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály létrehozása

Az SQL Database szolgáltatás egy tűzfalat hoz létre, amely megakadályozza, hogy a külső alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz és adatbázishoz. Kövesse az alábbi lépéseket, és hozzon létre egy [SQL Database-kiszolgálószintű tűzfalszabályt](sql-database-firewall-configure.md) az IP-címhez, amellyel engedélyezheti a külső kapcsolatokat az SQL Database-tűzfalon keresztül. 

1. Kattintson a **Kiszolgálótűzfal beállítása** lehetőségre az adatbázis eszköztárán. Megnyílik az SQL Database kiszolgálóhoz tartozó **Tűzfalbeállítások** oldal. 

      ![kiszolgálói tűzfalszabály](./media/sql-database-get-started/server-firewall-rule.png) 

2. Kattintson az **Ügyfél IP-címének hozzáadása** elemre az eszköztárban, majd kattintson a **Mentés** gombra. A rendszer létrehoz egy kiszolgálószintű tűzfalszabályt az aktuális IP-címhez.

3. Kattintson az **OK** gombra, majd az **X**-re a Tűzfalbeállítások oldal bezárásához.

Mostantól csatlakozhat az adatbázishoz és annak kiszolgálójához az SQL Server Management Studio vagy más választott eszköz használatával.

## <a name="query-the-sql-database"></a>Az SQL-adatbázis lekérdezése

Kövesse az alábbi lépéseket az adatbázis az Azure Portalon található lekérdezésszerkesztő segítségével történő lekérdezéséhez. 

1. Az adatbázishoz tartozó SQL Database oldalon kattintson az **Eszközök** elemre az eszköztárban. Megnyílik a **Lekérdezésszerkesztő** előzetes oldala.

     ![eszközök menü](./media/sql-database-get-started/tools-menu.png) 

2. Kattintson a **Lekérdezésszerkesztő (előzetes verzió)** lehetőségre, kattintson az **Előzetes verziójú szolgáltatás feltételei** jelölőnégyzetre, majd az **OK** gombra. Megnyílik a lekérdezésszerkesztő.

3. Kattintson a **Bejelentkezés** elemre, majd amikor a rendszert kéri, válassza ki az **SQL Server-hitelesítés** lehetőséget, és adja meg a kiszolgáló rendszergazdai bejelentkezési nevét és jelszavát.
4. A bejelentkezéshez kattintson az **OK** gombra.

5. A hitelesítés után írja be a kívánt lekérdezést a lekérdezési ablakba, például a következő lekérdezést:

   ```
   SELECT pc.Name as CategoryName, p.name as ProductName
   FROM SalesLT.ProductCategory pc
   JOIN SalesLT.Product p
   ON pc.productcategoryid = p.productcategoryid;
   ```

6. Kattintson a **Futtatás** gombra, majd tekintse át a lekérdezési eredményeket az **Eredmények** ablaktáblán.

    ![lekérdezésszerkesztő: eredmények](./media/sql-database-get-started/query-editor-results.png)

7. A Lekérdezésszerkesztő oldal bezárásához kattintson az **X**-re.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

A jelen gyűjteményben található **Csatlakozási** rövid útmutatók és az oktatóanyag-gyűjtemény oktatóanyagai is erre a rövid útmutatóra épülnek. Ha azt tervezi, hogy az ezt rövid útmutatókkal vagy az oktatóanyagokkal dolgozik tovább, akkor ne törölje az ebben a rövid útmutatóban létrehozott erőforrásokat. Ha nem folytatja a munkát, akkor a következő lépésekkel törölheti az Azure Portalon a rövid útmutatóhoz létrehozott összes erőforrást.

1. Az Azure Portal bal oldali menüjében kattintson az **Erőforráscsoportok** lehetőségre, majd kattintson a **myResourceGroup** elemre. 
2. Az erőforráscsoport oldalán kattintson a **Törlés** elemre, írja be a **myResourceGroup** szöveget a szövegmezőbe, majd kattintson a **Törlés** gombra.

## <a name="next-steps"></a>Következő lépések

- Az SQL Server Management Studióval történő csatlakozáshoz és lekérdezéshez lásd [az SSMS segítségével történő csatlakozással és lekérdezéssel](sql-database-connect-query-ssms.md) foglalkozó témakört.
- Ha a Visual Studióval szeretne kapcsolódni, lásd a [Visual Studióval végzett csatlakozásról és lekérdezésről](sql-database-connect-query.md) szóló témakört.
- Az SQL Database műszaki áttekintését az [SQL Database szolgáltatást ismertető](sql-database-technical-overview.md) témakörben találja.

