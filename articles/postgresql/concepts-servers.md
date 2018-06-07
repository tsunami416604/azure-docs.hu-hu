---
title: Kiszolgáló fogalmak PostgreSQL Azure-adatbázisban
description: Ez a cikk a szempontok és konfigurálása és kezelése az Azure-adatbázis PostgreSQL-kiszolgálók iránymutatásokat tartalmaz.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 05/22/2018
ms.openlocfilehash: f877f6df51cd7aed29260331d27d5c96f0584afc
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34640013"
---
# <a name="azure-database-for-postgresql-servers"></a>Azure Database for PostgreSQL-kiszolgálók
Ez a cikk szempontjait és útmutatást biztosít PostgreSQL-kiszolgálók Azure-adatbázis használata.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Mi az az Azure-adatbázis PostgreSQL-kiszolgáló?
Egy Azure PostgreSQL-kiszolgáló adatbázisa több adatbázis egy központi felügyeleti pontot. Az azonos PostgreSQL server szerkezet, amely akkor lehetséges, hogy ismernie kell a helyszíni világ. A PostgreSQL szolgáltatás kifejezetten, felügyelt, teljesítmény garanciákat nyújt, hozzáférési és szolgáltatásokat a kiszolgáló szintjén teszi elérhetővé.

Egy PostgreSQL-kiszolgálóhoz tartozó Azure-adatbázis:

- Azure-előfizetés jön létre.
- A szülő erőforrás adatbázisok van.
- Az adatbázisok névteret biztosít.
- A tároló erős élettartama szemantikájú - kiszolgáló törlése, és törli a tartalmazott adatbázisok.
- Collocates erőforrások régióban.
- A csatlakozási végpont biztosít a kiszolgáló és az adatbázis-hozzáférési (. postgresql.database.azure.com).
- Felügyeleti házirendek vonatkoznak annak adatbázisok hatóköre biztosít: bejelentkezés, tűzfal, felhasználók, szerepkörök, konfiguráció stb.
- Több verzióban érhető el. További információkért lásd: [támogatott PostgreSQL-adatbázis verziója](concepts-supported-versions.md).
- Az extensible felhasználók. További információkért lásd: [PostgreSQL-bővítmények](concepts-extensions.md).

Azure adatbázisban PostgreSQL-kiszolgáló egy vagy több adatbázist is létrehozhat. Dönthet úgy, hogy az erőforrások teljes kihasználása érdekében kiszolgálónként egy adatbázist hoz létre, vagy úgy, hogy több adatbázis létrehozásával megosztja az erőforrásokat. Az árképzés strukturált kiszolgálónként, tarifacsomag, vCores, és tárhely (GB), a konfiguráció alapján. További információkért lásd: [Tarifacsomagok](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Hogyan csatlakozzon és hitelesíti magát egy Azure-adatbázisban PostgreSQL-kiszolgáló?
A következő elemek biztosítható, hogy az adatbázishoz való biztonságos hozzáférés:

|||
|:--|:--|
| **Hitelesítés és engedélyezés** | Azure-adatbázis PostgreSQL-kiszolgáló natív PostgreSQL-hitelesítését támogatja. Csatlakozhat, és a kiszolgáló-rendszergazdai bejelentkezés a kiszolgálón elvégzett hitelesítéshez. |
| **Protocol (Protokoll)** | A szolgáltatás egy PostgreSQL által használt üzenet-alapú protokoll használatát támogatja. |
| **TCP/IP** | A protokoll támogatott TCP/IP felett, és a Unix-tartomány szoftvercsatornákon keresztül. |
| **Tűzfal** | Az adatok védelme érdekében egy tűzfalszabály megakadályozza, hogy minden hozzáférés a kiszolgálóhoz és az adatbázisok csak akkor adja meg, mely számítógépek rendelkeznek engedéllyel. Lásd: [PostgreSQL-kiszolgáló tűzfalszabályainak az Azure-adatbázis](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>A kiszolgáló kezelése
Kezelheti az Azure Database PostgreSQL-kiszolgálók használatával a [Azure-portálon](https://portal.azure.com) vagy a [Azure CLI](/cli/azure/postgres).

Egy kiszolgáló létrehozásakor állíthatja be a hitelesítő adatokat a rendszergazda felhasználó számára. A rendszergazdai jogú felhasználó az a legnagyobb jogosultsággal felhasználó, a kiszolgálón. A szerepkör azure_pg_admin tartozik. Ez a szerepkör nem rendelkezik teljes felügyelő engedélyekkel. 

A PostgreSQL felügyelő attribútum a azure_superuser, a felügyelt szolgáltatás, amely hozzá van rendelve. Nincs hozzáférése ehhez a szerepkörhöz.

Egy PostgreSQL-kiszolgálóhoz tartozó Azure-adatbázis két alapértelmezett adatbázis rendelkezik: 
- **postgres** -csatlakozhat egyszer a kiszolgáló alapértelmezett adatbázis jön létre.
- **azure_maintenance** -ezt az adatbázist választja el egymástól a a folyamatok, amelyek a felügyelt szolgáltatást, a felhasználói műveleteket. Nincs hozzáférése ehhez az adatbázishoz.


## <a name="server-parameters"></a>Kiszolgálói paraméterek
A PostgreSQL server paraméterek határozzák meg a kiszolgáló konfigurációját. PostgreSQL Azure-adatbázisban paraméterek listáját lehet megtekinteni és szerkeszteni az Azure-portálon vagy az Azure parancssori felület használatával. 

A Postgres felügyelt szolgáltatásként konfigurálható PostgreSQL az Azure-adatbázis paraméterei a paramétereket a Postgres helyi példányát egy részét (Postgres paraméterek további információkért lásd: a [PostgreSQL-dokumentáció](https://www.postgresql.org/docs/9.6/static/runtime-config.html)). Az Azure-adatbázis PostgreSQL-kiszolgáló létrehozása az egyes paramétereket alapértelmezett értékekkel engedélyezve van. Egyes paraméterek, amelyek a kiszolgáló igényelnének indítsa újra, vagy a módosítások életbe léptetéséhez felügyelő hozzáférés nem lehet konfigurálni a felhasználó által.


## <a name="next-steps"></a>További lépések
- A szolgáltatás áttekintését lásd: [Azure adatbázis PostgreSQL áttekintés](overview.md).
- Adott erőforrásokra vonatkozó információkból tájékoódhat kvótái és korlátai alapján a **szolgáltatásréteg**, lásd: [szolgáltatásszintek](concepts-pricing-tiers.md).
- A szolgáltatáshoz való csatlakozáskor információkért lásd: [PostgreSQL az Azure-adatbázis adatkapcsolattárak](concepts-connection-libraries.md).
- Megtekintése és szerkesztése a kiszolgáló paraméterek keresztül [Azure-portálon](howto-configure-server-parameters-using-portal.md) vagy [Azure CLI](howto-configure-server-parameters-using-cli.md).
