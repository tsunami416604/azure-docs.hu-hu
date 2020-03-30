---
title: Kiszolgálói fogalmak – Azure Database for MySQL
description: Ez a témakör az Azure Database for MySQL-kiszolgálók használatával kapcsolatos szempontokat és irányelveket ismerteti.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 300470b2e8fb10fda7cfc59517cef00c07bf0632
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79537006"
---
# <a name="server-concepts-in-azure-database-for-mysql"></a>Kiszolgálói fogalmak az Azure Database for MySQL-ben

Ez a cikk az Azure Database for MySQL-kiszolgálók használatával kapcsolatos szempontokat és irányelveket ismerteti.

## <a name="what-is-an-azure-database-for-mysql-server"></a>Mi az Azure-adatbázis a MySQL-kiszolgálóhoz?

A MySQL-kiszolgálókazure-adatbázisa több adatbázis központi felügyeleti pontja. Ez ugyanaz a MySQL szerver konstrukció, amelyet a helyszíni világban ismerhet. Pontosabban az Azure Database for MySQL szolgáltatás felügyelt, teljesítménygaranciákat biztosít, és kiszolgálói szinten elérhetővé teszi a hozzáférést és a funkciókat.

Azure-adatbázis a MySQL-kiszolgálóhoz:

- Egy Azure-előfizetésen belül jön létre.
- Az adatbázisok szülőerőforrása.
- Névteret biztosít az adatbázisokszámára.
- Van egy konténer erős élettartamú szemantika - törölje a kiszolgálót, és törli a tartalmazott adatbázisokat.
- Erőforrások at helyez el egy régióban.
- Csatlakozási végpontot biztosít a kiszolgáló és az adatbázis-hozzáférés hez.
- Az adatbázisokra vonatkozó felügyeleti házirendek hatókörét tartalmazza: bejelentkezés, tűzfal, felhasználók, szerepkörök, konfigurációk stb.
- Több verzióban is elérhető. További információ: [Supported Azure Database for MySQL database versions](./concepts-supported-versions.md).

A MySQL-kiszolgálóhoz létrehozott Azure-adatbázisban egy vagy több adatbázist is létrehozhat. Dönthet úgy, hogy kiszolgálónként egyetlen adatbázist hoz létre az összes erőforrás használatához, vagy több adatbázist hoz létre az erőforrások megosztásához. A díjszabás kiszolgálónként i. strukturált, a tarifacsomag, a virtuális magok és a tárolás (GB) konfigurációja alapján. További információ: [Tarifacsomagok](./concepts-service-tiers.md).

## <a name="how-do-i-connect-and-authenticate-to-an-azure-database-for-mysql-server"></a>Hogyan csatlakozhatok és hitelesíthetek egy Azure Database for MySQL-kiszolgálóhoz?

A következő elemek biztosítják az adatbázishoz való biztonságos hozzáférést.

|     |     |
| :-- | :-- |
| **Hitelesítés és engedélyezés** | Az Azure Database for MySQL server támogatja a natív MySQL-hitelesítést. A kiszolgáló rendszergazdai bejelentkezésével csatlakozhat és hitelesítheti magát egy kiszolgálóhoz. |
| **Protocol (Protokoll)** | A szolgáltatás támogatja a MySQL által használt üzenetalapú protokollt. |
| **TCP/IP** | A protokoll tcp/IP és Unix-tartomány szoftvercsatornákon keresztül támogatott. |
| **Tűzfal** | Az adatok védelme érdekében a tűzfalszabály mindaddig megakadályozza az adatbázis-kiszolgálóhoz való hozzáférést, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. Lásd: [Azure Database for MySQL Server tűzfalszabályok](./concepts-firewall-rules.md). |
| **SSL** | A szolgáltatás támogatja az SSL-kapcsolatok érvényesítését az alkalmazások és az adatbázis-kiszolgáló között.  Lásd [SSL-összekapcsolhatóság konfigurálása az alkalmazásban a MySQL Azure-adatbázisához való biztonságos kapcsolódás érdekében](./howto-configure-ssl.md). |

## <a name="how-do-i-manage-a-server"></a>Hogyan kezelhetek egy kiszolgálót?

Az Azure Database for MySQL-kiszolgálók az Azure Portalon vagy az Azure CLI használatával kezelheti.

## <a name="next-steps"></a>További lépések

- A szolgáltatás áttekintéséhez lásd: [Azure Database for MySQL Overview](./overview.md)
- A **szolgáltatási szintalapján**meghatározott erőforráskvótákról és korlátozásokról a Szolgáltatási szintek című [témakörben](./concepts-service-tiers.md) talál további információt.
- A szolgáltatáshoz való csatlakozásról a [Connection libraries for Azure Database for MySQL](./concepts-connection-libraries.md)című témakörben talál további információt.
