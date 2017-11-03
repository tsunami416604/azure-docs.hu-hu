---
title: "Az Azure SQL Database automatikus hangolása engedélyezése |} Microsoft Docs"
description: "Automatikus hangolása az Azure SQL adatbázis könnyen engedélyezheti."
services: sql-database
documentationcenter: 
author: veljko-msft
manager: drasumic
editor: vvasic
ms.assetid: 
ms.service: sql-database
ms.custom: monitor & tune
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: Inactive
ms.date: 09/19/2016
ms.author: veljko-msft
ms.openlocfilehash: 82db5996c1ba1f224593e4eaa5b3b0067755db49
ms.sourcegitcommit: e5355615d11d69fc8d3101ca97067b3ebb3a45ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/31/2017
---
# <a name="enable-automatic-tuning"></a>Automatikus hangolás engedélyezése

Az Azure SQL Database az automatikusan kezelt adatok szolgáltatása, amely folyamatosan figyeli a lekérdezéseket, és azonosítja a végrehajtandó műveletet a számítási feladatok teljesítményének javításával végezheti el. Áttekintheti javaslatok és manuálisan alkalmazhatja azokat, vagy lehetővé teszik az Azure SQL Database automatikusan alkalmazza a javítási műveleteket – ez az úgynevezett **automatikus hangolási módot**. Az automatikus hangolással a kiszolgáló vagy az adatbázis szintjén engedélyezhető.

## <a name="enable-automatic-tuning-on-server"></a>Engedélyezze a kiszolgálón automatikus hangolása

Engedélyezi az automatikus hangolással Azure SQL adatbázis-kiszolgálón, keresse fel a kiszolgálót az Azure portálon, és válassza ki **automatikus hangolása** a menüben. Válassza ki a engedélyezése, és válassza ki az automatikus hangolási lehetőségeket **alkalmaz**:

![Kiszolgáló](./media/sql-database-automatic-tuning-enable/server.png)

A kiszolgáló automatikus hangolási lehetőségeket a kiszolgálón lévő összes adatbázis is vonatkozik. Alapértelmezés szerint minden adatbázisok a konfigurációs öröklése a fölérendelt kiszolgáló, de ez felül, és az egyes adatbázisok külön-külön megadott.

## <a name="configure-automatic-tuning-on-database"></a>Konfigurálja az adatbázis automatikus hangolása

Az Azure-portál lehetővé teszi, hogy egyesével adja meg az automatikus hangolási beállítás az összes adatbázisra.

> [!NOTE]
> Az általános javasoljuk, hogy ugyanazokat a konfigurációs beállításokat is az összes adatbázis automatikusan alkalmazza az automatikus hangolási beállítás kiszolgálói szinten kezelését. Konfigurálja automatikus hangolással a egyedi adatbázis Ha az adatbázis különböző, hogy mások ugyanazon a kiszolgálón.
>

Egy önálló adatbázis automatikus hangolása engedélyezéséhez nyissa meg az adatbázis az Azure portálon, és majd, és válassza ki **automatikus hangolása**. Beállíthatja, hogy a beállítások öröklése a kiszolgálóról a beállítás kiválasztásával egyetlen adatbázis, vagy a konfigurációs adatbázis egyesével adja meg.

![Adatbázis](./media/sql-database-automatic-tuning-enable/database.png)

Miután kiválasztotta a megfelelő konfigurációs, kattintson a **alkalmaz**.

## <a name="next-steps"></a>Következő lépések
* Olvassa el a [automatikus hangolási cikk](sql-database-automatic-tuning.md) tudhat meg többet automatikus hangolása és szerepéről javítják a teljesítményt.
* Lásd: [teljesítmény javaslatok](sql-database-advisor.md) teljesítmény javaslatok az Azure SQL Database áttekintését.
* Lásd: [lekérdezési teljesítmény Insights](sql-database-query-performance.md) a teljesítményre gyakorolt hatását a leggyakoribb lekérdezések megtekintésének megismerése.
