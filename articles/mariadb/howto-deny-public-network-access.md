---
title: Nyilvános hálózati hozzáférés megtagadása – Azure Portal-Azure Database for MariaDB
description: Megtudhatja, hogyan konfigurálhatja a nyilvános hálózati hozzáférés megtagadását a Azure Database for MariaDB Azure Portal használatával
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: how-to
ms.date: 03/10/2020
ms.openlocfilehash: f33dbfa0b96d7f6d85443005ff7e8b1a780c75a0
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86104384"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Nyilvános hálózati hozzáférés megtagadása Azure Database for MariaDB a Azure Portal használatával

Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for MariaDB kiszolgálót az összes nyilvános konfiguráció megtagadásához, és a hálózati biztonság növelése érdekében csak privát végpontokon keresztüli kapcsolatokat engedélyezhet.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

* Egy [Azure Database for MariaDB](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Nyilvános hálózati hozzáférés megtagadásának beállítása

Az alábbi lépéseket követve állíthatja be, hogy a MariaDB-kiszolgáló megtagadja a nyilvános hálózati hozzáférést:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for MariaDB-kiszolgálót.

1. A MariaDB-kiszolgáló lap **Beállítások**területén kattintson a **kapcsolatbiztonsági** elemre a kapcsolatbiztonsági konfiguráció lap megnyitásához.

1. A nyilvános hálózati hozzáférés megtagadása területen válassza az **Igen** lehetőséget a MariaDB-kiszolgálóhoz való nyilvános hozzáférés megtagadásának engedélyezéséhez.

    ![Azure Database for MariaDB hálózati hozzáférés megtagadása](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Kattintson a **Mentés** gombra a módosítások mentéséhez.

1. Egy értesítés megerősíti, hogy a kapcsolatbiztonsági beállítás engedélyezése sikeres volt.

    ![Azure Database for MariaDB a hálózati hozzáférés megtagadása sikeres](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>További lépések

Útmutató [riasztások létrehozásához mérőszámokon](howto-alert-metric.md).