---
title: Azure-adatbázis PostgreSQL-kiszolgáló tűzfalszabályainak
description: Ez a cikk ismerteti a tűzfalszabályokat az Azure-adatbázis PostgreSQL-kiszolgáló számára.
services: postgresql
author: rachel-msft
ms.author: raagyema
manager: kfile
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 02/28/2018
ms.openlocfilehash: 8a3f5d9fa8f1c36d8468c38f7dda803d3ca1d832
ms.sourcegitcommit: c765cbd9c379ed00f1e2394374efa8e1915321b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/28/2018
ms.locfileid: "29689887"
---
# <a name="azure-database-for-postgresql-server-firewall-rules"></a>Azure-adatbázis PostgreSQL-kiszolgáló tűzfalszabályainak
Azure-adatbázis PostgreSQL-kiszolgáló tűzfal megakadályozza a összes az adatbázis-kiszolgáló csak akkor adja meg, mely számítógépek rendelkeznek engedéllyel. A tűzfal engedélyezi a hozzáférést a kiszolgálóhoz, az egyes kérelmek az eredeti IP-címe alapján.
A tűzfal konfigurálásakor olyan tűzfalszabályokat adhat meg, amelyek meghatározzák az elfogadható IP-címtartományokat. Tűzfal-szabályokat hozhat létre a kiszolgálói szinten.

**Tűzfal-szabályok:** ezek a szabályok engedélyezi az ügyfelek számára a teljes Azure-adatbázis eléréséhez PostgreSQL-kiszolgáló esetén ez azt jelenti, hogy összes adatbázis belül az azonos logikai kiszolgáló. Kiszolgálószintű tűzfal-szabályokat konfigurálhatja az Azure-portál használatával vagy az Azure parancssori felület parancsait. Kiszolgálószintű tűzfal-szabályok létrehozása, az előfizetés tulajdonosa vagy egy előfizetés közreműködői kell lennie.

## <a name="firewall-overview"></a>Tűzfal áttekintése
Az adatbázis elérésére az Azure-adatbázis PostgreSQL-kiszolgáló alapértelmezés szerint a tűzfal blokkolja. A kiszolgáló egy másik számítógépről használatának megkezdéséhez meg kell adnia egy vagy több kiszolgálószintű engedélyezésére szolgáló tűzfalszabályok a kiszolgáló elérését. Használja a tűzfalszabályok adja meg, melyik IP-címtartományok engedélyezi az internetről. A tűzfalszabályok nincs hatással a hozzáférést az Azure portál magát.
Kapcsolódási kísérletek az internetről és az Azure először eltelik a tűzfalon keresztül is eléri a PostgreSQL-adatbázisban a következő ábrán látható módon:

