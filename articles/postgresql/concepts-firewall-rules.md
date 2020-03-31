---
title: Tűzfalszabályok - Azure Database for PostgreSQL - Single Server
description: Ez a cikk azt ismerteti, hogyan csatlakozhat tűzfalszabályok at az Azure Database for PostgreSQL – Single Server használatával.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 01/15/2020
ms.openlocfilehash: 5d462be1caa3787cb7ff9a455be595ec5784eefe
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76157270"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---single-server"></a>Tűzfalszabályok az Azure Database for PostgreSQL-ben – Egykiszolgálós
Az Azure Database for PostgreSQL kiszolgálótűzfal mindaddig megakadályozza az adatbázis-kiszolgálóhoz való hozzáférést, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. A tűzfal hozzáférést biztosít a kiszolgálóhoz az egyes kérelmek eredeti IP-címe alapján.
A tűzfal konfigurálásakor olyan tűzfalszabályokat adhat meg, amelyek meghatározzák az elfogadható IP-címtartományokat. Tűzfalszabályokat a kiszolgáló szintjén hozhat létre.

**Tűzfalszabályok:** Ezek a szabályok lehetővé teszik az ügyfelek számára, hogy hozzáférjenek a teljes Azure-adatbázis postgreSQL-kiszolgálóhoz, azaz az összes adatbázis ugyanazon logikai kiszolgálón belül. A kiszolgálószintű tűzfalszabályok konfigurálhatók az Azure Portal használatával vagy az Azure CLI-parancsok használatával. Kiszolgálószintű tűzfalszabályok létrehozásához az előfizetés tulajdonosának vagy az előfizetés közreműködőjének kell lennie.

## <a name="firewall-overview"></a>Tűzfal áttekintése
A PostgreSQL-kiszolgálóhoz való Azure Database for Default blokkolva van az Azure Database for PostgreSQL-kiszolgáló összes adatbázis-hozzáférését. Ha egy másik számítógépről szeretné használni a kiszolgálót, meg kell adnia egy vagy több kiszolgálószintű tűzfalszabályt a kiszolgáló elérésének engedélyezéséhez. A tűzfalszabályok segítségével megadhatja, hogy az internetről mely IP-címtartományokat engedélyezze. Az Azure Portal-webhelyhez való hozzáférést a tűzfalszabályok nem érintik.
Az internetről és az Azure-ból érkező csatlakozási kísérleteknek először át kell haladniuk a tűzfalon, mielőtt elérnék a PostgreSQL-adatbázist, ahogy az az alábbi ábrán látható:

