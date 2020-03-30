---
title: Tűzfalszabályok – Azure-adatbázis a MariaDB-hez
description: Ismerje meg, hogy használatával tűzfalszabályok at az Azure Database for MariaDB-kiszolgáló hoz való csatlakozás engedélyezéséhez.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: 743e3f50d747993250399493d97fc2becab19319
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79532042"
---
# <a name="azure-database-for-mariadb-server-firewall-rules"></a>Azure Database for MariaDB kiszolgáló tűzfalszabályai
A tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgálóhoz való hozzáférést, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. A tűzfal hozzáférést biztosít a kiszolgálóhoz az egyes kérelmek eredeti IP-címe alapján.

A tűzfal konfigurálásához hozzon létre olyan tűzfalszabályokat, amelyek elfogadható IP-címtartományokat adnak meg. Tűzfalszabályokat a kiszolgáló szintjén hozhat létre.

**Tűzfalszabályok:** Ezek a szabályok lehetővé teszik az ügyfelek számára, hogy hozzáférjenek a mariadb-kiszolgáló teljes Azure-adatbázisához, azaz az ugyanazon logikai kiszolgálón belüli összes adatbázishoz. A kiszolgálószintű tűzfalszabályok konfigurálhatók az Azure Portal vagy az Azure CLI-parancsok használatával. Kiszolgálószintű tűzfalszabályok létrehozásához az előfizetés tulajdonosának vagy az előfizetés közreműködőjének kell lennie.

## <a name="firewall-overview"></a>Tűzfal áttekintése
A MariaDB-kiszolgáló Azure-adatbázisához való összes adatbázis-hozzáférést alapértelmezés szerint blokkolja a tűzfal. Ha egy másik számítógépről szeretné használni a kiszolgálót, meg kell adnia egy vagy több kiszolgálószintű tűzfalszabályt a kiszolgáló elérésének engedélyezéséhez. A tűzfalszabályok segítségével megadhatja, hogy az internetről mely IP-címtartományokat engedélyezze. Az Azure Portal-webhelyhez való hozzáférést a tűzfalszabályok nem érintik.

Az internetről és az Azure-ból érkező csatlakozási kísérleteknek először át kell haladniuk a tűzfalon, mielőtt elérnék a MariaDB-adatbázis Azure-adatbázisát, ahogy az az alábbi ábrán látható:

