---
title: Kiszolgáló újraindítása - Azure portal - Azure Database for PostgreSQL - Single Server
description: Ez a cikk ismerteti, hogyan lehet újraindítani egy Azure Database for PostgreSQL - Single Server az Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: 52ffb3943e6e3f209fd236216cc44026dff59dad
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "74770084"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Az Azure Database for PostgreSQL újraindítása – Egyetlen kiszolgáló az Azure portal használatával
Ez a témakör azt ismerteti, hogyan indíthatja újra az Azure Database for PostgreSQL-kiszolgálót. Előfordulhat, hogy karbantartási okokból újra kell indítania a kiszolgálót, ami rövid leállást okoz, mivel a kiszolgáló végrehajtja a műveletet.

A kiszolgáló újraindítása le lesz tiltva, ha a szolgáltatás foglalt. Például a szolgáltatás lehet, hogy egy korábban kért műveletet dolgoz fel, például a virtuális magok méretezése.
 
Az újraindítás befejezéséhez szükséges idő a PostgreSQL helyreállítási folyamatátattól függ. Az újraindítási idő csökkentése érdekében azt javasoljuk, hogy minimalizálja a kiszolgálón az újraindítás előtt előforduló tevékenység mennyiségét.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató befejezéséhez a következőkre van szükség:
- [Egy Azure-adatbázis a PostgreSQL-kiszolgálóhoz](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Kiszolgáló újraindításának végrehajtása

A következő lépések újraindítják a PostgreSQL szervert:

1. Az [Azure Portalon](https://portal.azure.com/)válassza ki az Azure Database for PostgreSQL-kiszolgáló.

2. A kiszolgáló **áttekintése** lap eszköztárán kattintson az **Újraindítás gombra.**

   ![Azure Database for PostgreSQL – Áttekintés – Újraindítás gomb](./media/howto-restart-server-portal/2-server.png)

3. Kattintson az **Igen** gombra a kiszolgáló újraindításának megerősítéséhez.

   ![Azure Database for PostgreSQL – Újraindítás megerősítése](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Figyelje meg, hogy a kiszolgáló állapota "Újraindítás" állapotra változik.

   ![Azure Database for PostgreSQL – Újraindítás állapota](./media/howto-restart-server-portal/4-restarting-status.png)

5. Ellenőrizze, hogy a kiszolgáló újraindítása sikeres-e.

   ![Azure Database for PostgreSQL – Sikeres újraindítás](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>További lépések

További információ [a paraméterek beállításáról az Azure Database for PostgreSQL-ben](howto-configure-server-parameters-using-portal.md)