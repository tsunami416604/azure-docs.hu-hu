---
title: Nyilvános hálózati hozzáférés megtagadása – Azure portal – Azure-adatbázis a MariaDB-hez
description: Megtudhatja, hogy miként konfigurálhatja a nyilvános hálózati hozzáférés megtagadását az Azure Portal használatával a MariaDB Azure-adatbázisához
author: kummanish
ms.author: manishku
ms.service: mariadb
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 87b6033e486e9009573436628d0183c8a022aced
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375238"
---
# <a name="deny-public-network-access-in-azure-database-for-mariadb-using-azure-portal"></a>Nyilvános hálózati hozzáférés megtagadása a MariaDB Azure Database-ben az Azure Portal használatával

Ez a cikk azt ismerteti, hogyan konfigurálhatja az Azure Database for MariaDB-kiszolgálót, hogy megtagadja az összes nyilvános konfigurációt, és csak a privát végpontokon keresztüli kapcsolatokat engedélyezze a hálózati biztonság további növelése érdekében.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató befejezéséhez a következőkre van szükség:

* [A MariaDB Azure-adatbázisa](quickstart-create-MariaDB-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Nyilvános hálózati hozzáférés megtagadásának beállítása

A MariaDB kiszolgáló nyilvános hálózati hozzáférés megtagadásának beállításához kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a meglévő Azure-adatbázis MariaDB-kiszolgáló.

1. A MariaDB kiszolgálólapján a **Beállítások**csoportban kattintson a **Kapcsolat biztonsága** elemre a kapcsolatbiztonsági konfigurációs lap megnyitásához.

1. A Nyilvános hálózati hozzáférés megtagadása csoportban válassza az **Igen** lehetőséget a MariaDB-kiszolgáló nyilvános hozzáférésének engedélyezéséhez.

    ![Azure Database for MariaDB Hálózati hozzáférés megtagadása](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Kattintson a **Mentés** gombra a módosítások mentéséhez.

1. Egy értesítés megerősíti, hogy a kapcsolat biztonsági beállítása sikeresen engedélyezve van.

    ![A MariaDB Azure Database szolgáltatása Sikeres hálózati hozzáférés megtagadása](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>További lépések

További információ [arról, hogyan hozhat létre riasztásokat a mérőszámokon.](howto-alert-metric.md)