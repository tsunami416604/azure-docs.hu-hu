---
title: Kiszolgáló újraindítása – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan lehet újraindítani egy Azure Database for PostgreSQL-egy kiszolgálót a Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: how-to
ms.date: 5/6/2019
ms.openlocfilehash: d25c8908b46608ac64bcf135404a070740413fe0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90907380"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Azure Database for PostgreSQL újraindítása – egyetlen kiszolgáló a Azure Portal használatával
Ez a témakör azt ismerteti, hogyan lehet újraindítani egy Azure Database for PostgreSQL-kiszolgálót. Előfordulhat, hogy a kiszolgálót karbantartás miatt újra kell indítania, ami rövid kimaradást okoz, mivel a kiszolgáló végrehajtja a műveletet.

A kiszolgáló újraindítása le lesz tiltva, ha a szolgáltatás foglalt. Előfordulhat például, hogy a szolgáltatás feldolgoz egy korábban kért műveletet, például a skálázási virtuális mag.
 
Az újraindítás befejezéséhez szükséges idő a PostgreSQL helyreállítási folyamattól függ. Az újraindítási idő csökkentése érdekében javasoljuk, hogy csökkentse a kiszolgálón előforduló tevékenységek mennyiségét az újraindítás előtt.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Kiszolgáló újraindítása

A következő lépések újraindítják a PostgreSQL-kiszolgálót:

1. A [Azure Portal](https://portal.azure.com/)válassza ki a Azure Database for PostgreSQL kiszolgálót.

2. A kiszolgáló **Áttekintés** lapjának eszköztárán kattintson az **Újraindítás**elemre.

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="Azure Database for PostgreSQL – áttekintés – Újraindítás gomb":::

3. A kiszolgáló újraindításának megerősítéséhez kattintson az **Igen** gombra.

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for PostgreSQL – újraindítás megerősítése":::

4. Figyelje meg, hogy a kiszolgáló állapota "újraindítás" értékűre változik.

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for PostgreSQL – újraindítás állapota":::

5. A kiszolgáló újraindításának megerősítése sikeres volt.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="Azure Database for PostgreSQL – sikeres újraindítás":::

## <a name="next-steps"></a>Következő lépések

Útmutató [Paraméterek beállításához a Azure Database for PostgreSQL](howto-configure-server-parameters-using-portal.md)