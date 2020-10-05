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
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "91268383"
---
# <a name="what-is-azure-database-for-postgresql---hyperscale-citus"></a>Mi az Azure Database for PostgreSQL-nagy kapacitású (Citus)?

A Azure Database for PostgreSQL egy, a fejlesztők számára készült Microsoft-felhőben található, a Microsoft felhőalapú szolgáltatása. Ez a nyílt forráskódú [PostgreSQL](https://www.postgresql.org/) adatbázismotor közösségi verzióján alapul.

A nagy kapacitású (Citus) olyan központi telepítési lehetőség, amely horizontálisan méretezi a különböző gépeken futó lekérdezéseket. A lekérdezési motor párhuzamosan hajtja végre a bejövő SQL-lekérdezéseket ezeken kiszolgálókon, hogy a nagy méretű adathalmazok esetében is gyors választ biztosítson. Olyan alkalmazásokat kínál, amelyek nagyobb méretet és teljesítményt igényelnek, mint a többi üzembe helyezési lehetőség: általában megközelíthető munkaterhelések, vagy már több, mint 100 GB adat.

A nagy kapacitású (Citus) a következőket biztosítja:

- Horizontális skálázás több gépen a sharding (horizontális skálázás) segítségével
- A lekérdezések párhuzamosítása a kiszolgálókon a nagy méretű adathalmazokról adott gyors válaszokhoz
- Kiváló támogatás a több-bérlős alkalmazások, a valós idejű operatív elemzések és a nagy teljesítményű tranzakciós számítási feladatok számára

A PostgreSQL-re épülő alkalmazások [a standard szintű](./concepts-connection-libraries.md) nagy kapacitású (Citus) elosztott lekérdezéseket futtathatnak, és minimális módosításokat hajtanak végre.

## <a name="next-steps"></a>További lépések

- Első lépésként [hozza létre első](./quickstart-create-hyperscale-portal.md) Azure Database for PostgreSQL-nagy kapacitású (Citus) kiszolgálócsoportét.
- Tekintse meg a [díjszabási oldalt](https://azure.microsoft.com/pricing/details/postgresql/) a Cost-összehasonlításokhoz és a számológépekhez. A nagy kapacitású (Citus) előre fizetett, fenntartott példányokra vonatkozó kedvezményeket is kínál, további részletekért lásd a [nagy kapacitású (Citus) ri díjszabási](concepts-hyperscale-reserved-pricing.md) oldalát.
- A kiszolgálócsoport legjobb [kezdeti méretének](howto-hyperscale-scaling.md#picking-initial-size) meghatározása
