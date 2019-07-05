---
title: Az Azure Database for PostgreSQL - kiszolgáló egyetlen támogatott verzió
description: Ismerteti a támogatott verziók az Azure Database for PostgreSQL – egyetlen kiszolgáló.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 06/11/2019
ms.custom: fasttrack-edit
ms.openlocfilehash: c328f0dc510c1b7d36b547e6560a292f98d72f6f
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67448048"
---
# <a name="supported-postgresql-database-versions"></a>PostgreSQL-adatbázis támogatott verziók
A Microsoft célja, hogy támogatja a PostgreSQL-motor n-2 verzióit az Azure Database for PostgreSQL – egyetlen kiszolgáló. A verziók lenne, az aktuális főbb verzióhoz az Azure-ban (n) és a két előző főbb verzió (-2).

Azure Database for postgresql-hez jelenleg az alábbi verzióit támogatja:

## <a name="postgresql-version-112"></a>11,2 PostgreSQL-verzió
Tekintse meg a [PostgreSQL dokumentációja](https://www.postgresql.org/docs/11/static/release-11-2.html) tudhat meg többet a fejlesztések és javítások kisebb ebben a verzióban.

>[!NOTE]
> 11-es verzió PostgreSQL előzetes verzióban érhető el. Létrehozás az Azure portal használatával támogatása tesszük elérhetővé, és előfordulhat, hogy még nem érhető el a régióban. Használhatja a [Azure CLI-vel](quickstart-create-server-database-azure-cli.md) Postgres 11 kiszolgálót hoz létre bármely régióban. Például: `az postgres server create -g group -n server -u username -p password -l westeurope --sku-name GP_Gen5_2 --version 11`.

## <a name="postgresql-version-107"></a>PostgreSQL-verzió 10.7
Tekintse meg a [PostgreSQL dokumentációja](https://www.postgresql.org/docs/10/static/release-10-7.html) tudhat meg többet a fejlesztések és javítások kisebb ebben a verzióban.

## <a name="postgresql-version-9612"></a>PostgreSQL-verzió 9.6.12
Tekintse meg a [PostgreSQL dokumentációja](https://www.postgresql.org/docs/9.6/static/release-9-6-12.html) tudhat meg többet a fejlesztések és javítások kisebb ebben a verzióban.

## <a name="postgresql-version-9516"></a>PostgreSQL-verzió 9.5.16
Tekintse meg a [PostgreSQL dokumentációja](https://www.postgresql.org/docs/9.5/static/release-9-5-16.html) kapcsolatos fejlesztések és javítások kisebb ebben a verzióban.

## <a name="managing-updates-and-upgrades"></a>Frissítések kezelése
Azure Database for postgresql-hez automatikusan kezeli a alverzió javítások. Főverzió frissítés jelenleg nem támogatott. Például PostgreSQL 9,5 PostgreSQL 9.6 rendszerről történő frissítés nem támogatott. Ha szeretné, a következő fő verzióra való frissítéshez, hozzon létre egy adatbázist [memóriakép és visszaállítás](./howto-migrate-using-dump-and-restore.md) , hogy egy kiszolgálót, amely az új motor verziójával lett létrehozva.

> PostgreSQL, 10-es verzió előtt vegye figyelembe, hogy a [PostgreSQL versioning házirend](https://www.postgresql.org/support/versioning/) számít egy _főverzió_ megnöveli az első frissítés _vagy_ száma (a második például tekintették 9,5 a 9.6- _fő_ termékverzió-frissítés).
> Től 10-es verzió, csak az első szám módosítása a főverzió-frissítés számít (például 10.0, 10.1 van egy _kisebb_ termékverzió-frissítés, illetve 10, 11 egy _fő_ termékverzió-frissítés).

## <a name="next-steps"></a>További lépések
A különböző PostgreSQL-bővítmények a támogatásával kapcsolatos információkat lásd: [PostgreSQL-bővítmények](concepts-extensions.md).
