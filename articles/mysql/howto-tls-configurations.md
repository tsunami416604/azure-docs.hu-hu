---
title: TLS-konfiguráció – Azure Portal-Azure Database for MySQL
description: Ismerje meg, hogyan állíthatja be a TLS-konfigurációt a Azure Database for MySQL Azure Portal használatával
author: kummanish
ms.author: manishku
ms.service: mysql
ms.topic: conceptual
ms.date: 03/10/2020
ms.openlocfilehash: 495fea21c4fca12cd1ec6c1ea45105178d72fe1b
ms.sourcegitcommit: 512d4d56660f37d5d4c896b2e9666ddcdbaf0c35
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/14/2020
ms.locfileid: "79375290"
---
# <a name="configuring-tls-settings-in-azure-database-for-mysql-using-azure-portal"></a>A TLS-beállítások konfigurálása a Azure Database for MySQL a Azure Portal használatával

Ez a cikk azt ismerteti, hogyan konfigurálhat egy Azure Database for MySQL kiszolgálót úgy, hogy egy minimális TLS-verzióhoz tartozó kapcsolatokat kényszerítse ki, és megtagadja az alacsonyabb TLS-verzióval létesített kapcsolatokat, így fokozza a hálózati biztonságot.

Az ügyfeleknek lehetősége van arra, hogy a TLS-verziót a Azure Database for MySQLhoz való csatlakozásra kényszerítse. Az ügyfelek most már dönthetnek úgy, hogy az adatbázis-kiszolgáló minimális TLS-verzióját szeretnék beállítani. Ha például ezt a minimális TLS-verziót 1,0-re állítja be, akkor engedélyezni kell a TLS 1.0, 1.1 és 1,2 használatával csatlakozó ügyfeleket. Azt is megteheti, hogy a 1,2 értékre állítja azt, hogy csak a TLS 1,2 használatával csatlakozó ügyfeleket engedélyezze, és a TLS 1,0 és a TLS 1,1 összes bejövő kapcsolata el lesz utasítva.

## <a name="prerequisites"></a>Előfeltételek

Ez az útmutató végrehajtásához lesz szüksége:

* Egy [Azure Database for MySQL](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="set-tls-configurations-for-azure-database-for-mysql"></a>TLS-konfigurációk beállítása Azure Database for MySQLhoz

A MySQL-kiszolgáló minimális TLS-verziójának beállításához kövesse az alábbi lépéseket:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a meglévő Azure Database for MySQL-kiszolgálót.

1. A MySQL-kiszolgáló lap **Beállítások**területén kattintson a **kapcsolatbiztonsági** elemre a kapcsolatbiztonsági konfiguráció lap megnyitásához.

1. A **TLS minimális verziója**beállításnál válassza a **1,2** lehetőséget, hogy megtagadja a TLS 1,2-nál kisebb TLS-verzióval létesített kapcsolatokat a MySQL-kiszolgálóhoz.

    ![TLS-konfiguráció Azure Database for MySQL](./media/howto-tls-configurations/setting-tls-value.png)

1. Kattintson a **Mentés** gombra a módosítások mentéséhez.

1. Egy értesítés megerősíti, hogy a kapcsolatbiztonsági beállítás engedélyezése sikeres volt.

    ![Azure Database for MySQL TLS-konfiguráció sikeres](./media/howto-tls-configurations/setting-tls-value-success.png)

## <a name="next-steps"></a>Következő lépések

Útmutató [riasztások létrehozásához mérőszámokon](howto-alert-on-metric.md).
