---
title: A Machine Learning Services legfontosabb különbségei (előzetes verzió)
description: Ez a témakör az Azure SQL Database Machine Learning Services (R) és az SQL Server Machine Learning Services közötti főbb különbségeket ismerteti.
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
ms.openlocfilehash: 34ba75b6126024c9cd43d6fe474f7c1b62dd990f
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81453149"
---
# <a name="key-differences-between-machine-learning-services-in-azure-sql-database-preview-and-sql-server"></a>Az Azure SQL Database (előzetes verzió) és az SQL Server Machine Learning Services szolgáltatása közötti főbb különbségek

Az Azure SQL Database Machine Learning Services (R- rel) funkciója (előzetes verzió) hasonló az [SQL Server Machine Learning Services szolgáltatáshoz.](https://docs.microsoft.com/sql/advanced-analytics/what-is-sql-server-machine-learning) Az alábbiakban néhány kulcsfontosságú különbséget.

[!INCLUDE[ml-preview-note](../../includes/sql-database-ml-preview-note.md)]

## <a name="language-support"></a>Nyelvi támogatás

Az SQL Server támogatja az R és a Python a [bővíthetőségi keretrendszeren](https://docs.microsoft.com/sql/advanced-analytics/concepts/extensibility-framework)keresztül. Az SQL Database nem támogatja mindkét nyelvet. A legfontosabb különbségek a következők:

- Az R az SQL Database egyetlen támogatott nyelve. A Python jelenleg nem támogatott.
- Az R verzió 3.4.4.
- Nincs szükség a `external scripts enabled` konfigurálására a . `sp_configure`

## <a name="package-management"></a>Csomagkezelés

Az R csomagkezelés és -telepítés az SQL Database és az SQL Server között eltérő. Ezek a különbségek a következők:

- Az R csomagok [telepítése sqlmlutils](https://github.com/Microsoft/sqlmlutils) vagy [CREATE EXTERNAL LIBRARY](https://docs.microsoft.com/sql/t-sql/statements/create-external-library-transact-sql)segítségével kerül telepítésre.
- A csomagok nem tudnak kimenő hálózati hívásokat végrehajtani. Ez a korlátozás hasonló az SQL Server [Machine Learning Services alapértelmezett tűzfalszabályaihoz,](https://docs.microsoft.com//sql/advanced-analytics/security/firewall-configuration) de az SQL Database-ben nem módosítható.
- Nincs olyan csomagok támogatása, amelyek a külső futásidőktől (például a Java-tól) függenek, vagy a telepítéshez vagy használathoz az operációs rendszer API-khoz való hozzáférésre van szükségük.

## <a name="writing-to-a-temporary-table"></a>Írás egy ideiglenes táblába

Ha az Azure SQL Database-ben rodbc-t használ, akkor nem írhat ideiglenes táblába, függetlenül attól, hogy az a `sp_execute_external_script` munkameneten belül vagy kívül jön létre. A megoldás az [RxOdbcData](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxodbcdata) és [az rxDataStep](https://docs.microsoft.com/machine-learning-server/r-reference/revoscaler/rxdatastep) (felülírás=FALSE és append="sorok" használatával) a `sp_execute_external_script` lekérdezés előtt létrehozott globális ideiglenes táblába való íráshoz.

## <a name="resource-governance"></a>Erőforrások szabályozása

Az R-erőforrások nem korlátozva [erőforrás-delegáltat](https://docs.microsoft.com/sql/relational-databases/resource-governor/resource-governor) és külső erőforráskészleteken keresztül.

A nyilvános előzetes verzió során az R-erőforrások az SQL Database-erőforrások legfeljebb 20%-ára vannak beállítva, és attól függnek, hogy melyik szolgáltatási szintet választja. További információ: [Azure SQL Database vásárlási modellek.](https://docs.microsoft.com/azure/sql-database/sql-database-service-tiers)
### <a name="insufficient-memory-error"></a>Nincs elegendő memóriahiba

Ha nincs elegendő memória az R számára, hibaüzenet jelenik meg. Gyakori hibaüzenetek a következők:

- Nem lehet kommunikálni az "R" parancsfájl futási idejével a kérelemazonosítóhoz: *******. Ellenőrizze az "R" futásidejű
- A HRESULT 0x80004004 "sp_execute_external_script" végrehajtása során "R" parancsfájlhiba történt. ... külső parancsfájlhiba történt: ".. nem lehetett memóriát (0 Mb) lefoglalni a "R_AllocStringBuffer" c függvényben
- Külső parancsfájlhiba történt: Hiba: nem lehet lefoglalni a vektort.

A memóriahasználat attól függ, hogy mennyit használ az R-parancsfájlokban, és hogy hány párhuzamos lekérdezést hajt végre. Ha a fenti hibákat kapja, az adatbázist magasabb szolgáltatási szintre méretezheti a probléma megoldásához.

## <a name="next-steps"></a>További lépések

- Tekintse meg az áttekintést, [az Azure SQL Database Machine Learning Services R (előzetes verzió)](sql-database-machine-learning-services-overview.md).
- Ha meg szeretné tudni, hogyan lehet az R használatával lekérdezni az Azure SQL Database Machine Learning Services -t (előzetes verzió), olvassa el a [rövid útmutatót.](sql-database-connect-query-r.md)
- Néhány egyszerű R-parancsfájl létrehozásáról [és futtatásáról az Azure SQL Database Machine Learning Services szolgáltatásban (előzetes verzió) című témakörben található egyszerű R-parancsfájlok létrehozása és futtatása.](sql-database-quickstart-r-create-script.md)
