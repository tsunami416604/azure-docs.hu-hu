---
title: Kiszolgálói fogalmak – Azure Database for MySQL
description: Ez a témakör a Azure Database for MySQL-kiszolgálók használatának szempontjait és irányelveit ismerteti.
author: savjani
ms.author: pariks
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 5477aa0c4eb2a66af816b287a8b6d7da8a53952f
ms.sourcegitcommit: 6ab718e1be2767db2605eeebe974ee9e2c07022b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2020
ms.locfileid: "94534891"
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

A MySQL-kiszolgálóhoz létrehozott Azure-adatbázisban egy vagy több adatbázist is létrehozhat. Dönthet úgy is, hogy kiszolgálónként létrehoz egy adatbázist az összes erőforrás használatára, vagy több adatbázist hoz létre az erőforrások megosztásához. A díjszabás a kiszolgálón strukturálva, a díjszabási csomag, a virtuális mag és a tárterület (GB) konfigurációjától függően. További információ: [díjszabási szintek](./concepts-pricing-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Hogyan a kapcsolódást és a hitelesítést egy Azure Database for MySQL-kiszolgálón?

A következő elemek segítenek biztosítani az adatbázis biztonságos elérését.

|     |     |
| :-- | :-- |
| **Hitelesítés és engedélyezés** | Azure Database for MySQL-kiszolgáló támogatja a natív MySQL-hitelesítést. A kiszolgáló rendszergazdai felhasználónevével csatlakozhat és hitelesítheti a kiszolgálókat. |
| **Protokoll** | A szolgáltatás támogatja a MySQL által használt Message-alapú protokollt. |
| **TCP/IP** | A protokoll TCP/IP-és UNIX-tartományi szoftvercsatornák esetén támogatott. |
| **Tűzfal** | Az adatai védelme érdekében egy tűzfalszabály megakadályozza az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. Tekintse meg [Azure Database for MySQL kiszolgáló tűzfalszabályok szabályait](./concepts-firewall-rules.md). |
| **SSL** | A szolgáltatás támogatja az alkalmazások és az adatbázis-kiszolgáló közötti SSL-kapcsolatok kényszerítését.  Lásd [SSL-összekapcsolhatóság konfigurálása az alkalmazásban a MySQL Azure-adatbázisához való biztonságos kapcsolódás érdekében](./howto-configure-ssl.md). |

## <a name="stopstart-an-azure-database-for-mysql-preview"></a>Azure Database for MySQL leállítása/elindítása (előzetes verzió)

Azure Database for MySQL lehetővé teszi a kiszolgáló **leállítását** , ha nincs használatban, és **elindítja** a kiszolgálót a tevékenység folytatásakor. Ez lényegében az adatbázis-kiszolgálók költségeinek megtakarítására szolgál, és csak használat közben kell fizetnie az erőforrásért. Ez még fontosabbá válik a fejlesztési és tesztelési feladatokhoz, és ha csak a nap egy részében használja a kiszolgálót. Ha leállítja a kiszolgálót, az összes aktív kapcsolat el lesz dobva. Később, amikor újra online állapotba szeretné állítani a kiszolgálót, használhatja a [Azure Portal](how-to-stop-start-server.md) vagy a [CLI](how-to-stop-start-server.md)-t.

Ha a kiszolgáló **leállított** állapotban van, a kiszolgáló számítási felszámítása nem történik meg. A tárterület azonban továbbra is számlázásra kerül, mivel a kiszolgáló tárterülete továbbra is biztosítani fogja, hogy az adatfájlok elérhetők legyenek a kiszolgáló újraindításakor.

> [!IMPORTANT]
> Ha **leállítja** a kiszolgálót, akkor a következő 7 nap során marad ebben az állapotban. Ha ez idő alatt nem **indítja el** manuálisan, a rendszer 7 nap elteltével automatikusan elindítja a kiszolgálót. Ha nem használja a kiszolgálót, úgy is eldöntheti, hogy újra **leállítja** .

Az időkiszolgáló leállításakor nem hajthatók végre felügyeleti műveletek a kiszolgálón. A kiszolgáló konfigurációs beállításainak módosításához [el kell indítania a kiszolgálót](how-to-stop-start-server.md).

### <a name="limitations-of-stopstart-operation"></a>A leállítási/indítási művelet korlátai
- Olvasási replika-konfigurációk (forrás-és replikák) esetén nem támogatott.

## <a name="how-do-i-manage-a-server"></a>Hogyan felügyel egy kiszolgálót?

Azure Database for MySQL-kiszolgálókat a Azure Portal vagy az Azure CLI használatával kezelheti.

## <a name="next-steps"></a>Következő lépések

- A szolgáltatás áttekintését lásd: [Azure Database for MySQL áttekintése](./overview.md)
- Az egyes **erőforrás-** kvótákkal és korlátozásokkal kapcsolatos információkért tekintse meg az [árképzési szintek](./concepts-pricing-tiers.md) című témakört.
- További információ a szolgáltatáshoz való csatlakozásról: [Azure Database for MySQL kapcsolódási kódtárai](./concepts-connection-libraries.md).
