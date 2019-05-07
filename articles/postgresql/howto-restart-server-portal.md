---
title: Indítsa újra az Azure Database for postgresql-hez - Azure portal használatával egyetlen kiszolgáló
description: Ez a cikk bemutatja, hogyan újraindíthatja az Azure Database for PostgreSQL – egyetlen kiszolgáló az Azure portal használatával.
author: ajlam
ms.author: andrela
ms.service: postgresql
ms.topic: conceptual
ms.date: 5/6/2019
ms.openlocfilehash: fd92e27f53f52de3e9a7fd65d577c9dfea44991b
ms.sourcegitcommit: 0568c7aefd67185fd8e1400aed84c5af4f1597f9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2019
ms.locfileid: "65066839"
---
# <a name="restart-azure-database-for-postgresql---single-server-using-the-azure-portal"></a>Indítsa újra az Azure Database for PostgreSQL – egyetlen kiszolgáló az Azure portal használatával
Ez a témakör ismerteti, hogyan újraindítható egy Azure Database for PostgreSQL-kiszolgálóhoz. Indítsa újra a kiszolgálót, a kiszolgáló a műveletet hajt végre egy rövid kimaradás okozó karbantartási okokból szükségessé.

A kiszolgáló újraindítására, ha a szolgáltatás nem foglalt le lesz tiltva. Ha például a szolgáltatás feldolgozás lehet egy korábban kért művelet, például a virtuális magok méretezés.
 
Befejezéséhez újraindítás szükséges idő attól függ, hogy a PostgreSQL-helyreállítási folyamatot. Az újraindítás idő csökkentéséhez, javasoljuk, hogy a kiszolgálón, az újraindítás előtt előforduló tevékenység minimalizálására.

## <a name="prerequisites"></a>Előfeltételek
Ez az útmutató végrehajtásához lesz szüksége:
- Egy [, Azure Database for PostgreSQL-kiszolgáló](quickstart-create-server-database-portal.md)

## <a name="perform-server-restart"></a>Hajtsa végre a kiszolgáló újraindítása

Az alábbi lépéseket a PostgreSQL-kiszolgáló újraindítása:

1. Az a [az Azure portal](https://portal.azure.com/), válassza ki az Azure Database for PostgreSQL-kiszolgálóhoz.

2. Az eszköztáron a kiszolgáló **áttekintése** kattintson **indítsa újra a**.

   ![Azure Database for PostgreSQL – áttekintés – újraindítás gomb](./media/howto-restart-server-portal/2-server.png)

3. Kattintson a **Igen** annak ellenőrzéséhez, hogy a kiszolgáló újraindítása.

   ![Azure Database for PostgreSQL – Újraindítás megerősítése](./media/howto-restart-server-portal/3-restart-confirm.png)

4. Figyelje meg, hogy a kiszolgáló állapota "Újraindítás folyamatban".

   ![Azure Database for PostgreSQL – újraindítás állapota](./media/howto-restart-server-portal/4-restarting-status.png)

5. Ellenőrizze, hogy létrejött a kiszolgáló újraindítására.

   ![Azure Database for PostgreSQL - újraindítása sikerült](./media/howto-restart-server-portal/5-restart-success.png)

## <a name="next-steps"></a>További lépések

Ismerje meg [hogyan paraméterek beállítása az Azure Database for postgresql-hez](howto-configure-server-parameters-using-portal.md)