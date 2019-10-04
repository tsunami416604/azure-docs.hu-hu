---
title: Azure Database for MariaDB kiszolgálói tűzfalszabályok
description: Leírja a Azure Database for MariaDB-kiszolgáló tűzfalszabályok szabályait.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 09/22/2019
ms.openlocfilehash: 94efc87cdea8e1d9b63361d4652b63909c6ce3e7
ms.sourcegitcommit: 8a717170b04df64bd1ddd521e899ac7749627350
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/23/2019
ms.locfileid: "71203598"
---
# <a name="azure-database-for-mariadb-server-firewall-rules"></a>Azure Database for MariaDB kiszolgálói tűzfalszabályok
A tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgáló elérését, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. A tűzfal az egyes kérések származó IP-címe alapján engedélyezi a hozzáférést a kiszolgálóhoz.

A tűzfal konfigurálásához hozzon létre olyan tűzfalszabályok, amelyek elfogadható IP-címek tartományait határozzák meg. A tűzfalszabályok a kiszolgáló szintjén hozhatók létre.

**Tűzfalszabályok:** Ezek a szabályok lehetővé teszik az ügyfelek számára, hogy elérjék a teljes Azure Database for MariaDB-kiszolgálót, azaz az azonos logikai kiszolgálón belüli összes adatbázist. A kiszolgálói szintű tűzfalszabályok a Azure Portal vagy az Azure CLI-parancsok használatával konfigurálhatók. A kiszolgálói szintű tűzfalszabályok létrehozásához az előfizetés tulajdonosának vagy az előfizetés közreműködőinek kell lennie.

## <a name="firewall-overview"></a>Tűzfal áttekintése
A tűzfal alapértelmezés szerint blokkolja a Azure Database for MariaDB-kiszolgálóhoz tartozó összes adatbázis-hozzáférést. A kiszolgáló másik számítógépről való használatának megkezdéséhez meg kell adnia egy vagy több kiszolgálóoldali tűzfalszabályok használatát a kiszolgálóhoz való hozzáférés engedélyezéséhez. A tűzfalszabályok használatával adhatja meg, hogy mely IP-címtartományok legyenek engedélyezve az internetről. A tűzfalszabályok nem érintik a Azure Portal webhelyhez való hozzáférést.

Az internetről és az Azure-ról érkező csatlakozási kísérleteknek előbb át kell haladnia a tűzfalon, mielőtt azok elérnék a Azure Database for MariaDB-adatbázist, ahogy az a következő ábrán is látható:

![Példa a tűzfal működésének folyamatára](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Csatlakozás az internetről
A kiszolgálói szintű tűzfalszabályok a Azure Database for MariaDB-kiszolgálón lévő összes adatbázisra érvényesek.

Ha a kérés IP-címe a kiszolgálói szintű tűzfalszabályok megadott tartományán belül van, akkor a rendszer megadja a kapcsolatokat.

Ha a kérelem IP-címe kívül esik az adatbázis-vagy a kiszolgálói szintű tűzfalszabályok valamelyikében megadott tartományon, akkor a kapcsolati kérelem sikertelen lesz.

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból
Ha engedélyezni szeretné, hogy az Azure-alkalmazások csatlakozni tudjanak a Azure Database for MariaDB-kiszolgálóhoz, engedélyezni kell az Azure-kapcsolatokat. Például egy Azure Web Apps-alkalmazás vagy egy Azure-beli virtuális gépen futó alkalmazás üzemeltetéséhez, vagy egy Azure Data Factory adatkezelési átjáróból való kapcsolódáshoz. Az erőforrásoknak nem kell ugyanabban a Virtual Networkban (VNet) vagy erőforráscsoporthoz kell lenniük ahhoz, hogy engedélyezze ezeket a kapcsolatokat. Amikor egy Azure-alkalmazás megkísérel csatlakozni az adatbázis-kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezve vannak-e. Az ilyen típusú kapcsolatok engedélyezéséhez több módszer is rendelkezésre áll. A 0.0.0.0 kezdő- és zárócímet tartalmazó tűzfalbeállítás jelzi, hogy ezek a kapcsolatok engedélyezettek. Azt is megteheti, hogy az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** lehetőségre kattint a portálon a **kapcsolat biztonsági** paneljén, és megnyomja a **Mentés gombot**. Ha a kapcsolódási kísérlet nem engedélyezett, a kérelem nem éri el a Azure Database for MariaDB-kiszolgálót.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

![Az Azure-szolgáltatásokhoz való hozzáférés engedélyezése a portálon](./media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Tűzfalszabályok szoftveres kezelése
A Azure Portalon kívül a tűzfalszabályok programozott módon is kezelhetők az Azure CLI használatával. 

<!--See also [Create and manage Azure Database for MariaDB firewall rules using Azure CLI](./howto-manage-firewall-using-cli.md)-->

## <a name="troubleshooting-firewall-issues"></a>Tűzfalakkal kapcsolatos problémák elhárítása
Vegye figyelembe a következő szempontokat, amikor a MariaDB-kiszolgáló szolgáltatáshoz való hozzáférés nem Microsoft Azure a várt módon viselkedik:

* **Az engedélyezési lista módosításai még nem léptek érvénybe:** Az Azure Database for MariaDB-kiszolgáló tűzfal-konfigurációján végzett módosítások érvénybe lépéséig akár öt perc is eltelhet.

* **A bejelentkezési azonosító nincs engedélyezve, vagy helytelen jelszót használt:** Ha a bejelentkezési azonosító nem rendelkezik engedéllyel a Azure Database for MariaDB-kiszolgálón, vagy helytelen a használt jelszó, a rendszer megtagadja a kapcsolódást a Azure Database for MariaDB-kiszolgálóhoz. Egy tűzfalbeállítás létrehozása lehetővé teszi az ügyfelek számára a kiszolgálóhoz való csatlakozás megkísérlését, azonban minden egyes ügyfélnek meg kell adnia a szükséges biztonsági hitelesítő adatokat.

* **Dinamikus IP-cím:** Ha a dinamikus IP-címzéssel rendelkező internetkapcsolattal rendelkezik, és a tűzfalon keresztül nem sikerül bejutnia, akkor a következő megoldások valamelyikét kipróbálhatja:

   * Kérje meg az internetszolgáltatót (ISP) a Azure Database for MariaDB-kiszolgálót elérő ügyfélszámítógépekhez hozzárendelt IP-címtartomány számára, majd adja hozzá az IP-címtartományt tűzfalszabályként.

   * Állítson be statikus IP-címeket az ügyfélszámítógépei számára, majd adja meg az IP-címeket tűzfalszabályokként.

* **Úgy tűnik, hogy a kiszolgáló IP-címe nyilvános:** A Azure Database for MariaDB-kiszolgálóval létesített kapcsolatok egy nyilvánosan elérhető Azure-átjárón keresztül irányíthatók. Az aktuális kiszolgálói IP-címet azonban a tűzfal védi. További információért látogasson el a [kapcsolati architektúra című cikkben](concepts-connectivity-architecture.md). 

## <a name="next-steps"></a>További lépések
- [Azure Database for MariaDB tűzfalszabályok létrehozása és kezelése a Azure Portal használatával](./howto-manage-firewall-portal.md)

<!--
- [Create and manage Azure Database for MariaDB firewall rules using Azure CLI](./howto-manage-firewall-using-cli.md) -->
