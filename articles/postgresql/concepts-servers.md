---
title: Szerverek - Azure-adatbázis postgreSQL - Egykiszolgálós
description: Ez a cikk az Azure Database for PostgreSQL – Single Server konfigurálásával és kezelésével kapcsolatos szempontokat és irányelveket ismerteti.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: d86170a53b4bfbe712bbca12db4d6063214aba21
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74768163"
---
# <a name="azure-database-for-postgresql---single-server"></a>Azure Database for PostgreSQL – Önálló kiszolgáló
Ez a cikk az Azure Database for PostgreSQL – Single Server használatával kapcsolatos szempontokat és irányelveket ismerteti.

## <a name="what-is-an-azure-database-for-postgresql-server"></a>Mi az Azure Database for PostgreSQL-kiszolgáló?
Az Azure Database for PostgreSQL – Single Server telepítési lehetőség kiszolgálója több adatbázis központi felügyeleti pontja. Ez ugyanaz a PostgreSQL szerver konstrukció, amelyet a helyszíni világban ismerhet. Pontosabban a PostgreSQL szolgáltatás kezelése, teljesítménygaranciákat nyújt, hozzáférést és szolgáltatásokat tesz elérhetővé a kiszolgáló szintjén.

Egy Azure-adatbázis a PostgreSQL kiszolgálóhoz:

- Egy Azure-előfizetésen belül jön létre.
- Az adatbázisok szülőerőforrása.
- Névteret biztosít az adatbázisokszámára.
- Van egy konténer erős élettartamú szemantika - törölje a kiszolgálót, és törli a tartalmazott adatbázisokat.
- Erőforrások at helyez el egy régióban.
- Kapcsolatvégpontot biztosít a kiszolgáló- és adatbázis-hozzáféréshez 
- Az adatbázisokra vonatkozó felügyeleti házirendek hatókörét tartalmazza: bejelentkezés, tűzfal, felhasználók, szerepkörök, konfigurációk stb.
- Több verzióban is elérhető. További információ: [Támogatott PostgreSQL adatbázis-verziók](concepts-supported-versions.md).
- A felhasználók bővíthetők. További információ: [PostgreSQL extensions](concepts-extensions.md).

A PostgreSQL-kiszolgálók Azure-adatbázisán belül egy vagy több adatbázist hozhat létre. Dönthet úgy, hogy az erőforrások teljes kihasználása érdekében kiszolgálónként egy adatbázist hoz létre, vagy úgy, hogy több adatbázis létrehozásával megosztja az erőforrásokat. A díjszabás kiszolgálónként i. strukturált, a tarifacsomag, a virtuális magok és a tárolás (GB) konfigurációja alapján. További információ: [Tarifacsomagok](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-postgresql-server"></a>Hogyan csatlakozhatok és hitelesíthetek egy Azure Database for PostgreSQL-kiszolgálóhoz?
A következő elemek biztosítják az adatbázishoz való biztonságos hozzáférést:

|||
|:--|:--|
| **Hitelesítés és engedélyezés** | Az Azure Database for PostgreSQL-kiszolgáló támogatja a natív PostgreSQL-hitelesítést. A kiszolgálóhoz történő csatlakozás és a hitelesítés rendszergazdai bejelentkezéssel végezhető el. |
| **Protocol (Protokoll)** | A szolgáltatás támogatja a PostgreSQL által használt üzenetalapú protokollt. |
| **TCP/IP** | A protokoll tcp/IP protokollon keresztül és Unix-tartomány os szoftvercsatornákon keresztül támogatott. |
| **Tűzfal** | Az adatok védelme érdekében a tűzfalszabály mindaddig megakadályozza a kiszolgálóhoz és az adatbázisokhoz való hozzáférést, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. Lásd: [Azure Database for PostgreSQL Server tűzfalszabályok](concepts-firewall-rules.md). |

## <a name="managing-your-server"></a>A kiszolgáló kezelése
A PostgreSQL-kiszolgálókHoz készült Azure Database for PostgreSQL-kiszolgálók az [Azure portal vagy](https://portal.azure.com) az Azure [CLI](/cli/azure/postgres)használatával kezelhetők.

A kiszolgáló létrehozása közben be kell állítania a rendszergazdai felhasználó hitelesítő adatait. A rendszergazdai felhasználó a legmagasabb jogosultsággal rendelkező felhasználó a kiszolgálón. A azure_pg_admin szerephez tartozik. Ez a szerepkör nem rendelkezik teljes rendszergazdai engedélyekkel. 

A PostgreSQL rendszergazdai attribútum a felügyelt szolgáltatáshoz tartozó azure_superuser van hozzárendelve. Nem fér hozzá ehhez a szerepkörhöz.

A PostgreSQL-kiszolgálók azure-adatbázisa alapértelmezett adatbázisokkal rendelkezik: 
- **postgres** - Az alapértelmezett adatbázis csatlakozhat, ha a kiszolgáló létrehozása.
- **azure_maintenance** – Ez az adatbázis a felügyelt szolgáltatást biztosító folyamatok és a felhasználói műveletek elkülönítésére szolgál. Nincs hozzáférése az adatbázishoz.
- **azure_sys** – a lekérdezési tároló adatbázisa. Ez az adatbázis nem halmoz fel adatokat, ha a Lekérdezéstár ki van kapcsolva; ez az alapértelmezett beállítás. További információt a [Lekérdezéstároló áttekintése című témakörben talál.](concepts-query-store.md)


## <a name="server-parameters"></a>Kiszolgálóparaméterek
A PostgreSQL kiszolgáló paraméterei határozzák meg a kiszolgáló konfigurációját. Az Azure Database for PostgreSQL a paraméterek listáját megtekinthető és szerkeszthető az Azure Portalon vagy az Azure CLI használatával. 

A Postgres felügyelt szolgáltatásaként az Azure Database for PostgreSQL konfigurálható paraméterei a paraméterek egy részét képezik egy helyi Postgres-példányban (A Postgres paramétereiről további információt a [PostgreSQL dokumentációjában](https://www.postgresql.org/docs/9.6/static/runtime-config.html)talál). Az Azure Database for PostgreSQL-kiszolgáló alapértelmezésrel van engedélyezve az egyes paraméterek létrehozásakor. Egyes paramétereket, amelyek a módosítások érvénybe léptetéséhez a kiszolgáló újraindítását vagy rendszergazdai hozzáférését igénylik, a felhasználó nem konfigurálhatja.


## <a name="next-steps"></a>További lépések
- A szolgáltatás áttekintését az [Azure Database for PostgreSQL – áttekintés című témakörben találja.](overview.md)
- A **szolgáltatási szint**alapján meghatározott erőforráskvótákról és korlátozásokról a Szolgáltatási szintek című [témakörben](concepts-pricing-tiers.md)talál.
- A szolgáltatáshoz való csatlakozásról a [Kapcsolattárak az Azure Database for PostgreSQL kapcsolattárak](concepts-connection-libraries.md)című témakörben talál.
- A kiszolgáló paramétereinek megtekintése és szerkesztése az [Azure Portalon](howto-configure-server-parameters-using-portal.md) vagy az [Azure CLI-n](howto-configure-server-parameters-using-cli.md)keresztül.
