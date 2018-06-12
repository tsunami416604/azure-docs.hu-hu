---
title: Kiszolgáló kapcsolatos fogalmak, MySQL az Azure-adatbázis
description: Ez a témakör szempontjait és irányelveket MySQL-kiszolgálók Azure-adatbázishoz való munkához.
services: mysql
author: ajlam
ms.author: andrela
manager: kfile
editor: jasonwhowell
ms.service: mysql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 9d94f897546ea1e1190aab91e80eb9868224e5a7
ms.sourcegitcommit: 1b8665f1fff36a13af0cbc4c399c16f62e9884f3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35265459"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Kiszolgáló kapcsolatos fogalmak, MySQL az Azure-adatbázis
Ez a cikk szempontjait és útmutatást biztosít a MySQL-kiszolgálók Azure-adatbázis használata.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Mi az az Azure-adatbázis MySQL-kiszolgáló?

Egy Azure MySQL-kiszolgáló adatbázisa több adatbázis egy központi felügyeleti pontot. Az azonos MySQL server szerkezet, amely akkor lehetséges, hogy ismernie kell a helyszíni világ. Pontosabban az Azure-adatbázishoz a MySQL-szolgáltatás felügyelt, teljesítmény garanciákat nyújt, és elérhetővé teszi a hozzáférést és a szolgáltatások kiszolgálói szinten.

Egy MySQL-kiszolgálóhoz tartozó Azure-adatbázis:

- Azure-előfizetés jön létre.
- A szülő erőforrás adatbázisok van.
- Az adatbázisok névteret biztosít.
- A tároló erős élettartama szemantikájú - kiszolgáló törlése, és törli a tartalmazott adatbázisok.
- Collocates erőforrások régióban.
- A csatlakozási végpont server és adatbázis-hozzáférést biztosít.
- Felügyeleti házirendek vonatkoznak annak adatbázisok hatóköre biztosít: bejelentkezés, tűzfal, felhasználók, szerepkörök, konfiguráció stb.
- Több verzióban érhető el. További információkért lásd: [támogatott Azure-adatbázis MySQL-adatbázis verziók](./concepts-supported-versions.md).

A MySQL-kiszolgálóhoz létrehozott Azure-adatbázisban egy vagy több adatbázist is létrehozhat. Dönthet úgy is, egy kiszolgálón, az erőforrások használatára, vagy hozzon létre az erőforrások megosztása több adatbázis egyetlen adatbázist hozhat létre. Az árképzés strukturált kiszolgálónként, tarifacsomag, vCores, és tárhely (GB), a konfiguráció alapján. További információkért lásd: [Tarifacsomagok](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Hogyan csatlakozzon és hitelesíti magát egy Azure-adatbázisban, MySQL-kiszolgáló?

A következő elemek biztosíthatja, hogy az adatbázishoz való hozzáférést.
|||
| :-- | :-- |
| **Hitelesítés és engedélyezés** | MySQL-kiszolgálóhoz tartozó Azure-adatbázis natív MySQL-hitelesítését támogatja. Csatlakozhat, és a kiszolgáló-rendszergazdai bejelentkezés a kiszolgáló hitelesítéséhez. |
| **Protocol (Protokoll)** | A szolgáltatás támogatja a MySQL által használt üzenet-alapú protokoll. |
| **TCP/IP** | A protokoll támogatott TCP/IP felett, és a Unix-tartomány szoftvercsatornákon keresztül. |
| **Tűzfal** | Az adatok védelme érdekében egy tűzfalszabály megakadályozza a összes az adatbázis-kiszolgáló csak akkor adja meg, mely számítógépek rendelkeznek engedéllyel. Lásd: [Azure-adatbázis a MySQL-kiszolgáló tűzfalszabályainak](./concepts-firewall-rules.md). |
| **SSL** | A szolgáltatás támogatja végrehajtó SSL-kapcsolatok az alkalmazások és az adatbázis-kiszolgáló között.  Lásd [SSL-összekapcsolhatóság konfigurálása az alkalmazásban a MySQL Azure-adatbázisához való biztonságos kapcsolódás érdekében](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Hogyan kezelheti a kiszolgáló?
Kezelheti az Azure Database MySQL-kiszolgálók az Azure-portálon vagy az Azure parancssori felület használatával.

## <a name="next-steps"></a>További lépések
- A szolgáltatás áttekintését lásd: [Azure adatbázis MySQL – áttekintés](./overview.md)
- Adott erőforrásokra vonatkozó információkból tájékoódhat kvótái és korlátai alapján a **szolgáltatásréteg**, lásd: [Szolgáltatásszinteken](./concepts-service-tiers.md)
- További információ a szolgáltatáshoz való csatlakozáskor: [MySQL az Azure-adatbázis adatkapcsolattárak](./concepts-connection-libraries.md).