![Példa a tűzfal működésére](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Csatlakozás az internetről
A kiszolgálószintű tűzfalszabályok ugyanazon Azure Database for PostgreSQL-kiszolgáló összes adatbázisára vonatkoznak. Ha a kérés IP-címe a kiszolgálószintű tűzfalszabályokban megadott tartományok egyikében található, a tűzfal engedélyezi a csatlakozást.
Ha a kérelem IP-címe nem a kiszolgálószintű tűzfalszabályok bármelyikében megadott tartományon belül van, a csatlakozási kérelem sikertelen lesz.
Ha például az alkalmazás csatlakozik a PostgreSQL JDBC illesztőprogramjával, előfordulhat, hogy ez a hibaüzenet akkor próbál csatlakozni, amikor a tűzfal blokkolja a kapcsolatot.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "adminuser", database "postgresql", SSL

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból
Javasoljuk, hogy keresse meg bármely alkalmazás vagy szolgáltatás kimenő IP-címét, és kifejezetten engedélyezze a hozzáférést az egyes IP-címekhez vagy -tartományokhoz. Például megkeresheti egy Azure App Service kimenő IP-címét, vagy használhat egy virtuális géphez vagy más erőforráshoz kötött nyilvános IP-címet (lásd alább a virtuális gép privát IP-címhez való csatlakozással kapcsolatos információkat a szolgáltatás végpontok felett). 

Ha egy rögzített kimenő IP-cím nem érhető el az Azure-szolgáltatáshoz, fontolja meg az összes Azure-adatközpont IP-címből származó kapcsolatok engedélyezését. Ez a beállítás az Azure Portalon is engedélyezhető, ha a Kapcsolat biztonsági ablaktábláján az **Azure-szolgáltatások hoz való hozzáférés engedélyezése** beállítást **a** Kapcsolat biztonsági ablaktáblájáról a **Be** értékre **állítja,** és a Mentés gombra kerül. Az Azure CLI-ből a 0.0.0.0 kezdő és záró című tűzfalszabály-beállítás felel meg ennek. Ha a csatlakozási kísérlet nem engedélyezett, a kérelem nem éri el az Azure Database for PostgreSQL-kiszolgáló.

> [!IMPORTANT]
> Az **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítás úgy konfigurálja a tűzfalat, hogy az Azure-ból érkező összes kapcsolat engedélyezze, beleértve a más ügyfelek előfizetéseiből származó kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

![Hozzáférés engedélyezése az Azure-szolgáltatásokhoz a portálon konfigurálása](media/concepts-firewall-rules/allow-azure-services.png)

### <a name="connecting-from-a-vnet"></a>Csatlakozás virtuális hálózatról
Ha biztonságosan szeretne csatlakozni az Azure Database for PostgreSQL-kiszolgálóhoz egy virtuális hálózatról, fontolja meg [a Virtuálishálózat-szolgáltatás végpontjainak](./concepts-data-access-and-security-vnet.md)használatát. 

## <a name="programmatically-managing-firewall-rules"></a>Tűzfalszabályok szoftveres kezelése
Az Azure Portalon kívül a tűzfalszabályok az Azure CLI használatával programozott módon is kezelhetők.
Lásd [még: Azure Database for PostgreSQL tűzfalszabályok létrehozása és kezelése az Azure CLI használatával](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-firewall-issues"></a>Tűzfalproblémák elhárítása
Vegye figyelembe a következő pontokat, ha a Microsoft Azure Database for PostgreSQL Server szolgáltatáshoz való hozzáférés nem a várt módon működik:

* **Az engedélyezési lista módosításai még nem léptek hatályba:** Előfordulhat, hogy akár öt perces késéssel módosítja az Azure Database for PostgreSQL Server tűzfalkonfigurációjának érvénybe léptetését.

* **A bejelentkezés nem engedélyezett, vagy helytelen jelszót használtak:** Ha egy bejelentkezési jogosultsággal nem rendelkezik az Azure Database for PostgreSQL kiszolgálón, vagy a használt jelszó helytelen, a rendszer megtagadja az Azure Database for PostgreSQL-kiszolgálóhoz való csatlakozást. A tűzfalbeállítás létrehozása csak lehetőséget biztosít az ügyfeleknek a kiszolgálóhoz való csatlakozásra; minden ügyfélnek meg kell adnia a szükséges biztonsági hitelesítő adatokat.

   Egy JDBC-ügyfél használatával például a következő hiba jelenhet meg.
   > java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: jelszóhitelesítés nem sikerült a felhasználó "yourusername"

* **Dinamikus IP-cím**: Ha az internetkapcsolata dinamikus IP-címkezeléssel rendelkezik, és problémákat okoz a tűzfalon való átjutás, próbálja ki a következő megoldások valamelyikét:

   * Kérdezze meg az internetszolgáltatótól az Azure-adatbázist a PostgreSQL-kiszolgálóhoz hozzáférő ügyfélszámítógépekhez rendelt IP-címtartományt, majd adja hozzá az IP-címtartományt tűzfalszabályként.

   * Az ügyfélszámítógépek számára ehelyett statikus IP-címzést kaphat, majd adja hozzá a statikus IP-címet tűzfalszabályként.

* **A kiszolgáló IP-címe nyilvánosnak tűnik:** A PostgreSQL-kiszolgálóhoz készült Azure Database-kiszolgálóhoz való kapcsolatok egy nyilvánosan elérhető Azure-átjárón keresztül kerülnek átirányítva. A kiszolgáló tényleges IP-címét azonban tűzfal védi. További információt a [kapcsolódási architektúra című cikkben](concepts-connectivity-architecture.md)talál. 

## <a name="next-steps"></a>További lépések
A kiszolgálószintű és adatbázis-szintű tűzfalszabályok létrehozásáról szóló cikkek a következő témakörben találhatóak:
* [Azure Database for PostgreSQL tűzfalszabályok létrehozása és kezelése az Azure Portalon](howto-manage-firewall-using-portal.md)
* [Azure Database postgreSQL tűzfalszabályok létrehozása és kezelése az Azure CLI használatával](howto-manage-firewall-using-cli.md)
- [Virtuális hálózat szolgáltatásvégpontjai a PostgreSQL Azure Database szolgáltatásában](./concepts-data-access-and-security-vnet.md)
