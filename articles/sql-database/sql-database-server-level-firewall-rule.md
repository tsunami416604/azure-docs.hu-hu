---
title: Kiszolgálószintű tűzfalszabály létrehozása
description: SQL Database-kiszolgálószintű tűzfalszabály létrehozása egy- és készletezésű adatbázisokhoz
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
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "73818259"
---
# <a name="quickstart-create-a-server-level-firewall-rule-for-single-and-pooled-databases-using-the-azure-portal"></a>Rövid útmutató: Hozzon létre egy kiszolgálószintű tűzfalszabályt egy- és készletalapú adatbázisokhoz az Azure Portal használatával

Ez a rövid útmutató bemutatja, hogyan hozhat létre [egy kiszolgálószintű tűzfalszabályt](sql-database-firewall-configure.md) az azure-beli SQL Database-ben az Azure SQL Database-ben az Azure Portal használatával, amely lehetővé teszi az adatbázis-kiszolgálókhoz, az egyes adatbázisokhoz, valamint a rugalmas készletekhez és azok adatbázisaihoz való csatlakozást. A tűzfalszabály szükséges csatlakozni más Azure-erőforrások és a helyszíni erőforrások.

## <a name="prerequisites"></a>Előfeltételek

Ez a rövid útmutató az [egyetlen adatbázis létrehozása az Azure Portal kiindulási pontként használva](sql-database-single-database-get-started.md) létrehozott erőforrásokat használja.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra.](https://portal.azure.com/)

## <a name="create-a-server-level-ip-firewall-rule"></a>Kiszolgálószintű IP-tűzfalszabály létrehozása

Az SQL Database szolgáltatás létrehoz egy tűzfalat az adatbázis-kiszolgáló szintjén az egy- és készletezésű adatbázisokhoz. Ez a tűzfal megakadályozza, hogy az ügyfélalkalmazások csatlakozzanak a kiszolgálóhoz vagy annak bármely egy- vagy készletezésű adatbázisához, hacsak nem hoz létre IP-tűzfalszabályt a tűzfal megnyitásához. Az Azure-on kívüli IP-címről származó kapcsolat hoz létre egy tűzfalszabályt egy adott IP-címhez vagy címtartományhoz, amelyet csatlakozni szeretne. A kiszolgálószintű és adatbázis-szintű IP-tűzfalszabályokról az [SQL Database-kiszolgáló- és adatbázis-szintű IP-tűzfalszabályok](sql-database-firewall-configure.md)című témakörben talál további információt.

> [!NOTE]
> Az SQL Database az 1433-as porton kommunikál. Ha vállalati hálózaton belülről próbál csatlakozni, elképzelhető, hogy a hálózati tűzfal nem engedélyezi a kimenő forgalmat az 1433-as porton keresztül. Ha igen, nem tud csatlakozni az Azure SQL Database-kiszolgálóhoz, kivéve, ha az informatikai részleg megnyitja az 1433-as portot.
> [!IMPORTANT]
> A 0.0.0.0 tűzfalszabály lehetővé teszi, hogy az összes Azure-szolgáltatás áthaladjon a kiszolgálószintű tűzfalszabályon, és megkíséreljen csatlakozni egy vagy egyesített adatbázishoz a kiszolgálón keresztül. 

Az alábbi lépésekkel hozzon létre egy kiszolgálószintű IP-tűzfalszabályt az ügyfél IP-címéhez, és engedélyezze a külső kapcsolatot az SQL-adatbázis tűzfalán keresztül csak az IP-címhez.

1. Miután az [előfeltételként az Azure SQL-adatbázis](#prerequisites) telepítése befejeződött, válassza az **SQL-adatbázisok** a bal oldali menüben, és válassza a **mySampleDatabase** az **SQL adatbázisok** lapon. Megnyílik az adatbázis áttekintő oldala, amelyen látható a teljes kiszolgálónév (például: **mynewserver-20170824.database.windows.net**), valamint a további konfigurálható beállítások.

2. Másolja a teljesen minősített kiszolgálónevet, hogy más rövid útmutatóban kapcsolódjon a kiszolgálóhoz és annak adatbázisaihoz.

   ![kiszolgáló neve](./media/sql-database-get-started-portal/server-name.png)

3. Az eszköztáron válassza a **Kiszolgálótűzfal beállítása** lehetőséget. Megnyílik az adatbázis-kiszolgáló **tűzfalbeállításai** lap.

   ![kiszolgálószintű IP-tűzfal szabály](./media/sql-database-get-started-portal/server-firewall-rule.png)

4. Válassza az **eszköztár Ügyfél IP hozzáadása** parancsát, ha az aktuális IP-címet hozzá szeretné adni egy új kiszolgálószintű IP-tűzfalszabályhoz. A kiszolgálószintű IP-tűzfalszabály egyetlen IP-címhez vagy IP-címtartományhoz is megnyithatja az 1433-as portot.

   > [!IMPORTANT]
   > Alapértelmezés szerint az SQL Database tűzfalon keresztüli hozzáférés le van tiltva az összes Azure-szolgáltatás. Válassza **a BE** lehetőséget ezen a lapon, ha engedélyezni szeretné a hozzáférést az összes Azure-szolgáltatáshoz.
   >

5. Kattintson a **Mentés** gombra. Az SQL Database-kiszolgálón az aktuális 1433-as IP-címmegnyitásához kiszolgálószintű IP-tűzfalszabály jön létre.

6. Zárja be a **Tűzfal beállításai** lapot.

Az SQL Server Management Studio vagy más választott eszköz használatával most már csatlakozhat az SQL Database kiszolgálóhoz és annak adatbázisaihoz erről az IP-címről a korábban létrehozott kiszolgálófelügyeleti fiók használatával.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Mentse ezeket az erőforrásokat, ha a [Következő lépésekre](#next-steps) szeretne lépni, és meg szeretné tudni, hogyan milyen módokon csatlakozhat az adatbázishoz és végezhet róla lekérdezéseket. Ha azonban törölni szeretné az ebben a rövid útmutatóban létrehozott erőforrásokat, kövesse az alábbi lépéseket.

1. Az Azure Portal bal oldali menüjében válassza az **Erőforráscsoportok** lehetőséget, majd válassza a **myResourceGroup**lehetőséget.
2. Az erőforráscsoport lapján válassza a **Törlés**lehetőséget, írja be a **myResourceGroup** parancsot a szövegmezőbe, majd kattintson a **Törlés gombra.**

## <a name="next-steps"></a>További lépések

- Most, hogy rendelkezik egy adatbázissal, [kapcsolódhat hozzá és lekérdezéseket hajthat végre rajta](sql-database-connect-query.md) a kedvenc eszközeinek vagy nyelveinek egyikével, többek között
  - [Kapcsolódás és lekérdezés az SQL Server Management Studióval](sql-database-connect-query-ssms.md)
  - [Kapcsolódás és lekérdezés az Azure Data Studióval](/sql/azure-data-studio/quickstart-sql-database?toc=/azure/sql-database/toc.json)
- Az első adatbázis megtervezésével, a táblák létrehozásával és az adatok beszúrásával kapcsolatos információkért tekintse meg az alábbi oktatóanyagok egyikét:
  - [Az első egyetlen adatbázis tervezése az Azure SQL-adatbázisban az SSMS használatával](sql-database-design-first-database.md)
  - [Egyetlen adatbázis tervezése az Azure SQL Database-ben, és csatlakozás a C# és ADO.NET](sql-database-design-first-database-csharp.md)
