---
title: Az Azure SQL Database kibocsátási megjegyzései |} A Microsoft Docs
description: Ismerje meg az új funkciók és fejlesztések az Azure SQL Database szolgáltatás és az Azure SQL Database-dokumentáció
services: sql-database
author: stevestein
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: be39aa63cf923e74dc7ed13ef78ba554ffd3b8a4
ms.sourcegitcommit: cf438e4b4e351b64fd0320bf17cc02489e61406a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2019
ms.locfileid: "67658019"
---
# <a name="sql-database-release-notes"></a>Az SQL Database kibocsátási megjegyzései

Ez a cikk felsorolja az SQL Database funkciói, amely jelenleg nyilvános előzetes verzióban érhető el. SQL Database-frissítéseket és fejlesztéseket: [SQL Database szolgáltatási hírek](https://azure.microsoft.com/updates/?product=sql-database). Frissítések és más Azure-szolgáltatások fejlesztései: [Szolgáltatásfrissítésekkel](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Nyilvános előzetes verzióban elérhető funkciók

| Funkció | Részletek |
| ---| --- |
| Az önálló adatbázisok és rugalmas készletek gyorsított adatbázis-helyreállítás | További információ: [gyorsított adatbázis-helyreállítás](sql-database-accelerated-database-recovery.md).|
|Különböző hozzávetőleges száma|További információ: [hozzávetőleges eltérők darabszáma](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|(A kompatibilitási szint 150) Sortárindex kötegelt mód|További információ: [Sortárindex kötegelt módban](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Adatfelderítés és besorolás  |További információ: [Azure SQL Database és az SQL Data Warehouse adatfelderítés és besorolás](sql-database-data-discovery-and-classification.md).|
| Rugalmas adatbázis-feladatok | További információ: [létrehozását, konfigurálását és kezelését, az elastic jobs](elastic-jobs-overview.md). |
| Rugalmas lekérdezések | További információ: [rugalmas lekérdezés – áttekintés](sql-database-elastic-query-overview.md). |
| Rugalmas tranzakciók | [Elosztott tranzakciók több felhőalapú adatbázisban](sql-database-elastic-transactions-overview.md). |
|A visszajelzés memóriabeli ideiglenes (sor mód) (a kompatibilitási szint 150)|További információ: [(sor mód) visszajelzés memóriabeli ideiglenes](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| A Lekérdezésszerkesztő az Azure Portalon |További információ: [csatlakozás és adatok lekérdezése az Azure portal SQL-Lekérdezésszerkesztő használatával](sql-database-connect-query-portal.md).|
| Az R services / gépi tanulás az önálló adatbázisok és rugalmas készletek |További információ: [Machine Learning-szolgáltatások az Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
| Hozza létre újra a felügyelt példányok eldobott adatbázisok |További információ: [hozza létre újból eldobott adatbázisok az Azure SQL felügyelt példánya](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| Replikáció a felügyelt példány |További információ: [-replikálás konfigurálása az Azure SQL Database felügyelt példány adatbázisa a](replication-with-sql-database-managed-instance.md).|
| Kiszolgáló nélküli számítási szint | További információ: [SQL Database kiszolgáló nélküli (előzetes verzió)](sql-database-serverless.md).|
|SQL Analytics|További információ: [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Tábla változó a Funkciófrissítéseket fordítási (a kompatibilitási szint 150)|További információ: [tábla változó a Funkciófrissítéseket fordítási](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| A felügyelt példány fenyegetésészlelési |További információ: [fenyegetésészlelés konfigurálása az Azure SQL Database felügyelt példány](sql-database-managed-instance-threat-detection.md).|
| Transzparens adattitkosítás (TDE) a Bring Your Own Key (BYOK) felügyelt példányok |További információ: [Azure SQL transzparens adattitkosítás az ügyfél által felügyelt kulcsok Azure Key vaultban: Bring Your Own Key-támogatás](transparent-data-encryption-byok-azure-sql.md).|
| &nbsp; |

## <a name="updates"></a>Frissítések

Az SQL Database-frissítéseket és fejlesztéseket listáját lásd: [SQL Database szolgáltatási hírek](https://azure.microsoft.com/updates/?product=sql-database).

Frissítések és az összes Azure-szolgáltatások fejlesztései: [Szolgáltatásfrissítésekkel](https://azure.microsoft.com/updates).

## <a name="contribute-to-content"></a>Tartalmi közreműködés

Az Azure SQL Database-dokumentációban való közreműködéshez, tekintse meg a [Docs-közreműködői útmutató](https://docs.microsoft.com/contribute/).
