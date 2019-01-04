---
title: Támogatott verziók MariaDB-hez készült Azure Database-ben
description: Ismerteti a támogatott verziók az Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: 69330e9d5a05fbcc892889f70a04f5eb4a4a2fb9
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53541421"
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
