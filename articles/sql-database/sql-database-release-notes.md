---
title: Az Azure SQL Database kibocsátási megjegyzései |} A Microsoft Docs
description: Ismerje meg az új funkciók és fejlesztések az Azure SQL Database szolgáltatás és az Azure SQL Database-dokumentáció
services: sql-database
author: CarlRabeler
manager: craigg
ms.service: sql-database
ms.subservice: service
ms.devlang: ''
ms.topic: conceptual
ms.date: 04/10/2019
ms.author: carlrab
ms.openlocfilehash: 9b961436c81282381f963d16c6c6dd5f289d1259
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "59495105"
---
# <a name="sql-database-release-notes"></a>Az SQL Database kibocsátási megjegyzései

Ez a cikk felsorolja az új funkciók és fejlesztések az SQL Database szolgáltatás és az SQL Database-dokumentáció. Az SQL Database szolgáltatás javításai, lásd még [SQL Database szolgáltatási hírek](https://azure.microsoft.com/updates/?product=sql-database). Más Azure-szolgáltatások fejlesztései, lásd: [Szolgáltatásfrissítésekkel](https://azure.microsoft.com/updates).

## <a name="features-in-public-preview"></a>Nyilvános előzetes verzióban elérhető funkciók

| Szolgáltatás | Részletek |
| ---| --- |
| Rugalmas adatbázis-feladatok | További információ: [létrehozását, konfigurálását és kezelését a rugalmas feladatok](elastic-jobs-overview.md) |
| Rugalmas tranzakciók | [Elosztott tranzakciók több felhőalapú adatbázisban](sql-database-elastic-transactions-overview.md) |
| Rugalmas lekérdezések | További információ: [rugalmas lekérdezés – áttekintés](sql-database-elastic-query-overview.md) |
| Replikáció a felügyelt példány |További információ: [-replikálás konfigurálása a egy Azure SQL Database felügyelt példány adatbázisa](replication-with-sql-database-managed-instance.md)|
| Felügyelt példányokkal rendelkező példány rendezése |További információ: [a PowerShell szolgáltatás használatával az Azure SQL Database felügyelt példány létrehozása Azure Resource Manager-sablonnal](./scripts/sql-managed-instance-create-powershell-azure-resource-manager-template.md)|
| Az R services / gépi tanulás az önálló adatbázisok és rugalmas készletek |További információ: [Machine Learning-szolgáltatások az Azure SQL Database-ben](https://docs.microsoft.com/sql/advanced-analytics/what-s-new-in-sql-server-machine-learning-services?view=sql-server-2017#machine-learning-services-in-azure-sql-database)|
| Az önálló adatbázisok és rugalmas készletek gyorsított adatbázis-helyreállítás | További információ: [gyorsított adatbázis-helyreállítás](sql-database-accelerated-database-recovery.md)|
| Adatfelderítés és besorolás  |További információ: [Azure SQL Database és az SQL Data Warehouse adatfelderítés és besorolás](sql-database-data-discovery-and-classification.md)|
| Transzparens adattitkosítás (TDE) a Bring Your Own Key (BYOK) felügyelt példányok |További információ: [Azure SQL transzparens adattitkosítás az ügyfél által felügyelt kulcsok Azure Key vaultban: Bring Your Own Key-támogatás](transparent-data-encryption-byok-azure-sql.md)|
| Hozza létre újra a felügyelt példányok eldobott adatbázisok |További információ: [hozza létre újból eldobott adatbázisok az Azure SQL felügyelt példánya](https://medium.com/azure-sqldb-managed-instance/re-create-dropped-databases-in-azure-sql-managed-instance-dc369ed60266)|
| A felügyelt példány fenyegetésészlelési |További információ: [fenyegetésészlelés konfigurálása az Azure SQL Database felügyelt példánya](sql-database-managed-instance-threat-detection.md)|
| Nagy kapacitású szolgáltatási szintekről az önálló adatbázisok |További információ: [akár 100 TB kapacitású szolgáltatási szintjei](sql-database-service-tier-hyperscale.md)|
| A Lekérdezésszerkesztő az Azure Portalon |További információ: [csatlakozás és adatok lekérdezése az Azure portal SQL-Lekérdezésszerkesztő használatával](sql-database-connect-query-portal.md)|
|Különböző hozzávetőleges száma|További információ: [hozzávetőleges eltérők darabszáma](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#approximate-query-processing)|
|(A kompatibilitási szint 150) Sortárindex kötegelt mód|További információ: [Sortárindex kötegelt mód](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#batch-mode-on-rowstore)|
|A visszajelzés memóriabeli ideiglenes (sor mód) (a kompatibilitási szint 150)|További információ: [Memóriaengedély visszajelzését (sor mód)](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#row-mode-memory-grant-feedback)|
|Tábla változó a Funkciófrissítéseket fordítási (a kompatibilitási szint 150)|További információ: [tábla változó a Funkciófrissítéseket fordítás](https://docs.microsoft.com/sql/relational-databases/performance/intelligent-query-processing#table-variable-deferred-compilation)|
|SQL Analytics|További információ: [Azure SQL Analytics](../azure-monitor/insights/azure-sql.md)|
| Felügyelt példányok időzóna támogatása|További információkért lásd: [időzóna az Azure SQL Database felügyelt példánya](sql-database-managed-instance-timezone.md)|
|||

## <a name="march-2019"></a>2019. március

### <a name="service-improvements"></a>Szolgáltatás fejlesztései

| Szolgáltatás fejlesztései | Részletek |
| --- | --- |
| Általános elérhetőség: Olvasásra optimalizált felskálázás támogatása az Azure SQL Database-nél | További információkért lásd: [olvasási horizontális felskálázás](sql-database-read-scale-out.md)|
| &nbsp; |

### <a name="documentation-improvements"></a>Dokumentáció fejlesztései

| Dokumentáció fejlesztései | Részletek |
| --- | --- |
| Felügyelt példányok időzóna támogatása|További információkért lásd: [időzóna az Azure SQL Database felügyelt példánya](sql-database-managed-instance-timezone.md)|
| A hozzáadott log egyetlen adatbázisok korlátai|További információkért lásd: [egyetlen adatbázis virtuális mag erőforráskorlátok](sql-database-vcore-resource-limits-single-databases.md).|
| Hozzáadott log rugalmas készletek és a készletezett adatbázisok korlátai|További információkért lásd: [rugalmas adatbáziskészletekhez virtuális mag erőforráskorlátok](sql-database-vcore-resource-limits-elastic-pools.md).|
| A hozzáadott tranzakciós napló arány cégirányítási| Hozzáadott új tartalmak [tranzakciós napló arány cégirányítási](sql-database-resource-limits-database-server.md#transaction-log-rate-governance).|
| Frissített PowerShell-minták az önálló adatbázisok és rugalmas készletek az.sql modul használata | További információkért lásd: [önálló adatbázisok és rugalmas készletek PowerShell-minták](sql-database-powershell-samples.md#single-database-and-elastic-pools).|
| &nbsp; |

## <a name="february-2019"></a>2019. február

### <a name="service-improvements"></a>Szolgáltatás fejlesztései

| Szolgáltatás fejlesztései | Részletek |
| --- | --- |
|A folytatható online index létrehozását már általánosan elérhető| További információkért lásd: [a Create Index](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql).|
|Felügyelt példány támogatása továbbfejlesztett útvonaltáblák| További információkért lásd: [hálózati követelmények](sql-database-managed-instance-connectivity-architecture.md#network-requirements).|
|Adatbázis átnevezése támogatja a felügyelt példány | További részletekért tekintse meg a [ALTER DATABASE](https://docs.microsoft.com/sql/t-sql/statements/alter-database-transact-sql?view=azuresqldb-mi-current) és [sp_rename szintaxis](https://docs.microsoft.com/sql/relational-databases/system-stored-procedures/sp-rename-transact-sql) szintaxist.|
|SQL-adatbázis a Stream Analytics referenciaadatok forrásaként. | További információkért lásd: [Stream Analytics](https://azure.microsoft.com/services/stream-analytics/).|
|Data Migration Assistant felügyelt példányt támogat. |További információkért lásd: [What's new in DMA](https://docs.microsoft.com/sql/dma/dma-whatsnew).|
|Az SQL Server Migration Assistant szolgáltatást támogat a felügyelt példány cél readiness assessment. | További információkért lásd: [SQL Server Migration Assistant](https://docs.microsoft.com/sql/ssma/sql-server-migration-assistant).
|Adatok áttelepítése szolgáltatás támogatja az Amazon RDS felügyelt példányába történő migrálás | További információkért lásd: [oktatóanyag: A távoli asztali szolgáltatások SQL Server migrálása az Azure SQL Database vagy Azure SQL Database felügyelt példány online DMS használatával](../dms/tutorial-rds-sql-server-azure-sql-and-managed-instance-online.md).|
| &nbsp; |

### <a name="documentation-improvements"></a>Dokumentáció fejlesztései

| Dokumentáció fejlesztései | Részletek |
| --- | --- |
|A felügyelt példány üzembe helyezési lehetőség magyarázata|Frissítve azzal a pontosítással önálló adatbázis, a rugalmas készlet és a felügyelt példány üzembe helyezési beállítások alkalmazhatósági cikkeket. |
|DTU-alapú vásárlási modell frissített tempdb méretei | További információkért lásd: [Tempdb-adatbázis az SQL Database](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database#tempdb-database-in-sql-database).|
|Frissített importálása és exportálása a felügyelt példány támogatás bacpac-fájlba| További információkért lásd: [importálás BACPAC a](sql-database-import.md) és [exportálása BACPAC](sql-database-export.md). |
| &nbsp; |


## <a name="january-2019"></a>2019. január

### <a name="service-improvements"></a>Szolgáltatás fejlesztései

| Szolgáltatás fejlesztései | Részletek |
| --- | --- |
| A számítási erőforrások további granularitási lehetőségei | Az általános célú és a kritikus fontosságú üzleti szolgáltatás réteget [önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md) és [rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md) most már további részletes számítási lehetőségek.|
| A naplórekordok megtekintése a felügyelt példány az Azure Portalon | Megtekintés [naplórekordok felügyelt példányok](sql-database-managed-instance-auditing.md) az Azure Portalon mostantól támogatott.|
| Speciális veszélyforrások elleni duplikálásészlelési szolgáltatását a speciális adatbiztonság neve: | Új előzetes threat detection funkció [adatok biztonságú](sql-advanced-threat-protection.md) önálló adatbázisok, rugalmas készletek és a felügyelt példányok. |
| &nbsp; |

### <a name="documentation-improvements"></a>Dokumentáció fejlesztései

| Dokumentáció fejlesztései | Részletek |
| --- | --- |
| Felügyelt példányok és a tranzakciós replikáció | Új cikk használatáról [tranzakciós replikáció, a felügyelt példány](replication-with-sql-database-managed-instance.md) |
| Új Azure ad-ben a felügyelt példány – oktatóanyag | Ez [Azure AD a felügyelt példány](sql-database-managed-instance-aad-security-tutorial.md) oktatóanyag azt mutatja be, tesztelése és konfigurálása kell felügyelt példány biztonsági az Azure AD bejelentkezési adatok használata. |
| Frissített tartalom a Transact-SQL-parancsfájlok használatával feladatok automatizálásához | Frissítve, és a tartalom egyértelművé [feladat automatizálása a Transact-SQL-parancsfájlok használatával](sql-database-job-automation-overview.md) önálló adatbázisok, rugalmas készletek és a felügyelt példány |
| Biztonsági tartalom felügyelt példányok frissítése | Frissítve, és egyértelművé tartalmát a [felügyelt példányok biztonsági modell](sql-database-security-overview.md), és jelentkezzen be a biztonsági modell önálló adatbázisokhoz és rugalmas készletek szokásos testhelyzetből |
| Frissítve az összes útmutatóink és oktatóanyagaink segítségével | A rövid útmutatók és oktatóanyagok az összes a [dokumentáció](https://docs.microsoft.com/azure/sql-database) frissítve, és frissül, hogy megfeleljen a módosításokat az Azure Portalon |
| A hozzáadott gyors áttekintés útmutatók | Hozzáadott egy áttekintés gyorsútmutató [önálló adatbázisok](sql-database-quickstart-guide.md) és [felügyelt példányai](sql-database-managed-instance-quickstart-guide.md) |
| Új SQL Database szószedet | Ez [feltételek szószedet](sql-database-glossary-terms.md) cikk SQL-adatbázis használati és az elsődleges fogalmi oldal, amely ismerteti a környezetben kifejezés mutató hivatkozások végleges listáját tartalmazza. |
| &nbsp; |

## <a name="contribute-to-content-improvement"></a>Hozzájárulnak a tartalom javítása

Az Azure SQL dokumentációját nyílt forráskódú lesz. Nyílt használata több előnyökkel jár:

- Nyílt forrású adattáraknál a tervezés nyílt, milyen dokumentumokra van a leginkább szükség visszajelzést.
- Nyílt forrású adattáraknál tekintse át a nyílt, hogy az már az első kiadásban a leghasznosabb tartalom közzététele.
- Nyílt forrású adattáraknál a frissítés nyílt, hogy egyszerűbb legyen a tartalom folyamatos javítása.

Hozzájárul az Azure SQL Database-dokumentáció tartalmát, tekintse meg a [Microsoft Docs-közreműködői útmutató – áttekintés](https://docs.microsoft.com/contribute/). A felhasználói élmény a [docs.microsoft.com](https://docs.microsoft.com/) integrálható [GitHub](https://github.com/) munkafolyamatok közvetlen ezt még egyszerűbbé. Első lépésként [megtekintett dokumentum szerkesztésével](https://docs.microsoft.com/contribute/#quick-edits-to-existing-documents). Vagy segítsen [új témakörök áttekintésével](https://docs.microsoft.com/contribute/#review-open-prs), vagy [minőségi problémák](https://docs.microsoft.com/contribute/#create-quality-issues).
