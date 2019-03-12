---
title: Fontos különbség az Azure SQL Database, Machine Learning Services (előzetes verzió)
description: Ez a témakör az Azure SQL Database Machine Learning-szolgáltatások (az r nyelv) és az SQL Server Machine Learning-szolgáltatások közötti fő különbségeket ismerteti.
services: sql-database
ms.service: sql-database
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 57ea52c179376e8378680f436d396ffaf9357f68
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57771850"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Machine Learning-szolgáltatások az Azure SQL Database (előzetes verzió) és az SQL Server közötti fő különbségeket

Az Azure SQL Database Machine Learning-szolgáltatások (az r nyelv) (előzetes verzió) funkciója hasonló [SQL Server Machine Learning-szolgáltatások](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Az alábbiakban néhány fontos eltérés.

> [!IMPORTANT]
> Az Azure SQL Database Machine Learning-szolgáltatások jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

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

Már nem R erőforrásai a korlátozásához [erőforrás-vezérlő](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) és külső erőforrás-készletek.

A nyilvános előzetes során R erőforrások vannak beállítva, hogy egy legfeljebb 20 %-át az SQL Database-erőforrásokat, és függő melyik szolgáltatási szintet választja. További információkért lásd: [vásárlási modellek az Azure SQL Database](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).

### <a name="insufficient-memory-error"></a>Nincs elég memória hiba

Ha nincs elegendő memória áll rendelkezésre az R, hibaüzenetet kap. Gyakori hibaüzenetek a következők:

- Nem sikerült a kérelem azonosítója "R" szkript futtatókörnyezetével: x. Ellenőrizze az "R" futtatókörnyezet követelményeit
- "R" parancsfájlhiba történt az "sp_execute_external_script", HRESULT: 0x80004004 végrehajtása közben. ... külső parancsfájlhiba történt: ".. nem sikerült memóriát (0 Mb) a C funkciót "R_AllocStringBuffer" "
- Külső parancsfájlhiba történt: Hiba: nem foglalható le méretű vektort.

Használati attól függ, hogy mennyi memóriát használja az R-szkriptek és a lekérdezések párhuzamos végrehajtása számát. Ha a fenti hibákat kap, magasabb szolgáltatási szintre a probléma megoldásához az adatbázis is méretezhető.

## <a name="next-steps"></a>További lépések

- Tekintse meg a [SQL Server Machine Learning-szolgáltatások](https://docs.microsoft.com/sql/advanced-analytics) általános információkat dokumentációja
- Az Azure SQL Database használata a Machine Learning-szolgáltatások (az r nyelv) című témakörben talál [a rövid útmutató](sql-database-connect-query-r.md).
- További tudnivalók a [SQL Server R nyelv oktatóanyagok](https://docs.microsoft.com/sql/advanced-analytics/tutorials/sql-server-r-tutorials)