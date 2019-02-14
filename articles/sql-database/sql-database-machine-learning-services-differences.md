---
title: Fontos különbség a Machine Learning-szolgáltatások (az r nyelv) az Azure SQL Database (előzetes verzió) – áttekintés
description: Ez a témakör az Azure SQL Database Machine Learning-szolgáltatások (az r nyelv) és az SQL Server Machine Learning-szolgáltatások közötti fő különbségeket ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning-services
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 01/31/2019
ms.openlocfilehash: 4350fb0e75f140e120ba6cd2f074ffa1816a8fce
ms.sourcegitcommit: de81b3fe220562a25c1aa74ff3aa9bdc214ddd65
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/13/2019
ms.locfileid: "56237484"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-and-sql-server"></a>Machine Learning-szolgáltatások az Azure SQL Database és az SQL Server közötti fő különbségeket

Az Azure SQL Database-ben a Machine Learning Services (with R) funkciói hasonlóak az [SQL Server Machine Learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning) funkcióihoz. Az alábbiakban ezek közötti fontosabb különbségeket.

## <a name="language-support"></a>Nyelvi támogatás

Az SQL Server R és Python használatával támogatással rendelkezik a [bővíthetőségi keretrendszerét](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework). Az SQL Database nem támogatja a mindkét nyelven. A legfontosabb különbségek a következők:

- Az R a SQL Database-ben az egyetlen támogatott nyelv. A Python jelenleg nem támogatott.
- Az R 3.4.4 verziószáma.
- Nem kell konfigurálása `external scripts enabled` keresztül `sp_configure`. Ha már [regisztrált](sql-database-machine-learning-services-overview.md#signup), machine learning-engedélyezve van az SQL-adatbázis.

## <a name="package-management"></a>Csomagkezelés

R csomag kezelés és telepítés működik különböző SQL Database és SQL Server között. Ezek a különbségek a következők:

- R-csomagok települnek keresztül [sqlmlutils](https://github.com/Microsoft/sqlmlutils) vagy [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- Csomagok nem hajtható végre a kimenő hálózati hívások. Ezt a korlátozást hasonlít a [tűzfalszabályok alapértéke a Machine Learning-szolgáltatások](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) az SQL-kiszolgálón, de nem lehet módosítani az SQL Database-ben.
- A rendszer nem támogatja, a csomagok telepítést vagy a használati függ (például Java) külső modulok vagy az operációs rendszer API-k hozzáférést.

## <a name="resource-governance"></a>Erőforrások szabályozása

Már nem R erőforrásai a korlátozásához [erőforrás-vezérlő](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) és külső erőforrás-készletek. R-erőforrások százalékos arányában az SQL Database-erőforrásokat, és függő melyik szolgáltatási szintet választja. További információkért lásd: [vásárlási modellek az Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

## <a name="security-isolation"></a>Biztonsági elkülönítés

Az Azure SQL Database az SQL Platform absztrakciós réteg (SQLPAL) el vannak különítve a külső folyamatokhoz. Ez az elkülönítés egy további R-szkriptek futtatásához biztonsági réteget nyújt.

## <a name="next-steps"></a>További lépések

- Tekintse meg a [SQL Server Machine Learning-szolgáltatások](https://docs.microsoft.com/sql/advanced-analytics) általános információkat dokumentációja
- Az Azure SQL Database használata a Machine Learning-szolgáltatások (az r nyelv) című témakörben talál [a rövid útmutató](sql-database-connect-query-r.md).
- További tudnivalók a [SQL Server R nyelv oktatóanyagok](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)