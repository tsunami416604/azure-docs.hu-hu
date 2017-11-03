---
title: "Azure adatbázisban a következő PostgreSQL Server fogalmak |} Microsoft Docs"
description: "Ez a témakör szempontjait és útmutatást biztosít PostgreSQL-kiszolgálók Azure-adatbázis használata."
services: postgresql
author: SaloniSonpal
ms.author: salonis
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 07/06/2017
ms.openlocfilehash: 09b8634160c35f3c6a48812358ec872e52d8b21c
ms.sourcegitcommit: 9c3150e91cc3075141dc2955a01f47040d76048a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/26/2017
---
# <a name="azure-database-for-postgresql-servers"></a>Azure-adatbázis PostgreSQL-kiszolgálók
Ez a témakör szempontjait és útmutatást biztosít PostgreSQL-kiszolgálók Azure-adatbázis használata.

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
- Több verzióban érhető el. További információkért lásd: [PostgreSQL támogatott adatbázis-verziók](concepts-supported-versions.md).
- Az extensible felhasználók. További információkért lásd: [PostgreSQL-bővítmények](concepts-extensions.md).

Azure adatbázisban PostgreSQL-kiszolgáló egy vagy több adatbázist is létrehozhat. Dönthet úgy, hogy az erőforrások teljes kihasználása érdekében kiszolgálónként egy adatbázist hoz létre, vagy úgy, hogy több adatbázis létrehozásával megosztja az erőforrásokat. Az árképzés strukturált kiszolgálónként, tarifacsomag, számítási egység, és tárhely (GB), a konfiguráció alapján. További részletekért lásd: [Tarifacsomagok](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Hogyan csatlakozzon és hitelesíti magát egy Azure-adatbázisban PostgreSQL-kiszolgáló?
A következő elemek biztosíthatja, hogy az adatbázishoz való hozzáférést.

|||
| :-- | :-- |
| **Hitelesítés és engedélyezés** | Azure-adatbázis PostgreSQL-kiszolgáló natív PostgreSQL-hitelesítését támogatja. Csatlakozhat, és a kiszolgáló-rendszergazdai bejelentkezés a kiszolgálón elvégzett hitelesítéshez. |
| **Protocol (Protokoll)** | A szolgáltatás egy PostgreSQL által használt üzenet-alapú protokoll használatát támogatja. |
| **TCP/IP** | A protokoll támogatott TCP/IP felett, és a Unix-tartomány szoftvercsatornákon keresztül. |
| **Tűzfal** | Az adatok védelme érdekében egy tűzfalszabály megakadályozza, hogy minden hozzáférés meg az adatbázis-kiszolgáló és az adatbázisok csak akkor adja meg, mely számítógépek rendelkeznek engedéllyel. Lásd: [PostgreSQL-kiszolgáló tűzfalszabályainak az Azure-adatbázis](concepts-firewall-rules.md). |
|||

## <a name="how-do-i-manage-a-server"></a>Hogyan kezelheti a kiszolgáló?
Kezelheti az Azure Database PostgreSQL-kiszolgálók az Azure portál használatával vagy a [Azure CLI](/cli/azure/postgres).

## <a name="next-steps"></a>Következő lépések
- A szolgáltatás áttekintését lásd: [Azure adatbázis PostgreSQL áttekintés](overview.md).
- Adott erőforrásokra vonatkozó információkból tájékoódhat kvótái és korlátai alapján a **szolgáltatásréteg**, lásd: [szolgáltatásszintek](concepts-service-tiers.md).
- A szolgáltatáshoz való csatlakozáskor információkért lásd: [PostgreSQL az Azure-adatbázis adatkapcsolattárak](concepts-connection-libraries.md).
