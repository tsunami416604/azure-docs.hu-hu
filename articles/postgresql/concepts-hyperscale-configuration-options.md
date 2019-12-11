---
title: Teljesítmény beállításai – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Nagy kapacitású-(Citus-) kiszolgálócsoport beállításai, beleértve a csomópontok számítását, tárolását és régióit.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 10/17/2019
ms.openlocfilehash: 2ee3e661d6c01aa2e4f37ac9a70e00be5da5f794
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74975635"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Azure Database for PostgreSQL – nagy kapacitású (Citus) teljesítményének beállításai

## <a name="compute-and-storage"></a>Számítás és tárolás
 
A számítási és tárolási beállításokat a munkavégző csomópontok és a nagy kapacitású-(Citus-) kiszolgálócsoport koordinátor csomópontja egymástól függetlenül is kiválaszthatja.  A számítási erőforrások virtuális mag-ként vannak megadva, amely az alapul szolgáló hardver logikai PROCESSZORát jelöli. A kiépítés tárolási mérete a nagy kapacitású-(Citus-) kiszolgálócsoport koordinátora és munkavégző csomópontjai számára elérhető kapacitásra utal. A tároló tartalmazza az adatbázisfájlok, az ideiglenes fájlok, a tranzakciós naplók és a postgres-kiszolgáló naplóit. A kiépített tárterület teljes mennyisége határozza meg az egyes feldolgozók és a koordinátorok csomópontja számára elérhető I/O-kapacitást is.
 
|                       | Munkavégző csomópont           | Koordinátor csomópont      |
|-----------------------|-----------------------|-----------------------|
| Számítás, virtuális mag       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memória/virtuális mag, GiB | 8                     | 4                     |
| Tároló mérete, TiB     | 0,5, 1, 2             | 0,5, 1, 2             |
| Tárhely típusa          | Általános célú (SSD) | Általános célú (SSD) |
| IO                  | Legfeljebb 3 IOPS/GiB      | Legfeljebb 3 IOPS/GiB      |


## <a name="regions"></a>Térségek
A nagy kapacitású-(Citus-) kiszolgálócsoportok a következő Azure-régiókban érhetők el:

* Amerika
    * Közép-Kanada *
    * USA keleti régiója
    * USA 2. keleti régiója
    * USA északi középső régiója *
    * USA 2. nyugati régiója
* Ázsia és a Csendes-óceáni térség:
    * Kelet-Ausztrália *
    * Délkelet-Ázsia
* Európa
    * Észak-Európa
    * Egyesült Királyság déli régiója
    * Nyugat-Európa

A csillaggal (\*) rendelkező régiók még nem támogatják a [magas rendelkezésre állást](concepts-hyperscale-high-availability.md).

## <a name="pricing"></a>Díjszabás
A legfrissebb díjszabási információkért tekintse meg a szolgáltatás [díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/postgresql/).
A kívánt konfiguráció költségének megtekintéséhez a [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) a kiválasztott beállítások alapján a **configure (Konfigurálás** ) lapon szereplő havi költséget jeleníti meg. Ha még nem rendelkezik Azure-előfizetéssel, az Azure díjszabási kalkulátor használatával megbecsülheti a becsült árat. Az [Azure díjszabási kalkulátor](https://azure.microsoft.com/pricing/calculator/) webhelyén válassza az **elemek hozzáadása**, majd az **adatbázisok** kategóriát, és válassza a **Azure Database for PostgreSQL – nagy kapacitású (Citus)** lehetőséget a beállítások testreszabásához.
 
## <a name="next-steps"></a>Következő lépések
Ismerje meg, hogyan [hozhat létre nagy kapacitású-(Citus-) kiszolgálócsoport a portálon](quickstart-create-hyperscale-portal.md).