![A tűzfal működése áramló – példa](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Csatlakozás az internetről
Kiszolgálószintű tűzfal-szabályokat alkalmazni az azonos Azure-adatbázishoz PostgreSQL-kiszolgálón lévő összes adatbázis. Ha a kérés IP-címe a kiszolgálószintű tűzfalszabályokban megadott tartományok egyikében található, a tűzfal engedélyezi a csatlakozást.
Ha az IP-cím, a kérelem nem esik a kiszolgálószintű tűzfalszabály megadott tartományok, a kapcsolódási kérelem sikertelen lesz.
Például ha az alkalmazás a PostgreSQL JDBC-illesztőt csatlakozik, felmerülhet a hiba történt a csatlakozás, ha a tűzfal blokkolja a kapcsolatot.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: végzetes: nincs pg\_hba.conf bejegyzés állomás "123.45.67.890", a felhasználó "adminuser", az adatbázis "postgresql", SSL

## <a name="connecting-from-azure"></a>Csatlakozás az Azure-ból
Lehetővé teszik az alkalmazások az Azure-ból az Azure-adatbázis PostgreSQL-kiszolgálóhoz való kapcsolódáshoz, Azure-kapcsolatok engedélyezni kell. Például egy Azure Web Apps alkalmazást vagy olyan alkalmazás, amely egy Azure virtuális gép üzemeltetésére, vagy csatlakoztassa egy Azure Data Factory az adatkezelési átjáró. Az erőforrások nem kell ugyanazt a virtuális hálózatot (VNet) vagy a tűzfalszabály tartozó erőforráscsoport ahhoz, hogy ezeket a kapcsolatokat. Amikor egy Azure-alkalmazás megkísérel csatlakozni az adatbázis-kiszolgálóhoz, a tűzfal ellenőrzi, hogy az Azure-kapcsolatok engedélyezve vannak-e. Többféle módszer ahhoz, hogy ilyen típusú kapcsolatokat. A 0.0.0.0 kezdő- és zárócímet tartalmazó tűzfalbeállítás jelzi, hogy ezek a kapcsolatok engedélyezettek. Beállíthatja azt is megteheti, a **Azure-szolgáltatásokhoz való hozzáférés engedélyezése** lehetőséggel **ON** a portálon a **kapcsolatbiztonsági** ablaktáblán, és nyomja le **mentése**. A kapcsolódási kísérlet nem engedélyezett, ha a kérelem nem érte el az Azure-adatbázishoz PostgreSQL-kiszolgáló.

> [!IMPORTANT]
> Ez a beállítás konfigurálja a tűzfalat arra, hogy engedélyezzen minden, az Azure felől érkező kapcsolatot, beleértve a más ügyfelek előfizetéseiből érkező kapcsolatokat is. Ezen beállítás kiválasztásakor győződjön meg arról, hogy a bejelentkezési és felhasználói engedélyei a hozzáféréseket az arra jogosult felhasználókra korlátozzák.
> 

![Azure-szolgáltatásokhoz való hozzáférés engedélyezése a portál konfigurálása](media/concepts-firewall-rules/allow-azure-services.png)

## <a name="programmatically-managing-firewall-rules"></a>Tűzfalszabályok szoftveres kezelése
Az Azure portálon kívül tűzfalszabályok programozott módon, Azure parancssori Felülettel kezelhetők.
Lásd még: [hozzon létre és kezelheti az Azure-adatbázis PostgreSQL-tűzfalszabályok Azure parancssori felület használatával](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-server-firewall"></a>Az adatbázis-kiszolgáló tűzfal hibaelhárítása
Amikor PostgreSQL-kiszolgáló szolgáltatás a Microsoft Azure adatbázishoz való hozzáférés nem a várt módon működnek várja, vegye figyelembe a következő szempontokat:

* **Az engedélyezési listán módosításai nem lépnek érvénybe még:** lehet, mint amennyit egy 5 perces késleltetést használ az vált, az Azure-adatbázishoz PostgreSQL-kiszolgáló tűzfal konfigurációjának érvénybe léptetéséhez.

* **A bejelentkezés nem engedélyezett, vagy helytelen jelszót használt:** egy bejelentkezési nincs engedélye az PostgreSQL-kiszolgálóhoz tartozó Azure-adatbázis, vagy a használt jelszó nem megfelelő, ha a kapcsolat az Azure-adatbázishoz PostgreSQL-kiszolgáló megtagadva. Csak egy tűzfal-beállítás létrehozásához biztosít az ügyfelek; a kiszolgálóhoz való kapcsolódás sikertelen bejelentkezési kísérletet lehetőséget minden ügyfél továbbra is biztosítania kell a szükséges hitelesítő adatokat.

Például egy JDBC-ügyfélprogram segítségével a következő hiba jelenhetnek meg.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: végzetes: "felhasználónév" felhasználói jelszó hitelesítése sikertelen.

* **Dinamikus IP-cím**: Ha az internetkapcsolata dinamikus IP-címkezeléssel rendelkezik, és problémákat okoz a tűzfalon való átjutás, próbálja ki a következő megoldások valamelyikét:

* Az internetszolgáltató (ISP) kérjen a PostgreSQL-kiszolgáló az Azure-adatbázishoz hozzáférő ügyfélszámítógépekhez rendelt IP-címtartományt, és adja hozzá az IP-címtartományt, egy tűzfalszabályt.

* Beolvasása a statikus IP-címzés helyette az ügyfélszámítógépek számára, és adja hozzá a statikus IP-cím tűzfal szabály.

## <a name="next-steps"></a>További lépések
A kiszolgáló- és adatbázis tűzfal-szabályok létrehozása a cikkekben talál:
* [Hozzon létre és kezelheti az Azure-adatbázis PostgreSQL-tűzfalszabályok az Azure portál használatával](howto-manage-firewall-using-portal.md)
* [Hozzon létre és kezelheti az Azure-adatbázis PostgreSQL-tűzfalszabályok Azure parancssori felület használatával](howto-manage-firewall-using-cli.md)
