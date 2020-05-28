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
ms.date: 11/20/2019
ROBOTS: NOINDEX
ms.openlocfilehash: ae913325d3c1fcbe4bd7cbf0005a449f17e722d9
ms.sourcegitcommit: 053e5e7103ab666454faf26ed51b0dfcd7661996
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2020
ms.locfileid: "84048223"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>A Azure SQL Database (előzetes verzió) és a SQL Server Machine Learning Services közötti fő különbségek
[!INCLUDE[appliesto-sqldb](../includes/appliesto-sqldb.md)]

Az (előzetes verzió) Azure SQL Database Machine Learning Services (R) funkciója a következőhöz hasonló: [SQL Server Machine learning Services](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning). Az alábbiakban néhány kulcsfontosságú különbség szerepel.

[!INCLUDE[ml-preview-note](../../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>Nyelvi támogatás

SQL Server támogatja az R és a Python használatát a [bővíthetőségi keretrendszer](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)segítségével. A SQL Database mindkét nyelvet nem támogatja. A legfontosabb különbségek a következők:

- Az R az egyetlen támogatott nyelv a SQL Databaseban. A Python jelenleg nem támogatott.
- Az R verziója 3.4.4.
- Nincs szükség a-on keresztül történő konfigurálásra `external scripts enabled` `sp_configure` .

## <a name="package-management"></a>Csomagkezelés

Az R-csomag kezelése és a telepítés SQL Database és SQL Server között különbözik. Ezek a különbségek a következők:

- Az R-csomagok a [sqlmlutils](https://github.com/Microsoft/sqlmlutils) -on keresztül települnek, vagy [külső függvénytárat hoznak létre](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql).
- A csomagok nem hajthatnak végre kimenő hálózati hívásokat. Ez a korlátozás hasonlít a SQL Server [Machine learning Services alapértelmezett tűzfalszabályok esetében](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) , de nem módosítható SQL Databaseban.
- A külső futtatókörnyezettől (például Java) függő csomagok nem támogatottak, vagy az operációs rendszer API-jait kell telepíteni a telepítéshez vagy a használathoz.

## <a name="writing-to-a-temporary-table"></a>Ideiglenes táblába való írás

Ha a RODBC-t használja a Azure SQL Databaseban, akkor nem írhat ideiglenes táblába, függetlenül attól, hogy az a munkameneten belül vagy kívül van-e létrehozva `sp_execute_external_script` . A megkerülő megoldás az [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) és a [rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (a felülírás = false és a Hozzáfűzés = "sorok") használata a lekérdezés előtt létrehozott globális ideiglenes táblába való íráshoz `sp_execute_external_script` .

## <a name="resource-governance"></a>Erőforrások szabályozása

Nem lehetséges az R-erőforrások korlátozása [Resource Governor](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) és külső erőforráskészlet használatával.

A nyilvános előzetes verzióban az R-erőforrások a SQL Database erőforrások legfeljebb 20%-ában vannak beállítva, és attól függnek, hogy melyik szolgáltatási szintet választja. További információ: [Azure SQL Database vásárlási modellek](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers).
### <a name="insufficient-memory-error"></a>Nincs elég memória-hiba

Ha nincs elegendő szabad memória az R-hez, hibaüzenet jelenik meg. Gyakori hibaüzenetek:

- Nem lehet kommunikálni a futtatókörnyezettel a (z) * * * * * * * kérelem azonosítójának "R" parancsfájljában. Tekintse meg az "R" futtatókörnyezet követelményeit
- "R" parancsfájl hiba történt a (z) "sp_execute_external_script" végrehajtása során a HRESULT 0x80004004. ... külső parancsfájl hiba történt: ".. nem foglalható le memória (0 MB) a (z) "R_AllocStringBuffer" C függvényben
- Külső parancsfájl hiba történt: hiba: nem foglalható le a méret vektora.

A memóriahasználat attól függ, hogy mennyit használ az R-parancsfájlokban, és hogy hány párhuzamos lekérdezést hajt végre a rendszer. Ha a fenti hibákat kapja, a megoldáshoz méretezheti az adatbázist egy magasabb szolgáltatási szintjére.

## <a name="next-steps"></a>Következő lépések

- Tekintse meg az áttekintést, [Azure SQL Database Machine learning Services az R-vel (előzetes verzió)](machine-learning-services-overview.md).
- Ha szeretné megtudni, hogyan használhatja az R-t a Azure SQL Database Machine Learning Services (előzetes verzió) lekérdezéséhez, tekintse meg a gyors [üzembe helyezési útmutatót](connect-query-r.md).
- Néhány egyszerű R-szkript megkezdéséhez lásd: [egyszerű r-parancsfájlok létrehozása és futtatása Azure SQL Database Machine learning Servicesban (előzetes verzió)](r-script-create-quickstart.md).
