---
title: Azure Database for PostgreSQL-kiszolgáló tűzfalszabályait
description: Ez a cikk ismerteti a tűzfalszabályok az Azure Database for PostgreSQL-kiszolgáló számára.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: conceptual
ms.date: 02/28/2018
ms.openlocfilehash: 793a68ee829d87a8433d5bdd77fe7b43e2f3af19
ms.sourcegitcommit: 71ee622bdba6e24db4d7ce92107b1ef1a4fa2600
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/17/2018
ms.locfileid: "53548837"
---
# <a name="azure-database-for-postgresql-server-firewall-rules"></a>Azure Database for PostgreSQL-kiszolgáló tűzfalszabályait
Azure Database for PostgreSQL-kiszolgáló tűzfal megakadályozza, hogy minden hozzáférés, az adatbázis-kiszolgáló csak akkor adja meg, hogy mely számítógépek rendelkeznek ehhez engedéllyel. A tűzfal hozzáférést biztosít a kiszolgáló minden kérés eredeti IP-címe alapján.
A tűzfal konfigurálásakor olyan tűzfalszabályokat adhat meg, amelyek meghatározzák az elfogadható IP-címtartományokat. A kiszolgálószintű tűzfalszabályok is létrehozhat.

**Tűzfal-szabályok:** Ezek a szabályok engedélyezése az ügyfelek számára eléréséhez a teljes Azure Database for PostgreSQL-kiszolgálóhoz, vagyis lévő összes adatbázis az egyazon logikai kiszolgálón. Kiszolgálószintű tűzfalszabályok az Azure portal használatával, vagy az Azure CLI-parancsok használatával konfigurálható. Kiszolgálószintű tűzfalszabályok létrehozásához, az előfizetés tulajdonosa vagy az előfizetés közreműködői kell lennie.

## <a name="firewall-overview"></a>Tűzfal áttekintése
Minden adatbázis-hozzáférést az Azure Database for PostgreSQL-kiszolgáló blokkolja a tűzfal alapértelmezés szerint. A kiszolgáló egy másik számítógépről használatának megkezdéséhez szüksége adjon meg egy vagy több kiszolgálószintű tűzfalszabályokat a kiszolgáló elérésének engedélyezéséhez. Használja a tűzfal-szabályokat, adja meg, hogy mely IP-címtartományok, hogy az internetről. Az Azure portál maga a hozzáférést a tűzfalszabályok nem befolyásolják.
Kapcsolódási kísérletek az internetről és az Azure először át kell jutniuk a tűzfalon, mielőtt elérnék a PostgreSQL-adatbázis, az alábbi ábrán látható módon:

