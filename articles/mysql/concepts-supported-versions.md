---
title: A Azure Database for MySQL támogatott verziói
description: A Azure Database for MySQL támogatott verzióit ismerteti.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: 25251b617522840412a4868331e155285f64a18c
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962583"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Támogatott Azure Database for MySQL Server-verziók

A Azure Database for MySQL a [MySQL Community Editionből](https://www.mysql.com/products/community/)lett kifejlesztve, a InnoDB motor használatával.

A MySQL az X. Y. Z elnevezési sémát használja. Az X a főverzió, az Y a másodlagos verzió, a Z pedig a hibajavítási kiadás. A sémával kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> A szolgáltatásban az átjárónak az a feladata, hogy átirányítsa a kapcsolatokat a kiszolgálópéldányokra. A kapcsolatok létrejötte után a MySQL-ügyfél az átjárón beállított MySQL-verziót jeleníti meg, nem a MySQL-kiszolgálópéldányon futó tényleges verziót. A MySQL-kiszolgálópéldány verziójának meghatározásához használja a `SELECT VERSION();` parancsot a MySQL parancssorában.

A Azure Database for MySQL jelenleg a következő verziókat támogatja:

## <a name="mysql-version-56"></a>MySQL 5,6-es verzió

Hibajavítási kiadás: 5.6.44

Tekintse meg a MySQL [kibocsátási megjegyzéseit](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-44.html) , ahol további információt talál a jelen verzió javításait és javításait ismertető cikkben.

## <a name="mysql-version-57"></a>MySQL 5,7-es verzió

Hibajavítási kiadás: 5.7.26

Tekintse meg a MySQL [kibocsátási megjegyzéseit](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-26.html) , ahol további információt talál a jelen verzió javításait és javításait ismertető cikkben.

## <a name="mysql-version-80"></a>MySQL 8,0-es verzió

> [!IMPORTANT]
> A MySQL 8,0 jelenleg előzetes verzióban érhető el.

Hibajavítási kiadás: 8.0.15

Tekintse meg a MySQL [kibocsátási megjegyzéseit](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) , ahol további információt talál a jelen verzió javításait és javításait ismertető cikkben.

## <a name="managing-updates-and-upgrades"></a>Frissítések és frissítések kezelése
A szolgáltatás automatikusan kezeli a hibajavítások verziófrissítésének javítását. Például 5.7.20 telepítése a 5.7.21.  

Jelenleg a másodlagos és a főverzió frissítése nem támogatott. Például a MySQL 5,6-ről a MySQL 5,7-re való frissítés nem támogatott. Ha 5,6-ről 5,7-re szeretne frissíteni, hozzon létre egy [memóriaképet, és állítsa vissza](./concepts-migrate-dump-restore.md) egy olyan kiszolgálóra, amely az új motor verziójával lett létrehozva.

## <a name="next-steps"></a>További lépések

A **szolgáltatási szintek**alapján meghatározott erőforrás-kvótákkal és korlátozásokkal kapcsolatos információkért lásd: [szolgáltatási szintek](./concepts-pricing-tiers.md)
