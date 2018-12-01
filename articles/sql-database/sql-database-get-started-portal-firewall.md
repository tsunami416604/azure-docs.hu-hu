---
title: 'Azure Portal: SQL Database-tűzfalszabály létrehozása | Microsoft Docs'
description: SQL Database kiszolgáló szintű tűzfalszabály létrehozása
services: sql-database
ms.service: sql-database
ms.subservice: security
ms.custom: ''
ms.devlang: ''
ms.topic: quickstart
author: sachinpMSFT
ms.author: sachinp
ms.reviewer: vanto, carlrab
manager: craigg
ms.date: 12/01/2018
ms.openlocfilehash: cab92539b5019d4807ddefb2b84279c844f53016
ms.sourcegitcommit: 333d4246f62b858e376dcdcda789ecbc0c93cd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/01/2018
ms.locfileid: "52721901"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-your-sql-database-using-the-azure-portal"></a>Rövid útmutató: Kiszolgálószintű tűzfalszabály létrehozása az SQL-adatbázishoz az Azure Portalon

Ez a rövid útmutató végigvezeti egy olyan, Azure SQL-adatbázishoz készült kiszolgálószintű tűzfalszabály létrehozásának lépésein, amely engedélyezi, hogy egy helyszíni erőforrásból csatlakozzon az adatbázishoz.

## <a name="prerequisites"></a>Előfeltételek

Ebben a rövid útmutatóban létrehozott erőforrásokat használja [egy Azure SQL database létrehozása az Azure Portalon](sql-database-get-started-portal.md) kiindulási pontként.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-server-level-firewall-rule"></a>Kiszolgálószintű tűzfalszabály létrehozása

Az SQL Database szolgáltatás a kiszolgáló szintjén hoz létre tűzfalat. Ez a tűzfal megakadályozza, hogy alkalmazások és eszközök csatlakozzanak a kiszolgálóhoz vagy a kiszolgáló adatbázisokhoz, ha nem hoz létre egy tűzfalszabályt, megnyitja a tűzfalat. Azure-on kívülről IP-címről érkező kapcsolatot hozzon létre egy tűzfalszabályt egy adott IP-címet vagy címtartományt. A tűzfalszabályokkal kapcsolatos további információkért lásd: [SQL Database kiszolgálószintű tűzfalszabályt](sql-database-firewall-configure.md).

> [!NOTE]
> Az SQL Database az 1433-as porton kommunikál. Csatlakozás a vállalati hálózaton belülről próbál, ha a hálózati tűzfal előfordulhat, hogy nem engedélyezett a kimenő forgalmat az 1433-as porton keresztül. Ha igen, nem lehet csatlakoztatni az Azure SQL Database-kiszolgálóhoz, ha az informatikai részleg megnyitja az 1433-as porton.
>

Hajtsa végre a következő lépésekkel hozzon létre egy kiszolgálószintű tűzfalszabályt az ügyfél IP-címe, és engedélyezheti a külső kapcsolatokat csak az IP-cím az SQL Database-tűzfalon keresztül.

1. Után az [előfeltételként szükséges Azure SQL database](#prerequisites) üzembe helyezés befejeződött, válassza a **SQL-adatbázisok** elemet a bal oldali menüben, és válassza a **mySampleDatabase** a a **SQL-adatbázisok** lapot. Megnyílik az adatbázis áttekintő oldala, amelyen látható a teljes kiszolgálónév (például: **mynewserver-20170824.database.windows.net**), valamint a további konfigurálható beállítások.

2. Másolja ki a teljes kiszolgálónévre a kiszolgálóhoz és a többi rövid útmutató az adatbázisokhoz való csatlakozáskor használandó.

   ![kiszolgáló neve](./media/sql-database-get-started-portal/server-name.png)

3. Válassza ki **kiszolgálótűzfal beállítása** az eszköztáron. Megnyílik az SQL Database kiszolgálóhoz tartozó **Tűzfalbeállítások** oldal.

   ![kiszolgálói tűzfalszabály](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Válasszon **ügyfél IP-cím hozzáadása** az aktuális IP-cím hozzáadása egy új tűzfalszabályt az eszköztáron. A tűzfalszabály az 1433-as portot egy egyedi IP-cím vagy egy IP-címtartomány számára nyithatja meg.

   > [!IMPORTANT]
   > Alapértelmezés szerint az összes Azure-szolgáltatás számára engedélyezett a hozzáférés az SQL Database tűzfalán keresztül. Válasszon **OFF** ezen az oldalon letiltása az Azure-szolgáltatásokhoz.
   >

5. Kattintson a **Mentés** gombra. A rendszer létrehoz egy kiszolgálószintű tűzfalszabályt az aktuális IP-címhez, és megnyitja az 1433-as portot a logikai kiszolgálón.

6. Zárja be a **tűzfalbeállítások** lapot.

SQL Server Management Studio vagy más választott eszköz használatával, mostantól csatlakozhat az SQL Database-kiszolgáló és az adatbázisokhoz az IP-címet a korábban létrehozott kiszolgálói rendszergazdai fiók használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Mentse ezeket az erőforrásokat, ha a [Következő lépésekre](#next-steps) szeretne lépni, és meg szeretné tudni, hogyan milyen módokon csatlakozhat az adatbázishoz és végezhet róla lekérdezéseket. Ha azonban törölni szeretné az ebben a rövid útmutatóban létrehozott erőforrásokat, használja az alábbi lépéseket.


1. A bal oldali menüben az Azure Portalon, válassza ki a **erőforráscsoportok** majd **myResourceGroup**.
2. Az erőforráscsoport lapján, válassza ki a **törlése**, típus **myResourceGroup** a szövegmezőbe, és válassza ki a **törlése**.

## <a name="next-steps"></a>További lépések

- Most, hogy rendelkezik egy adatbázissal, [kapcsolódhat hozzá és lekérdezéseket hajthat végre rajta](sql-database-connect-query.md) a kedvenc eszközeinek vagy nyelveinek egyikével, többek között
  - [Kapcsolódás és lekérdezés az SQL Server Management Studióval](sql-database-connect-query-ssms.md)
  - [Kapcsolódás és lekérdezés az Azure Data Studióval](https://docs.microsoft.com/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Az első adatbázis megtervezésével, a táblák létrehozásával és az adatok beszúrásával kapcsolatos információkért tekintse meg az alábbi oktatóanyagok egyikét:
  - [Az első Azure SQL-adatbázis megtervezése SSMS használatával](sql-database-design-first-database.md)
  - [Azure SQL-adatbázis megtervezése, és kapcsolódás C# és ADO.NET használatával](sql-database-design-first-database-csharp.md)
