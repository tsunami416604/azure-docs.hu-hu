---
title: 'Gyors útmutató: Horizontális felskálázás az Azure SQL Data Warehouse – Azure portal számítási |} A Microsoft Docs'
description: Azure SQL Data Warehouse-beli számítási bővítés az Azure Portalon. Felskálázással a számítások teljesítménye növelhető, leskálázással a költségek csökkenthetők.
services: sql-data-warehouse
author: kevinvngo
manager: craigg
ms.service: sql-data-warehouse
ms.topic: quickstart
ms.subservice: implement
ms.date: 04/17/2018
ms.author: kevin
ms.reviewer: jrasnick
ms.openlocfilehash: 2e3bb00cee679671ed382ee46690fc4dff8bddfb
ms.sourcegitcommit: 75fef8147209a1dcdc7573c4a6a90f0151a12e17
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/20/2019
ms.locfileid: "56453885"
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>Gyors útmutató: Az Azure SQL Data Warehouse számítási bővítés az Azure Portalon

Azure SQL Data Warehouse-beli számítási bővítés az Azure Portalon. [Felskálázással](sql-data-warehouse-manage-compute-overview.md) a számítások teljesítménye növelhető, leskálázással a költségek csökkenthetők. 

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="before-you-begin"></a>Előkészületek

Egy adattárház, amely már rendelkezik, vagy használjon méretezheti [a rövid útmutató: létrehozás és csatlakozás – portál](create-data-warehouse-portal.md) nevű adattárház létrehozásához **mySampleDataWarehouse**.  A jelen gyors útmutató a **mySampleDataWarehouse** nevű adattárház méretezését mutatja be.

>[!Note]
>Az adattárház méretezése online állapotban kell lennie. 

## <a name="scale-compute"></a>Számítások méretezése

Az SQL Data Warehouse számítási erőforrások növelésével vagy csökkentésével adattárházegységek skálázhatók. A [létrehozás és csatlakozás – portál] létrehozott quickstart(create-data-warehouse-portal.md) **mySampleDataWarehouse** és inicializálta azt 400 adattárházegységgel. Az alábbi lépésekkel módosíthatja a **mySampleDataWarehouse** adattárházban az adattárházegységek számát.

Az adattárházegységek számának módosításához:

1. Az Azure Portal bal oldali panelén kattintson az **SQL Data Warehouse-adattárházak** elemre.
2. Az **SQL adattárház** lapon jelölje ki a **mySampleDataWarehouse** elemet. Ekkor megnyílik az adattárház.
3. Kattintson **Méretezés** lehetőségre.

    ![Méretezés lehetőség választása](media/quickstart-scale-compute-portal/click-scale.png)

2. A Méretezés panelen húzza a csúszkát balra vagy jobbra az adattárházegységek (DWU-k) beállításának módosításához.

    ![Csúszka mozgatása](media/quickstart-scale-compute-portal/scale-dwu.png)

3. Kattintson a **Save** (Mentés) gombra. Ekkor megjelenik egy megerősítő üzenet. Kattintson az **igen** gombra a megerősítéshez vagy a **nem** gombra az elvetéshez.

    ![Kattintson a Save (Mentés) gombra.](media/quickstart-scale-compute-portal/confirm-change.png)



## <a name="next-steps"></a>További lépések
Most már az adattárház számítási bemutattuk. Ha bővebb információra van szüksége az Azure SQL Data Warehouse-zal kapcsolatban, folytassa az adatok betöltésével foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
>[Adatok betöltése az SQL Data Warehouse-okba](load-data-from-azure-blob-storage-using-polybase.md)
