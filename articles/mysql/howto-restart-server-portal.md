---
title: Kiszolgáló újraindítása – Azure Portal – Azure Database for MySQL
description: Ez a cikk azt ismerteti, hogyan lehet újraindítani egy Azure Database for MySQL kiszolgálót a Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 12/02/2019
ms.openlocfilehash: 1857c67a77b9600f3d8f7c222a8e06f899fba728
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774051"
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

   ![Azure Database for MySQL – áttekintés – Újraindítás gomb](./media/howto-restart-server-portal/2-server.png)

3. A kiszolgáló újraindításának megerősítéséhez kattintson az **Igen** gombra.

   ![Azure Database for MySQL – újraindítás megerősítése](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Figyelje meg, hogy a kiszolgáló állapota "újraindítás" értékűre változik.

   ![Azure Database for MySQL – újraindítás állapota](./media/howto-restart-server-portal/4-restarting-status.png)

5. A kiszolgáló újraindításának megerősítése sikeres volt.

   ![Azure Database for MySQL – sikeres újraindítás](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>Következő lépések

[Gyors útmutató: Azure Database for MySQL kiszolgáló létrehozása Azure Portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)
