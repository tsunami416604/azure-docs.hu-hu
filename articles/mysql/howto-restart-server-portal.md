---
title: Indítsa újra az Azure Database for MySQL-kiszolgálóhoz az Azure portal használatával
description: Ez a cikk bemutatja, hogyan újraindítható egy Azure Database for MySQL-kiszolgálóhoz az Azure Portal használatával.
author: ajlam
ms.author: andrela
ms.service: mysql
ms.topic: conceptual
ms.date: 2/7/2019
ms.openlocfilehash: cd9275e9e112d24d9dbb3d1d5a60c871d7d8ae5c
ms.sourcegitcommit: e51e940e1a0d4f6c3439ebe6674a7d0e92cdc152
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/08/2019
ms.locfileid: "55890456"
---
# <a name="restart-azure-database-for-mysql-server-using-azure-portal"></a>Indítsa újra az Azure Database for MySQL-kiszolgálóhoz az Azure portal használatával
Ez a témakör ismerteti, hogyan újraindítható egy Azure Database for MySQL-kiszolgálóhoz. Indítsa újra a kiszolgálót, a kiszolgáló a műveletet hajt végre egy rövid kimaradás okozó karbantartási okokból szükségessé.

A kiszolgáló újraindítására, ha a szolgáltatás nem foglalt le lesz tiltva. Ha például a szolgáltatás feldolgozás lehet egy korábban kért művelet, például a virtuális magok méretezés.

Befejezéséhez újraindítás szükséges idő attól függ, hogy a MySQL-helyreállítási folyamatot. Az újraindítás idő csökkentéséhez, javasoljuk, hogy a kiszolgálón, az újraindítás előtt előforduló tevékenység minimalizálására.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató végrehajtásához lesz szüksége:
- Egy [, Azure Database for MySQL-kiszolgáló és adatbázis](quickstart-create-mysql-server-database-using-azure-portal.md)

## <a name="perform-server-restart"></a>Hajtsa végre a kiszolgáló újraindítása

Az alábbi lépéseket a MySQL-kiszolgáló újraindítása:

1. Az Azure Portalon válassza ki az Azure Database for MySQL-kiszolgálóhoz.

2. Az eszköztáron a kiszolgáló **áttekintése** kattintson **indítsa újra a**.

   ![Azure Database for MySQL – áttekintés – újraindítás gomb](./media/howto-restart-server-portal/2-server.png)

3. Kattintson a **Igen** annak ellenőrzéséhez, hogy a kiszolgáló újraindítása.

   ![Azure Database for MySQL - Újraindítás megerősítése ](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Figyelje meg, hogy a kiszolgáló állapota "Újraindítás folyamatban".

   ![Azure Database for MySQL - újraindítás állapota ](./media/howto-restart-server-portal/4-restarting-status.png)

5. Ellenőrizze, hogy létrejött a kiszolgáló újraindítására.

   ![Azure Database for MySQL - újraindítása sikerült ](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>További lépések

[Rövid útmutató: Hozzon létre az Azure Database for MySQL-kiszolgálóhoz az Azure portal használatával](./quickstart-create-mysql-server-database-using-azure-portal.md)