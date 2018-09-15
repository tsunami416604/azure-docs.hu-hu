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
ms.date: 09/07/2018
ms.openlocfilehash: f337679279f4a3eb6d01808f15a59fee4c8f3cd1
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45630585"
---
# <a name="supported-postgresql-database-versions"></a>PostgreSQL-adatbázishoz a támogatott verziók
A Microsoft célja, hogy a PostgreSQL-motor n-2 verzióit támogatja az Azure Database for PostgreSQL szolgáltatás, azaz a jelenleg kiadott főverzió (n), és a két előző főbb verzió (-2).

Azure Database for postgresql-hez jelenleg az alábbi verzióit támogatja:

## <a name="postgresql-version-104"></a>PostgreSQL-verzió 10.4
Tekintse meg a [PostgreSQL dokumentációja](https://www.postgresql.org/docs/10/static/release-10-4.html) tudhat meg többet a fejlesztések és javítások kisebb ebben a verzióban.

## <a name="postgresql-version-969"></a>PostgreSQL-verzió 9.6.9
Tekintse meg a [PostgreSQL dokumentációja](https://www.postgresql.org/docs/9.6/static/release-9-6-9.html) tudhat meg többet a fejlesztések és javítások kisebb ebben a verzióban.

## <a name="postgresql-version-9513"></a>PostgreSQL-verzió 9.5.13
Tekintse meg a [PostgreSQL dokumentációja](https://www.postgresql.org/docs/9.5/static/release-9-5-13.html) kapcsolatos fejlesztések és javítások kisebb ebben a verzióban.

## <a name="managing-updates-and-upgrades"></a>Frissítések kezelése
Azure Database for postgresql-hez automatikusan kezeli a javítása az alverzió frissítéseket. Főverzió frissítés jelenleg nem támogatott. Például PostgreSQL 9,5 PostgreSQL 9.6 rendszerről történő frissítés nem támogatott. Ha szeretné, a következő fő verzióra való frissítéshez, egy [memóriakép és visszaállítás](./howto-migrate-using-dump-and-restore.md) , hogy egy kiszolgálót, amely az új motor verziójával lett létrehozva.

## <a name="next-steps"></a>További lépések
A különböző PostgreSQL-bővítmények a támogatásával kapcsolatos információkat lásd: [PostgreSQL-bővítmények](concepts-extensions.md).
