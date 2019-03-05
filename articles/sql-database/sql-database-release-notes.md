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
ms.date: 03/04/2019
ms.author: carlrab
ms.openlocfilehash: af0fd591393f9f187a75f79fea980d41085df5cb
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57342445"
---
# <a name="sql-database-release-notes"></a>Az SQL Database kibocsátási megjegyzései

Ez a cikk felsorolja az új funkciók és fejlesztések az SQL Database szolgáltatás és az SQL Database-dokumentáció. Más Azure-szolgáltatások fejlesztései, lásd: [Szolgáltatásfrissítésekkel](https://azure.microsoft.com/updates).

## <a name="march-2019"></a>2019. március

### <a name="service-improvements"></a>Szolgáltatás fejlesztései

| Szolgáltatás fejlesztései | Részletek |
| --- | --- |

### <a name="documentation-improvements"></a>Dokumentáció fejlesztései

| Dokumentáció fejlesztései | Részletek |
| --- | --- |
| A hozzáadott log egyetlen adatbázisok korlátai|További információkért lásd: [egyetlen adatbázis virtuális mag erőforráskorlátok](sql-database-vcore-resource-limits-single-databases.md)|
| Hozzáadott log rugalmas készletek és a készletezett adatbázisok korlátai|További információkért lásd: [rugalmas adatbáziskészletekhez virtuális mag erőforráskorlátok](sql-database-vcore-resource-limits-elastic-pools.md)|
| A hozzáadott tranzakciós napló arány cégirányítási| Hozzáadott új tartalmak [tranzakciós napló arány cégirányítási](sql-database-resource-limits-database-server.md#transaction-log-rate-governance)|
| Frissített PowerShell-minták az önálló adatbázisok és rugalmas készletek az.sql modul használata | További információkért lásd: [önálló adatbázisok és rugalmas készletek PowerShell-minták](sql-database-powershell-samples.md#single-database-and-elastic-pools).

## <a name="february-2019"></a>2019. február

### <a name="service-improvements"></a>Szolgáltatás fejlesztései

| Szolgáltatás fejlesztései | Részletek |
| --- | --- |
|A folytatható online index létrehozását már általánosan elérhető| További információkért lásd: [Index létrehozása](https://docs.microsoft.com/sql/t-sql/statements/create-index-transact-sql)|
|Felügyelt példány támogatása továbbfejlesztett útvonaltáblák| További információkért lásd: [hálózati követelmények](sql-database-managed-instance-connectivity-architecture.md#network-requirements)|

### <a name="documentation-improvements"></a>Dokumentáció fejlesztései

| Dokumentáció fejlesztései | Részletek |
| --- | --- |
|A felügyelt példány üzembe helyezési lehetőség magyarázata|Frissítve azzal a pontosítással önálló adatbázis, a rugalmas készlet és a felügyelt példány üzembe helyezési beállítások alkalmazhatósági számos cikkek |
|DTU-alapú vásárlási modell frissített tempdb méretei | További információkért lásd: [Tempdb-adatbázis, SQL Database-ben](https://docs.microsoft.com/sql/relational-databases/databases/tempdb-database?view=sql-server-2017#tempdb-database-in-sql-database)|
|Frissített importálása és exportálása a felügyelt példány támogatás bacpac-fájlba| További információkért lásd: [importálás BACPAC a](sql-database-import.md) és [BACPAC exportálása](sql-database-export.md) |

## <a name="january-2019"></a>2019. január

### <a name="service-improvements"></a>Szolgáltatás fejlesztései

| Szolgáltatás fejlesztései | Részletek |
| --- | --- |
| A számítási erőforrások további granularitási lehetőségei | Az általános célú és a kritikus fontosságú üzleti szolgáltatás réteget [önálló adatbázisok](sql-database-vcore-resource-limits-single-databases.md) és [rugalmas készletek](sql-database-vcore-resource-limits-elastic-pools.md) most már további részletes számítási lehetőségek.|
| A naplórekordok megtekintése a felügyelt példány az Azure Portalon | Megtekintés [naplórekordok felügyelt példányok](sql-database-managed-instance-auditing.md) az Azure Portalon mostantól támogatott.|
| Speciális veszélyforrások elleni duplikálásészlelési szolgáltatását a speciális adatbiztonság neve: | Új előzetes threat detection funkció [adatok biztonságú](sql-advanced-threat-protection.md) önálló adatbázisok, rugalmas készletek és a felügyelt példány |
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

Hozzájárul az Azure SQL Database-dokumentáció tartalmát, tekintse meg a [Microsoft Docs-közreműködői útmutató – áttekintés](https://docs.microsoft.com/en-us/contribute/). A felhasználói élmény a [docs.microsoft.com](https://docs.microsoft.com/) integrálható [GitHub](https://github.com/) munkafolyamatok közvetlen ezt még egyszerűbbé. Első lépésként [megtekintett dokumentum szerkesztésével](https://docs.microsoft.com/en-us/contribute/#quick-edits-to-existing-documents). Vagy segítsen [új témakörök áttekintésével](https://docs.microsoft.com/en-us/contribute/#review-open-prs), vagy [minőségi problémák](https://docs.microsoft.com/en-us/contribute/#create-quality-issues).
