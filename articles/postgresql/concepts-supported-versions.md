---
title: Támogatott verziók – Azure Database for PostgreSQL – egyetlen kiszolgáló
description: A Azure Database for PostgreSQL-egyetlen kiszolgálón támogatott postgres fő-és alverziókat ismerteti.
author: lfittl-msft
ms.author: lufittl
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/22/2020
ms.custom: fasttrack-edit
ms.openlocfilehash: cfe4b92dbed69440ee2c07cff758faad7e01293f
ms.sourcegitcommit: 19dce034650c654b656f44aab44de0c7a8bd7efe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/04/2020
ms.locfileid: "91707914"
---
# <a name="supported-postgresql-major-versions"></a>Támogatott PostgreSQL főverziók
A Microsoft célja, hogy támogassa a PostgreSQL-motor n-2 verzióját Azure Database for PostgreSQL – egyetlen kiszolgálón. A verziók a jelenlegi főverzió az Azure-ban (n) és a két korábbi főverzió (-2).

A Azure Database for PostgreSQL jelenleg a következő főbb verziókat támogatja:

## <a name="postgresql-version-11"></a>PostgreSQL 11-es verzió
Az aktuális alverzió 11,6. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/11/static/release-11-6.html) , ahol további információt talál az ebben a másodlagos kiadásban található javításokról és javításokról.

## <a name="postgresql-version-10"></a>PostgreSQL 10-es verzió
Az aktuális alverzió 10,11. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/10/static/release-10-11.html) , ahol további információt talál az ebben a másodlagos kiadásban található javításokról és javításokról.

## <a name="postgresql-version-96"></a>PostgreSQL 9,6-es verzió
Az aktuális másodlagos kiadás a 9.6.16. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/9.6/static/release-9-6-16.html) , ahol további információt talál az ebben a másodlagos kiadásban található javításokról és javításokról.

## <a name="postgresql-version-95"></a>PostgreSQL 9,5-es verzió
Az aktuális másodlagos kiadás a 9.5.20. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/9.5/static/release-9-5-20.html) , amely az ebben a másodlagos kiadásban található javításokat és javításokat ismerteti.

## <a name="managing-upgrades"></a>Frissítések kezelése
A PostgreSQL-projekt rendszeresen kiad kisebb kiadásokat a jelentett hibák kijavítása érdekében. Azure Database for PostgreSQL a szolgáltatás havi üzembe helyezése során a rendszer a kisebb kiadású kiszolgálók automatikus javítását végzi. 

A főverziók automatikus helyi frissítése nem támogatott. A következő főverzióra való frissítéshez 
   * A [pg_dump és a pg_restore](./howto-migrate-using-dump-and-restore.md) használatával helyezzen át egy adatbázist az új motor verziójával létrehozott kiszolgálóra
   * Azt is megteheti, hogy a PostgreSQL 10 és 11 közötti verziójára frissít az [Azure Database Migration Service](..\dms\tutorial-azure-postgresql-to-azure-postgresql-online-portal.md) használatával

### <a name="version-syntax"></a>Verzió szintaxisa
A PostgreSQL 10-es verziójának megkezdése előtt a [PostgreSQL verziószámozási házirendje](https://www.postgresql.org/support/versioning/) _jelentős_ verziófrissítést eredményezett, hogy az első _vagy_ a második szám növekszik. Például a 9,5 – 9,6 _fő_ verziófrissítésnek számít. A 10-es verziótól kezdve a rendszer csak az első szám változását tekinti jelentős verziófrissítésnek. Például a 10,0 – 10,1 egy _kisebb_ kiadású frissítés. A 10 – _11 verzió a főverzió frissítése_ .

## <a name="next-steps"></a>Következő lépések
A PostgreSQL támogatott bővítményeivel kapcsolatos információkért tekintse meg [a kiterjesztések dokumentumát](concepts-extensions.md).
