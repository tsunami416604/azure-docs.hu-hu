---
title: Kiszolgáló újraindítása - Azure portal – Azure Database for MySQL
description: Ez a cikk ismerteti, hogyan lehet újraindítani egy Azure Database for MySQL-kiszolgáló az Azure Portalhasználatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: d7e158124347b302492364df46ccf5b5e78e75c8
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80063297"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>A MySQL-kiszolgáló Azure-adatbázisának újraindítása az Azure Portal használatával
Ez a témakör azt ismerteti, hogyan indíthatja újra az Azure Database for MySQL-kiszolgálót. Előfordulhat, hogy karbantartási okokból újra kell indítania a kiszolgálót, ami rövid leállást okoz, mivel a kiszolgáló végrehajtja a műveletet.

A kiszolgáló újraindítása le lesz tiltva, ha a szolgáltatás foglalt. Például a szolgáltatás lehet, hogy egy korábban kért műveletet dolgoz fel, például a virtuális magok méretezése.

Az újraindítás befejezéséhez szükséges idő a MySQL helyreállítási folyamatátattól függ. Az újraindítási idő csökkentése érdekében azt javasoljuk, hogy minimalizálja a kiszolgálón az újraindítás előtt előforduló tevékenység mennyiségét.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató befejezéséhez a következőkre van szükség:
- [Azure-adatbázis a MySQL-kiszolgálóhoz](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Kiszolgáló újraindításának végrehajtása

A következő lépések újraindítják a MySQL szervert:

1. Az Azure Portalon válassza ki az Azure Database for MySQL-kiszolgáló.

2. A kiszolgáló **áttekintése** lap eszköztárán kattintson az **Újraindítás gombra.**

   ![Azure Database for MySQL – Áttekintés – Újraindítás gomb](./media/howto-restart-server-portal/2-server.png)

3. Kattintson az **Igen** gombra a kiszolgáló újraindításának megerősítéséhez.

   ![Azure Database for MySQL – Újraindítás megerősítése](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Figyelje meg, hogy a kiszolgáló állapota "Újraindítás" állapotra változik.

   ![Azure Database for MySQL – Újraindítás állapota](./media/howto-restart-server-portal/4-restarting-status.png)

5. Ellenőrizze, hogy a kiszolgáló újraindítása sikeres-e.

   ![Azure Database for MySQL – Sikeres újraindítás](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>További lépések

[Rövid útmutató: Azure-adatbázis létrehozása a MySQL-kiszolgálóhoz az Azure Portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)
