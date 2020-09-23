---
title: Kiszolgáló újraindítása – Azure Portal – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan lehet újraindítani egy Azure Database for MySQL kiszolgálót a Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: how-to
ms.date: 3/18/2020
ms.openlocfilehash: d885cc64eeebd4873ad5993b39b48845d1365c23
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90902757"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>Azure Database for MySQL kiszolgáló újraindítása Azure Portal használatával
Ez a témakör azt ismerteti, hogyan lehet újraindítani egy Azure Database for MySQL-kiszolgálót. Előfordulhat, hogy a kiszolgálót karbantartás miatt újra kell indítania, ami rövid kimaradást okoz, mivel a kiszolgáló végrehajtja a műveletet.

A kiszolgáló újraindítása le lesz tiltva, ha a szolgáltatás foglalt. Előfordulhat például, hogy a szolgáltatás feldolgoz egy korábban kért műveletet, például a skálázási virtuális mag.

Az újraindítás befejezéséhez szükséges idő a MySQL helyreállítási folyamattól függ. Az újraindítási idő csökkentése érdekében javasoljuk, hogy csökkentse a kiszolgálón előforduló tevékenységek mennyiségét az újraindítás előtt.

## <a name="prerequisites"></a>Előfeltételek
A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:
- Egy [Azure Database for MySQL-kiszolgáló](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Kiszolgáló újraindítása

A következő lépések újraindítják a MySQL-kiszolgálót:

1. A Azure Portal válassza ki a Azure Database for MySQL kiszolgálót.

2. A kiszolgáló **Áttekintés** lapjának eszköztárán kattintson az **Újraindítás**elemre.

   :::image type="content" source="./media/howto-restart-server-portal/2-server.png" alt-text="Azure Database for MySQL – áttekintés – Újraindítás gomb":::

3. A kiszolgáló újraindításának megerősítéséhez kattintson az **Igen** gombra.

   :::image type="content" source="./media/howto-restart-server-portal/3-restart-confirm.png" alt-text="Azure Database for MySQL – újraindítás megerősítése":::

4. Figyelje meg, hogy a kiszolgáló állapota "újraindítás" értékűre változik.

   :::image type="content" source="./media/howto-restart-server-portal/4-restarting-status.png" alt-text="Azure Database for MySQL – újraindítás állapota":::

5. A kiszolgáló újraindításának megerősítése sikeres volt.

   :::image type="content" source="./media/howto-restart-server-portal/5-restart-success.png" alt-text="Azure Database for MySQL – sikeres újraindítás":::

## <a name="next-steps"></a>Következő lépések

[Gyors útmutató: Azure Database for MySQL kiszolgáló létrehozása Azure Portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)
