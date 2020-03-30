---
title: Támogatott verziók – Azure Database for MariaDB
description: Ismerje meg, hogy a MariaDB-kiszolgáló mely verzióit támogatja az Azure Database for MariaDB szolgáltatás.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 361ba17532d27a7020be1b6874993da999f48604
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527707"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Támogatott Azure-adatbázis a MariaDB kiszolgálóverzióihoz

A MariaDB Azure Database a nyílt forráskódú [MariaDB-kiszolgálóról](https://downloads.mariadb.org/)lett kifejlesztve az InnoDB motor használatával.

A MariaDB az X.Y.Z elnevezési sémát használja. X a főverzió, az Y az alverzió, a Z pedig a patch verzió.

> [!NOTE]
> A szolgáltatásban az átjárónak az a feladata, hogy átirányítsa a kapcsolatokat a kiszolgálópéldányokra. A kapcsolat létrejötte után a MySQL-ügyfél a MariaDB-készlet verzióját jeleníti meg az átjáróban, nem pedig a MariaDB kiszolgálópéldányon futó tényleges verziót. A MariaDB kiszolgálópéldány verziójának meghatározásához `SELECT VERSION();` használja a parancsot.

A MariaDB Azure Database jelenleg a következő verziót támogatja:

## <a name="mariadb-version-102"></a>MariaDB verzió 10.2

Patch verzió: 10.2.25

Ebben a verzióban a fejlesztésekről és javításokról a [MariaDB dokumentációjában](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/) olvashat bővebben.

## <a name="mariadb-version-103"></a>MariaDB verzió 10,3

Patch verzió: 10.3.16

Ebben a verzióban a fejlesztésekről és javításokról a [MariaDB dokumentációjában](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/) olvashat bővebben.

## <a name="managing-updates-and-upgrades"></a>Frissítések és frissítések kezelése
A szolgáltatás automatikusan kezeli a javításfrissítések frissítéseit. Például 10.2.21 -hoz 10.2.23.  

Az al- vagy a főverzió frissítései jelenleg nem támogatottak. Például a MariaDB 10.2-es verziójáról a MariaDB 10.3-as verziójára történő frissítés nem támogatott. Ha 10.2-ről 10.3-ra szeretne frissíteni, vegyen egy [memóriaképet, és állítsa vissza](./howto-migrate-dump-restore.md) az új motorverzióval létrehozott kiszolgálóra.

## <a name="next-steps"></a>További lépések

- A **szolgáltatási szint**alapján meghatározott erőforráskvótákról és korlátozásokról a Szolgáltatási szintek című [témakörben](./concepts-pricing-tiers.md)talál.
