---
title: Kiszolgálószintű tűzfalszabály létrehozása
description: SQL Database kiszolgálói szintű tűzfalszabály létrehozása egyetlen és készletezett adatbázisokhoz
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, carlrab
ms.date: 02/11/2019
ms.openlocfilehash: ff2508952b75bad88ff8ff92388c20ba52f50f42
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73818259"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-single-and-pooled-databases-using-the-azure-portal"></a>Rövid útmutató: kiszolgálói szintű tűzfalszabály létrehozása az önálló és a készletezett adatbázisokhoz a Azure Portal használatával

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre egy [kiszolgálói szintű tűzfalszabály](sql-database-firewall-configure.md) a Azure SQL Database az önálló és a készletezett adatbázisokhoz a Azure Portal segítségével, amely lehetővé teszi az adatbázis-kiszolgálókhoz, önálló adatbázisokhoz és rugalmas készletekhez és azok adatbázisaihoz való kapcsolódást. A más Azure-erőforrásokból és a helyszíni erőforrásokból való kapcsolódáshoz tűzfalszabály szükséges.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató az [önálló adatbázis létrehozása a Azure Portal a](sql-database-single-database-get-started.md) kiindulási pontként létrehozott erőforrásokat használja.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure portálra](https://portal.azure.com/).

## <a name="create-a-server-level-ip-firewall-rule"></a>Kiszolgálói szintű IP-tűzfalszabály létrehozása

A SQL Database szolgáltatás egy tűzfalat hoz létre az adatbázis-kiszolgáló szintjén az önálló és a készletezett adatbázisokhoz. Ez a tűzfal megakadályozza, hogy az ügyfélalkalmazások csatlakozzanak a kiszolgálóhoz vagy az önálló vagy készletezett adatbázisokhoz, kivéve, ha a tűzfal megnyitásához létrehoz egy IP-tűzfalszabály-szabályt. Az Azure-on kívüli IP-címről létesített kapcsolathoz hozzon létre egy tűzfalszabály egy adott IP-cím vagy címtartomány számára, amelyhez csatlakozni szeretne. A kiszolgálói szintű és az adatbázis szintű IP-tűzfalszabályok részletes ismertetését lásd: [SQL Database kiszolgálói szintű és adatbázis-szintű IP-tűzfalszabályok](sql-database-firewall-configure.md).

> [!NOTE]
> Az SQL Database az 1433-as porton kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ha igen, nem tud csatlakozni a Azure SQL Database-kiszolgálóhoz, ha az informatikai részleg nem nyitja meg a 1433-es portot.
> [!IMPORTANT]
> A 0.0.0.0 tűzfalszabály lehetővé teszi, hogy az összes Azure-szolgáltatás áthaladjon a kiszolgálói szintű tűzfalszabályok között, és megpróbáljon csatlakozni egyetlen vagy készletezett adatbázishoz a kiszolgálón keresztül. 

Kövesse az alábbi lépéseket egy kiszolgálói szintű IP-tűzfalszabály létrehozásához az ügyfél IP-címéhez, és engedélyezze a külső kapcsolatot a SQL Database tűzfalon keresztül csak az IP-címéhez.

1. Az [Azure SQL Database üzembe helyezésének előfeltétele](#prerequisites) után válassza ki az SQL- **adatbázisok** elemet a bal oldali menüben, majd válassza a **mySampleDatabase** lehetőséget az **SQL-adatbázisok** lapon. Megnyílik az adatbázis áttekintő oldala, amelyen látható a teljes kiszolgálónév (például: **mynewserver-20170824.database.windows.net**), valamint a további konfigurálható beállítások.

2. Másolja ezt a teljes kiszolgálónevet, amelyet a kiszolgálóhoz és az adatbázisaihoz való csatlakozáskor használni szeretne más gyors útmutatókban.

   ![kiszolgáló neve](./media/sql-database-get-started-portal/server-name.png)

3. Válassza a **kiszolgáló tűzfal beállítása** lehetőséget az eszköztáron. Megnyílik az adatbázis-kiszolgáló **tűzfalbeállítások** lapja.

   ![kiszolgálói szintű IP-tűzfalszabály](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Válassza az **ügyfél IP-** címének hozzáadása lehetőséget az eszköztáron, és adja hozzá az aktuális IP-címet egy új kiszolgálói szintű IP-tűzfalszabály eléréséhez. A kiszolgálói szintű IP-tűzfalszabály egyetlen IP-cím vagy egy IP-címtartomány 1433-as portját nyithatja meg.

   > [!IMPORTANT]
   > Alapértelmezés szerint a SQL Database tűzfalon keresztüli hozzáférés le van tiltva az összes Azure-szolgáltatás esetében. Ha engedélyezni szeretné az összes Azure-szolgáltatás elérését, válassza az ezen **a** lapon lehetőséget.
   >

5. Kattintson a **Mentés** gombra. A rendszer létrehoz egy kiszolgálói szintű IP-tűzfalszabály-szabályt az aktuális IP-címhez, amely az 1433-es portot nyitja meg a SQL Database kiszolgálón.

6. A **tűzfalbeállítások** oldalának lezárása.

Ha SQL Server Management Studio vagy egy másik eszközt használ, mostantól csatlakozhat a SQL Database-kiszolgálóhoz és annak adatbázisaihoz a korábban létrehozott kiszolgálói rendszergazdai fiók használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Mentse ezeket az erőforrásokat, ha a [Következő lépésekre](#next-steps) szeretne lépni, és meg szeretné tudni, hogyan milyen módokon csatlakozhat az adatbázishoz és végezhet róla lekérdezéseket. Ha azonban törölni szeretné az ebben a rövid útmutatóban létrehozott erőforrásokat, kövesse az alábbi lépéseket.

1. A Azure Portal bal oldali menüjében válassza az **erőforráscsoportok** lehetőséget, majd válassza a **myResourceGroup**lehetőséget.
2. Az erőforráscsoport lapon válassza a **Törlés**lehetőséget, írja be a **myResourceGroup** szöveget a szövegmezőbe, majd válassza a **Törlés**lehetőséget.

## <a name="next-steps"></a>További lépések

- Most, hogy rendelkezik egy adatbázissal, [kapcsolódhat hozzá és lekérdezéseket hajthat végre rajta](sql-database-connect-query.md) a kedvenc eszközeinek vagy nyelveinek egyikével, többek között
  - [Kapcsolódás és lekérdezés az SQL Server Management Studióval](sql-database-connect-query-ssms.md)
  - [Kapcsolódás és lekérdezés az Azure Data Studióval](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Az első adatbázis megtervezésével, a táblák létrehozásával és az adatok beszúrásával kapcsolatos információkért tekintse meg az alábbi oktatóanyagok egyikét:
  - [Az első önálló adatbázis megtervezése Azure SQL Database a SSMS használatával](sql-database-design-first-database.md)
  - [Egyetlen adatbázis tervezése Azure SQL Databaseban és a és a C# ADO.net összekötése](sql-database-design-first-database-csharp.md)
