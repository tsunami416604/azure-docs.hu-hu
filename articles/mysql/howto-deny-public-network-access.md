---
title: Nyilvános hálózati hozzáférés megtagadása – Azure portal – Azure-adatbázis a MySQL-hez
description: Megtudhatja, hogy miként konfigurálható a nyilvános hálózati hozzáférés megtagadása az Azure Portal használatával a MySQL Azure-adatbázisához
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: b5f93f3a3583900810ca75f925c6a88df9102652
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79374952"
---
# <a name="deny-public-network-access-in-azure-database-for-mysql-using-azure-portal"></a>Nyilvános hálózati hozzáférés megtagadása a MySQL Azure-adatbázisában az Azure Portal használatával

Ez a cikk azt ismerteti, hogyan konfigurálhatja az Azure Database for MySQL-kiszolgáló t az összes nyilvános konfiguráció kondijának megtagadására, és csak a privát végpontokon keresztüli kapcsolatok engedélyezésére a hálózati biztonság további növelése érdekében.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató befejezéséhez a következőkre van szükség:

* [Azure-adatbázis a MySQL-hez](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Nyilvános hálózati hozzáférés megtagadásának beállítása

A MySQL kiszolgáló nyilvános hálózati hozzáférés megtagadásának beállításához kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a meglévő Azure-adatbázis mySQL-kiszolgáló.

1. A MySQL kiszolgáló lap **Beállítások**területén kattintson a **Kapcsolatbiztonság elemre** a kapcsolatbiztonsági konfigurációs lap megnyitásához.

1. A **Nyilvános hálózati hozzáférés megtagadása**csoportban válassza az **Igen** lehetőséget a Nyilvános hozzáférés megtagadásának engedélyezéséhez a MySQL-kiszolgálóhoz.

    ![Azure Database for MySQL Hálózati hozzáférés megtagadása](./media/howto-deny-public-network-access/setting-deny-public-network-access.PNG)

1. Kattintson a **Mentés** gombra a módosítások mentéséhez.

1. Egy értesítés megerősíti, hogy a kapcsolat biztonsági beállítása sikeresen engedélyezve van.

    ![Az Azure Database for MySQL A hálózati hozzáférés megtagadása sikeres](./media/howto-deny-public-network-access/setting-deny-public-network-access-success.png)

## <a name="next-steps"></a>További lépések

További információ [arról, hogyan hozhat létre riasztásokat a mérőszámokon.](howto-alert-on-metric.md)