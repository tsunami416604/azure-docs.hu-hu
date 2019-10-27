---
title: Azure Database for PostgreSQL – egyetlen kiszolgáló által támogatott verziók
description: A Azure Database for PostgreSQL-Single Server támogatott verzióit ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 10/25/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 2a2b8b71e07e5dac74d73d3a81c150ac5d980ea2
ms.sourcegitcommit: 4c3d6c2657ae714f4a042f2c078cf1b0ad20b3a4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/25/2019
ms.locfileid: "72935338"
---
# <a name="supported-postgresql-database-versions"></a>A PostgreSQL-adatbázisok támogatott verziói
A Microsoft célja, hogy támogassa a PostgreSQL-motor n-2 verzióját Azure Database for PostgreSQL – egyetlen kiszolgálón. A verziók a jelenlegi főverzió az Azure-ban (n) és a két korábbi főverzió (-2).

A Azure Database for PostgreSQL jelenleg a következő főbb verziókat támogatja:

## <a name="postgresql-version-11"></a>PostgreSQL 11-es verzió
Az aktuális alverzió 11,5. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/11/static/release-11-5.html) , ahol további információt talál a jelen alverzióban található javításokról és javításokról.

## <a name="postgresql-version-10"></a>PostgreSQL 10-es verzió
Az aktuális alverzió 10,10. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/10/static/release-10-10.html) , ahol további információt talál a jelen alverzióban található javításokról és javításokról.

## <a name="postgresql-version-96"></a>PostgreSQL 9,6-es verzió
A jelenlegi alverzió a 9.6.15. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html) , ahol további információt talál a jelen alverzióban található javításokról és javításokról.

## <a name="postgresql-version-95"></a>PostgreSQL 9,5-es verzió
A jelenlegi alverzió a 9.5.19. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html) , amely a jelen alverzióban elérhető javításokat és javításokat ismerteti.

## <a name="managing-upgrades"></a>Frissítések kezelése
A Azure Database for PostgreSQL automatikusan kezeli a másodlagos verziók frissítését. 

A főverzió automatikus frissítése nem támogatott. Például a PostgreSQL 9.5-ös verziójáról a PostgreSQL 9.6-os verziójára történő automatikus frissítés nem érhető el. A következő főverzióra történő frissítéshez hozzon létre egy [adatbázismentése, és végezzen visszaállítást](./howto-migrate-using-dump-and-restore.md) az új motorverzióval létrehozott kiszolgálón.

### <a name="version-syntax"></a>Verzió szintaxisa
A PostgreSQL 10-es verziójának megkezdése előtt a [PostgreSQL verziószámozási házirendje](https://www.postgresql.org/support/versioning/) _jelentős_ verziófrissítést eredményezett, hogy az első _vagy_ a második szám növekszik. Például a 9,5 – 9,6 _fő_ verziófrissítésnek számít. A 10-es verziótól kezdve a rendszer csak az első szám változását tekinti jelentős verziófrissítésnek. Például a 10,0 – 10,1 egy _másodlagos_ verziófrissítés. A 10 – _11 verzió a főverzió frissítése_ .

## <a name="next-steps"></a>Következő lépések
A PostgreSQL támogatott bővítményeivel kapcsolatos információkért tekintse meg [a kiterjesztések dokumentumát](concepts-extensions.md).
