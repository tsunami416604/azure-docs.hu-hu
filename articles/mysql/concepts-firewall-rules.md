---
title: Tűzfalszabályok – Azure Database for MySQL
description: Ismerje meg, hogy használatával tűzfalszabályok at az Azure Database for MySQL-kiszolgáló engedélyezéséhez.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: a82d2317314c79a82fe80c5a25afc950fb728815
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76155196"
---
# <a name="azure-database-for-mysql-server-firewall-rules"></a>Azure Database for MySQL kiszolgáló tűzfalszabályai
A tűzfalak mindaddig megakadályozzák az adatbázis-kiszolgálóhoz való hozzáférést, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. A tűzfal hozzáférést biztosít a kiszolgálóhoz az egyes kérelmek eredeti IP-címe alapján.

A tűzfal konfigurálásához hozzon létre olyan tűzfalszabályokat, amelyek elfogadható IP-címtartományokat adnak meg. Tűzfalszabályokat a kiszolgáló szintjén hozhat létre.

**Tűzfalszabályok:** Ezek a szabályok lehetővé teszik az ügyfelek számára, hogy hozzáférjenek a teljes Azure-adatbázis a MySQL-kiszolgáló, azaz az összes adatbázis ugyanazon a logikai kiszolgálón belül. A kiszolgálószintű tűzfalszabályok konfigurálhatók az Azure Portal vagy az Azure CLI-parancsok használatával. Kiszolgálószintű tűzfalszabályok létrehozásához az előfizetés tulajdonosának vagy az előfizetés közreműködőjének kell lennie.

## <a name="firewall-overview"></a>Tűzfal áttekintése
A mySQL-kiszolgálóhoz való Azure Database-adatbázis hoz való összes adatbázis-hozzáférést alapértelmezés szerint blokkolja a tűzfal. Ha egy másik számítógépről szeretné használni a kiszolgálót, meg kell adnia egy vagy több kiszolgálószintű tűzfalszabályt a kiszolgáló elérésének engedélyezéséhez. A tűzfalszabályok segítségével megadhatja, hogy az internetről mely IP-címtartományokat engedélyezze. Az Azure Portal-webhelyhez való hozzáférést a tűzfalszabályok nem érintik.

Az internetről és az Azure-ból érkező csatlakozási kísérleteknek először át kell haladniuk a tűzfalon, mielőtt elérnék az Azure-adatbázist a MySQL-adatbázishoz, ahogy az az alábbi ábrán látható:

