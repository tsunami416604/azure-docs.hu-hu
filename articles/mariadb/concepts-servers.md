---
title: Kiszolgálók – Azure Database for MariaDB
description: Ez a témakör a Azure Database for MariaDB-kiszolgálók használatának szempontjait és irányelveit ismerteti.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 444d7f1574cf1517b01250bcb9d810731030182d
ms.sourcegitcommit: 849bb1729b89d075eed579aa36395bf4d29f3bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/28/2020
ms.locfileid: "79527792"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>A kiszolgálói fogalmak a Azure Database for MariaDB
Ez a cikk a Azure Database for MariaDB-kiszolgálók használatának szempontjait és irányelveit ismerteti.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Mi az Azure Database for MariaDB-kiszolgáló?

Az Azure Database for MariaDB-kiszolgáló több adatbázis központi felügyeleti pontja. Ez ugyanaz a MariaDB-kiszolgáló-konstrukció, amely a helyszíni világban is ismerős lehet. A Azure Database for MariaDB szolgáltatás felügyelt, teljesítménybeli garanciákat biztosít, és a kiszolgáló szintjén elérhetővé teszi a hozzáférést és a szolgáltatásokat.

Egy Azure Database for MariaDB-kiszolgáló:

- Egy Azure-előfizetésen belül jön létre.
- Az adatbázisokhoz tartozó szülő erőforrás.
- Névteret biztosít az adatbázisok számára.
- Egy erős élettartamú szemantikaú tároló, amely töröl egy kiszolgálót, és törli a bennük lévő adatbázisokat.
- Collocates-erőforrások egy régióban.
- Kapcsolati végpontot biztosít a kiszolgáló és az adatbázis eléréséhez.
- Az adatbázisokra vonatkozó felügyeleti házirendek hatókörét biztosítja: bejelentkezés, tűzfal, felhasználók, szerepkörök, konfigurációk stb.
- A MariaDB Engine 10,2-es verziójában érhető el. További információ: [támogatott Azure Database for MariaDB adatbázis-verziók](./concepts-supported-versions.md).

Az Azure Database for MariaDB-kiszolgálón egy vagy több adatbázist is létrehozhat. Dönthet úgy is, hogy kiszolgálónként létrehoz egy adatbázist az összes erőforrás használatára, vagy több adatbázist hoz létre az erőforrások megosztásához. A díjszabás a kiszolgálón strukturálva, a díjszabási csomag, a virtuális mag és a tárterület (GB) konfigurációjától függően. További információ: [díjszabási szintek](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Hogyan biztonságos egy Azure Database for MariaDB-kiszolgálót?

A következő elemek segítenek biztosítani az adatbázis biztonságos elérését.

|||
| :--| :--|
| **Hitelesítés és engedélyezés** | Azure Database for MariaDB-kiszolgáló támogatja a natív MySQL-hitelesítést. A kiszolgáló rendszergazdai felhasználónevével csatlakozhat és hitelesítheti a kiszolgálókat. |
| **Protocol (Protokoll)** | A szolgáltatás támogatja a MySQL által használt Message-alapú protokollt. |
| **TCP/IP** | A protokoll TCP/IP-és UNIX-tartományi szoftvercsatornák esetén támogatott. |
| **Tűzfal** | Az adatai védelme érdekében egy tűzfalszabály megakadályozza az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. Tekintse meg [Azure Database for MariaDB kiszolgáló tűzfalszabályok szabályait](./concepts-firewall-rules.md). |
| **SSL** | A szolgáltatás támogatja az alkalmazások és az adatbázis-kiszolgáló közötti SSL-kapcsolatok kényszerítését. Lásd: az [SSL-kapcsolat konfigurálása az alkalmazásban a Azure Database for MariaDBhoz való biztonságos csatlakozáshoz](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Hogyan felügyel egy kiszolgálót?
Azure Database for MariaDB-kiszolgálókat a Azure Portal vagy az Azure CLI használatával kezelheti.

## <a name="next-steps"></a>További lépések
- A szolgáltatás áttekintését lásd: [Azure Database for MariaDB áttekintése](./overview.md)
- A **szolgáltatási szintek**alapján meghatározott erőforrás-kvótákkal és korlátozásokkal kapcsolatos információkért lásd: [szolgáltatási szintek](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
