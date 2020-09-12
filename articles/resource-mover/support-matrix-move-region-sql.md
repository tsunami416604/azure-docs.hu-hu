---
title: Az Azure SQL-erőforrások régiók közötti áthelyezésének támogatása az Azure-erőforrás-mozgató szolgáltatással.
description: Tekintse át az Azure SQL-erőforrások régiók közötti áthelyezésének támogatását az Azure-erőforrás-mozgató szolgáltatással.
author: rayne-wiselman
manager: evansma
ms.service: resource-move
ms.topic: conceptual
ms.date: 09/07/2020
ms.author: raynew
ms.openlocfilehash: 4925f6ffd2383c21f8ff9b0e3196d44fc15bb657
ms.sourcegitcommit: 3be3537ead3388a6810410dfbfe19fc210f89fec
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/10/2020
ms.locfileid: "89652869"
---
# <a name="support-for-moving-azure-sql-resources-between-azure-regions"></a>Azure SQL-erőforrások Azure-régiók közötti áthelyezésének támogatása

Ez a cikk az Azure SQL-erőforrások Azure-régiók közötti áthelyezésének támogatását és előfeltételeit foglalja össze az Azure-beli erőforrás-mozgató szolgáltatással.

## <a name="requirements"></a>Követelmények

A követelményeket a következő táblázat foglalja össze.

**Jellemző** | **Támogatott/nem támogatott** | **Részletek**
--- | --- | ---
**Azure SQL Database nagy kapacitású** | Nem támogatott | Az adatbázisok nem helyezhetők át az Azure SQL nagy kapacitású szolgáltatási szintjébe az erőforrás-mozgató szolgáltatással.
**Zónaredundancia** | Támogatott |  Támogatott áthelyezési beállítások:<br/><br/> – A zóna redundanciát támogató régiók között.<br/><br/> – A zónák redundanciát nem támogató régiók között.<br/><br/> – Egy olyan régió között, amely támogatja a zóna-redundancia-t egy olyan régióban, amely nem támogatja a zóna-redundanciát.<br/><br/> – Egy olyan régió között, amely nem támogatja a zóna-redundancia támogatását egy olyan régióban, amely támogatja a zóna-redundanciát. 
**Adatszinkronizálás** | Központ/szinkronizálási adatbázis: nem támogatott<br/><br/> Szinkronizálási tag: támogatott. | Szinkronizálási tag áthelyezésekor be kell állítania az adatszinkronizálást az új céladatbázis számára.
**Meglévő geo-replikáció** | Támogatott | A meglévő földrajzi replikákat a rendszer a célként megadott régió új elsődleges részére rendeli hozzá.<br/><br/> A beültetést az áthelyezés után inicializálni kell. [További információ](/azure/sql-database/sql-database-active-geo-replication-portal)
**Transzparens adattitkosítás (TDE) Bring Your Own Key (BYOK)** | Támogatott | [További](../key-vault/general/move-region.md) információ a Key vaultok régiók közötti áthelyezéséről.
**TDE szolgáltatás által felügyelt kulccsal** | Támogatott. |  [További](../key-vault/general/move-region.md) információ a Key vaultok régiók közötti áthelyezéséről.
**Dinamikus adatmaszkolási szabályok** | Támogatott. | A rendszer automatikusan átmásolja a szabályokat a célként megadott régióba az áthelyezés részeként. [További információ](https://docs.microsoft.com/azure/sql-database/sql-database-dynamic-data-masking-get-started-portal).
**Advanced Data Security** | Nem támogatott. | Áthidaló megoldás: állítsa be a SQL Server szinten a célként megadott régióban. [További információ](https://docs.microsoft.com/azure/sql-database/sql-database-advanced-data-security).
**Tűzfalszabályok** | Nem támogatott. | Megkerülő megoldás: tűzfalszabályok beállítása a célként megadott régióban lévő SQL Serverhoz. Az adatbázis-szintű tűzfalszabályok a forráskiszolgálóról a célkiszolgálóra másolódnak. [További információ](https://docs.microsoft.com/azure/sql-database/sql-database-server-level-firewall-rule).
**Naplózási házirendek** | Nem támogatott. | Az áthelyezés után a házirendek visszaállnak az alapértelmezett értékre. [Útmutató](https://docs.microsoft.com/azure/sql-database/sql-database-auditing) az alaphelyzetbe állításához.
**Biztonsági mentés megőrzése** | Támogatott. | A forrásadatbázis biztonsági mentési adatmegőrzési szabályzatait a rendszer átadja a céladatbázis számára. [Megtudhatja](/azure/sql-database/sql-database-long-term-backup-retention-configure) , hogyan módosíthatja a beállításokat az áthelyezés után.
**Automatikus hangolás** | Nem támogatott. | Áthidaló megoldás: az áthelyezés után állítsa be az automatikus finomhangolási beállításokat. [További információ](https://docs.microsoft.com/azure/sql-database/sql-database-automatic-tuning-enable).
**Adatbázis-riasztások** | Nem támogatott. | Megkerülő megoldás: az áthelyezés után riasztásokat állíthat be. [További információ](https://docs.microsoft.com/azure/sql-database/sql-database-insights-alerts-portal).
**Azure SQL Server stretch Database** | Nem támogatott | Az SQL Server stretch Database-adatbázisok nem helyezhetők át az erőforrás-mozgatóval.
**Azure Synapse Analytics** | Nem támogatott | A szinapszis Analytics (korábban Azure SQL Data Warehouse) nem helyezhető át az erőforrás-mozgatóval.
## <a name="next-steps"></a>Következő lépések

Próbálja ki az [Azure SQL-erőforrásokat](tutorial-move-region-sql.md) egy másik régióba az erőforrás-mozgató használatával.