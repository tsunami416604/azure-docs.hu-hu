---
title: Kiszolgálói fogalmak – Azure Database for MySQL
description: Ez a témakör a Azure Database for MySQL-kiszolgálók használatának szempontjait és irányelveit ismerteti.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 9a2e2eb022d96af1437ea4189d11f5fa69339325
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74769999"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>A kiszolgálói fogalmak a Azure Database for MySQL

Ez a cikk a Azure Database for MySQL-kiszolgálók használatának szempontjait és irányelveit ismerteti.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Mi az Azure Database for MySQL-kiszolgáló?

Az Azure Database for MySQL-kiszolgáló több adatbázis központi felügyeleti pontja. Ez ugyanaz a MySQL-kiszolgáló-konstrukció, amely a helyszíni világban is ismerős lehet. A Azure Database for MySQL szolgáltatás felügyelt, teljesítménybeli garanciákat biztosít, és a kiszolgáló szintjén elérhetővé teszi a hozzáférést és a szolgáltatásokat.

Egy Azure Database for MySQL-kiszolgáló:

- Egy Azure-előfizetésen belül jön létre.
- Az adatbázisokhoz tartozó szülő erőforrás.
- Névteret biztosít az adatbázisok számára.
- Egy erős élettartamú szemantikaú tároló, amely töröl egy kiszolgálót, és törli a bennük lévő adatbázisokat.
- Collocates-erőforrások egy régióban.
- Kapcsolati végpontot biztosít a kiszolgáló és az adatbázis eléréséhez.
- Az adatbázisokra vonatkozó felügyeleti házirendek hatókörét biztosítja: bejelentkezés, tűzfal, felhasználók, szerepkörök, konfigurációk stb.
- Több verzióban is elérhető. További információ: [támogatott Azure Database for MySQL adatbázis-verziók](./concepts-supported-versions.md).

A MySQL-kiszolgálóhoz létrehozott Azure-adatbázisban egy vagy több adatbázist is létrehozhat. Dönthet úgy is, hogy kiszolgálónként létrehoz egy adatbázist az összes erőforrás használatára, vagy több adatbázist hoz létre az erőforrások megosztásához. A díjszabás a kiszolgálón strukturálva, a díjszabási csomag, a virtuális mag és a tárterület (GB) konfigurációjától függően. További információ: [díjszabási szintek](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Hogyan a kapcsolódást és a hitelesítést egy Azure Database for MySQL-kiszolgálón?

A következő elemek segítenek biztosítani az adatbázis biztonságos elérését.

|     |     |
| :-- | :-- |
| **Hitelesítés és engedélyezés** | Azure Database for MySQL-kiszolgáló támogatja a natív MySQL-hitelesítést. A kiszolgáló rendszergazdai felhasználónevével csatlakozhat és hitelesítheti a kiszolgálókat. |
| **Protocol (Protokoll)** | A szolgáltatás támogatja a MySQL által használt Message-alapú protokollt. |
| **TCP/IP** | A protokoll TCP/IP-és UNIX-tartományi szoftvercsatornák esetén támogatott. |
| **Tűzfal** | Az adatai védelme érdekében egy tűzfalszabály megakadályozza az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. Tekintse meg [Azure Database for MySQL kiszolgáló tűzfalszabályok szabályait](./concepts-firewall-rules.md). |
| **SSL** | A szolgáltatás támogatja az alkalmazások és az adatbázis-kiszolgáló közötti SSL-kapcsolatok kényszerítését.  Lásd [SSL-összekapcsolhatóság konfigurálása az alkalmazásban a MySQL Azure-adatbázisához való biztonságos kapcsolódás érdekében](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Hogyan felügyel egy kiszolgálót?

Azure Database for MySQL-kiszolgálókat a Azure Portal vagy az Azure CLI használatával kezelheti.

## <a name="next-steps"></a>Következő lépések

- A szolgáltatás áttekintését lásd: [Azure Database for MySQL áttekintése](./overview.md)
- A **szolgáltatási szintek**alapján meghatározott erőforrás-kvótákkal és korlátozásokkal kapcsolatos információkért lásd: [szolgáltatási szintek](./concepts-service-tiers.md)
- További információ a szolgáltatáshoz való csatlakozásról: [Azure Database for MySQL kapcsolódási kódtárai](./concepts-connection-libraries.md).
