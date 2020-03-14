---
title: TLS-konfiguráció – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ismerje meg, hogyan állíthatja be a TLS-konfigurációt a Azure Database for PostgreSQL egyetlen kiszolgálójának Azure Portal használatával
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 049b30689c86881ce89d4fbcd54175ee1ee5497e
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375108"
---
# <a name="configuring-tls-settings-in-azure-database-for-postgresql---single-server-using-azure-portal"></a>A TLS-beállítások konfigurálása Azure Database for PostgreSQL – egyetlen kiszolgálón a Azure Portal használatával

Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for PostgreSQL-egyetlen kiszolgálót az összes bejövő kapcsolat minimális TLS-verziójának betartatására, ami segít a hálózati biztonság javításában.

Az ügyfeleknek lehetősége van arra, hogy a TLS-verziót a Azure Database for PostgreSQL-kiszolgálóval való csatlakozásra kényszerítse. Az ügyfelek most már dönthetnek úgy, hogy az adatbázis-kiszolgáló minimális TLS-verzióját szeretnék beállítani. Például, ha a TLS 1,0-es minimális verziószámot állítja be a TLS-re, akkor a kiszolgáló engedélyezi a TLS 1,0, 1,1 és 1.2 + protokollt használó ügyfelek kapcsolódását. Azt is megteheti, hogy a 1,2 értékre állítja azt, hogy csak a TLS 1,2-t használó ügyfelek kapcsolatainak engedélyezése, valamint a TLS 1,0 és a TLS 1,1 használatával létesített összes kapcsolat el lesz utasítva.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató végrehajtásához lesz szüksége:

* Egy [Azure Database for PostgreSQL](quickstart-create-server-database-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-postgresql---single-server"></a>TLS-konfigurációk beállítása Azure Database for PostgreSQL – egyetlen kiszolgáló

Az alábbi lépéseket követve állítsa be a PostgreSQL-kiszolgáló minimális TLS-verzióját:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for PostgreSQL-egyetlen kiszolgálót.

1.  A Azure Database for PostgreSQL – egyetlen kiszolgáló lap **Beállítások**területén kattintson a **kapcsolatbiztonsági** elemre a kapcsolatbiztonsági konfiguráció lap megnyitásához.

1. A **TLS minimális verziója**beállításnál válassza az **1,2** lehetőséget, hogy megtagadja a TLS 1,2-nál kisebb TLS-verzióval létesített kapcsolatokat a PostgreSQL-kiszolgálónál.

    ![Egykiszolgálós TLS-konfiguráció Azure Database for PostgreSQL](./media/howto-tls-configurations/setting-tls-value.png)

1. Kattintson a **Mentés** gombra a módosítások mentéséhez.

1. Egy értesítés megerősíti, hogy a kapcsolatbiztonsági beállítás engedélyezése sikeres volt.

    ![Azure Database for PostgreSQL – egy kiszolgáló TLS-konfigurációja sikeres](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Következő lépések

Útmutató [riasztások létrehozásához mérőszámokon](howto-alert-on-metric.md).
