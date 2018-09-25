---
title: Támogatott verziók MariaDB-hez készült Azure Database-ben
description: Ismerteti a támogatott verziók az Azure Database for MariaDB.
author: ajlam
ms.author: andrela
editor: jasonwhowell
services: mariadb
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: aa83b9955839aaa03aa2ebf46c9e464dc75c8d3a
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46977502"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Támogatott, Azure Database for MariaDB-server-verziók
Azure Database for MariaDB fejlesztettek ki, a nyílt forráskódú [MariaDB kiszolgáló](https://downloads.mariadb.org/), az InnoDB motor használatával. Azure Database for MariaDB jelenleg a következő verziót támogatja:

## <a name="mariadb-version-10217"></a>A MariaDB verzió 10.2.17
Tekintse meg a [MariaDB dokumentáció](https://downloads.mariadb.org/mariadb/10.2.17/) további fejlesztések és javítások a MariaDB 10.2.17.

> [!NOTE]
> A szolgáltatásban az átjáró segítségével kiszolgálópéldányok irányítsa át a kapcsolatokat. A kapcsolat létrejötte után a MySQL-ügyfél az átjáróban, nem a tényleges verziója fut. a MariaDB-server-példányon állítsa MariaDB verzióját jeleníti meg. A MariaDB server-példány verziója meghatározásához használja a `SELECT VERSION();` parancsot a MySQL-parancssorban.

## <a name="managing-updates-and-upgrades"></a>Frissítések kezelése
A szolgáltatás automatikusan kezeli a javítása az alverzió frissítéseket.

## <a name="next-steps"></a>További lépések
További információ az adott erőforrás kvótái és korlátai alapján a **szolgáltatásszint**, lásd: [szolgáltatásszintek](./concepts-pricing-tiers.md)