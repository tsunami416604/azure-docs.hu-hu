---
title: A Azure Database for PostgreSQL-nagy kapacitású (Citus) áttekintése
description: Áttekintést nyújt a nagy kapacitású (Citus) üzembe helyezési lehetőségéről
author: jonels-msft
ms.author: jonels
ms.custom: mvc
ms.service: postgresql
ms.subservice: hyperscale-citus
ms.topic: overview
ms.date: 09/01/2020
ms.openlocfilehash: 90d3cb106fa93649f7d6dda5ab5755061d118f66
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91268383"
---
# <a name="what-is-azure-database-for-postgresql---hyperscale-citus"></a>Mi az Azure Database for PostgreSQL-nagy kapacitású (Citus)?

A Azure Database for PostgreSQL egy, a fejlesztők számára készült Microsoft-felhőben található, a Microsoft felhőalapú szolgáltatása. Ez a nyílt forráskódú [PostgreSQL](https://www.postgresql.org/) adatbázismotor közösségi verzióján alapul.

A nagy kapacitású (Citus) olyan központi telepítési lehetőség, amely horizontálisan méretezi a különböző gépeken futó lekérdezéseket. A lekérdezési motor parallelizes a bejövő SQL-lekérdezéseket ezen kiszolgálókon a nagyméretű adatkészletek gyorsabb megválaszolásához. Olyan alkalmazásokat kínál, amelyek nagyobb méretet és teljesítményt igényelnek, mint a többi üzembe helyezési lehetőség: általában megközelíthető munkaterhelések, vagy már több, mint 100 GB adat.

A nagy kapacitású (Citus) a következőket biztosítja:

- Horizontális skálázás több gépen több szegmensen keresztül
- Párhuzamos lekérdezése a kiszolgálók között a nagyméretű adatkészletek gyorsabb reagálásához
- Kiváló támogatás a több-bérlős alkalmazások, a valós idejű operatív elemzések és a nagy teljesítményű tranzakciós számítási feladatok számára

A PostgreSQL-re épülő alkalmazások [a standard szintű](./concepts-connection-libraries.md) nagy kapacitású (Citus) elosztott lekérdezéseket futtathatnak, és minimális módosításokat hajtanak végre.

## <a name="next-steps"></a>Következő lépések

- Első lépésként [hozza létre első](./quickstart-create-hyperscale-portal.md) Azure Database for PostgreSQL-nagy kapacitású (Citus) kiszolgálócsoportét.
- Tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/postgresql/) a Cost-összehasonlításokhoz és a számológépekhez. A nagy kapacitású (Citus) előre fizetett, fenntartott példányokra vonatkozó kedvezményeket is kínál, további részletekért lásd a [nagy kapacitású (Citus) ri díjszabási](concepts-hyperscale-reserved-pricing.md) oldalát.
- A kiszolgálócsoport legjobb [kezdeti méretének](howto-hyperscale-scaling.md#picking-initial-size) meghatározása
