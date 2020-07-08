---
title: Nyilvános hálózati hozzáférés megtagadása – Azure Portal-Azure Database for MySQL
description: Megtudhatja, hogyan konfigurálhatja a nyilvános hálózati hozzáférés megtagadását a Azure Database for MySQL Azure Portal használatával
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: b5f93f3a3583900810ca75f925c6a88df9102652
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "79374952"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Nyilvános hálózati hozzáférés megtagadása Azure Database for MySQL a Azure Portal használatával

Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for MySQL kiszolgálót az összes nyilvános konfiguráció megtagadásához, és a hálózati biztonság növelése érdekében csak privát végpontokon keresztüli kapcsolatokat engedélyezhet.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

* Egy [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Nyilvános hálózati hozzáférés megtagadásának beállítása

Kövesse az alábbi lépéseket a MySQL-kiszolgáló nyilvános hálózati hozzáférés megtagadásának beállításához:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for MySQL-kiszolgálót.

1. A MySQL-kiszolgáló lap **Beállítások**területén kattintson a **kapcsolatbiztonsági** elemre a kapcsolatbiztonsági konfiguráció lap megnyitásához.

1. A **nyilvános hálózati hozzáférés megtagadása**területen válassza az **Igen** lehetőséget a MySQL-kiszolgáló hozzáférésének megtagadásának engedélyezéséhez.

    ![Azure Database for MySQL hálózati hozzáférés megtagadása](./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG)

1. Kattintson a **Mentés** gombra a módosítások mentéséhez.

1. Egy értesítés megerősíti, hogy a kapcsolatbiztonsági beállítás engedélyezése sikeres volt.

    ![Azure Database for MySQL a hálózati hozzáférés megtagadása sikeres](./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png)

## <a name="next-steps"></a>További lépések

Útmutató [riasztások létrehozásához mérőszámokon](howto-alert-on-metric.md).