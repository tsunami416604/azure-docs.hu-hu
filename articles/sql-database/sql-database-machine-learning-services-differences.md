---
title: Machine Learning Services főbb eltérései (előzetes verzió)
description: Ez a témakör a Azure SQL Database Machine Learning Services (R) és a SQL Server Machine Learning Services közötti fő különbségeket ismerteti.
services: sql-database
ms.service: sql-database
ms.subservice: machine-learning
ms.custom: ''
ms.devlang: ''
ms.topic: conceptual
author: dphansen
ms.author: davidph
ms.reviewer: carlrab
manager: cgronlun
ms.date: 03/01/2019
ms.openlocfilehash: 1397f5d81ddf63740d733111b965a0517a2b917f
ms.sourcegitcommit: ac56ef07d86328c40fed5b5792a6a02698926c2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73827471"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>A Azure SQL Database (előzetes verzió) és a SQL Server Machine Learning Services közötti fő különbségek

Az (előzetes verzió) Azure SQL Database Machine Learning Services (R) funkciója a következőhöz hasonló: [SQL Server Machine learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Az alábbiakban néhány kulcsfontosságú különbség szerepel.

> [!IMPORTANT]
> A Azure SQL Database Machine Learning Services jelenleg nyilvános előzetes verzióban érhető el.
> Erre az előzetes verzióra nem vonatkozik szolgáltatói szerződés, és a használata nem javasolt éles számítási feladatok esetén. Előfordulhat, hogy néhány funkció nem támogatott, vagy korlátozott képességekkel rendelkezik.
> További információ: [Kiegészítő használati feltételek a Microsoft Azure előzetes verziójú termékeihez](https://azure.microsoft.com/support/legal/preview-supplemental-terms/).

## <a name="language-support"></a>Nyelvi támogatás

SQL Server támogatja az R és a Python használatát a [bővíthetőségi keretrendszer](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)segítségével. A SQL Database mindkét nyelvet nem támogatja. A legfontosabb különbségek a következők:

- Az R az egyetlen támogatott nyelv a SQL Databaseban. A Python jelenleg nem támogatott.
- Az R verziója 3.4.4.
- Nincs szükség a `external scripts enabled` konfigurálására `sp_configure`használatával. A [regisztráció](sql-database-machine-learning-services-overview.md#signup)után a Machine learning engedélyezve van az SQL-adatbázishoz.

## <a name="package-management"></a>Csomagkezelés

Az R-csomag kezelése és a telepítés SQL Database és SQL Server között különbözik. Ezek a különbségek a következők:

- Az R-csomagok a [sqlmlutils](https://github.com/Microsoft/sqlmlutils) -on keresztül települnek, vagy [külső függvénytárat hoznak létre](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- A csomagok nem hajthatnak végre kimenő hálózati hívásokat. Ez a korlátozás hasonlít a SQL Server [Machine learning Services alapértelmezett tűzfalszabályok esetében](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) , de nem módosítható SQL Databaseban.
- A külső futtatókörnyezettől (például Java) függő csomagok nem támogatottak, vagy az operációs rendszer API-jait kell telepíteni a telepítéshez vagy a használathoz.

## <a name="writing-to-a-temporary-table"></a>Ideiglenes táblába való írás

Ha a RODBC-t használja a Azure SQL Databaseban, akkor nem írhat ideiglenes táblába, függetlenül attól, hogy az a `sp_execute_external_script`-munkameneten belül vagy kívül jön létre. A megkerülő megoldás az [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) és a [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (a felülírás = false és a Hozzáfűzés = "sorok") használata az `sp_execute_external_script` lekérdezés előtt létrehozott globális ideiglenes táblába való íráshoz.

## <a name="resource-governance"></a>Erőforrások szabályozása

Nem lehetséges az R-erőforrások korlátozása [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) és külső erőforráskészlet használatával.

A nyilvános előzetes verzióban az R-erőforrások a SQL Database erőforrások legfeljebb 20%-ában vannak beállítva, és attól függnek, hogy melyik szolgáltatási szintet választja. További információ: [Azure SQL Database vásárlási modellek](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Nincs elég memória-hiba

Ha nincs elegendő szabad memória az R-hez, hibaüzenet jelenik meg. Gyakori hibaüzenetek:

- Nem lehet kommunikálni a futtatókörnyezettel a (z) * * * * * * * kérelem azonosítójának "R" parancsfájljában. Tekintse meg az "R" futtatókörnyezet követelményeit
- "R" parancsfájl hiba történt a (z) "sp_execute_external_script" végrehajtása során a HRESULT 0x80004004. ... külső parancsfájl hiba történt: ".. nem foglalható le memória (0 MB) a (z) "R_AllocStringBuffer" C függvényben
- Külső parancsfájl hiba történt: hiba: nem foglalható le a méret vektora.

A memóriahasználat attól függ, hogy mennyit használ az R-parancsfájlokban, és hogy hány párhuzamos lekérdezést hajt végre a rendszer. Ha a fenti hibákat kapja, a megoldáshoz méretezheti az adatbázist egy magasabb szolgáltatási szintjére.

## <a name="next-steps"></a>További lépések

- Tekintse meg az áttekintést, [Azure SQL Database Machine learning Services az R-vel (előzetes verzió)](sql-database-machine-learning-services-overview.md).
- Ha szeretné megtudni, hogyan használhatja az R-t a Azure SQL Database Machine Learning Services (előzetes verzió) lekérdezéséhez, tekintse meg a gyors [üzembe helyezési útmutatót](sql-database-connect-query-r.md).
- Néhány egyszerű R-szkript megkezdéséhez lásd: [egyszerű r-parancsfájlok létrehozása és futtatása Azure SQL Database Machine learning Servicesban (előzetes verzió)](sql-database-quickstart-r-create-script.md).
