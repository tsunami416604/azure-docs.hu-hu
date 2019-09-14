---
title: Azure SQL Database kibocsátási megjegyzések | Microsoft Docs
description: Ismerje meg a Azure SQL Database szolgáltatás új szolgáltatásait és funkcióit, valamint a Azure SQL Database dokumentációját
services: sql-database
author: stevestein
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 05/15/2019
ms.author: sstein
ms.openlocfilehash: ef19d22b5c47c51f5ee3f74a4d7ab06725f7ed41
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70968334"
---
# <a name="sql-database-release-notes"></a>SQL Database kibocsátási megjegyzések

Ez a cikk a jelenleg nyilvános előzetes verzióban elérhető SQL Database-szolgáltatásokat sorolja fel. SQL Database frissítésekhez és a tökéletesítésekhez lásd: [SQL Database Service Updates](https://azure.microsoft.com/updates/?product=sql-database). Az egyéb Azure-szolgáltatások frissítéseihez és tökéletesítéséhez tekintse meg a [Service Updates](https://azure.microsoft.com/updates)című témakört.

## <a name="features-in-public-preview"></a>A nyilvános előzetes verzióban elérhető funkciók

### <a name="single-databasetabsingle-database"></a>[Önálló adatbázis](#tab/single-database)

| Funkció | Részletek |
| ---| --- |
| Gyorsított adatbázis-helyreállítás önálló adatbázisokkal és rugalmas készletekkel | További információ: [gyorsított adatbázis-helyreállítás](sql-database-accelerated-database-recovery.md).|
|Megközelítőleg eltérő számú|További információkért lásd: [eltérők megközelítő darabszáma](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing).|
|Batch mód a Sortárindex létrehozását (a 150-es kompatibilitási szint alatt)|További információ: [Batch Mode on sortárindex létrehozását](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore).|
| Adatfelderítés és besorolás  |További információ: [Azure SQL Database és SQL Data Warehouse adatfelderítési & besorolása](sql-database-data-discovery-and-classification.md).|
| Rugalmas adatbázis-feladatok | További információ: [rugalmas feladatok létrehozása, konfigurálása és kezelése](elastic-jobs-overview.md). |
| Rugalmas lekérdezések | További információ: [rugalmas lekérdezés áttekintése](sql-database-elastic-query-overview.md). |
| Rugalmas tranzakciók | [Elosztott tranzakciók felhőalapú adatbázisok között](sql-database-elastic-transactions-overview.md). |
|Memória-engedélyezési visszajelzés (sor mód) (a 150-es kompatibilitási szint alatt)|További információ: [memória-engedélyezési visszajelzés (sor mód)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback).|
| A Azure Portal lekérdezés-szerkesztője |További információ: [a Azure Portal SQL Query Editor használata az adatok összekapcsolásához és lekérdezéséhez](sql-database-connect-query-portal.md).|
| R-szolgáltatások/gépi tanulás önálló adatbázisokkal és rugalmas készletekkel |További információ: [Machine Learning Services Azure SQL Database](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database).|
| Eldobott adatbázisok újbóli létrehozása felügyelt példányokkal |További információ: [eldobott adatbázisok ismételt létrehozása az Azure SQL felügyelt példányában](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266).|
| Replikálás felügyelt példányokkal |További információ: [replikáció konfigurálása Azure SQL Database felügyelt példány-adatbázisban](replication-with-sql-database-managed-instance.md).|
| Kiszolgáló nélküli számítási szint | További információ: [SQL Database kiszolgáló nélküli (előzetes verzió)](sql-database-serverless.md).|
|SQL Analytics|További információ: [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md).|
|Táblázat változó késleltetett fordítása (a 150-es kompatibilitási szint alatt)|További információ: [Table változó késleltetett fordítása](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation).|
| Fenyegetések észlelése felügyelt példányokkal |További információ: [a veszélyforrások észlelésének konfigurálása Azure SQL Database felügyelt példányban](sql-database-managed-instance-threat-detection.md).|
| Transzparens adattitkosítás (TDE) Bring Your Own Key (BYOK) és felügyelt példányokkal |További információ [: Azure SQL transzparens adattitkosítás az ügyfél által felügyelt kulcsokkal a Azure Key Vaultban: Bring Your Own Key támogatás](transparent-data-encryption-byok-azure-sql.md).|
| &nbsp; |

### <a name="managed-instancetabmanaged-instance"></a>[Felügyelt példány](#tab/managed-instance)

| Funkció | Részletek |
| ---| --- |
| <a href="/azure/sql-database/sql-database-instance-pools">Példányok készletei</a> | A kisebb SQL-példányok felhőbe való áttelepíthető kényelmes és költséghatékony módszer. |
| <a href="https://aka.ms/managed-instance-tde-byok">Saját titkosítási kulcsok használata</a> | Telepítse át az adatbázist a helyszíni környezetből, amely már engedélyezte a transzparens adattitkosítás (TDE) szolgáltatást a meglévő TDE-kulcsokkal. |
| <a href="https://aka.ms/managed-instance-failover-groups">Földrajzilag elosztott feladatátvételi csoportok</a> | Őrizze meg a példány másolatát egy másik régióban, és győződjön meg arról, hogy az adatai még a regionális katasztrófa-forgatókönyvben is elérhetők lesznek. |
| <a href="https://aka.ms/managed-instance-aadlogins">Példány szintű Azure AD-kiszolgálói rendszerbiztonsági tag (bejelentkezések)</a> | Hozzon létre kiszolgálói szintű bejelentkezéseket <a href="https://docs.microsoft.com/sql/t-sql/statements/create-login-transact-sql?view=azuresqldb-mi-current">a külső szolgáltatói utasítás Create login</a> parancsával. |
| [Tranzakciós replikáció](sql-database-managed-instance-transactional-replication.md) | A táblák változásainak replikálása más, felügyelt példányokra, különálló adatbázisokra vagy SQL Server példányokra helyezett adatbázisokra, illetve a táblák frissítése, ha egyes sorok módosulnak más felügyelt példányokban vagy SQL Server példányokban. |
| &nbsp; |

---

## <a name="updates"></a>Frissítések

SQL Database frissítéseinek és tökéletesítésének listáját lásd: [SQL Database szolgáltatás frissítései](https://azure.microsoft.com/updates/?product=sql-database).

Az összes Azure-szolgáltatás frissítéséhez és tökéletesítéséhez tekintse meg a [Service Updates](https://azure.microsoft.com/updates)című témakört.

## <a name="contribute-to-content"></a>Közreműködés a tartalomban

A Azure SQL Database dokumentációhoz való hozzájáruláshoz tekintse meg a [docs közreműködői útmutatóját](https://docs.microsoft.com/contribute/).
