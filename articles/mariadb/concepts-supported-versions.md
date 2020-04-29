---
title: Támogatott verziók – Azure Database for MariaDB
description: Megtudhatja, hogy a MariaDB-kiszolgáló mely verziói támogatottak a Azure Database for MariaDB szolgáltatásban.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/18/2020
ms.openlocfilehash: 361ba17532d27a7020be1b6874993da999f48604
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79527707"
---
# <a name="supported-azure-database-for-mariadb-server-versions"></a>Támogatott Azure Database for MariaDB Server-verziók

A Azure Database for MariaDB a nyílt forráskódú [MariaDB-kiszolgálóról](https://downloads.mariadb.org/)lett kifejlesztve a InnoDB motor használatával.

A MariaDB az X. Y. Z elnevezési sémát használja. Az X a főverzió, az Y az alverzió, a Z pedig a javítás verziója.

> [!NOTE]
> A szolgáltatásban az átjárónak az a feladata, hogy átirányítsa a kapcsolatokat a kiszolgálópéldányokra. A kapcsolatok létrejötte után a MySQL-ügyfél megjeleníti az átjárón beállított MariaDB-verziót, nem pedig a MariaDB-kiszolgáló példányán futó tényleges verziót. A MariaDB Server-példány verziójának meghatározásához használja az `SELECT VERSION();` parancsot.

A Azure Database for MariaDB jelenleg a következő verziót támogatja:

## <a name="mariadb-version-102"></a>MariaDB 10,2-es verzió

Javítás verziója: 10.2.25

Tekintse meg a [MariaDB dokumentációját](https://mariadb.com/kb/en/library/mariadb-10225-release-notes/) , és ismerkedjen meg a jelen verzióban található javításokkal és javításokkal.

## <a name="mariadb-version-103"></a>MariaDB 10,3-es verzió

Javítás verziója: 10.3.16

Tekintse meg a [MariaDB dokumentációját](https://mariadb.com/kb/en/library/mariadb-10316-release-notes/) , és ismerkedjen meg a jelen verzióban található javításokkal és javításokkal.

## <a name="managing-updates-and-upgrades"></a>Frissítések és frissítések kezelése
A szolgáltatás automatikusan kezeli a javítások frissítéseit. Például 10.2.21 a 10.2.23.  

Az al- vagy a főverzió frissítései jelenleg nem támogatottak. Például a MariaDB 10.2-es verziójáról a MariaDB 10.3-as verziójára történő frissítés nem támogatott. Ha 10,2-ről 10,3-re szeretne frissíteni, hozzon létre egy [memóriaképet, és állítsa vissza](./howto-migrate-dump-restore.md) egy olyan kiszolgálóra, amely az új motor verziójával lett létrehozva.

## <a name="next-steps"></a>További lépések

- A **szolgáltatási szintek**alapján meghatározott erőforrás-kvótákkal és korlátozásokkal kapcsolatos információkért lásd: [szolgáltatási szintek](./concepts-pricing-tiers.md).
