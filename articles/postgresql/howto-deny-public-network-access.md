---
title: Nyilvános hálózati hozzáférés megtagadása – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Megtudhatja, hogyan konfigurálhatja a nyilvános hálózati hozzáférés megtagadását a Azure Database for PostgreSQL egyetlen kiszolgálójának Azure Portal használatával
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: b51a79027ce834190a4fae5d893e47f2be16abf7
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86102191"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Nyilvános hálózati hozzáférés megtagadása Azure Database for PostgreSQL egy kiszolgálón Azure Portal használatával

Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for PostgreSQL egyetlen kiszolgálót az összes nyilvános konfiguráció megtagadásához, és a hálózati biztonság növelése érdekében csak privát végpontokon keresztüli kapcsolatokat engedélyezhet.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

* Egy [Azure Database for PostgreSQL egyetlen kiszolgáló](quickstart-create-PostgreSQL Single server-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Nyilvános hálózati hozzáférés megtagadásának beállítása

Az alábbi lépéseket követve beállíthatja a PostgreSQL egyetlen kiszolgálójának a nyilvános hálózati hozzáférés megtagadását:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for PostgreSQL önálló kiszolgálót.

1. A PostgreSQL egykiszolgálós oldal **Beállítások**területén kattintson a **kapcsolatbiztonsági** elemre a kapcsolatbiztonsági konfiguráció lap megnyitásához.

1. A **nyilvános hálózati hozzáférés megtagadása**területen válassza az **Igen** lehetőséget a PostgreSQL-kiszolgáló hozzáférésének megtagadásának engedélyezéséhez.

    ![Azure Database for PostgreSQL egyetlen kiszolgáló megtagadja a hálózati hozzáférést](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Kattintson a **Mentés** gombra a módosítások mentéséhez.

1. Egy értesítés megerősíti, hogy a kapcsolatbiztonsági beállítás engedélyezése sikeres volt.

    ![Azure Database for PostgreSQL egyetlen kiszolgáló megtagadja a hálózati hozzáférés sikerességét](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>Következő lépések

Útmutató [riasztások létrehozásához mérőszámokon](howto-alert-on-metric.md).