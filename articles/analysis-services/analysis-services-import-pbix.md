---
title: Egy Power BI Desktop-fájl importálása az Azure Analysis Services |} A Microsoft Docs
description: Ismerteti, hogyan importálhatja a Power BI Desktop-fájl (pbix) Azure portal használatával.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 07/03/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 3dd90fc862e64812c0ba17bef74818d18788f4b5
ms.sourcegitcommit: 86cb3855e1368e5a74f21fdd71684c78a1f907ac
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/03/2018
ms.locfileid: "37440987"
---
# <a name="import-a-power-bi-desktop-file"></a>A Power BI Desktop-fájl importálása

Azure Analysis Services adatmodellt a Power BI Desktop-fájl (pbix) importálhatja. Modell metaadatainak, a gyorsítótárazott adatok és az adatforrás-kapcsolatok importálása. Jelentések és Vizualizációk nem lesznek importálva. Importált adatok a Power BI Desktopból modellek az 1400-as kompatibilitási szinten.

**Korlátozások**   
- A pbix-modell csatlakozhat **Azure SQL Database** és **Azure SQL Data Warehouse** csak adatforrásokhoz. 
- A pbix-modell nem lehet élő vagy DirectQuery-kapcsolatok. 
- Ha a pbix-adatmodellt tartalmaz, az Analysis Services nem támogatja a metaadatok meghiúsulhat.

## <a name="to-import-from-pbix"></a>Pbix-fájlt importálása

1. A kiszolgáló **áttekintése** > **webes Tervező**, kattintson a **nyílt**.

    ![Modell létrehozása az Azure Portalon](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. A **webes Tervező** > **modellek**, kattintson a **+ Hozzáadás**.

    ![Modell létrehozása az Azure Portalon](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. A **új modell**, írja be a modell neve, és válassza a Power BI Desktop-fájlt.

    ![Az Azure portal az új modell párbeszédpanel](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. A **importálás**, keresse meg és válassza ki a fájlt.

     ![Csatlakozás az Azure portal párbeszédpanel](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="see-also"></a>Lásd még

[Modell létrehozása az Azure Portalon](analysis-services-create-model-portal.md)   
[Csatlakozás az Azure Analysis Services](analysis-services-connect.md)  
