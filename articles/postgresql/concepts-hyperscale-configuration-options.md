---
title: Teljesítmény beállításai – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Nagy kapacitású-(Citus-) kiszolgálócsoport beállításai, beleértve a csomópontok számítását, tárolását és régióit.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 6ee876c800da7a14464d276cd726a49b504c45c5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/19/2020
ms.locfileid: "77462411"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Azure Database for PostgreSQL – nagy kapacitású (Citus) teljesítményének beállításai

## <a name="compute-and-storage"></a>Számítás és tárolás
 
A számítási és tárolási beállításokat a munkavégző csomópontok és a nagy kapacitású-(Citus-) kiszolgálócsoport koordinátor csomópontja egymástól függetlenül is kiválaszthatja.  A számítási erőforrások virtuális mag-ként vannak megadva, amely az alapul szolgáló hardver logikai PROCESSZORát jelöli. A kiépítés tárolási mérete a nagy kapacitású-(Citus-) kiszolgálócsoport koordinátora és munkavégző csomópontjai számára elérhető kapacitásra utal. A tároló tartalmazza az adatbázisfájlok, az ideiglenes fájlok, a tranzakciós naplók és a postgres-kiszolgáló naplóit. A kiépített tárterület teljes mennyisége határozza meg az egyes feldolgozók és a koordinátorok csomópontja számára elérhető I/O-kapacitást is.
 
|                       | Munkavégző csomópont           | Koordinátor csomópont      |
|-----------------------|-----------------------|-----------------------|
| Számítás, virtuális mag       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memória/virtuális mag, GiB | 8                     | 4                     |
| Tároló mérete, TiB     | 0,5, 1, 2             | 0,5, 1, 2             |
| Tárolási típus          | Általános célú (SSD) | Általános célú (SSD) |
| IOPS                  | Legfeljebb 3 IOPS/GiB      | Legfeljebb 3 IOPS/GiB      |


## <a name="regions"></a>Régiók
A nagy kapacitású-(Citus-) kiszolgálócsoportok a következő Azure-régiókban érhetők el:

* Amerika
    * Közép-Kanada
    * USA középső régiója
    * USA keleti régiója
    * USA 2. keleti régiója
    * USA északi középső régiója
    * USA nyugati régiója, 2.
* Ázsia és a Csendes-óceáni térség:
    * Kelet-Ausztrália
    * Kelet-Japán
    * Dél-Korea középső régiója
    * Délkelet-Ázsia
* Európa
    * Észak-Európa
    * Az Egyesült Királyság déli régiója
    * Nyugat-Európa

Előfordulhat, hogy a régiók némelyike nem aktiválódik minden Azure-előfizetésen. Ha a fenti listából szeretne egy régiót használni, és nem látja az előfizetésben, vagy ha a listán nem szereplő régiót szeretne használni, nyisson meg egy [támogatási kérést](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest).

## <a name="pricing"></a>Díjszabás
A legfrissebb díjszabási információkért tekintse meg a szolgáltatás [díjszabását ismertető oldalt](https://azure.microsoft.com/pricing/details/postgresql/).
A kívánt konfiguráció költségének megtekintéséhez a [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) a kiválasztott beállítások alapján a **configure (Konfigurálás** ) lapon szereplő havi költséget jeleníti meg. Ha még nem rendelkezik Azure-előfizetéssel, az Azure díjszabási kalkulátor használatával megbecsülheti a becsült árat. Az [Azure díjszabási kalkulátor](https://azure.microsoft.com/pricing/calculator/) webhelyén válassza az **elemek hozzáadása**, majd az **adatbázisok** kategóriát, és válassza a **Azure Database for PostgreSQL – nagy kapacitású (Citus)** lehetőséget a beállítások testreszabásához.
 
## <a name="next-steps"></a>Következő lépések
Ismerje meg, hogyan [hozhat létre nagy kapacitású-(Citus-) kiszolgálócsoport a portálon](quickstart-create-hyperscale-portal.md).
