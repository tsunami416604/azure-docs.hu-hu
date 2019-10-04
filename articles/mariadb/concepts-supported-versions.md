---
title: A Azure Database for MariaDB támogatott verziói
description: A Azure Database for MariaDB támogatott verzióit ismerteti.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/12/2019
ms.openlocfilehash: e5d1dbc8c212d4cdefb12fb740a454324d3adfa1
ms.sourcegitcommit: dd69b3cda2d722b7aecce5b9bd3eb9b7fbf9dc0a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/12/2019
ms.locfileid: "70962922"
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

Jelenleg a másodlagos és a főverzió frissítése nem támogatott. Például a MariaDB 10,2-ről a MariaDB 10,3-re való frissítés nem támogatott. Ha 10,2-ről 10,3-re szeretne frissíteni, hozzon létre egy [memóriaképet, és állítsa vissza](./howto-migrate-dump-restore.md) egy olyan kiszolgálóra, amely az új motor verziójával lett létrehozva.

## <a name="next-steps"></a>További lépések

- A **szolgáltatási szintek**alapján meghatározott erőforrás-kvótákkal és korlátozásokkal kapcsolatos információkért lásd: [szolgáltatási szintek](./concepts-pricing-tiers.md).
