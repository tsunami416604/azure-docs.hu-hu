---
title: Tűzfalszabályok - Nagykapacitású (Citus) – Azure-adatbázis a PostgreSQL-hez
description: Ez a cikk az Azure Database for PostgreSQL – Hyperscale (Citus) tűzfalszabályait ismerteti.
author: jonels-msft
ms.author: jonels
ms.service: postgresql
ms.topic: conceptual
ms.date: 9/12/2019
ms.openlocfilehash: b843cd1528630a21255053f623356a0379daacf6
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74975567"
---
# <a name="firewall-rules-in-azure-database-for-postgresql---hyperscale-citus"></a>Tűzfalszabályok az Azure Database for PostgreSQL - Hyperscale (Citus) rendszerben
Az Azure Database for PostgreSQL kiszolgálótűzfal megakadályozza a nagykapacitású (Citus) koordinátori csomóponthoz való összes hozzáférést, amíg meg nem adja, hogy mely számítógépek rendelkeznek engedéllyel. A tűzfal hozzáférést biztosít a kiszolgálóhoz az egyes kérelmek eredeti IP-címe alapján.
A tűzfal konfigurálásakor olyan tűzfalszabályokat adhat meg, amelyek meghatározzák az elfogadható IP-címtartományokat. Tűzfalszabályokat a kiszolgáló szintjén hozhat létre.

**Tűzfalszabályok:** Ezek a szabályok lehetővé teszik az ügyfelek számára a nagykapacitású (Citus) koordinátori csomópont elérését, azaz az ugyanazon logikai kiszolgálón belüli összes adatbázist. A kiszolgálószintű tűzfalszabályok konfigurálhatók az Azure Portal használatával. Kiszolgálószintű tűzfalszabályok létrehozásához az előfizetés tulajdonosának vagy az előfizetés közreműködőjének kell lennie.

## <a name="firewall-overview"></a>Tűzfal áttekintése
A koordinátor csomóponthoz való összes adatbázis-hozzáférést alapértelmezés szerint a tűzfal blokkolja. Ha egy másik számítógépről szeretné használni a kiszolgálót, meg kell adnia egy vagy több kiszolgálószintű tűzfalszabályt a kiszolgáló elérésének engedélyezéséhez. A tűzfalszabályok segítségével megadhatja, hogy az internetről mely IP-címtartományokat engedélyezze. Az Azure Portal-webhelyhez való hozzáférést a tűzfalszabályok nem érintik.
Az internetről és az Azure-ból érkező csatlakozási kísérleteknek először át kell haladniuk a tűzfalon, mielőtt elérnék a PostgreSQL-adatbázist, ahogy az az alábbi ábrán látható:

![Példa a tűzfal működésére](media/concepts-hyperscale-firewall-rules/1-firewall-concept.png)

## <a name="connecting-from-the-internet-and-from-azure"></a>Csatlakozás az internetről és az Azure-ból

A nagyméretű (Citus) kiszolgálócsoport tűzfala határozza meg, hogy ki csatlakozhat a csoport koordinátori csomópontjához. A tűzfal a hozzáférést a szabályok konfigurálható listájának áttekintésével határozza meg. Minden szabály egy IP-cím vagy címtartomány, amely bevan engedve.

Amikor a tűzfal blokkolja a kapcsolatokat, alkalmazáshibákat okozhat. A PostgreSQL JDBC illesztőprogram használata például a következőhöz hasonló hibát vet fel:

> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: no pg\_hba.conf entry for host "123.45.67.890", user "citus", database "citus", SSL

A szabályok definiálásának módjáról a [Tűzfalszabályok létrehozása és kezelése](howto-hyperscale-manage-firewall-using-portal.md) című témakörben olvashat.

## <a name="troubleshooting-the-database-server-firewall"></a>Az adatbázis-kiszolgáló tűzfalának hibáinak elhárítása
Ha a Microsoft Azure Database for PostgreSQL – Hyperscale (Citus) szolgáltatáshoz való hozzáférés nem a várt nak felel meg, vegye figyelembe az alábbi szempontokat:

* **Az engedélyezési lista módosításai még nem léptek hatályba:** A nagykapacitású (Citus) tűzfal konfigurációjának módosításai akár öt perces késéssel is érvénybe léphetnek.

* **A felhasználó nem jogosult, vagy helytelen jelszót használt:** Ha a felhasználó nem rendelkezik engedélyekkel a kiszolgálón, vagy a használt jelszó helytelen, a kapcsolat a kiszolgálóval megszakad. A tűzfalbeállítás létrehozása csak lehetőséget biztosít az ügyfeleknek a kiszolgálóhoz való csatlakozásra; minden ügyfélnek meg kell adnia a szükséges biztonsági hitelesítő adatokat.

Egy JDBC-ügyfél használatával például a következő hiba jelenhet meg.
> java.util.concurrent.ExecutionException: java.lang.RuntimeException: org.postgresql.util.PSQLException: FATAL: jelszóhitelesítés nem sikerült a felhasználó "yourusername"

* **Dinamikus IP-cím**: Ha az internetkapcsolata dinamikus IP-címkezeléssel rendelkezik, és problémákat okoz a tűzfalon való átjutás, próbálja ki a következő megoldások valamelyikét:

* Kérdezze meg az internetszolgáltatótól a Nagykapacitású (Citus) koordinátori csomópontot elérő ügyfélszámítógépekhez rendelt IP-címtartományt, majd adja hozzá tűzfalszabályként az IP-címtartományt.

* Az ügyfélszámítógépek számára ehelyett statikus IP-címzést kaphat, majd adja hozzá a statikus IP-címet tűzfalszabályként.

## <a name="next-steps"></a>További lépések
A kiszolgálószintű és adatbázis-szintű tűzfalszabályok létrehozásáról szóló cikkek a következő témakörben találhatóak:
* [Azure Database for PostgreSQL tűzfalszabályok létrehozása és kezelése az Azure Portalon](howto-hyperscale-manage-firewall-using-portal.md)
