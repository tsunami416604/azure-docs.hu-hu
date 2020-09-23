---
title: TLS-konfiguráció – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ismerje meg, hogyan állíthatja be a TLS-konfigurációt a Azure Database for PostgreSQL egyetlen kiszolgálójának Azure Portal használatával
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 06/02/2020
ms.openlocfilehash: 26470709b37c2623c581499ec55572da402e96cb
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906454"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql-single---server-using-azure-portal"></a>A TLS-beállítások konfigurálása Azure Database for PostgreSQL egykiszolgálós Azure Portal használatával

Ez a cikk bemutatja, hogyan konfigurálhat egy Azure Database for PostgreSQLt a kapcsolatok minimális TLS-verziójának betartatására, és megtagadhatja az összes olyan kapcsolatot, amely alacsonyabb TLS-verzióval rendelkezik, mint a konfigurált minimális TLS-verzió, ami javítja a hálózati biztonságot.

A TLS-verziót kényszerítheti a Azure Database for PostgreSQLhoz való csatlakozásra. Az ügyfelek most már dönthetnek úgy, hogy az adatbázis-kiszolgáló minimális TLS-verzióját szeretnék beállítani. Például, ha a TLS 1,0-es minimális verziószámot állítja be a TLS-re, akkor a kiszolgáló engedélyezi a TLS 1,0, 1,1 és 1.2 + protokollt használó ügyfelek kapcsolódását. Ehelyett, ha a minimális TLS-verziót 1,2 + értékre állítja, csak a TLS 1,2-t használó ügyfelek kapcsolatait engedélyezi, és a TLS 1,0 és a TLS 1,1 protokollal létesített összes kapcsolat el lesz utasítva.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

* Egy [Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>TLS-konfigurációk beállítása Azure Database for PostgreSQL – egyetlen kiszolgáló

A PostgreSQL minimális TLS-verziójának beállításához kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for PostgreSQL.

1.  A Azure Database for PostgreSQL – egyetlen kiszolgáló lap **Beállítások**területén kattintson a **kapcsolatbiztonsági** elemre a kapcsolatbiztonsági konfiguráció lap megnyitásához.

1. A **TLS minimális verziója**beállításnál válassza az **1,2** lehetőséget, hogy megtagadja a TLS 1,2-nál kisebb TLS-verzióval létesített kapcsolatokat a PostgreSQL-kiszolgálónál.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value.png" alt-text="Egykiszolgálós TLS-konfiguráció Azure Database for PostgreSQL":::

1. Kattintson a **Mentés** gombra a módosítások mentéséhez.

1. Egy értesítés megerősíti, hogy a kapcsolatbiztonsági beállítás engedélyezése sikeres volt.

    :::image type="content" source="./media/howto-tls-configurations/setting-tls-value-success.png" alt-text="Azure Database for PostgreSQL – egy kiszolgáló TLS-konfigurációja sikeres":::

## <a name="next-steps"></a>Következő lépések

Útmutató [riasztások létrehozásához mérőszámokon](howto-alert-on-metric.md)