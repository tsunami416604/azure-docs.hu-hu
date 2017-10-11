---
title: "Azure-adatbázis PostgreSQL-kiszolgáló tűzfalszabályainak |} Microsoft Docs"
description: "Tűzfalszabályok ismerteti az Azure-adatbázis PostgreSQL-kiszolgáló számára."
services: postgresql
author: jasonwhowell
ms.author: jasonh
manager: jhubbard
editor: jasonwhowell
ms.service: postgresql
ms.topic: article
ms.date: 05/10/2017
ms.openlocfilehash: 150c4f53c5ab6a6425b6af7d286d4c1518b006f8
ms.sourcegitcommit: 18ad9bc049589c8e44ed277f8f43dcaa483f3339
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/29/2017
---
# <a name="azure-database-for-postgresql-server-firewall-rules"></a>Azure-adatbázis PostgreSQL-kiszolgáló tűzfalszabályainak
Tűzfalak tagadni a hozzáférést minden az adatbázis-kiszolgáló csak akkor adja meg, mely számítógépek rendelkeznek engedéllyel. A tűzfal engedélyezi a hozzáférést a kiszolgálóhoz, az egyes kérelmek az eredeti IP-címe alapján.
A tűzfal konfigurálásakor olyan tűzfalszabályokat adhat meg, amelyek meghatározzák az elfogadható IP-címtartományokat. Tűzfal-szabályokat hozhat létre a kiszolgálói szinten.

**Tűzfal-szabályok:** ezek a szabályok engedélyezi az ügyfelek számára a teljes Azure-adatbázis eléréséhez PostgreSQL-kiszolgáló esetén ez azt jelenti, hogy összes adatbázis belül az azonos logikai kiszolgáló. Kiszolgálószintű tűzfal-szabályokat konfigurálhatja az Azure-portál használatával vagy az Azure parancssori felület parancsait. Kiszolgálószintű tűzfal-szabályok létrehozása, az előfizetés tulajdonosa vagy egy előfizetés közreműködői kell lennie.

## <a name="firewall-overview"></a>Tűzfal áttekintése
Az adatbázis elérésére az Azure-adatbázis PostgreSQL-kiszolgáló alapértelmezés szerint a tűzfal blokkolja. A kiszolgáló egy másik számítógépről használatának megkezdéséhez meg kell adnia egy vagy több kiszolgálószintű engedélyezésére szolgáló tűzfalszabályok a kiszolgáló elérését. Használja a tűzfalszabályok adja meg, melyik IP-címtartományok engedélyezi az internetről. A tűzfalszabályok nincs hatással a hozzáférést az Azure portál magát.
Kapcsolódási kísérletek az internetről és az Azure először eltelik a tűzfalon keresztül is eléri a PostgreSQL-adatbázisban a következő ábrán látható módon:

![A tűzfal működése áramló – példa](media/concepts-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet"></a>Csatlakozás az internetről
Az Azure-adatbázishoz PostgreSQL-kiszolgálón lévő összes adatbázis kiszolgálószintű tűzfal-szabályokat alkalmazni. Ha a kérés IP-címe a kiszolgálószintű tűzfalszabályokban megadott tartományok egyikében található, a tűzfal engedélyezi a csatlakozást.
Ha az IP-cím, a kérelem nem az adatbázis-szintjére, vagy a kiszolgálószintű tűzfalszabály megadott tartományok belül van, a kapcsolódási kérelem sikertelen lesz.
Például ha az alkalmazás a PostgreSQL JDBC-illesztőt csatlakozik, felmerülhet a hiba történt a csatlakozás, ha a tűzfal blokkolja a kapcsolatot.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: végzetes: nincs pg\_hba.conf bejegyzés állomás "123.45.67.890", a felhasználó "adminuser", az adatbázis "postgresql", SSL

## <a name="programmatically-managing-firewall-rules"></a>Tűzfalszabályok szoftveres kezelése
Az Azure portálon kívül tűzfalszabályok programozott módon, Azure parancssori Felülettel kezelhetők.
Lásd még: [hozzon létre és kezelheti az Azure-adatbázis PostgreSQL-tűzfalszabályok Azure parancssori felület használatával](howto-manage-firewall-using-cli.md)

## <a name="troubleshooting-the-database-firewall"></a>Az adatbázistűzfal hibaelhárítása
Amikor PostgreSQL-kiszolgáló szolgáltatás a Microsoft Azure adatbázishoz való hozzáférés nem a várt módon működnek várja, vegye figyelembe a következő szempontokat:

* **Az engedélyezési listán módosításai nem lépnek érvénybe még:** lehet, mint amennyit egy 5 perces késleltetést használ az vált, az Azure-adatbázishoz PostgreSQL-kiszolgáló tűzfal konfigurációjának érvénybe léptetéséhez.

* **A bejelentkezés nem engedélyezett, vagy helytelen jelszót használt:** egy bejelentkezési nincs engedélye az PostgreSQL-kiszolgálóhoz tartozó Azure-adatbázis, vagy a használt jelszó nem megfelelő, ha a kapcsolat az Azure-adatbázishoz PostgreSQL-kiszolgáló megtagadva. Egy tűzfalbeállítás létrehozása lehetővé teszi az ügyfelek számára a kiszolgálóhoz való csatlakozás megkísérlését, azonban minden egyes ügyfélnek meg kell adnia a szükséges biztonsági hitelesítő adatokat.

Például egy JDBC-ügyfélprogram segítségével a következő hiba jelenhetnek meg.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: végzetes: "felhasználónév" felhasználói jelszó hitelesítése sikertelen.

* **Dinamikus IP-cím**: Ha az internetkapcsolata dinamikus IP-címkezeléssel rendelkezik, és problémákat okoz a tűzfalon való átjutás, próbálja ki a következő megoldások valamelyikét:

* Az internetszolgáltató (ISP) kérjen a PostgreSQL-kiszolgáló az Azure-adatbázishoz hozzáférő ügyfélszámítógépekhez rendelt IP-címtartományt, és adja hozzá az IP-címtartományt, egy tűzfalszabályt.

* Állítson be statikus IP-címeket az ügyfélszámítógépei számára, majd adja meg az IP-címeket tűzfalszabályokként.

## <a name="next-steps"></a>Következő lépések
A kiszolgáló- és adatbázis tűzfal-szabályok létrehozása a cikkekben talál:
* [Hozzon létre és kezelheti az Azure-adatbázis PostgreSQL-tűzfalszabályok az Azure portál használatával](howto-manage-firewall-using-portal.md)
* [Hozzon létre és kezelheti az Azure-adatbázis PostgreSQL-tűzfalszabályok Azure parancssori felület használatával](howto-manage-firewall-using-cli.md)