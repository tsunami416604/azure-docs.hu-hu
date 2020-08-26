---
title: Azure Database for PostgreSQL kezelése – Azure Portal
description: Megtudhatja, hogyan kezelheti Azure Database for PostgreSQL-kiszolgálókat a Azure Portal.
author: rachel-msft
ms.author: raagyema
ms.service: postgresql
ms.topic: how-to
ms.date: 11/20/2019
ms.openlocfilehash: 908a61a00f0e33016074a6f985271ac94157fdf4
ms.sourcegitcommit: b33c9ad17598d7e4d66fe11d511daa78b4b8b330
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/25/2020
ms.locfileid: "88854995"
---
# <a name="manage-an-azure-database-for-postgresql-server-using-the-azure-portal"></a>Azure Database for PostgreSQL-kiszolgáló kezelése a Azure Portal használatával

Ez a cikk bemutatja, hogyan kezelheti a Azure Database for PostgreSQL-kiszolgálókat. A felügyeleti feladatok közé tartozik a számítási és tárolási skálázás, a rendszergazdai jelszó alaphelyzetbe állítása és a kiszolgáló adatainak megtekintése.

## <a name="sign-in"></a>Bejelentkezés

Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

## <a name="create-a-server"></a>A kiszolgáló létrehozása

A rövid útmutatóból megtudhatja [, hogyan](quickstart-create-server-database-portal.md) hozhat létre és kezdjen el egy Azure Database for PostgreSQL-kiszolgálót.

## <a name="scale-compute-and-storage"></a>Számítási és tárolási méretezés

A kiszolgáló létrehozása után a általános célú és a memória optimalizált szintjei között méretezheti az igények változását. A számítási és a memória méretezése a virtuális mag növelésével vagy csökkentésével is elvégezhető. A tárterület méretezhető (azonban nem méretezheti le a tárterületet).

### <a name="scale-between-general-purpose-and-memory-optimized-tiers"></a>Méretezés általános célú és a memória optimalizált szintjei között

Általános célúról a memóriára optimalizált és fordítva is méretezhető. A kiszolgáló létrehozása után az alapszintű csomagra való váltás nem támogatott.

1. Válassza ki a kiszolgálót a Azure Portal. Válassza ki az **árképzési szintet**, amely a **Beállítások** szakaszban található.

2. Válassza ki az **általános célú** vagy a **memória optimalizált**elemet attól függően, hogy mit kíván méretezni.

   ![A Azure Portal képernyőképe az alapszintű, általános célú vagy a memória optimalizált szintjeinek kiválasztásához Azure Database for PostgreSQL](./media/howto-create-manage-server-portal/change-pricing-tier.png)

   > [!NOTE]
   > A rétegek módosítása a kiszolgáló újraindítását eredményezi.

3. A módosítások mentéséhez kattintson **az OK gombra** .

### <a name="scale-vcores-up-or-down"></a>Méretezési virtuális mag felfelé vagy lefelé

1. Válassza ki a kiszolgálót a Azure Portal. Válassza ki az **árképzési szintet**, amely a **Beállítások** szakaszban található.

2. Módosítsa a **virtuális mag** beállítást úgy, hogy áthelyezi a csúszkát a kívánt értékre.

   ![Képernyőkép a Azure Portalról a virtuális mag lehetőség kiválasztásához Azure Database for PostgreSQL](./media/howto-create-manage-server-portal/scaling-compute.png)

   > [!NOTE]
   > A skálázás virtuális mag a kiszolgáló újraindítását okozza.

3. A módosítások mentéséhez kattintson **az OK gombra** .

### <a name="scale-storage-up"></a>A tárterület felskálázása

1. Válassza ki a kiszolgálót a Azure Portal. Válassza ki az **árképzési szintet**, amely a **Beállítások** szakaszban található.

2. Módosítsa a **tárolási** beállításokat úgy, hogy a csúszkát felfelé helyezi a kívánt értékre.

   ![Képernyőfelvétel a Azure Portal a tárolási skála kiválasztásához Azure Database for PostgreSQL](./media/howto-create-manage-server-portal/scaling-storage.png)

   > [!NOTE]
   > A tárterület nem méretezhető le.

3. A módosítások mentéséhez kattintson **az OK gombra** .

## <a name="update-admin-password"></a>Rendszergazdai jelszó frissítése

A rendszergazdai szerepkör jelszava a Azure Portal használatával módosítható.

1. Válassza ki a kiszolgálót a Azure Portal. Az **Áttekintés** ablakban válassza a **jelszó alaphelyzetbe állítása**lehetőséget.

   ![Képernyőkép a Azure Portalről a jelszó alaphelyzetbe állításához Azure Database for PostgreSQL](./media/howto-create-manage-server-portal/overview-reset-password.png)

2. Adjon meg egy új jelszót, és erősítse meg a jelszót. A szövegmező a jelszó bonyolultságára vonatkozó követelményeket fogja kérni.

   ![Képernyőkép a Azure Portalről a jelszó alaphelyzetbe állításához és a mentéshez Azure Database for PostgreSQL](./media/howto-create-manage-server-portal/reset-password.png)

3. Az új jelszó mentéséhez kattintson **az OK gombra** .

## <a name="delete-a-server"></a>Kiszolgáló törlése

Ha már nincs szüksége rá, törölheti a kiszolgálót. 

1. Válassza ki a kiszolgálót a Azure Portal. Az **Áttekintés** ablakban válassza a **Törlés**lehetőséget.

   ![Képernyőkép a Azure Portalről a kiszolgáló törléséhez Azure Database for PostgreSQL](./media/howto-create-manage-server-portal/overview-delete.png)

2. Írja be a kiszolgáló nevét a beviteli mezőbe annak megerősítéséhez, hogy ez a kiszolgáló, amelyet törölni szeretne.

   ![Képernyőkép a Azure Portalről a kiszolgáló törlésének megerősítéséhez Azure Database for PostgreSQL](./media/howto-create-manage-server-portal/confirm-delete.png)

   > [!NOTE]
   > A kiszolgáló törlése visszafordíthatatlan.

3. Válassza a **Törlés** elemet.

## <a name="next-steps"></a>Következő lépések

- A [biztonsági másolatok és a kiszolgáló-visszaállítás](howto-restore-server-portal.md) ismertetése
- Ismerkedjen meg [a hangolási és figyelési lehetőségekkel Azure Database for PostgreSQL](concepts-monitoring.md)
