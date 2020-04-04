---
title: Csatlakozás az sqlcmd-hez
description: Az sqlcmd parancssori segédprogrammal csatlakozhat egy Synapse SQL-készlethez, és lekérdezheti azt.
services: synapse-analytics
author: XiaoyuMSFT
manager: craigg
ms.service: synapse-analytics
ms.topic: conceptual
ms.subservice: ''
ms.date: 04/17/2018
ms.author: xiaoyul
ms.reviewer: igorstan
ms.custom: seo-lt-2019
ms.openlocfilehash: cfb1b7558f8d7675009a0ebc729cc1a560f03d12
ms.sourcegitcommit: d597800237783fc384875123ba47aab5671ceb88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/03/2020
ms.locfileid: "80633382"
---
# <a name="connect-to-synapse-sql-pool-with-sqlcmd"></a>Csatlakozás a Synapse SQL-készlethez sqlcmd segítségével
> [!div class="op_single_selector"]
> * [Power BI](sql-data-warehouse-get-started-visualize-with-power-bi.md)
> * [Azure Machine Learning](sql-data-warehouse-get-started-analyze-with-azure-machine-learning.md)
> * [Visual Studio](sql-data-warehouse-query-visual-studio.md)
> * [sqlcmd](sql-data-warehouse-get-started-connect-sqlcmd.md) 
> * [SSMS](sql-data-warehouse-query-ssms.md)
> 
> 

Az [sqlcmd][sqlcmd] parancssori segédprogrammal csatlakozhat egy SQL-készlethez, és lekérdezheti azt.  

## <a name="1-connect"></a>1. Csatlakozás
Az [sqlcmd][sqlcmd] alkalmazással való ismerkedéshez nyissa meg a parancssort, és írja be az **sqlcmd** parancsot, majd az SQL-készlet adatbázisának kapcsolati karakterláncát. A kapcsolati sztringben a következő paraméterekre van szükség:

* **Server (-S):** A kiszolgáló neve `<`kiszolgálónév`>`.database.windows.net formátumban.
* **Database (-d):** Az adatbázis neve.
* **Idézett azonosítók engedélyezése (-I):** Az SQL-készletpéldányhoz való csatlakozáshoz engedélyezni kell az idézett azonosítókat.

Az SQL Server-hitelesítés használatához meg kell adnia a felhasználónév/jelszó paramétereit:

* **Felhasználó (-U):** Kiszolgáló felhasználója `<`a Felhasználó űrlapon`>`
* **Password (-P):** A felhasználóhoz tartozó jelszó.

A kapcsolati sztring például a következőképpen nézhet ki:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
```

Az Azure Active Directory beépített hitelesítés használatához meg kell adnia az Azure Active Directory paramétereit:

* **Azure Active Directory Authentication (-G):** az Azure Active Directory használata a hitelesítéshez

A kapcsolati sztring például a következőképpen nézhet ki:

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -G -I
```

> [!NOTE]
> Az Active Directory használatával történő hitelesítéshez [engedélyeznie kell az Azure Active Directory-hitelesítést](sql-data-warehouse-authentication.md).
> 
> 

## <a name="2-query"></a>2. Lekérdezés
A kapcsolódás után kiadhatók a példányon a támogatott Transact-SQL utasítások.  Ebben a példában a lekérdezések elküldése interaktív módban történik.

```sql
C:\>sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I
1> SELECT name FROM sys.tables;
2> GO
3> QUIT
```

A következő példák bemutatják, hogyan lehet a lekérdezéseket kötegelt módban futtatni a -Q kapcsoló használatával vagy az SQL átirányításával az sqlcmd-re.

```sql
sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I -Q "SELECT name FROM sys.tables;"
```

```sql
"SELECT name FROM sys.tables;" | sqlcmd -S MySqlDw.database.windows.net -d Adventure_Works -U myuser -P myP@ssword -I > .\tables.out
```

## <a name="next-steps"></a>További lépések
Az sqlcmd-ben elérhető lehetőségekről további információt az [sqlcmd dokumentációjában](https://msdn.microsoft.com/library/ms162773.aspx) talál.
