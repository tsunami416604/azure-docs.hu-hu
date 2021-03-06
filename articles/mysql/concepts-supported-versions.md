---
title: Támogatott verziók – Azure Database for MySQL
description: Megtudhatja, hogy a MySQL-kiszolgáló mely verziói támogatottak a Azure Database for MySQL szolgáltatásban.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 6/3/2020
ms.openlocfilehash: 8033117d9e3c31f8aa9bba06afb7c3b1b7bba67f
ms.sourcegitcommit: 1bf144dc5d7c496c4abeb95fc2f473cfa0bbed43
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/24/2020
ms.locfileid: "95751028"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Támogatott Azure Database for MySQL Server-verziók

A Azure Database for MySQL a [MySQL Community Editionből](https://www.mysql.com/products/community/)lett kifejlesztve, a InnoDB motor használatával.

A MySQL az X. Y. Z elnevezési sémát használja. Az X a főverzió, az Y a másodlagos verzió, a Z pedig a hibajavítási kiadás. A sémával kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).


> [!NOTE]
> A szolgáltatásban az átjárónak az a feladata, hogy átirányítsa a kapcsolatokat a kiszolgálópéldányokra. A kapcsolatok létrejötte után a MySQL-ügyfél az átjárón beállított MySQL-verziót jeleníti meg, nem a MySQL-kiszolgálópéldányon futó tényleges verziót. A MySQL-kiszolgálópéldány verziójának meghatározásához használja a `SELECT VERSION();` parancsot a MySQL parancssorában.

A Azure Database for MySQL jelenleg a következő verziókat támogatja:

## <a name="mysql-version-56"></a>MySQL 5,6-es verzió

Hibajavítási kiadás: 5.6.47

Tekintse meg a MySQL [kibocsátási megjegyzéseit](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-47.html) , ahol további információt talál a jelen verzió javításait és javításait ismertető cikkben.

## <a name="mysql-version-57"></a>MySQL 5,7-es verzió

Hibajavítási kiadás: 5.7.29

Tekintse meg a MySQL [kibocsátási megjegyzéseit](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-29.html) , ahol további információt talál a jelen verzió javításait és javításait ismertető cikkben.

## <a name="mysql-version-80"></a>MySQL 8,0-es verzió

Hibajavítási kiadás: 8.0.15

Tekintse meg a MySQL [kibocsátási megjegyzéseit](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) , ahol további információt talál a jelen verzió javításait és javításait ismertető cikkben.

## <a name="managing-updates-and-upgrades"></a>Frissítések és frissítések kezelése
A szolgáltatás automatikusan kezeli a hibajavítások verziófrissítésének javítását. Például 5.7.20 telepítése a 5.7.21.  

A szolgáltatás jelenleg a főverzió frissítését támogatja a MySQL v 5.6-ról v 5.7-re való frissítéshez. További részletekért tekintse [meg a főbb verziófrissítések elvégzését ismertető témakört](how-to-major-version-upgrade.md). Ha 5,7-ről 8,0-re kíván frissíteni, javasoljuk, hogy a [memóriaképet és a visszaállítást](./concepts-migrate-dump-restore.md) olyan kiszolgálóra végezze, amely az új motor verziójával lett létrehozva.

## <a name="next-steps"></a>Következő lépések

- A Azure Database for MySQL verziószámozási szabályzatával kapcsolatos részletekért tekintse meg [a következő dokumentumot](concepts-version-policy.md):.
- A **szolgáltatási szintek** alapján meghatározott erőforrás-kvótákkal és korlátozásokkal kapcsolatos információkért lásd: [szolgáltatási szintek](./concepts-pricing-tiers.md)
