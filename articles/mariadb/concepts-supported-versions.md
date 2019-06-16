---
title: Támogatott verziók MariaDB-hez készült Azure Database-ben
description: Ismerteti a támogatott verziók az Azure Database for MariaDB.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 06/11/2019
ms.openlocfilehash: 760cb39ea1f3980faba348c7aa1de68a66b20a8d
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67065718"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Támogatott, Azure Database for MariaDB-server-verziók

Azure Database for MariaDB fejlesztettek ki, a nyílt forráskódú [MariaDB kiszolgáló](https://downloads.mariadb.org/), az InnoDB motor használatával. Azure Database for MariaDB jelenleg a következő verziót támogatja:

## <a name="mariadb-version-102"></a>A MariaDB verzió 10.2

Tekintse meg a [MariaDB dokumentáció](https://mariadb.com/kb/en/library/mariadb-10223-release-notes/) további fejlesztések és javítások a MariaDB 10.2.23.

## <a name="mariadb-version-103"></a>A MariaDB verzió 10.3

Tekintse meg a [MariaDB dokumentáció](https://mariadb.com/kb/en/library/mariadb-10314-release-notes/) további fejlesztések és javítások a MariaDB 10.3.14.

> [!NOTE]
> A szolgáltatásban az átjáró segítségével kiszolgálópéldányok irányítsa át a kapcsolatokat. A kapcsolat létrejötte után a MySQL-ügyfél az átjáróban, nem a tényleges verziója fut. a MariaDB-server-példányon állítsa MariaDB verzióját jeleníti meg. A MariaDB server-példány verziója meghatározásához használja a `SELECT VERSION();` parancsot a MySQL-parancssorban.

## <a name="managing-updates-and-upgrades"></a>Frissítések kezelése

A szolgáltatás automatikusan kezeli a javítása az alverzió frissítéseket.

## <a name="next-steps"></a>További lépések

- További információ az adott erőforrás kvótái és korlátai alapján a **szolgáltatásszint**, lásd: [szolgáltatásszintek](./concepts-pricing-tiers.md).
