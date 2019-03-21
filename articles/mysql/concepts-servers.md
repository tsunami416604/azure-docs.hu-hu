---
title: Az Azure Database for MySQL Server-fogalmak
description: Ez a témakör szempontokat és irányelveket tartalmaz az Azure Database for MySQL-kiszolgálók használatához.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 565e1bf7a4972e230b3cf56232ebd24519fcab5c
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "58007902"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Az Azure Database for MySQL Server-fogalmak

Ez a cikk szempontokat és irányelveket tartalmaz az Azure Database for MySQL-kiszolgálók használatához.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Mi az Azure Database for MySQL-kiszolgálót?

Egy Azure Database for MySQL-kiszolgálót több adatbázis egy központi felügyeleti pont. Az azonos MySQL server-szerkezet, amely, előfordulhat, hogy ismernie kell a helyszíni világ. Pontosabban az Azure Database for MySQL-szolgáltatás felügyelt, teljesítményre vonatkozó garanciákat biztosít, és elérhetővé teszi a hozzáférés és a kiszolgáló szintjén szolgáltatások.

Azure Database for MySQL-kiszolgáló:

- Egy Azure-előfizetésen belül jön létre.
- A szülő erőforrás-adatbázisokat.
- Névteret biztosít az adatbázisok.
- Egy tároló erős élettartam-szemantikai képességekkel – kiszolgáló törlése és a tartalmazott adatbázisok törli.
- Közösen helyezi el egy adott régió erőforrásait.
- A kiszolgáló és adatbázis-hozzáférés kapcsolódási végpontot biztosít.
- A hatókört az adatbázisokra vonatkozó felügyeleti szabályzatokat biztosít: bejelentkezés, tűzfal, felhasználók, szerepkörök, konfigurációk, stb.
- Több verzió érhető el. További információkért lásd: [támogatott, Azure Database for MySQL-adatbázis verziója](./concepts-supported-versions.md).

A MySQL-kiszolgálóhoz létrehozott Azure-adatbázisban egy vagy több adatbázist is létrehozhat. A naplózott összes erőforrását, vagy hozhat létre az erőforrások megosztását több adatbázis kiszolgálónként egy önálló adatbázis létrehozása. A díjszabás is a strukturált kiszolgálónkénti, árképzési szint, a virtuális magok és a tárterület (GB) konfigurációja alapján. További információkért lásd: [Tarifacsomagok](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Hogyan csatlakozzon és egy Azure Database for MySQL-kiszolgáló hitelesítést?

A következő elemek biztosíthatja, hogy az adatbázishoz való biztonságos hozzáférést.

|     |     |
| :-- | :-- |
| **Hitelesítés és engedélyezés** | Azure Database for MySQL-kiszolgáló támogatja a natív MySQL-hitelesítést. Képes csatlakozni és hitelesítése a kiszolgálóra a kiszolgálói rendszergazdai bejelentkezéssel. |
| **Protocol (Protokoll)** | A szolgáltatás egy üzenet-alapú protokoll, amelyet a MySQL támogatja. |
| **TCP/IP** | A protokoll támogatott TCP/IP felett, és a Unix-tartomány szoftvercsatornákon keresztül. |
| **Tűzfal** | Az adatok védelme érdekében egy tűzfalszabály megakadályozza a minden az adatbázis-kiszolgálóhoz, amíg, adja meg, hogy mely számítógépek rendelkeznek ehhez engedéllyel. Lásd: [, Azure Database for MySQL-kiszolgáló tűzfalszabályainak](./concepts-firewall-rules.md). |
| **SSL** | A szolgáltatás támogatja a korlátozást előírni az alkalmazások és az adatbázis-kiszolgáló közötti SSL-kapcsolatot.  Lásd [SSL-összekapcsolhatóság konfigurálása az alkalmazásban a MySQL Azure-adatbázisához való biztonságos kapcsolódás érdekében](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Hogyan kezelhetem a kiszolgáló?

Azure Database for MySQL-kiszolgálók az Azure Portalon vagy az Azure CLI használatával kezelheti.

## <a name="next-steps"></a>További lépések

- A szolgáltatás áttekintését lásd: [, Azure Database for MySQL áttekintése](./overview.md)
- További információ az adott erőforrás kvótái és korlátai alapján a **szolgáltatásszint**, lásd: [szolgáltatásszintek](./concepts-service-tiers.md)
- További információ a szolgáltatáshoz való csatlakozás: [adatkapcsolattárak az Azure Database for MySQL](./concepts-connection-libraries.md).
