---
title: Azure Database for postgresql-hez a támogatott verziók
description: Ismerteti a támogatott verziók az Azure Database for postgresql-hez.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 11/12/2018
ms.openlocfilehash: 04e47e15206e22e7965121c6c277e4032ea04ddf
ms.sourcegitcommit: 1f9e1c563245f2a6dcc40ff398d20510dd88fd92
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/14/2018
ms.locfileid: "51621054"
---
# <a name="supported-postgresql-database-versions"></a>PostgreSQL-adatbázis támogatott verziók
A Microsoft célja, hogy az Azure Database for PostgreSQL szolgáltatás a PostgreSQL-motor n-2 verzióit támogatja. A verziók lenne, az aktuális főbb verzióhoz az Azure-ban (n) és a két előző főbb verzió (-2).

Azure Database for postgresql-hez jelenleg az alábbi verzióit támogatja:

## <a name="postgresql-version-105"></a>PostgreSQL-verzió 10,5
Tekintse meg a [PostgreSQL dokumentációja](https://www.postgresql.org/docs/10/static/release-10-5.html) tudhat meg többet a fejlesztések és javítások kisebb ebben a verzióban.

## <a name="postgresql-version-9610"></a>PostgreSQL-verzió 9.6.10
Tekintse meg a [PostgreSQL dokumentációja](https://www.postgresql.org/docs/9.6/static/release-9-6-10.html) tudhat meg többet a fejlesztések és javítások kisebb ebben a verzióban.

## <a name="postgresql-version-9514"></a>PostgreSQL-verzió 9.5.14
Tekintse meg a [PostgreSQL dokumentációja](https://www.postgresql.org/docs/9.5/static/release-9-5-14.html) kapcsolatos fejlesztések és javítások kisebb ebben a verzióban.

## <a name="managing-updates-and-upgrades"></a>Frissítések kezelése
Azure Database for postgresql-hez automatikusan kezeli a alverzió javítások. Főverzió frissítés jelenleg nem támogatott. Például PostgreSQL 9,5 PostgreSQL 9.6 rendszerről történő frissítés nem támogatott. Ha szeretné, a következő fő verzióra való frissítéshez, egy [memóriakép és visszaállítás](./howto-migrate-using-dump-and-restore.md) , hogy egy kiszolgálót, amely az új motor verziójával lett létrehozva.

## <a name="next-steps"></a>További lépések
A különböző PostgreSQL-bővítmények a támogatásával kapcsolatos információkat lásd: [PostgreSQL-bővítmények](concepts-extensions.md).
