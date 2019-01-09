---
title: Azure Database for MySQL-hez a támogatott verziók
description: Ismerteti a támogatott verziók az Azure Database for MySQL-hez.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/07/2019
ms.openlocfilehash: ecd6466d8d7a7e4497d076ced0c9f2375d5dfb7f
ms.sourcegitcommit: 30d23a9d270e10bb87b6bfc13e789b9de300dc6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54106036"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Támogatott, Azure Database for MySQL server-verziók
Azure Database for MySQL-hez az identitáskezelési [MySQL Community Edition](https://www.mysql.com/products/community/), az InnoDB motor használatával.

MySQL X.Y.Z elnevezési sémája használ. X főverziója az Y az alverzió és Z az a hibajavítás kiadás. A rendszer kapcsolatos további információkért lásd: a [MySQL dokumentációja](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

Azure Database for MySQL jelenleg az alábbi verzióit támogatja:

## <a name="mysql-version-56"></a>MySQL 5.6-os verziója

Hibajavítás a kiadásban: 5.6.39

Tekintse meg a MySQL [kibocsátási megjegyzések](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-39.html) további fejlesztések és javítások MySQL 5.6.39-ben.

## <a name="mysql-version-57"></a>MySQL 5.7-es verzióra

Hibajavítás a kiadásban: 5.7.21

Tekintse meg a MySQL [kibocsátási megjegyzések](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-21.html) kapcsolatos fejlesztések és javítások MySQL 5.7.21-ben.

> [!NOTE]
> A szolgáltatásban az átjáró segítségével kiszolgálópéldányok irányítsa át a kapcsolatokat. A kapcsolat létrejötte után a MySQL-ügyfél az átjáró nem futó verzió az MySQL server-példányon állítsa be a MySQL verzióját jeleníti meg. A MySQL server-példány verziója meghatározásához használja a `SELECT VERSION();` parancsot a MySQL-parancssorban.

## <a name="managing-updates-and-upgrades"></a>Frissítések kezelése
A szolgáltatás automatikusan kezeli a hibajavítás verzió frissítések javítása. Ha például a 5.7.21 5.7.20.  

Kis és nagy verziófrissítések jelenleg nem támogatottak. Ha például frissítése a MySQL 5.6-os MySQL 5.7-es nem támogatott. Ha szeretné az 5.7 5.6-os verzióról, egy [memóriakép és visszaállítás](./concepts-migrate-dump-restore.md) , hogy egy kiszolgálót, amely az új motor verziójával lett létrehozva.

## <a name="next-steps"></a>További lépések

További információ az adott erőforrás kvótái és korlátai alapján a **szolgáltatásszint**, lásd: [szolgáltatásszintek](./concepts-pricing-tiers.md)
