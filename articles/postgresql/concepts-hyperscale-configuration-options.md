---
title: Konfigurációs beállítások – Nagykapacitású (Citus) – Azure-adatbázis a PostgreSQL-hez
description: A nagykapacitású (Citus) kiszolgálócsoport beállításai, beleértve a csomópont-számítási, tárolási és régiókat.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 4/6/2020
ms.openlocfilehash: a2c376ec2bd1f03b626c11b0d6a6c3850c9ef8c4
ms.sourcegitcommit: 6397c1774a1358c79138976071989287f4a81a83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/07/2020
ms.locfileid: "80804588"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database for PostgreSQL – Nagykapacitású (Citus) konfigurációs beállítások

## <a name="compute-and-storage"></a>Számítás és tárolás
 
A számítási és tárolási beállításokat egymástól függetlenül választhatja ki a munkavégző csomópontokhoz és a koordinátor csomóponthoz egy nagykapacitású (Citus) kiszolgálócsoportban.  A számítási erőforrások virtuális magként vannak megadva, amelyek az alapul szolgáló hardver logikai processzorát képviselik. A kiépítés tárolási mérete a koordinátor és a feldolgozócsomópontok számára a nagy kapacitású (Citus) kiszolgálócsoportban elérhető kapacitásra vonatkozik. A tároló adatbázisfájlokat, ideiglenes fájlokat, tranzakciónaplókat és postgres kiszolgálónaplókat tartalmaz.
 
|                       | Dolgozói csomópont           | Koordinátor csomópont      |
|-----------------------|-----------------------|-----------------------|
| Számítástechnika, virtuális magok       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memória virtuális magonként, GiB | 8                     | 4                     |
| Tároló mérete, TiB     | 0.5, 1, 2             | 0.5, 1, 2             |
| Tárolási típus          | Általános cél (SSD) | Általános cél (SSD) |
| IOPS                  | Akár 3 IOPS/GiB      | Akár 3 IOPS/GiB      |

A ram teljes mennyisége egyetlen nagy kapacitású (Citus) csomópontban a kiválasztott virtuális magok számától függ.

| virtuális magok | Egy munkavégző csomópont, GiB RAM | Koordinátor csomópont, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

A teljes raktármennyiséget kiépíti is meghatározza az egyes feldolgozók és a koordinátor csomópont elérhető I/O-kapacitás.

| Tároló mérete, TiB | Maximális IOPS |
|-------------------|--------------|
| 0,5               | 1,536        |
| 1                 | 3,072        |
| 2                 | 6,148        |

A teljes nagykapacitású (Citus) fürt esetében az összesített IOPS a következő értékeket dolgozza ki:

| Dolgozó csomópontok | 0,5 TiB, teljes IOPS | 1 TiB, teljes IOPS | 2 TiB, teljes IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3,072               | 6,144             | 12,296            |
| 3            | 4,608               | 9,216             | 18,444            |
| 4            | 6,144               | 12,288            | 24,592            |
| 5            | 7,680               | 15,360            | 30,740            |
| 6            | 9,216               | 18,432            | 36,888            |
| 7            | 10,752              | 21,504            | 43,036            |
| 8            | 12,288              | 24,576            | 49,184            |
| 9            | 13,824              | 27,648            | 55,332            |
| 10           | 15,360              | 30,720            | 61,480            |
| 11           | 16,896              | 33,792            | 67,628            |
| 12           | 18,432              | 36,864            | 73,776            |
| 13           | 19,968              | 39,936            | 79,924            |
| 14           | 21,504              | 43,008            | 86,072            |
| 15           | 23,040              | 46,080            | 92,220            |
| 16           | 24,576              | 49,152            | 98,368            |
| 17           | 26,112              | 52,224            | 104,516           |
| 18           | 27,648              | 55,296            | 110,664           |
| 19           | 29,184              | 58,368            | 116,812           |
| 20           | 30,720              | 61,440            | 122,960           |

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
