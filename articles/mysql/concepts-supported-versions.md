---
title: Támogatott verziók – Azure Database for MySQL
description: Ismerje meg, hogy a MySQL-kiszolgáló mely verziói támogatottak az Azure Database for MySQL szolgáltatásban.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 197b3100190711a51cfe125fe1214a59c18e1491
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79536972"
---
# <a name="supported-azure-database-for-mysql-server-versions"></a>Támogatott Azure-adatbázis a MySQL kiszolgálóverziókhoz

Az Azure Database for MySQL a [MySQL Community Edition-ből](https://www.mysql.com/products/community/)lett fejlesztve, az InnoDB motor használatával.

A MySQL az X.Y.Z elnevezési sémát használja. X a főverzió, az Y az alverzió, a Z pedig a hibajavítás kiadása. A rendszerrel kapcsolatos további információkért tekintse meg a [MySQL dokumentációját.](https://dev.mysql.com/doc/refman/5.7/en/which-version.html)

> [!NOTE]
> A szolgáltatásban az átjárónak az a feladata, hogy átirányítsa a kapcsolatokat a kiszolgálópéldányokra. A kapcsolatok létrejötte után a MySQL-ügyfél az átjárón beállított MySQL-verziót jeleníti meg, nem a MySQL-kiszolgálópéldányon futó tényleges verziót. A MySQL-kiszolgálópéldány verziójának meghatározásához használja a `SELECT VERSION();` parancsot a MySQL parancssorában.

Az Azure Database for MySQL jelenleg a következő verziókat támogatja:

## <a name="mysql-version-56"></a>MySQL 5.6-os verzió

Hibajavítás kiadása: 5.6.45

Ebben a verzióban a fejlesztésekről és javításokról a MySQL [kiadási megjegyzésekben](https://dev.mysql.com/doc/relnotes/mysql/5.6/en/news-5-6-45.html) olvashat bővebben.

## <a name="mysql-version-57"></a>MySQL 5.7-es verzió

Hibajavítás kiadása: 5.7.27

Ebben a verzióban a fejlesztésekről és javításokról a MySQL [kiadási megjegyzésekben](https://dev.mysql.com/doc/relnotes/mysql/5.7/en/news-5-7-27.html) olvashat bővebben.

## <a name="mysql-version-80"></a>MySQL 8.0-s verzió

Hibajavítás kiadása: 8.0.15

Ebben a verzióban a fejlesztésekről és javításokról a MySQL [kiadási megjegyzésekben](https://dev.mysql.com/doc/relnotes/mysql/8.0/en/news-8-0-15.html) olvashat bővebben.

## <a name="managing-updates-and-upgrades"></a>Frissítések és frissítések kezelése
A szolgáltatás automatikusan kezeli a hibajavítás verziófrissítéseit. Például 5.7.20 -hoz 5.7.21.  

Az al- vagy a főverzió frissítései jelenleg nem támogatottak. Például a MySQL 5.6-os verzióról a MySQL 5.7-es verzióra történő frissítés nem támogatott. Ha az 5.6-os verzióról az 5.7-es verzióra szeretne frissíteni, készítsen [biztonsági másolatot, és végezzen visszaállítást](./concepts-migrate-dump-restore.md) az új motorverzióval létrehozott kiszolgálón.

## <a name="next-steps"></a>További lépések

A **szolgáltatási szintalapján**meghatározott erőforráskvótákról és korlátozásokról a Szolgáltatási szintek című [témakörben](./concepts-pricing-tiers.md) talál további információt.
