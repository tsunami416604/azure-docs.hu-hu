---
title: Az Azure Database for PostgreSQL kiszolgáló fogalmak
description: Ez a cikk a szempontokat és irányelveket konfigurálása és kezelése az Azure Database for PostgreSQL-kiszolgálók tartalmaz.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 11/07/2018
ms.openlocfilehash: a5b6db9f4bfbe9a11119270e9236580b41fae8c7
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53540861"
---
# <a name="azure-database-for-postgresql-servers"></a>Azure Database for PostgreSQL-kiszolgálók
Ez a cikk szempontokat és irányelveket tartalmaz az Azure Database for PostgreSQL-kiszolgálók használatához.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Mi az Azure Database for PostgreSQL-kiszolgálót?
Egy Azure Database for PostgreSQL-kiszolgálót több adatbázis egy központi felügyeleti pont. Az azonos PostgreSQL kiszolgáló szerkezet, amely, előfordulhat, hogy ismernie kell a helyszíni világ. A PostgreSQL szolgáltatás kifejezetten, felügyelt, teljesítményre vonatkozó garanciákat biztosít, hozzáférés és a szolgáltatásokat a kiszolgáló szintjén teszi elérhetővé.

Azure Database for PostgreSQL-kiszolgáló:

- Egy Azure-előfizetésen belül jön létre.
- A szülő erőforrás-adatbázisokat.
- Névteret biztosít az adatbázisok.
- Egy tároló erős élettartam-szemantikai képességekkel – kiszolgáló törlése és a tartalmazott adatbázisok törli.
- Közösen helyezi el egy adott régió erőforrásait.
- A kiszolgáló és adatbázis-hozzáférés kapcsolódási végpontot biztosít 
- A hatókört az adatbázisokra vonatkozó felügyeleti szabályzatokat biztosít: bejelentkezés, tűzfal, felhasználók, szerepkörök, konfigurációk, stb.
- Több verzió érhető el. További információkért lásd: [PostgreSQL adatbázis-verziók támogatott](concepts-supported-versions.md).
- A felhasználó által bővíthető. További információkért lásd: [PostgreSQL-bővítmények](concepts-extensions.md).

Az Azure database for PostgreSQL-kiszolgálót egy vagy több adatbázist is létrehozhat. Dönthet úgy, hogy az erőforrások teljes kihasználása érdekében kiszolgálónként egy adatbázist hoz létre, vagy úgy, hogy több adatbázis létrehozásával megosztja az erőforrásokat. A díjszabás is a strukturált kiszolgálónkénti, árképzési szint, a virtuális magok és a tárterület (GB) konfigurációja alapján. További információkért lásd: [Tarifacsomagok](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Hogyan csatlakozzon és egy Azure Database for PostgreSQL-kiszolgáló hitelesítést?
A következő elemeket az adatbázishoz való biztonságos hozzáférés biztosítása érdekében:

|||
|:--|:--|
| **Hitelesítés és engedélyezés** | Azure Database for PostgreSQL-kiszolgáló támogatja a natív PostgreSQL hitelesítést. Képes csatlakozni és hitelesítése a kiszolgálóra a kiszolgálói rendszergazdai bejelentkezéssel. |
| **Protocol (Protokoll)** | A szolgáltatás egy üzenet-alapú protokoll, amelyet a PostgreSQL támogatja. |
| **TCP/IP** | A protokoll támogatott TCP/IP felett, és a Unix-tartomány szoftvercsatornákon keresztül. |
| **Tűzfal** | Az adatok védelme érdekében egy tűzfalszabály megakadályozza, hogy minden hozzáférés a kiszolgáló és a hozzá tartozó adatbázisok csak akkor adja meg, hogy mely számítógépek rendelkeznek ehhez engedéllyel. Lásd: [, Azure Database for PostgreSQL-kiszolgáló tűzfalszabályainak](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>A kiszolgáló kezelése
Azure Database for PostgreSQL-kiszolgálók használatával kezelheti a [az Azure portal](https://portal.azure.com) vagy a [Azure CLI-vel](/cli/azure/postgres).

A kiszolgáló létrehozásakor beállíthatja a hitelesítő adatokat a rendszergazda felhasználó számára. A rendszergazda nem rendelkezik a kiszolgálón a legmagasabb szintű jogosultságú felhasználó. A szerepkör azure_pg_admin tartozik. Ez a szerepkör nem rendelkezik teljes felügyelői engedélyekkel. 

A PostgreSQL superuser attribútum a azure_superuser, a felügyelt szolgáltatás, amely hozzá van rendelve. Nincs hozzáférése ehhez a szerepkörhöz.

Egy Azure Database for PostgreSQL-kiszolgáló alapértelmezett adatbázisok rendelkezik: 
- **postgres** – kapcsolódás után a kiszolgáló alapértelmezett adatbázis jön létre.
- **azure_maintenance** – ehhez az adatbázishoz a folyamatokat a felhasználói műveletek a felügyelt szolgáltatást nyújtó elválasztására szolgál. Nincs hozzáférése ehhez az adatbázishoz.
- **azure_sys** – a Query Store az adatbázis. Ez az adatbázis nem halmozódnak adatokat, amikor a Query Store ki van kapcsolva; Ez az alapértelmezett beállítása. További információkért lásd: a [Query Store áttekintése](concepts-query-store.md).


## <a name="server-parameters"></a>Kiszolgálói paraméterek
A PostgreSQL-kiszolgáló paramétereinek határozza meg, hogy a kiszolgáló konfigurációját. Az Azure Database for postgresql-hez, paraméterek listáját is lehet megtekinteni és szerkeszteni, az Azure portal vagy az Azure CLI használatával. 

A Postgres felügyelt szolgáltatásként az Azure Database for postgresql-hez konfigurálható paraméterek a paramétereket a helyi Postgres-példányt a részhalmazát képezik (Postgres paraméterek további információkért lásd: a [PostgreSQL dokumentációja](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). Az Azure Database for PostgreSQL-kiszolgáló engedélyezve van, minden paraméter létrehozása az alapértelmezett értékekkel. Egyes paraméterek, amelyek esetén a kiszolgáló újraindítása vagy superuser hozzáférés a módosítások érvénybe léptetéséhez nem lehet konfigurálni a felhasználó által.


## <a name="next-steps"></a>További lépések
- A szolgáltatás áttekintését lásd: [, Azure Database for PostgreSQL áttekintő](overview.md).
- További információ az adott erőforrás kvótái és korlátai alapján a **szolgáltatásszint**, lásd: [szolgáltatásszintek](concepts-pricing-tiers.md).
- A szolgáltatáshoz való csatlakozás kapcsolatos információkért lásd: [adatkapcsolattárak az Azure Database for PostgreSQL](concepts-connection-libraries.md).
- Megtekintheti és szerkesztheti a kiszolgáló paramétereinek keresztül [az Azure portal](howto-configure-server-parameters-using-portal.md) vagy [Azure CLI-vel](howto-configure-server-parameters-using-cli.md).
