---
title: Azure Database for PostgreSQL – egyetlen kiszolgáló által támogatott verziók
description: A Azure Database for PostgreSQL-Single Server támogatott verzióit ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 07/26/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: fae8dfb2e2cc532f4aed17f83f13bbee31d29113
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68551365"
---
# <a name="supported-postgresql-database-versions"></a>A PostgreSQL-adatbázisok támogatott verziói
A Microsoft célja, hogy támogassa a PostgreSQL-motor n-2 verzióját Azure Database for PostgreSQL – egyetlen kiszolgálón. A verziók a jelenlegi főverzió az Azure-ban (n) és a két korábbi főverzió (-2).

A Azure Database for PostgreSQL jelenleg a következő verziókat támogatja:

## <a name="postgresql-version-112"></a>PostgreSQL 11,2-es verzió
Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/11/static/release-11-2.html) , ahol további információt talál a jelen alverzióban található javításokról és javításokról.

## <a name="postgresql-version-107"></a>PostgreSQL 10,7-es verzió
Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/10/static/release-10-7.html) , ahol további információt talál a jelen alverzióban található javításokról és javításokról.

## <a name="postgresql-version-9612"></a>PostgreSQL-verzió 9.6.12
Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html) , ahol további információt talál a jelen alverzióban található javításokról és javításokról.

## <a name="postgresql-version-9516"></a>PostgreSQL-verzió 9.5.16
Tekintse meg a [PostgreSQL dokumentációját](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html) , amely a jelen alverzióban elérhető javításokat és javításokat ismerteti.

## <a name="managing-updates-and-upgrades"></a>Frissítések és frissítések kezelése
A Azure Database for PostgreSQL automatikusan kezeli a másodlagos verziók javításait. A fő verziófrissítés jelenleg nem támogatott. Például a PostgreSQL 9,5-ről a PostgreSQL 9,6-re való frissítés nem támogatott. Ha a következő főverzióra szeretne frissíteni, hozzon létre egy adatbázis [-memóriaképet, és állítsa vissza](./howto-migrate-using-dump-and-restore.md) egy olyan kiszolgálóra, amely az új motor verziójával lett létrehozva.

> Vegye figyelembe, hogy a PostgreSQL-es verzió előtt a [PostgreSQL verziószámozási házirendje](https://www.postgresql.org/support/versioning/) _jelentős_ verziófrissítést eredményezett, hogy az első _vagy_ a második szám (például 9,5 – 9,6) nagyobb legyen.
> A 10-es verziótól kezdve a rendszer csak az első szám változását tekinti jelentős verziófrissítésnek (például 10,0 – 10,1 egy _alverzió_ verziófrissítése, és 10 – 11 a _főverzió frissítése_ ).

## <a name="next-steps"></a>További lépések
A különböző PostgreSQL-bővítmények támogatásával kapcsolatos információkért lásd: [PostgreSQL](concepts-extensions.md)-bővítmények.
