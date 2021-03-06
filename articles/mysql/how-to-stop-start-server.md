---
title: Leállítás/indítás – Azure Portal – Azure Database for MySQL kiszolgáló
description: Ez a cikk azt ismerteti, hogyan lehet leállítani/elindítani a műveleteket a Azure Database for MySQLban.
author: mksuni
ms.author: sumuth
ms.service: mysql
ms.topic: how-to
ms.date: 09/21/2020
ms.openlocfilehash: 047a24133dfdf68e2176b20cf31a871d11f0d4f9
ms.sourcegitcommit: 80034a1819072f45c1772940953fef06d92fefc8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/03/2020
ms.locfileid: "93241157"
---
# <a name="stopstart-an-azure-database-for-mysql"></a>Azure Database for MySQL leállítása/elindítása

> [!IMPORTANT]
> A Azure Database for MySQL funkcióinak leállítása/elindítása jelenleg nyilvános előzetes verzióban érhető el.

Ez a cikk részletesen ismerteti az önálló kiszolgáló leállításának és indításának menetét.

## <a name="prerequisites"></a>Előfeltételek

A útmutató lépéseinek elvégzéséhez a következőkre lesz szüksége:

-   Azure Database for MySQL egyetlen kiszolgálóval kell rendelkeznie.

> [!NOTE]
> Tekintse meg a [Leállítás/indítás](concepts-servers.md#limitations-of-stopstart-operation) használatának korlátozását

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-azure-portal"></a>A Azure Database for MySQL leállítása/elindítása Azure Portal használatával

### <a name="stop-a-running-server"></a>Futó kiszolgáló leállítása

1.  A [Azure Portal](https://portal.azure.com/)válassza ki a leállítani kívánt MySQL-kiszolgálót.

2.  Az **Áttekintés** lapon kattintson a **Leállítás** gombra az eszköztáron.

    :::image type="content" source="./media/howto-stop-start-server/mysql-stop-server.png" alt-text="Kiszolgáló Azure Database for MySQL leállítása":::

    > [!NOTE]
    > A kiszolgáló leállítása után a többi felügyeleti művelet nem érhető el az önálló kiszolgálóhoz.

### <a name="start-a-stopped-server"></a>Leállított kiszolgáló indítása

1.  A [Azure Portal](https://portal.azure.com/)válassza ki az elindítani kívánt egyetlen kiszolgálót.

2.  Az **Áttekintés** lapon kattintson a **Start** gombra az eszköztáron.

    :::image type="content" source="./media/howto-stop-start-server/mysql-start-server.png" alt-text="Kiszolgáló Azure Database for MySQL leállítása":::

    > [!NOTE]
    > A kiszolgáló elindítása után az összes felügyeleti művelet már elérhető az egyes kiszolgálókon.

## <a name="how-to-stopstart-the-azure-database-for-mysql-using-cli"></a>A Azure Database for MySQL leállítása/elindítása a parancssori felület használatával

### <a name="stop-a-running-server"></a>Futó kiszolgáló leállítása

1.  A [Azure Portal](https://portal.azure.com/)válassza ki a leállítani kívánt MySQL-kiszolgálót.

2.  Az **Áttekintés** lapon kattintson a **Leállítás** gombra az eszköztáron.

    ```azurecli-interactive
    az mysql server stop --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > A kiszolgáló leállítása után a többi felügyeleti művelet nem érhető el az önálló kiszolgálóhoz.

### <a name="start-a-stopped-server"></a>Leállított kiszolgáló indítása

1.  A [Azure Portal](https://portal.azure.com/)válassza ki az elindítani kívánt egyetlen kiszolgálót.

2.  Az **Áttekintés** lapon kattintson a **Start** gombra az eszköztáron.

    ```azurecli-interactive
    az mysql server start --name <server-name> -g <resource-group-name>
    ```
    > [!NOTE]
    > A kiszolgáló elindítása után az összes felügyeleti művelet már elérhető az egyes kiszolgálókon.

## <a name="next-steps"></a>Következő lépések
Útmutató [riasztások létrehozásához mérőszámokon](howto-alert-on-metric.md).
