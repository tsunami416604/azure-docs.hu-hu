---
title: 'Gyors útmutató: számítási kapacitás méretezése – Azure Portal '
description: A számítási kapacitás méretezése az SQL-készletben a Azure Portalban. Horizontális felskálázással a számítások teljesítménye növelhető, szűkítéssel a költségek csökkenthetők.
services: sql-data-warehouse
author: Antvgski
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: anvang
ms.reviewer: jrasnick
ms.custom: seo-lt-2019, azure-synapse
ms.openlocfilehash: 7463849223fdf81466237c7d0c912763988e80e6
ms.sourcegitcommit: 225a0b8a186687154c238305607192b75f1a8163
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/29/2020
ms.locfileid: "78200343"
---
# <a name="quickstart-scale-compute-in-azure-synapse-analytics-sql-pool-in-the-azure-portal"></a>Gyors útmutató: a számítási műveletek méretezése az Azure szinapszis Analytics SQL-készletben a Azure Portal

A számítási kapacitás méretezése az SQL-készletben a Azure Portalban. [Bővítéssel](sql-data-warehouse-manage-compute-overview.md) a számítások teljesítménye növelhető, szűkítéssel a költségek csökkenthetők. 

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portal](https://portal.azure.com/).

## <a name="before-you-begin"></a>Előkészületek

A már meglévő SQL-készleteket méretezheti, vagy a **mySampleDataWarehouse**nevű SQL-készlet létrehozásához használja a következőt [: Create and csatlakozni-Portal](create-data-warehouse-portal.md) .  A jelen gyors útmutató a **mySampleDataWarehouse** nevű adattárház méretezését mutatja be.

>[!Note]
>Az SQL-készletnek online állapotban kell lennie a skálázáshoz. 

## <a name="scale-compute"></a>Számítások méretezése

Az SQL-készlet számítási erőforrásai méretezhetők az adatraktár-egységek növelésével vagy csökkentésével. A [Create and összekapcsolás-Portal] rövid útmutató (create-adatraktár-portal.md) létrehozta a **mySampleDataWarehouse** , és inicializálta azt 400 DWU. Az alábbi lépésekkel módosíthatja a **mySampleDataWarehouse** adattárházban az adattárházegységek számát.

Az adattárházegységek számának módosításához:

1. Az Azure Portal bal oldalán kattintson az **Azure szinapszis Analytics (korábban SQL DW)** elemre.
2. Válassza a **mySampleDataWarehouse** lehetőséget az **Azure szinapszis Analytics (korábban SQL DW)** lapon. Megnyílik az SQL-készlet.
3. Kattintson **Méretezés** lehetőségre.

    ![Méretezés lehetőség választása](media/quickstart-scale-compute-portal/click-scale.png)

2. A Méretezés panelen húzza a csúszkát balra vagy jobbra az adattárházegységek (DWU-k) beállításának módosításához. Ezután válassza a skála lehetőséget.

    ![Csúszka mozgatása](media/quickstart-scale-compute-portal/scale-dwu.png)

## <a name="next-steps"></a>További lépések
Most már megtanulta az SQL-készlet számítási felskálázását. Ha többet szeretne megtudni az SQL-készletről, folytassa a betöltési oktatóanyaggal.

> [!div class="nextstepaction"]
>[Betöltés az SQL-készletbe](load-data-from-azure-blob-storage-using-polybase.md)
