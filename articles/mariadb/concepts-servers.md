---
title: Az Azure Database for MariaDB kiszolgáló fogalmak
description: Ez a témakör szempontokat és irányelveket tartalmaz az Azure Database for MariaDB-kiszolgálók használata.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/24/2018
ms.openlocfilehash: c2497fef359d4a519c6d4927dfc0c38ee3d80d88
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53544566"
---
# <a name="server-concepts-in-azure-database-for-mariadb"></a>Az Azure Database for MariaDB kiszolgáló fogalmak
Ez a cikk szempontokat és irányelveket tartalmaz az Azure Database for MariaDB-kiszolgálók használata.

## <a name="what-is-an-azure-database-for-mariadb-server"></a>Mi az Azure Database for MariaDB-server?

Egy Azure Database for MariaDB-kiszolgáló több adatbázis egy központi felügyeleti pontot. Az azonos MariaDB kiszolgáló szerkezet, amely, előfordulhat, hogy ismernie kell a helyszíni világ. Pontosabban az Azure Database for MariaDB szolgáltatás felügyelt, teljesítményre vonatkozó garanciákat biztosít, és elérhetővé teszi a hozzáférés és a kiszolgáló szintjén szolgáltatások.

Egy Azure Database for MariaDB-kiszolgáló:

- Egy Azure-előfizetésen belül jön létre.
- A szülő erőforrás-adatbázisokat.
- Névteret biztosít az adatbázisok.
- Egy tároló erős élettartam-szemantikai képességekkel – kiszolgáló törlése és a tartalmazott adatbázisok törli.
- Közösen helyezi el egy adott régió erőforrásait.
- A kiszolgáló és adatbázis-hozzáférés kapcsolódási végpontot biztosít.
- A hatókört az adatbázisokra vonatkozó felügyeleti szabályzatokat biztosít: bejelentkezés, tűzfal, felhasználók, szerepkörök, konfigurációk, stb.
- MariaDB-motor verziója 10.2 érhető el. További információkért lásd: [támogatott, Azure Database for MariaDB adatbázis verziók](./concepts-supported-versions.md).

Az Azure Database for MariaDB-kiszolgálón egy vagy több adatbázist is létrehozhat. A naplózott összes erőforrását, vagy hozhat létre az erőforrások megosztását több adatbázis kiszolgálónként egy önálló adatbázis létrehozása. A díjszabás is a strukturált kiszolgálónkénti, árképzési szint, a virtuális magok és a tárterület (GB) konfigurációja alapján. További információkért lásd: [Tarifacsomagok](./concepts-pricing-tiers.md).

## <a name="how-do-i-secure-an-azure-database-for-mariadb-server"></a>Hogyan tehető biztonságossá egy Azure Database for MariaDB-kiszolgáló?

A következő elemek biztosíthatja, hogy az adatbázishoz való biztonságos hozzáférést.
|||
| :--| :--|
| **Hitelesítés és engedélyezés** | Azure Database for MariaDB-kiszolgáló támogatja a natív MySQL-hitelesítést. Képes csatlakozni és hitelesítése a kiszolgálóra a kiszolgálói rendszergazdai bejelentkezéssel. |
| **Protocol (Protokoll)** | A szolgáltatás egy üzenet-alapú protokoll, amelyet a MySQL támogatja. |
| **TCP/IP** | A protokoll támogatott TCP/IP felett, és a Unix-tartomány szoftvercsatornákon keresztül. |
| **Tűzfal** | Az adatok védelme érdekében egy tűzfalszabály megakadályozza a minden az adatbázis-kiszolgálóhoz, amíg, adja meg, hogy mely számítógépek rendelkeznek ehhez engedéllyel. Lásd: [, Azure Database for MariaDB-kiszolgáló tűzfalszabályainak](./concepts-firewall-rules.md). |
| **SSL** | A szolgáltatás támogatja a korlátozást előírni az alkalmazások és az adatbázis-kiszolgáló közötti SSL-kapcsolatot. Lásd: [SSL-összekapcsolhatóság konfigurálása az alkalmazásban való kapcsolódás az Azure Database for MariaDB](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Hogyan kezelhetem a kiszolgáló?
Azure Database for MariaDB-kiszolgálók az Azure Portalon vagy az Azure CLI használatával kezelheti.

## <a name="next-steps"></a>További lépések
- A szolgáltatás áttekintését lásd: [, Azure Database for MariaDB áttekintése](./overview.md)
- További információ az adott erőforrás kvótái és korlátai alapján a **szolgáltatásszint**, lásd: [szolgáltatásszintek](./concepts-pricing-tiers.md)

<!-- - For information about connecting to the service, see [Connection libraries for Azure Database for MariaDB](./concepts-connection-libraries.md). -->
