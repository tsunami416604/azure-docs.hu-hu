---
title: Hozzon létre egy kiszolgálószintű tűzfalszabályt – Azure SQL Database |} A Microsoft Docs
description: Hozzon létre egy SQL Database kiszolgálószintű tűzfalszabályt egyetlen vagy készletezett adatbázis
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
ms.date: 01/25/2019
ms.openlocfilehash: 2374707d3fdf4d389a7c96c18767d3c52a9efb2f
ms.sourcegitcommit: 039263ff6271f318b471c4bf3dbc4b72659658ec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/06/2019
ms.locfileid: "55757411"
---
# <a name="quickstart-create-a-server-level-firewall-rule-in-azure-sql-database-using-the-azure-portal"></a>Gyors útmutató: Kiszolgálószintű tűzfalszabály létrehozása az Azure SQL Database az Azure portal használatával

Ez a rövid útmutató végigvezeti egy kiszolgálószintű tűzfalszabály létrehozása az Azure SQL Database az Azure portal használatával, hogy egy helyszíni erőforrás-ból kapcsolódik hozzá.

## <a name="prerequisites"></a>Előfeltételek

Ebben a rövid útmutatóban létrehozott erőforrásokat használja [hozzon létre egy önálló adatbázis az Azure portal használatával](sql-database-single-database-get-started.md) kiindulási pontként.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="create-a-server-level-ip-firewall-rule"></a>IP-kiszolgálószintű tűzfalszabály létrehozása

Az SQL Database szolgáltatás egyetlen vagy készletezett adatbázisok adatbázis-kiszolgáló szintjén hoz létre tűzfalat. Ez a tűzfal megakadályozza, hogy csatlakozzanak a kiszolgálóhoz vagy az önálló vagy készletezett adatbázisok bármelyikét, ha nem hoz létre egy IP-tűzfalszabály, megnyitja a tűzfalat ügyfélalkalmazások számára. Azure-on kívülről IP-címről érkező kapcsolatot hozzon létre egy tűzfalszabályt egy adott IP-címet vagy címtartományt, amelyet szeretne csatlakozni. További információ a kiszolgálószintű és adatbázisszintű IP-tűzfalszabályainak: [SQL Database kiszolgálószintű és adatbázisszintű IP-tűzfalszabályainak](sql-database-firewall-configure.md).

> [!NOTE]
> Az SQL Database az 1433-as porton kommunikál. Csatlakozás a vállalati hálózaton belülről próbál, ha a hálózati tűzfal előfordulhat, hogy nem engedélyezett a kimenő forgalmat az 1433-as porton keresztül. Ha igen, nem lehet csatlakoztatni az Azure SQL Database-kiszolgálóhoz, ha az informatikai részleg megnyitja az 1433-as porton.
> [!IMPORTANT]
> 0.0.0.0 tűzfalszabály lehetővé teszi, hogy az Azure-szolgáltatásokhoz való továbbítása a kiszolgálószintű tűzfalszabályt, és megpróbál csatlakozni egy önálló vagy készletezett adatbázis-kiszolgálón keresztül. A virtuális hálózati szabályok használatával kapcsolatos további információkért lásd: [virtuális hálózati szabályok IP szabályok alternatívájaként](sql-database-firewall-configure.md#virtual-network-rules-as-alternatives-to-ip-rules).

Kövesse az alábbi lépéseket az ügyfél IP-címe a kiszolgálószintű IP tűzfalszabály létrehozása, és engedélyezheti a külső kapcsolatokat csak az IP-cím az SQL Database-tűzfalon keresztül.

1. Után az [előfeltételként szükséges Azure SQL database](#prerequisites) üzembe helyezés befejeződött, válassza a **SQL-adatbázisok** elemet a bal oldali menüben, és válassza a **mySampleDatabase** a a **SQL-adatbázisok** lapot. Megnyílik az adatbázis áttekintő oldala, amelyen látható a teljes kiszolgálónév (például: **mynewserver-20170824.database.windows.net**), valamint a további konfigurálható beállítások.

2. Másolja ki a teljes kiszolgálónévre a kiszolgálóhoz és a többi rövid útmutató az adatbázisokhoz való csatlakozáskor használandó.

   ![kiszolgáló neve](./media/sql-database-get-started-portal/server-name.png)

3. Válassza ki **kiszolgálótűzfal beállítása** az eszköztáron. A **tűzfalbeállítások** megnyílik az adatbázis-kiszolgáló.

   ![kiszolgálószintű IP-tűzfalszabály](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Válasszon **ügyfél IP-cím hozzáadása** az aktuális IP-cím hozzáadása egy új kiszolgálószintű IP-tűzfalszabály az eszköztáron. IP-kiszolgálószintű tűzfalszabály egyetlen IP-címet vagy egy IP-címtartományt az 1433-as port is megnyithatja.

   > [!IMPORTANT]
   > Alapértelmezés szerint az összes Azure-szolgáltatás számára engedélyezett a hozzáférés az SQL Database tűzfalán keresztül. Válasszon **OFF** ezen az oldalon letiltása az Azure-szolgáltatásokhoz.
   >

5. Kattintson a **Mentés** gombra. Egy IP-kiszolgálószintű tűzfalszabályt az aktuális IP-címhez, az SQL Database-kiszolgálóhoz az 1433-as port megnyitása jön létre.

6. Zárja be a **tűzfalbeállítások** lapot.

SQL Server Management Studio vagy más választott eszköz használatával, mostantól csatlakozhat az SQL Database-kiszolgáló és az adatbázisokhoz az IP-címet a korábban létrehozott kiszolgálói rendszergazdai fiók használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Mentse ezeket az erőforrásokat, ha a [Következő lépésekre](#next-steps) szeretne lépni, és meg szeretné tudni, hogyan milyen módokon csatlakozhat az adatbázishoz és végezhet róla lekérdezéseket. Ha azonban törölni szeretné az ebben a rövid útmutatóban létrehozott erőforrásokat, használja az alábbi lépéseket.

1. A bal oldali menüben az Azure Portalon, válassza ki a **erőforráscsoportok** majd **myResourceGroup**.
2. Az erőforráscsoport lapján, válassza ki a **törlése**, típus **myResourceGroup** a szövegmezőbe, és válassza ki a **törlése**.

## <a name="next-steps"></a>További lépések

- Most, hogy rendelkezik egy adatbázissal, [kapcsolódhat hozzá és lekérdezéseket hajthat végre rajta](sql-database-connect-query.md) a kedvenc eszközeinek vagy nyelveinek egyikével, többek között
  - [Kapcsolódás és lekérdezés az SQL Server Management Studióval](sql-database-connect-query-ssms.md)
  - [Kapcsolódás és lekérdezés az Azure Data Studióval](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Az első adatbázis megtervezésével, a táblák létrehozásával és az adatok beszúrásával kapcsolatos információkért tekintse meg az alábbi oktatóanyagok egyikét:
  - [Az első önálló adatbázis az SSMS használatával Azure SQL Database megtervezése](sql-database-design-first-database.md)
  - [Az Azure SQL Database egy adatbázis tervezése és csatlakozás az C# és az ADO.NET](sql-database-design-first-database-csharp.md)
