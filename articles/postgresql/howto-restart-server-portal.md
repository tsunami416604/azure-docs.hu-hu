---
title: Kiszolgáló újraindítása – Azure Portal-Azure Database for PostgreSQL – egyetlen kiszolgáló
description: Ez a cikk azt ismerteti, hogyan lehet újraindítani egy Azure Database for PostgreSQL-egy kiszolgálót a Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 52ffb3943e6e3f209fd236216cc44026dff59dad
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "74770084"
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

   ![Azure Database for PostgreSQL – áttekintés – Újraindítás gomb](./media/howto-restart-server-portal/2-server.png)

3. A kiszolgáló újraindításának megerősítéséhez kattintson az **Igen** gombra.

   ![Azure Database for PostgreSQL – újraindítás megerősítése](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Figyelje meg, hogy a kiszolgáló állapota "újraindítás" értékűre változik.

   ![Azure Database for PostgreSQL – újraindítás állapota](./media/howto-restart-server-portal/4-restarting-status.png)

5. A kiszolgáló újraindításának megerősítése sikeres volt.

   ![Azure Database for PostgreSQL – sikeres újraindítás](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>További lépések

Útmutató [Paraméterek beállításához a Azure Database for PostgreSQL](howto-configure-server-parameters-using-portal.md)