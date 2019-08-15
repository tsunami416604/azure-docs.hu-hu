---
title: A Azure Database for MySQL támogatott verziói
description: A Azure Database for MySQL támogatott verzióit ismerteti.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 08/09/2019
ms.openlocfilehash: 28d635dc5042799790d032ef4b46bf28118cb326
ms.sourcegitcommit: 124c3112b94c951535e0be20a751150b79289594
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/10/2019
ms.locfileid: "68947168"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Támogatott Azure Database for MySQL Server-verziók

A Azure Database for MySQL a [MySQL Community Editionből](https://www.mysql.com/products/community/)lett kifejlesztve, a InnoDB motor használatával.

A MySQL az X. Y. Z elnevezési sémát használja. Az X a főverzió, az Y a másodlagos verzió, a Z pedig a hibajavítási kiadás. A sémával kapcsolatos további információkért tekintse meg a [MySQL dokumentációját](https://dev.mysql.com/doc/refman/5.7/en/which-version.html).

> [!NOTE]
> A szolgáltatásban átjárót használ a kiszolgálók közötti kapcsolatok átirányításához. A létrehozást követően a MySQL-ügyfél megjeleníti a MySQL-t az átjáróban, nem pedig a MySQL-kiszolgáló példányán futó tényleges verziót. A MySQL-kiszolgálópéldány verziójának meghatározásához használja az `SELECT VERSION();` parancsot a MySQL-parancssorban.

A Azure Database for MySQL jelenleg a következő verziókat támogatja:

## <a name="mysql-version-56"></a>MySQL 5,6-es verzió

Hibajavítási kiadás: 5.6.42

Tekintse meg a MySQL [kibocsátási megjegyzéseit](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-42.html) , ahol többet tudhat meg a MySQL-5.6.42 javításait és javításait illetően.

## <a name="mysql-version-57"></a>MySQL 5,7-es verzió

Hibajavítási kiadás: 5.7.24

Tekintse meg a MySQL [kibocsátási megjegyzéseit](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-24.html) , amelyekkel megismerheti a MySQL-5.7.24 kapcsolatos javításokat és javításokat.

## <a name="mysql-version-80"></a>MySQL 8,0-es verzió

> [!IMPORTANT]
> A MySQL 8,0 jelenleg előzetes verzióban érhető el.

Hibajavítási kiadás: 8.0.15

Tekintse meg a MySQL [kibocsátási megjegyzéseit](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) , amelyekkel megismerheti a MySQL-8.0.15 kapcsolatos javításokat és javításokat.

## <a name="managing-updates-and-upgrades"></a>Frissítések és frissítések kezelése
A szolgáltatás automatikusan kezeli a hibajavítások verziófrissítésének javítását. Például 5.7.20 telepítése a 5.7.21.  

Jelenleg a másodlagos és a főverzió frissítése nem támogatott. Például a MySQL 5,6-ről a MySQL 5,7-re való frissítés nem támogatott. Ha 5,6-ről 5,7-re szeretne frissíteni, hozzon létre egy memóriaképet, [és állítsa vissza](./concepts-migrate-dump-restore.md) egy olyan kiszolgálóra, amely az új motor verziójával lett létrehozva.

## <a name="next-steps"></a>További lépések

A **szolgáltatási szintek**alapján meghatározott erőforrás-kvótákkal és korlátozásokkal kapcsolatos információkért lásd: [szolgáltatási szintek](./concepts-pricing-tiers.md)