![Példa a tűzfal működésére](./media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Csatlakozás az internetről
A kiszolgálószintű tűzfalszabályok az Azure Database for MySQL-kiszolgáló összes adatbázisára vonatkoznak.

Ha a kérelem IP-címe a kiszolgálószintű tűzfalszabályokban megadott tartományok egyikén belül van, akkor a kapcsolat megadható.

Ha a kérelem IP-címe kívül esik az adatbázis- vagy kiszolgálószintű tűzfalszabályok bármelyikében megadott tartományon, akkor a csatlakozási kérelem sikertelen lesz.

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból
Javasoljuk, hogy keresse meg bármely alkalmazás vagy szolgáltatás kimenő IP-címét, és kifejezetten engedélyezze a hozzáférést az egyes IP-címekhez vagy -tartományokhoz. Például megkeresheti egy Azure App Service kimenő IP-címét, vagy használhat egy virtuális géphez vagy más erőforráshoz kötött nyilvános IP-címet (lásd alább a virtuális gép privát IP-címhez való csatlakozással kapcsolatos információkat a szolgáltatás végpontok felett). 

Ha egy rögzített kimenő IP-cím nem érhető el az Azure-szolgáltatáshoz, fontolja meg az összes Azure-adatközpont IP-címből származó kapcsolatok engedélyezését. Ez a beállítás az Azure Portalon is engedélyezhető, ha a Kapcsolat biztonsági ablaktábláján az **Azure-szolgáltatások hoz való hozzáférés engedélyezése** beállítást **a** Kapcsolat biztonsági ablaktáblájáról a **Be** értékre **állítja,** és a Mentés gombra kerül. Az Azure CLI-ből a 0.0.0.0 kezdő és záró című tűzfalszabály-beállítás felel meg ennek. Ha a csatlakozási kísérlet nem engedélyezett, a kérelem nem éri el az Azure Database for MySQL-kiszolgáló.

> [!IMPORTANT]
> Az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás úgy konfigurálja a tűzfalat, hogy az Azure-ból érkező összes kapcsolat engedélyezze, beleértve a más ügyfelek előfizetéseiből származó kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

![Hozzáférés engedélyezése az Azure-szolgáltatásokhoz a portálon konfigurálása](./media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Csatlakozás virtuális hálózatról
Ha biztonságosan szeretne csatlakozni az Azure Database for MySQL-kiszolgálóhoz egy virtuális hálózatról, fontolja meg [a Virtuálishálózat-szolgáltatás végpontjainak](./concepts-data-access-and-security-vnet.md)használatát. 

## <a name="programmatically-managing-firewall-rules"></a>Tűzfalszabályok szoftveres kezelése
Az Azure Portalon kívül a tűzfalszabályok az Azure CLI használatával programozott módon is kezelhetők. Lásd [még: Azure Database for MySQL tűzfalszabályok létrehozása és kezelése az Azure CLI használatával](./howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Tűzfalproblémák elhárítása
Vegye figyelembe a következő pontokat, ha a Microsoft Azure Database for MySQL kiszolgálószolgáltatáshoz való hozzáférés nem a várt módon működik:

* **Az engedélyezési lista módosításai még nem léptek hatályba:** Előfordulhat, hogy akár öt perces késéssel módosítja az Azure Database for MySQL Server tűzfalkonfiguráció érvénybe léptetéséhez.

* **A bejelentkezés nem engedélyezett, vagy helytelen jelszót használtak:** Ha egy bejelentkezés imént nem rendelkezik engedélyekkel az Azure Database for MySQL-kiszolgálón, vagy a használt jelszó helytelen, a rendszer megtagadja az Azure Database for MySQL-kiszolgálóhoz való csatlakozást. Egy tűzfalbeállítás létrehozása lehetővé teszi az ügyfelek számára a kiszolgálóhoz való csatlakozás megkísérlését, azonban minden egyes ügyfélnek meg kell adnia a szükséges biztonsági hitelesítő adatokat.

* **Dinamikus IP-cím:** Ha dinamikus IP-címzéssel rendelkező internetkapcsolattal rendelkezik, és nem tud átjutni a tűzfalon, próbálkozzon az alábbi megoldási javaslatokkal:

   * Kérdezze meg az internetszolgáltatótól az Azure Database for MySQL-kiszolgálóhoz hozzáférő ügyfélszámítógépekhez rendelt IP-címtartományt, majd adja hozzá az IP-címtartományt tűzfalszabályként.

   * Állítson be statikus IP-címeket az ügyfélszámítógépei számára, majd adja meg az IP-címeket tűzfalszabályokként.

* **A kiszolgáló IP-címe nyilvánosnak tűnik:** A MySQL-kiszolgálóhoz készült Azure Database-adatbázishoz való kapcsolatok egy nyilvánosan elérhető Azure-átjárón keresztül kerülnek átirányítva. A kiszolgáló tényleges IP-címét azonban tűzfal védi. További információt a [kapcsolódási architektúra című cikkben](concepts-connectivity-architecture.md)talál. 

## <a name="next-steps"></a>További lépések

* [Azure Database for MySQL tűzfalszabályok létrehozása és kezelése az Azure Portalhasználatával](./howto-manage-firewall-using-portal.md)
* [Azure Database for MySQL tűzfalszabályok létrehozása és kezelése az Azure CLI használatával](./howto-manage-firewall-using-cli.md)
- [Virtuális hálózat szolgáltatásvégpontjai a MySQL-hez készült Azure Database-ben](./concepts-data-access-and-security-vnet.md)
