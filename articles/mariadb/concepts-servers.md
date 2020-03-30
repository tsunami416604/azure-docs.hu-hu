---
title: Kiszolgálók – Azure-adatbázis a MariaDB-hez
description: Ez a témakör az Azure Database for MariaDB-kiszolgálók használatával kapcsolatos szempontokat és irányelveket ismerteti.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 444d7f1574cf1517b01250bcb9d810731030182d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79527792"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Kiszolgálói fogalmak a MariaDB Azure Database szolgáltatásában
Ez a cikk az Azure Database for MariaDB-kiszolgálók használatával kapcsolatos szempontokat és irányelveket ismerteti.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Mi az Azure-adatbázis a MariaDB-kiszolgálóhoz?

A MariaDB-kiszolgálóazure-adatbázisa több adatbázis központi felügyeleti pontja. Ez ugyanaz a MariaDB kiszolgálókonstrukció, amelyet a helyszíni világban ismerhet. Pontosabban az Azure Database for MariaDB szolgáltatás felügyelt, teljesítménygaranciákat biztosít, és kiszolgálói szinten elérhetővé teszi a hozzáférést és a funkciókat.

Azure-adatbázis a MariaDB-kiszolgálóhoz:

- Egy Azure-előfizetésen belül jön létre.
- Az adatbázisok szülőerőforrása.
- Névteret biztosít az adatbázisokszámára.
- Van egy konténer erős élettartamú szemantika - törölje a kiszolgálót, és törli a tartalmazott adatbázisokat.
- Erőforrások at helyez el egy régióban.
- Csatlakozási végpontot biztosít a kiszolgáló és az adatbázis-hozzáférés hez.
- Az adatbázisokra vonatkozó felügyeleti házirendek hatókörét tartalmazza: bejelentkezés, tűzfal, felhasználók, szerepkörök, konfigurációk stb.
- Elérhető MariaDB motor 10.2-es verzióban. További információ: [Supported Azure Database for MariaDB database versions](./concepts-supported-versions.md).

Az Azure Database for MariaDB-kiszolgálón egy vagy több adatbázist is létrehozhat. Dönthet úgy, hogy kiszolgálónként egyetlen adatbázist hoz létre az összes erőforrás használatához, vagy több adatbázist hoz létre az erőforrások megosztásához. A díjszabás kiszolgálónként i. strukturált, a tarifacsomag, a virtuális magok és a tárolás (GB) konfigurációja alapján. További információ: [Tarifacsomagok](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Hogyan biztonságossá tehetek egy Azure-adatbázist a MariaDB-kiszolgálóhoz?

A következő elemek biztosítják az adatbázishoz való biztonságos hozzáférést.

|||
| :--| :--|
| **Hitelesítés és engedélyezés** | Az Azure Database for MariaDB server támogatja a natív MySQL-hitelesítést. A kiszolgáló rendszergazdai bejelentkezésével csatlakozhat és hitelesítheti magát egy kiszolgálóhoz. |
| **Protocol (Protokoll)** | A szolgáltatás támogatja a MySQL által használt üzenetalapú protokollt. |
| **TCP/IP** | A protokoll tcp/IP és Unix-tartomány szoftvercsatornákon keresztül támogatott. |
| **Tűzfal** | Az adatok védelme érdekében a tűzfalszabály mindaddig megakadályozza az adatbázis-kiszolgálóhoz való hozzáférést, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. Lásd: [Azure Database for MariaDB Server tűzfalszabályok](./concepts-firewall-rules.md). |
| **SSL** | A szolgáltatás támogatja az SSL-kapcsolatok érvényesítését az alkalmazások és az adatbázis-kiszolgáló között. Az [SSL-kapcsolat konfigurálása az alkalmazásban a MariaDB Azure Database szolgáltatáshoz való biztonságos csatlakozáshoz](./howto-configure-ssl.md)című témakörben található. |

## <a name="how-do-i-manage-a-server"></a>Hogyan kezelhetek egy kiszolgálót?
A MariaDB-kiszolgálók Azure Database for MariaDB-kiszolgálók at az Azure Portal vagy az Azure CLI használatával kezelheti.

## <a name="next-steps"></a>További lépések
- A szolgáltatás áttekintését az [Azure Database for MariaDB áttekintése című témakörben találja.](./overview.md)
- A **szolgáltatási szintalapján**meghatározott erőforráskvótákról és korlátozásokról a Szolgáltatási szintek című [témakörben](./concepts-pricing-tiers.md) talál további információt.

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
