---
title: Nyilvános hálózati hozzáférés megtagadása - Azure portal - Azure Database for PostgreSQL - Single server
description: Megtudhatja, hogy miként konfigurálható a nyilvános hálózati hozzáférés megtagadása az Azure Portal használatával az Azure Database for PostgreSQL Single server szolgáltatáshoz
author: kummanish
ms.author: manishku
ms.service: postgresql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 4dff2321414721dbd415b468e59aea0ab4b3acee
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79375121"
---
# <a name="deny-public-network-access-in-azure-database-for-postgresql-single-server-using-azure-portal"></a>Nyilvános hálózati hozzáférés megtagadása az Azure Database for PostgreSQL Single server szolgáltatásban az Azure Portal használatával

Ez a cikk azt ismerteti, hogyan konfigurálhatja az Azure Database for PostgreSQL Single server az összes nyilvános konfigurációk megtagadása, és csak a kapcsolatok privát végpontokon keresztül, hogy tovább fokozza a hálózati biztonság.

## <a name="prerequisites"></a>Előfeltételek

Az útmutató befejezéséhez a következőkre van szükség:

* [Egy Azure-adatbázis a PostgreSQL single server számára](quickstart-create-PostgreSQL Single server-server-database-using-azure-portal.md)

## <a name="set-deny-public-network-access"></a>Nyilvános hálózati hozzáférés megtagadásának beállítása

A PostgreSQL Single server Deny Public Network Access beállításához kövesse az alábbi lépéseket:

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki a meglévő Azure-adatbázis PostgreSQL single server.

1. A PostgreSQL Single server (Egyetlen kiszolgáló) lap **Beállítások**területén kattintson a **Kapcsolatbiztonság** elemre a kapcsolatbiztonsági konfigurációs lap megnyitásához.

1. A **Nyilvános hálózati hozzáférés megtagadása**csoportban válassza az **Igen** lehetőséget a Nyilvános hozzáférés megtagadásának engedélyezéséhez a PostgreSQL Single kiszolgálón.

    ![Azure Database for PostgreSQL Single server A hálózati hozzáférés megtagadása](./media/howto-deny-public-network-access/deny-public-network-access.PNG)

1. Kattintson a **Mentés** gombra a módosítások mentéséhez.

1. Egy értesítés megerősíti, hogy a kapcsolat biztonsági beállítása sikeresen engedélyezve van.

    ![Azure Database for PostgreSQL Single server A hálózati hozzáférés sikeresssé tételének megtagadása](./media/howto-deny-public-network-access/deny-public-network-access-success.png)

## <a name="next-steps"></a>További lépések

További információ [arról, hogyan hozhat létre riasztásokat a mérőszámokon.](howto-alert-on-metric.md)