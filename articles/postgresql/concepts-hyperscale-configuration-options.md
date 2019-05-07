---
title: Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió) Teljesítménybeállítások
description: Egy nagy kapacitású (Citus) kiszolgálócsoportot, beleértve a csomópont számítási, tárolási és régiók beállítások.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 05/06/2019
ms.openlocfilehash: e8c1c2e51ca14ae9b17f0d7efb20552cdd55139b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65077290"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-preview-performance-options"></a>Azure Database for PostgreSQL – nagy kapacitású (Citus) (előzetes verzió) Teljesítménybeállítások

## <a name="compute-and-storage"></a>Számítás és tárolás
 
Választhat a számítási és tárolási beállításai egymástól függetlenül feldolgozó csomópontokat, a koordinátor-csomópont egy nagy kapacitású (Citus) kiszolgáló csoportban.  A számítási erőforrások szolgálnak, amelyek jelölik az alapul szolgáló hardver logikai CPU, a virtuális magok. A tároló mérete üzembe helyezés a koordinátor és a feldolgozó csomópontok a nagy kapacitású (Citus) kiszolgáló csoportban a rendelkezésre álló kapacitás hivatkozik. A storage magában foglalja az adatbázisfájlokat, az ideiglenes fájlok, a tranzakciós naplók és a az Postgres server-naplók. A teljes összeg tárhelyet is üzembe helyezi a rendelkezésre álló i/o-kapacitás minden feldolgozó és koordináló csomóponton határozza meg.
 
|                       | Munkavégző csomópont           | Koordinátor-csomópont      |
|-----------------------|-----------------------|-----------------------|
| Számítás, a virtuális magok       | 4, 8, 16, 32          | 4, 8, 16, 32          |
| Virtuális mag, GiB-memória | 8                     | 4                     |
| A tárhely mérete Tib-ra     | 0.5, 1, 2             | 0.5, 1, 2             |
| Tárolási típus          | Általános célú (SSD) | Általános célú (SSD) |
| IO                  | Legfeljebb 3 IOPS/GiB      | Legfeljebb 3 IOPS/GiB      |


## <a name="regions"></a>Régiók
Nagy kapacitású (Citus) kiszolgálócsoportok a következő Azure-régiókban érhetők el:
* USA 2. keleti régiója
* Délkelet-Ázsia
* Nyugat-Európa
* USA nyugati régiója, 2.

## <a name="pricing"></a>Díjszabás
A legfrissebb díjszabási információkért tekintse meg a szolgáltatás [díjszabását ismertető lapon](https://azure.microsoft.com/pricing/details/postgresql/).
A költség, a konfiguráció azt szeretné, hogy a [az Azure portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) havi költségét jeleníti meg a **konfigurálása** lapon a kiválasztott beállítások alapján. Ha nem rendelkezik Azure-előfizetéssel, az Azure díjkalkulátorát segítségével a becsült ár beolvasása. A a [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) webhely, válassza **elemek hozzáadása**, bontsa ki a **adatbázisok** kategória, és válassza **, Azure Database for PostgreSQL – Nagy kapacitású (Citus)** testre szabhatja beállítások.
 
## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [egy nagy kapacitású (Citus) kiszolgálócsoport létrehozása a portálon](quickstart-create-hyperscale-portal.md).
