---
title: Az Azure Database for PostgreSQL - kiszolgáló egyetlen támogatott verzió
description: Ismerteti a támogatott verziók az Azure Database for PostgreSQL – egyetlen kiszolgáló.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 4dcbaf159fce6b5f5495a6a25a2a3420cad9e5e8
ms.sourcegitcommit: 509e1583c3a3dde34c8090d2149d255cb92fe991
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/27/2019
ms.locfileid: "65067261"
---
# <a name="supported-postgresql-database-versions"></a>PostgreSQL-adatbázis támogatott verziók
A Microsoft célja, hogy támogatja a PostgreSQL-motor n-2 verzióit az Azure Database for PostgreSQL – egyetlen kiszolgáló. A verziók lenne, az aktuális főbb verzióhoz az Azure-ban (n) és a két előző főbb verzió (-2).

Azure Database for postgresql-hez jelenleg az alábbi verzióit támogatja:

## <a name="postgresql-version-107"></a>PostgreSQL-verzió 10.7
Tekintse meg a [PostgreSQL dokumentációja](https://www.postgresql.org/docs/10/static/release-10-7.html) tudhat meg többet a fejlesztések és javítások kisebb ebben a verzióban.

## <a name="postgresql-version-9612"></a>PostgreSQL-verzió 9.6.12
Tekintse meg a [PostgreSQL dokumentációja](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html) tudhat meg többet a fejlesztések és javítások kisebb ebben a verzióban.

## <a name="postgresql-version-9516"></a>PostgreSQL-verzió 9.5.16
Tekintse meg a [PostgreSQL dokumentációja](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html) kapcsolatos fejlesztések és javítások kisebb ebben a verzióban.

## <a name="managing-updates-and-upgrades"></a>Frissítések kezelése
Azure Database for postgresql-hez automatikusan kezeli a alverzió javítások. Főverzió frissítés jelenleg nem támogatott. Például PostgreSQL 9,5 PostgreSQL 9.6 rendszerről történő frissítés nem támogatott. Ha szeretné, a következő fő verzióra való frissítéshez, hozzon létre egy adatbázist [memóriakép és visszaállítás](./howto-migrate-using-dump-and-restore.md) , hogy egy kiszolgálót, amely az új motor verziójával lett létrehozva.

## <a name="next-steps"></a>További lépések
A különböző PostgreSQL-bővítmények a támogatásával kapcsolatos információkat lásd: [PostgreSQL-bővítmények](concepts-extensions.md).
