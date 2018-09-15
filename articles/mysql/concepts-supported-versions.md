---
title: Azure Database for MySQL-hez a támogatott verziók
description: Ismerteti a támogatott verziók az Azure Database for MySQL-hez.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 05/23/2018
ms.openlocfilehash: 1b6dded1521489353e65b630ef5432ba6ff8f3e8
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45631565"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Támogatott, Azure Database for MySQL server-verziók
Azure Database for MySQL-hez az identitáskezelési [MySQL Community Edition](https://www.mysql.com/products/community/), az InnoDB motor használatával.  Azure Database for MySQL jelenleg az alábbi verzióit támogatja:

## <a name="mysql-version-5639"></a>MySQL verziója 5.6.39
Tekintse meg a MySQL [dokumentáció](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-39.html) további fejlesztések és javítások MySQL 5.6.39-ben.

## <a name="mysql-version-5721"></a>MySQL verziója 5.7.21
Tekintse meg a MySQL [dokumentáció](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-21.html) kapcsolatos fejlesztések és javítások MySQL 5.7.21-ben.

> [!NOTE]
> A szolgáltatásban az átjáró segítségével kiszolgálópéldányok irányítsa át a kapcsolatokat. A kapcsolat létrejötte után a MySQL-ügyfél az átjáró nem futó verzió az MySQL server-példányon állítsa be a MySQL verzióját jeleníti meg. A MySQL server-példány verziója meghatározásához használja a `SELECT VERSION();` parancsot a MySQL-parancssorban.

## <a name="managing-updates-and-upgrades"></a>Frissítések kezelése
A szolgáltatás automatikusan kezeli a javítása az alverzió frissítéseket. Főverzió frissítés jelenleg nem támogatott. Ha például a MySQL 5.6-os MySQL 5.7-es verzióra nem támogatott. Ha szeretné, a következő fő verzióra való frissítéshez, egy [memóriakép és visszaállítás](./concepts-migrate-dump-restore.md) , hogy egy kiszolgálót, amely az új motor verziójával lett létrehozva.

## <a name="next-steps"></a>További lépések

További információ az adott erőforrás kvótái és korlátai alapján a **szolgáltatásszint**, lásd: [szolgáltatásszintek](./concepts-pricing-tiers.md)
