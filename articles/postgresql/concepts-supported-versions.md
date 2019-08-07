---
title: Azure Database for PostgreSQL – egyetlen kiszolgáló által támogatott verziók
description: A Azure Database for PostgreSQL-Single Server támogatott verzióit ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 08/06/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: b77fd082be43b8cbdedf7cbe5875a8931eb0474a
ms.sourcegitcommit: bc3a153d79b7e398581d3bcfadbb7403551aa536
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/06/2019
ms.locfileid: "68837894"
---
# <a name="supported-postgresql-database-versions"></a>A PostgreSQL-adatbázisok támogatott verziói
A Microsoft célja, hogy támogassa a PostgreSQL-motor n-2 verzióját Azure Database for PostgreSQL – egyetlen kiszolgálón. A verziók a jelenlegi főverzió az Azure-ban (n) és a két korábbi főverzió (-2).

A Azure Database for PostgreSQL jelenleg a következő főbb verziókat támogatja:

## <a name="postgresql-version-11"></a>PostgreSQL 11-es verzió
Az aktuális alverzió 11,4. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/11/static/release-11-4.html) , ahol további információt talál a jelen alverzióban található javításokról és javításokról.

## <a name="postgresql-version-10"></a>PostgreSQL 10-es verzió
Az aktuális alverzió 10,9. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/10/static/release-10-9.html) , ahol további információt talál a jelen alverzióban található javításokról és javításokról.

## <a name="postgresql-version-96"></a>PostgreSQL 9,6-es verzió
A jelenlegi alverzió a 9.6.14. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/9.6/static/release-9-6-14.html) , ahol további információt talál a jelen alverzióban található javításokról és javításokról.

## <a name="postgresql-version-95"></a>PostgreSQL 9,5-es verzió
A jelenlegi alverzió a 9.5.18. Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/9.5/static/release-9-5-18.html) , amely a jelen alverzióban elérhető javításokat és javításokat ismerteti.

## <a name="managing-upgrades"></a>Frissítések kezelése
A Azure Database for PostgreSQL automatikusan kezeli a másodlagos verziók frissítését. 

Az automatikus főverzió frissítése nem támogatott. Például nincs automatikus frissítés a PostgreSQL 9,5-ről a PostgreSQL 9,6-re. A következő főverzióra való frissítéshez hozzon létre egy [adatbázis-memóriaképet, és állítsa vissza](./howto-migrate-using-dump-and-restore.md) egy olyan kiszolgálóra, amely az új motor verziójával lett létrehozva.

### <a name="version-syntax"></a>Verzió szintaxisa
A PostgreSQL 10-es verziójának megkezdése előtt a [PostgreSQL verziószámozási házirendje](https://www.postgresql.org/support/versioning/) _jelentős_ verziófrissítést eredményezett, hogy az első _vagy_ a második szám növekszik. Például a 9,5 – 9,6 _fő_ verziófrissítésnek számít. A 10-es verziótól kezdve a rendszer csak az első szám változását tekinti jelentős verziófrissítésnek. Például a 10,0 – 10,1 egy _másodlagos_ verziófrissítés. A 10 – 11 verzió a főverzió frissítése.

## <a name="next-steps"></a>További lépések
A PostgreSQL támogatott bővítményeivel kapcsolatos információkért tekintse meg [a kiterjesztések dokumentumát](concepts-extensions.md).