![A példában a folyamat a tűzfal működéséről](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Csatlakozás az internetről
Kiszolgálószintű tűzfalszabályok az azonos Azure Database for PostgreSQL-kiszolgáló összes adatbázisára vonatkoznak. Ha a kérés IP-címe a kiszolgálószintű tűzfalszabályokban megadott tartományok egyikében található, a tűzfal engedélyezi a csatlakozást.
Ha a kérés IP-cím nem esik a kiszolgálószintű tűzfal szabály megadott tartományok, a csatlakozási kísérlet meghiúsul.
Ha az alkalmazás JDBC-illesztőprogram a PostgreSQL-hez csatlakozik, például előfordulhat, hogy ez a próbál csatlakozni, ha a tűzfal blokkolja a kapcsolatot hiba jelentkezik.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: VÉGZETES: nincs pg\_hba.conf gazdagép "123.45.67.890", felhasználó "adminuser", adatbázis "postgresql", az SSL-bejegyzést

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból
Ahhoz, hogy az Azure-alkalmazások az Azure Database for PostgreSQL-kiszolgálóhoz való kapcsolódáshoz, engedélyezni kell az Azure-kapcsolatokat. Ha például üzemeltetése az Azure Web Apps-alkalmazáshoz, vagy egy Azure-beli virtuális gépen futó alkalmazást, vagy egy Azure Data Factory az adatkezelési átjáró csatlakozni. Az erőforrások nem kell ugyanazon a virtuális hálózaton (VNet) vagy a tűzfalszabály erőforráscsoportjának ahhoz, hogy ezeket a kapcsolatokat lehet. Amikor egy Azure-alkalmazás megkísérel csatlakozni az adatbázis-kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezve vannak-e. Van néhány módszerek ilyen típusú kapcsolatok engedélyezéséhez. A 0.0.0.0 kezdő- és zárócímet tartalmazó tűzfalbeállítás jelzi, hogy ezek a kapcsolatok engedélyezettek. Beállíthatja azt is megteheti, a **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** beállítást **ON** a portálon a **kapcsolatbiztonság** ablaktáblán, majd nyomja le **mentése**. A csatlakozási kísérlet nem engedélyezett, ha a kérés nem éri el az Azure Database for PostgreSQL-kiszolgálóhoz.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

![Konfigurálja az Azure-szolgáltatásokhoz való hozzáférés engedélyezése a portálon](media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Tűzfalszabályok szoftveres kezelése
Az Azure Portalon kívül tűzfalszabályok programozott módon az Azure parancssori felülettel kezelhetők.
Lásd még: [hozzon létre és kezelhető az Azure Database for PostgreSQL-tűzfalszabályok Azure CLI használatával](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-server-firewall"></a>A kiszolgáló adatbázistűzfal hibaelhárítása
Ha a hozzáférés a Microsoft Azure database for PostgreSQL-kiszolgáló szolgáltatás nem a várt módon működik a, vegye figyelembe a következőket:

* **Az engedélyezési lista módosításai nem lépnek érvénybe még:** Előfordulhat, mint öt perces késleltetés módosításait az Azure Database for PostgreSQL-kiszolgáló tűzfal-konfiguráció érvénybe léptetéséhez.

* **A bejelentkezés nem engedélyezett, vagy helytelen jelszó lett megadva:** Ha egy bejelentkezés nem rendelkezik engedélyekkel az Azure Database for PostgreSQL-kiszolgálóhoz, vagy a jelszó helytelen, a rendszer megtagadja a kapcsolatot az Azure database for PostgreSQL-kiszolgáló. Egy tűzfalbeállítás létrehozása lehetővé teszi az ügyfelek való csatlakozás a kiszolgálóhoz; lehetőség minden egyes ügyfélnek továbbra is meg kell adnia a szükséges biztonsági hitelesítő adatokat.

Ha például JDBC ügyfelet használ, a következő hiba jelenhet meg.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: VÉGZETES: felhasználó "felhasználónév" jelszó-hitelesítés sikertelen

* **Dinamikus IP-cím:** Ha internetkapcsolat és a dinamikus IP-címkezelés és problémákat okoz a tűzfalon, próbálkozzon az alábbi megoldások valamelyikét:

* Az internetszolgáltató (ISP) kér az ügyfélszámítógépeken, amelyek eléréséhez az Azure Database for PostgreSQL-kiszolgálóhoz rendelt IP-címtartományt, és hozzáadhatja az IP-címtartományt egy tűzfalszabályként.

* Első statikus IP-címkezelés helyette az ügyfélszámítógépek számára, és adja hozzá a statikus IP-cím egy tűzfalszabályként.

## <a name="next-steps"></a>További lépések
A kiszolgálószintű és adatbázisszintű tűzfalszabályok létrehozása a cikkekben talál:
* [Hozzon létre és kezelheti az Azure-adatbázis PostgreSQL-tűzfalszabályok az Azure portál használatával](howto-manage-firewall-using-portal.md)
* [Hozzon létre és kezelheti az Azure-adatbázis PostgreSQL-tűzfalszabályok Azure parancssori felület használatával](howto-manage-firewall-using-cli.md)
