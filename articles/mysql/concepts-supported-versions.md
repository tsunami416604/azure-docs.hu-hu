---
title: Támogatott verziók – Azure Database for MySQL
description: Megtudhatja, hogy a MySQL-kiszolgáló mely verziói támogatottak a Azure Database for MySQL szolgáltatásban.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/09/2019
ms.openlocfilehash: b4029d49eeba53e7a502a7ac68081bb5a2d549f8
ms.sourcegitcommit: 5ab4f7a81d04a58f235071240718dfae3f1b370b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2019
ms.locfileid: "74971927"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Támogatott Azure Database for MySQL Server-verziók

A Azure Database for MySQL a [MySQL Community Editionből](https://www.mysql.com/products/community/)lett kifejlesztve, a InnoDB motor használatával.

A MySQL az X. Y. Z elnevezési sémát használja. Az X a főverzió, az Y a másodlagos verzió, a Z pedig a hibajavítási kiadás. A sémával kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> A szolgáltatásban az átjárónak az a feladata, hogy átirányítsa a kapcsolatokat a kiszolgálópéldányokra. A kapcsolatok létrejötte után a MySQL-ügyfél az átjárón beállított MySQL-verziót jeleníti meg, nem a MySQL-kiszolgálópéldányon futó tényleges verziót. A MySQL-kiszolgálópéldány verziójának meghatározásához használja a `SELECT VERSION();` parancsot a MySQL parancssorában.

A Azure Database for MySQL jelenleg a következő verziókat támogatja:

## <a name="mysql-version-56"></a>MySQL 5,6-es verzió

Hibajavítási kiadás: 5.6.45

Tekintse meg a MySQL [kibocsátási megjegyzéseit](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-45.html) , ahol további információt talál a jelen verzió javításait és javításait ismertető cikkben.

## <a name="mysql-version-57"></a>MySQL 5,7-es verzió

Hibajavítási kiadás: 5.7.27

Tekintse meg a MySQL [kibocsátási megjegyzéseit](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-27.html) , ahol további információt talál a jelen verzió javításait és javításait ismertető cikkben.

## <a name="mysql-version-80"></a>MySQL 8,0-es verzió

> [!IMPORTANT]
> A MySQL 8,0 jelenleg előzetes verzióban érhető el.

Hibajavítási kiadás: 8.0.15

Tekintse meg a MySQL [kibocsátási megjegyzéseit](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) , ahol további információt talál a jelen verzió javításait és javításait ismertető cikkben.

## <a name="managing-updates-and-upgrades"></a>Frissítések és frissítések kezelése
A szolgáltatás automatikusan kezeli a hibajavítások verziófrissítésének javítását. Például 5.7.20 telepítése a 5.7.21.  

Az al- vagy a főverzió frissítései jelenleg nem támogatottak. Például a MySQL 5.6-os verzióról a MySQL 5.7-es verzióra történő frissítés nem támogatott. Ha az 5.6-os verzióról az 5.7-es verzióra szeretne frissíteni, készítsen [biztonsági másolatot, és végezzen visszaállítást](./concepts-migrate-dump-restore.md) az új motorverzióval létrehozott kiszolgálón.

## <a name="next-steps"></a>Következő lépések

A **szolgáltatási szintek**alapján meghatározott erőforrás-kvótákkal és korlátozásokkal kapcsolatos információkért lásd: [szolgáltatási szintek](./concepts-pricing-tiers.md)
