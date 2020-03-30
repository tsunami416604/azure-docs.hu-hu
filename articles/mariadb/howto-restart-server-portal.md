---
title: Kiszolgáló újraindítása – Azure portal – Azure-adatbázis a MariaDB-hez
description: Ez a cikk ismerteti, hogyan indíthatja újra az Azure Database for MariaDB-kiszolgálóaz Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: mariadb
ms.topic: conceptual
ms.date: 3/18/2020
ms.openlocfilehash: ed95304807a1a03880cc824c1a58f010203d418d
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79534711"
---
# <a name="restart-azure-database-for-mariadb-server-using-azure-portal"></a>A MariaDB-kiszolgáló Azure-adatbázisának újraindítása az Azure Portal használatával
Ez a témakör azt ismerteti, hogyan indíthatja újra a MariaDB-kiszolgálóazure-adatbázisát. Előfordulhat, hogy karbantartási okokból újra kell indítania a kiszolgálót, ami rövid leállást okoz, mivel a kiszolgáló végrehajtja a műveletet.

A kiszolgáló újraindítása le lesz tiltva, ha a szolgáltatás foglalt. Például a szolgáltatás lehet, hogy egy korábban kért műveletet dolgoz fel, például a virtuális magok méretezése.

Az újraindítás befejezéséhez szükséges idő a MariaDB helyreállítási folyamatától függ. Az újraindítási idő csökkentése érdekében azt javasoljuk, hogy minimalizálja a kiszolgálón az újraindítás előtt előforduló tevékenység mennyiségét.

## <a name="prerequisites"></a>Előfeltételek
Az útmutató befejezéséhez a következőkre van szükség:
- [Azure-adatbázis a MariaDB-kiszolgálóhoz](./quickstart-create-mariadb-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Kiszolgáló újraindításának végrehajtása

A következő lépések újraindítják a MariaDB kiszolgálót:

1. Az Azure Portalon válassza ki az Azure Database for MariaDB-kiszolgálót.

2. A kiszolgáló **áttekintése** lap eszköztárán kattintson az **Újraindítás gombra.**

   ![Azure Database for MariaDB – Áttekintés – Újraindítás gomb](./media/howto-restart-server-portal/2-server.png)

3. Kattintson az **Igen** gombra a kiszolgáló újraindításának megerősítéséhez.

   ![Azure Database for MariaDB – Újraindítás megerősítése](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Figyelje meg, hogy a kiszolgáló állapota "Újraindítás" állapotra változik.

   ![Azure Database for MariaDB – Újraindítás állapota](./media/howto-restart-server-portal/4-restarting-status.png)

5. Ellenőrizze, hogy a kiszolgáló újraindítása sikeres-e.

   ![Azure Database for MariaDB – Sikeres újraindítás](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>További lépések

[Rövid útmutató: Azure-adatbázis létrehozása a MariaDB-kiszolgálóhoz az Azure Portal használatával](./quickstart-create-mariadb-server-database-using-azure-portal.md)