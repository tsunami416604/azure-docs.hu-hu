---
title: Konfigurációs beállítások – nagy kapacitású (Citus) – Azure Database for PostgreSQL
description: Nagy kapacitású-(Citus-) kiszolgálócsoport beállításai, beleértve a csomópontok számítását, tárolását és régióit.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: conceptual
ms.date: 4/6/2020
ms.openlocfilehash: a2c376ec2bd1f03b626c11b0d6a6c3850c9ef8c4
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "80804588"
---
# <a name="azure-database-for-postgresql--hyperscale-citus-configuration-options"></a>Azure Database for PostgreSQL – nagy kapacitású (Citus) konfigurációs beállítások

## <a name="compute-and-storage"></a>Számítás és tárolás
 
A számítási és tárolási beállításokat a munkavégző csomópontok és a nagy kapacitású-(Citus-) kiszolgálócsoport koordinátor csomópontja egymástól függetlenül is kiválaszthatja.  A számítási erőforrások virtuális mag-ként vannak megadva, amely az alapul szolgáló hardver logikai PROCESSZORát jelöli. A kiépítés tárolási mérete a nagy kapacitású-(Citus-) kiszolgálócsoport koordinátora és munkavégző csomópontjai számára elérhető kapacitásra utal. A tároló tartalmazza az adatbázisfájlok, az ideiglenes fájlok, a tranzakciós naplók és a postgres-kiszolgáló naplóit.
 
|                       | Munkavégző csomópont           | Koordinátor csomópont      |
|-----------------------|-----------------------|-----------------------|
| Számítás, virtuális mag       | 4, 8, 16, 32, 64      | 4, 8, 16, 32, 64      |
| Memória/virtuális mag, GiB | 8                     | 4                     |
| Tároló mérete, TiB     | 0,5, 1, 2             | 0,5, 1, 2             |
| Tárolási típus          | Általános célú (SSD) | Általános célú (SSD) |
| IOPS                  | Legfeljebb 3 IOPS/GiB      | Legfeljebb 3 IOPS/GiB      |

Az egyetlen nagy kapacitású (Citus) csomópontban található RAM teljes mennyisége a kiválasztott számú virtuális mag alapul.

| Virtuális mag | Egy feldolgozói csomópont, GiB RAM | Koordinátori csomópont, GiB RAM |
|--------|--------------------------|---------------------------|
| 4      | 32                       | 16                        |
| 8      | 64                       | 32                        |
| 16     | 128                      | 64                        |
| 32     | 256                      | 128                       |
| 64     | 432                      | 256                       |

A kiépített tárterület teljes mennyisége határozza meg az egyes feldolgozók és a koordinátorok csomópontja számára elérhető I/O-kapacitást is.

| Tároló mérete, TiB | Maximális IOPS |
|-------------------|--------------|
| 0,5               | 1,536        |
| 1                 | 3,072        |
| 2                 | 6 148        |

A teljes nagy kapacitású (Citus) fürt esetében az összesített IOPS a következő értékekre működik:

| Munkavégző csomópontok | 0,5 TiB, összes IOPS | 1 TiB, összes IOPS | 2 TiB, összes IOPS |
|--------------|---------------------|-------------------|-------------------|
| 2            | 3,072               | 6,144             | 12 296            |
| 3            | 4 608               | 9 216             | 18 444            |
| 4            | 6,144               | 12 288            | 24 592            |
| 5            | 7 680               | 15 360            | 30 740            |
| 6            | 9 216               | 18 432            | 36 888            |
| 7            | 10 752              | 21 504            | 43 036            |
| 8            | 12 288              | 24 576            | 49 184            |
| 9            | 13 824              | 27 648            | 55 332            |
| 10           | 15 360              | 30 720            | 61 480            |
| 11           | 16 896              | 33 792            | 67 628            |
| 12           | 18 432              | 36 864            | 73 776            |
| 13           | 19 968              | 39 936            | 79 924            |
| 14           | 21 504              | 43 008            | 86 072            |
| 15           | 23 040              | 46 080            | 92 220            |
| 16           | 24 576              | 49 152            | 98 368            |
| 17           | 26 112              | 52 224            | 104 516           |
| 18           | 27 648              | 55 296            | 110 664           |
| 19           | 29 184              | 58 368            | 116 812           |
| 20           | 30 720              | 61 440            | 122 960           |

## <a name="regions"></a>Régiók
A nagy kapacitású-(Citus-) kiszolgálócsoportok a következő Azure-régiókban érhetők el:

* Amerika: 
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
 
## <a name="next-steps"></a>További lépések
Ismerje meg, hogyan [hozhat létre nagy kapacitású-(Citus-) kiszolgálócsoport a portálon](quickstart-create-hyperscale-portal.md).