![Példa a tűzfal működésére](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Csatlakozás az internetről
A kiszolgálószintű tűzfalszabályok az Azure Database for MariaDB-kiszolgáló összes adatbázisára vonatkoznak.

Ha a kérelem IP-címe a kiszolgálószintű tűzfalszabályokban megadott tartományok egyikén belül van, akkor a kapcsolat megadható.

Ha a kérelem IP-címe kívül esik az adatbázis- vagy kiszolgálószintű tűzfalszabályok bármelyikében megadott tartományon, akkor a csatlakozási kérelem sikertelen lesz.

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból
Javasoljuk, hogy keresse meg bármely alkalmazás vagy szolgáltatás kimenő IP-címét, és kifejezetten engedélyezze a hozzáférést az egyes IP-címekhez vagy -tartományokhoz. Például megkeresheti egy Azure App Service kimenő IP-címét, vagy használhat egy virtuális géphez vagy más erőforráshoz kötött nyilvános IP-címet (lásd alább a virtuális gép privát IP-címhez való csatlakozással kapcsolatos információkat a szolgáltatás végpontok felett). 

Ha egy rögzített kimenő IP-cím nem érhető el az Azure-szolgáltatáshoz, fontolja meg az összes Azure-adatközpont IP-címből származó kapcsolatok engedélyezését. Ez a beállítás az Azure Portalon is engedélyezhető, ha a Kapcsolat biztonsági ablaktábláján az **Azure-szolgáltatások hoz való hozzáférés engedélyezése** beállítást **a** Kapcsolat biztonsági ablaktáblájáról a **Be** értékre **állítja,** és a Mentés gombra kerül. Az Azure CLI-ből a 0.0.0.0 kezdő és záró című tűzfalszabály-beállítás felel meg ennek. Ha a csatlakozási kísérlet nem engedélyezett, a kérelem nem éri el az Azure Database for MariaDB-kiszolgálót.

> [!IMPORTANT]
> Az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás úgy konfigurálja a tűzfalat, hogy az Azure-ból érkező összes kapcsolat engedélyezze, beleértve a más ügyfelek előfizetéseiből származó kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

![Hozzáférés engedélyezése az Azure-szolgáltatásokhoz a portálon konfigurálása](./media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Csatlakozás virtuális hálózatról
Ha biztonságosan szeretne csatlakozni az Azure Database for MariaDB-kiszolgálóhoz egy virtuális hálózatról, fontolja meg [a Virtuálishálózat-szolgáltatás végpontjainak](./concepts-data-access-security-vnet.md)használatát. 

## <a name="programmatically-managing-firewall-rules"></a>Tűzfalszabályok szoftveres kezelése
Az Azure Portalon kívül a tűzfalszabályok az Azure CLI használatával programozott módon is kezelhetők. 

Lásd: [Azure Database for MariaDB tűzfalszabályok létrehozása és kezelése az Azure CLI használatával.](./howto-manage-firewall-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Tűzfalproblémák elhárítása
Vegye figyelembe a következő pontokat, ha a Microsoft Azure Database for MariaDB kiszolgálószolgáltatáshoz való hozzáférés nem a várt módon működik:

* **Az engedélyezési lista módosításai még nem léptek hatályba:** Előfordulhat, hogy a MariaDB Server tűzfalkonfigurációjának Azure Database módosításai akár öt perces késéssel lépnek életbe.

* **A bejelentkezés nem engedélyezett, vagy helytelen jelszót használtak:** Ha egy bejelentkezési jogosultsággal nem rendelkezik az Azure Database for MariaDB kiszolgálón, vagy a használt jelszó helytelen, a kapcsolat az Azure Database for MariaDB-kiszolgáló megtagadva. Egy tűzfalbeállítás létrehozása lehetővé teszi az ügyfelek számára a kiszolgálóhoz való csatlakozás megkísérlését, azonban minden egyes ügyfélnek meg kell adnia a szükséges biztonsági hitelesítő adatokat.

* **Dinamikus IP-cím:** Ha dinamikus IP-címzéssel rendelkező internetkapcsolattal rendelkezik, és nem tud átjutni a tűzfalon, próbálkozzon az alábbi megoldási javaslatokkal:

   * Kérdezze meg az internetszolgáltatótól az Azure-adatbázishoz a MariaDB-kiszolgálóhoz hozzáférő ügyfélszámítógépekhez rendelt IP-címtartományt, majd adja hozzá az IP-címtartományt tűzfalszabályként.

   * Állítson be statikus IP-címeket az ügyfélszámítógépei számára, majd adja meg az IP-címeket tűzfalszabályokként.

* **A kiszolgáló IP-címe nyilvánosnak tűnik:** A MariaDB-kiszolgálóazure-adatbázisával létesített kapcsolatok egy nyilvánosan elérhető Azure-átjárón keresztül kerülnek átirányítva. A kiszolgáló tényleges IP-címét azonban tűzfal védi. További információt a [kapcsolódási architektúra című cikkben](concepts-connectivity-architecture.md)talál. 

## <a name="next-steps"></a>További lépések
- [Azure Database létrehozása és kezelése a MariaDB tűzfalszabályaihoz az Azure Portal használatával](./howto-manage-firewall-portal.md)
- [Azure Database létrehozása és kezelése a MariaDB tűzfalszabályaihoz az Azure CLI használatával](./howto-manage-firewall-cli.md)
- [Virtuális hálózat szolgáltatásvégpontjai a MariaDB Azure Database-ben](./concepts-data-access-security-vnet.md)
