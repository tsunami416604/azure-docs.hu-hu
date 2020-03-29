---
title: Támogatott verziók - Azure Database for PostgreSQL - Single Server
description: A PostgrSQL – Single Server azure Database támogatott Postgres fő- és alverzióit ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 12/03/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: 61dd98028b7342290984615ea19b561b48aaeadb
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74792234"
---
# <a name="supported-postgresql-major-versions"></a>Támogatott PostgreSQL főverziók
A Microsoft célja, hogy támogassa a PostgreSQL motor n-2 verzióit az Azure Database for PostgreSQL - Single Server rendszerben. A verziók az Azure (n) aktuális főverziója és a két korábbi főverzió (-2) lennének.

Az Azure Database for PostgreSQL jelenleg a következő főverziókat támogatja:

## <a name="postgresql-version-11"></a>PostgreSQL verzió 11
A jelenlegi kisebb kiadás 11.5. Ebben a kisebb kiadásban a fejlesztésekről és javításokról a [PostgreSQL dokumentációjában](https://www.postgresql.org/docs/11/static/release-11-5.html) olvashat bővebben.

## <a name="postgresql-version-10"></a>PostgreSQL verzió 10
A jelenlegi kisebb kiadás 10.10. Ebben a kisebb kiadásban a fejlesztésekről és javításokról a [PostgreSQL dokumentációjában](https://www.postgresql.org/docs/10/static/release-10-10.html) olvashat bővebben.

## <a name="postgresql-version-96"></a>PostgreSQL 9.6-os verzió
A jelenlegi kisebb kiadás 9.6.15. Ebben a kisebb kiadásban a fejlesztésekről és javításokról a [PostgreSQL dokumentációjában](https://www.postgresql.org/docs/9.6/static/release-9-6-15.html) olvashat bővebben.

## <a name="postgresql-version-95"></a>PostgreSQL 9.5-ös verzió
A jelenlegi kisebb kiadás 9.5.19. Ebben a kisebb kiadásban a fejlesztésekről és javításokról a [PostgreSQL dokumentációjában](https://www.postgresql.org/docs/9.5/static/release-9-5-19.html) tájékozódhat.

## <a name="managing-upgrades"></a>Frissítések kezelése
A PostgreSQL projekt rendszeresen kisebb kiadásokat ad ki a jelentett hibák kijavítására. A PostgreSQL Azure Database automatikusan kijavítja a kiszolgálókat kisebb kiadásokkal a szolgáltatás havi telepítései során. 

A főverzió automatikus frissítése nem támogatott. Például a PostgreSQL 9.5-ös verziójáról a PostgreSQL 9.6-os verziójára történő automatikus frissítés nem érhető el. A következő főverzióra történő frissítéshez hozzon létre egy [adatbázismentése, és végezzen visszaállítást](./howto-migrate-using-dump-and-restore.md) az új motorverzióval létrehozott kiszolgálón.

### <a name="version-syntax"></a>Verziószintaxis
A PostgreSQL 10-es verziója előtt a [PostgreSQL verziószámozási politikája](https://www.postgresql.org/support/versioning/) úgy vélte, hogy a _főverziófrissítés_ az első _vagy_ a második szám növekedését jelent. A 9,5 és 9,6 között például _a rendszer főverzió-frissítést_ tartott. A 10-es verziótól csak az első szám változása számít főverziófrissítésnek. A 10.0 és 10.1 között például egy _kisebb_ kiadásfrissítés. A 10-es verzió _11-es_ verziója egy főverziófrissítés.

## <a name="next-steps"></a>További lépések
A támogatott PostgreSQL-bővítményekről [a bővítménydokumentumban](concepts-extensions.md)talál információt.
