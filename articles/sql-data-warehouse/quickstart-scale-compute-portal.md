---
title: "Gyors üzembe helyezés: Horizontális felskálázás az Azure SQL Data Warehouse - Azure-portálon számítási |} Microsoft Docs"
description: "Az Azure portál feladatok kezelésére számítási teljesítményt. Skála dwu-k beállításával számítási erőforrásokat. Vagy, és sablonok felfüggesztése és folytatása a számítási erőforrásokat költségek csökkentése érdekében."
services: sql-data-warehouse
documentationcenter: NA
author: hirokib
manager: jhubbard
editor: 
ms.service: sql-data-warehouse
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: data-services
ms.custom: manage
ms.date: 01/31/2018
ms.author: elbutter;barbkess
ms.openlocfilehash: cbe2f2d17f309e01e831aa9ee31e01e044896d10
ms.sourcegitcommit: eeb5daebf10564ec110a4e83874db0fb9f9f8061
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/03/2018
---
# <a name="quickstart-scale-compute-in-azure-sql-data-warehouse-in-the-azure-portal"></a>Gyors üzembe helyezés: Méretezési számítási az Azure SQL Data Warehouse az Azure-portálon

Az Azure-portálon az Azure SQL Data Warehouse számítás méretezése. A jobb teljesítmény számítási kibővítési vagy skálája vissza számítási költségek csökkentése érdekében. 

Ha nem rendelkezik Azure-előfizetéssel, első lépésként mindössze néhány perc alatt létrehozhat egy [ingyenes](https://azure.microsoft.com/free/) fiókot.

## <a name="sign-in-to-the-azure-portal"></a>Jelentkezzen be az Azure Portalra

Jelentkezzen be az [Azure Portalra](https://portal.azure.com/).

## <a name="before-you-begin"></a>Előkészületek

Amely már, vagy használjon adatraktár méretezheti [gyors üzembe helyezés: hozzon létre, és csatlakozzon - portál](create-data-warehouse-portal.md) nevű data warehouse létrehozása az **mySampleDataWarehouse**.  A gyors üzembe helyezés méretezi **mySampleDataWarehouse**.

## <a name="scale-compute"></a>Skála számítási

Az SQL Data Warehouse növelheti vagy csökkentheti a számítási erőforrásokat adattárházegységek beállításával. A [létrehozása és a Connect - portál](create-data-warehouse-portal.md) létrehozott **mySampleDataWarehouse** és 400 dwu-k inicializálása azt. Az alábbi lépéseket, állítsa be a dwu-k a **mySampleDataWarehouse**.

Adattárházegységek módosítása:

1. Kattintson a **SQL-adatbázisok** az Azure portál bal oldalán.
2. Válassza ki **mySampleDataWarehouse** a a **SQL-adatbázisok** lap. Megnyitja az adatraktárba.
3. Kattintson a **méretezési**.

    ![Kattintson a Scale](media/quickstart-scale-compute-portal/click-scale.png)

2. A skála panelen mozgassa a csúszkát balra vagy jobbra a DWU-beállítást.

    ![Csúszka](media/quickstart-scale-compute-portal/scale-dwu.png)

3. Kattintson a **Save** (Mentés) gombra. Egy megerősítő üzenet jelenik meg. Kattintson a **Igen** megerősítéséhez vagy **nem** megszakítja a műveletet.

    ![Kattintson a Save (Mentés) gombra.](media/quickstart-scale-compute-portal/confirm-change.png)



## <a name="next-steps"></a>További lépések
Most megtanulhatta, az adatraktár számítási méretezése. Ha bővebb információra van szüksége az Azure SQL Data Warehouse-zal kapcsolatban, folytassa az adatok betöltésével foglalkozó oktatóanyaggal.

> [!div class="nextstepaction"]
>[Adatok betöltése az SQL Data Warehouse-okba](load-data-from-azure-blob-storage-using-polybase.md)
