---
title: "Az Azure SQL Database - automatikus hangolása |} Microsoft Docs"
description: "Az Azure SQL-adatbázis SQL-lekérdezés elemzi, és automatikusan alkalmazkodik felhasználói munkaterhelés."
services: sql-database
documentationcenter: 
author: jovanpop-msft
manager: jhubbard
editor: 
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: On Demand
ms.date: 09/19/2017
ms.author: jovanpop
ms.openlocfilehash: 10f8cca8e519b28f0fe29605419b860f73e04a87
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="automatic-tuning-in-azure-sql-database"></a>Az Azure SQL-adatbázis automatikus hangolása

Az Azure SQL Database egy teljes körűen felügyelt szolgáltatás, amely figyeli a lekérdezések, amelyek az adatbázishoz, és automatikusan javítja a teljesítményt az adatbázisban munkaterhelés. Az Azure SQL Database beépített [automatikus hangolása](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) eszközintelligencia mechanizmus, amely automatikusan hangolása és javítása a lekérdezések az adatbázis, a munkaterhelés dinamikusan testreszabásához. Az Azure SQL-adatbázis automatikus hangolása lehet, a legfontosabb funkciók is engedélyezheti az Azure SQL Database a lekérdezések teljesítményének optimalizálása érdekében.

> [!VIDEO https://channel9.msdn.com/Blogs/Azure-in-the-Enterprise/Enabling-Azure-SQL-Database-Auto-Tuning-at-Scale-for-Microsoft-IT/player]
>

## <a name="automatic-tuning-options"></a>Automatikus hangolási lehetőségeket

[Automatikus hangolása](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) az Azure SQL-adatbázis elérhető lehetőségek a következők:
 1. **A CREATE INDEX** , amely azonosítja az indexeket, javíthatja a teljesítményt a munkaterhelés, az indexek létrehozása, amely ellenőrzi, hogy azok a lekérdezések teljesítményének javítása.
 2. **DROP INDEX** , amely azonosítja a redundancia és ismétlődő indexek és indexek, amelyet nem használtak a hosszú idő alatt.
 3. **TERV REGRESSZIÓS JAVÍTÁSI** , amely az SQL-lekérdezések által használt végrehajtási terv, amik lassabbak, mint az előző jó terv azonosítja, és az utolsó ismert helyes terv használ, a regressed terv nem.

Az Azure SQL Database azonosítja **a CREATE INDEX**, **DROP INDEX**, és **REGRESSZIÓS JAVÍTÁSI terv** javaslatokat is optimalizálhatja az adatbázist, és megjeleníti őket az Azure-ban portál. A módosítani kívánt indexek azonosítása további információt talál [indexjavaslatok az Azure portálon található](sql-database-advisor-portal.md). Manuálisan alkalmazhatja javaslatok a portál használatával, vagy lehetővé teszik az Azure SQL Database automatikusan javaslatok alkalmazásához számítási feladat figyeléséhez a módosítás után, és győződjön meg arról, hogy a javaslat javult a számítási feladatok teljesítményére.

Automatikus hangolása beállítások egymástól függetlenül kapcsolható be- és kikapcsolása adatbázisonként, vagy logikai kiszolgálón konfigurált és az összes adatbázisra, amelyet a örököl beállításokat a kiszolgálón alkalmazott. Konfigurálás [automatikus hangolása](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) beállítások a kiszolgáló és a beállítások a kiszolgálón az adatbázisok öröklődés ajánlott módszer konfigurálása automatikus hangolása, mert az automatikus hangolási lehetőségeket kezelését megkönnyíti a nagy számú adatbázis.

Ez a cikk lépéseit lásd: [engedélyezze az automatikus hangolással](sql-database-automatic-tuning-enable.md) az Azure portál használatával.

## <a name="next-steps"></a>Következő lépések

- Engedélyezze az automatikus hangolással az Azure SQL Database és az automatikus, teljes mértékben felügyelhesse a számítási feladatok hangolási szolgáltatás segítségével [engedélyezze az automatikus hangolással](sql-database-automatic-tuning-enable.md).
- Manuális hangolása használatához tekintse át [javaslatok az Azure-portálon hangolása](sql-database-advisor-portal.md) és alkalmazza manuálisan a gazdarendszerhez a lekérdezések teljesítményének javítása.
- Beállítja a beépített funkciói nyújt további információt a [Azure SQL Database](https://azure.microsoft.com/blog/artificial-intelligence-tunes-azure-sql-databases/).
- Tudjon meg többet az [automatikus hangolása](https://docs.microsoft.com/sql/relational-databases/automatic-tuning/automatic-tuning) az Azure SQL Database és SQL Server 2017.
