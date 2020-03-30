---
title: Teljesítménybeállítások – Nagykapacitású (Citus) – Azure-adatbázis a PostgreSQL-hez
description: A nagykapacitású (Citus) kiszolgálócsoport beállításai, beleértve a csomópont-számítási, tárolási és régiókat.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 2/18/2020
ms.openlocfilehash: 1c9b4b1099bda69764aa7a1a5a984a6316e1047d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77462411"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-performance-options"></a>Azure Database for PostgreSQL – Nagykapacitású (Citus) teljesítménybeállítások

## <a name="compute-and-storage"></a>Számítás és tárolás
 
A számítási és tárolási beállításokat egymástól függetlenül választhatja ki a munkavégző csomópontokhoz és a koordinátor csomóponthoz egy nagykapacitású (Citus) kiszolgálócsoportban.  A számítási erőforrások virtuális magként vannak megadva, amelyek az alapul szolgáló hardver logikai processzorát képviselik. A kiépítés tárolási mérete a koordinátor és a feldolgozócsomópontok számára a nagy kapacitású (Citus) kiszolgálócsoportban elérhető kapacitásra vonatkozik. A tároló adatbázisfájlokat, ideiglenes fájlokat, tranzakciónaplókat és postgres kiszolgálónaplókat tartalmaz. A teljes raktármennyiséget kiépíti is meghatározza az egyes feldolgozók és a koordinátor csomópont elérhető I/O-kapacitás.
 
|                       | Dolgozói csomópont           | Koordinátor csomópont      |
|-----------------------|-----------------------|-----------------------|
| Számítástechnika, virtuális magok       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memória virtuális magonként, GiB | 8                     | 4                     |
| Tároló mérete, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Tárolási típus          | Általános cél (SSD) | Általános cél (SSD) |
| IOPS                  | Akár 3 IOPS/GiB      | Akár 3 IOPS/GiB      |


## <a name="regions"></a>Régiók
A nagy kapacitású (Citus) kiszolgálócsoportok a következő Azure-régiókban érhetők el:

* Amerika: 
    * Közép-Kanada
    * USA középső régiója
    * USA keleti régiója
    * USA 2. keleti régiója
    * USA északi középső régiója
    * USA nyugati régiója, 2.
* Ázsia és a csendes-óceáni térség:
    * Kelet-Ausztrália
    * Kelet-Japán
    * Dél-Korea középső régiója
    * Délkelet-Ázsia
* Európa:
    * Észak-Európa
    * Az Egyesült Királyság déli régiója
    * Nyugat-Európa

Előfordulhat, hogy ezek közül a régiók közül néhány kezdetben nem aktiválható az összes Azure-előfizetésben. Ha a fenti listából szeretne használni egy régiót, és nem látja azt az előfizetésben, vagy ha nem ezen a listán szereplő régiót szeretne használni, nyisson meg egy [támogatási kérelmet.](https://portal.azure.com/#blade/Microsoft_Azure_Support/HelpAndSupportBlade/newsupportrequest)

## <a name="pricing"></a>Díjszabás
A legfrissebb árképzési információkat a szolgáltatás [díjszabási oldalán](https://azure.microsoft.com/pricing/details/postgresql/)találja.
A kívánt konfiguráció költségének megtekintéséhez az [Azure Portal](https://portal.azure.com/#create/Microsoft.PostgreSQLServer) a havi költséget jeleníti meg a **Konfigurálás** lapon a kiválasztott beállítások alapján. Ha nem rendelkezik Azure-előfizetéssel, az Azure díjkalkulátor használatával lehívhatja a becsült árat. Az [Azure díjkalkulátor](https://azure.microsoft.com/pricing/calculator/) webhelyén válassza az **Elemek hozzáadása**lehetőséget, bontsa ki az **Adatbázisok kategóriát,** és válassza az **Azure Database for PostgreSQL – Hyperscale (Citus)** lehetőséget a beállítások testreszabásához.
 
## <a name="next-steps"></a>További lépések
Megtudhatja, hogyan [hozhat létre nagykapacitású (Citus) kiszolgálócsoportot a portálon.](quickstart-create-hyperscale-portal.md)
