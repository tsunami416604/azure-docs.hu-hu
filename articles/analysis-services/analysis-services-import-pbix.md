---
title: A Power BI Desktop fájlt importálja az Azure Analysis Services |} Microsoft Docs
description: Ismerteti, hogyan importálhatja a Power BI Desktop-fájl (pbix-fájlt) az Azure portál használatával.
author: minewiskan
manager: kfile
ms.service: azure-analysis-services
ms.topic: conceptual
ms.date: 05/22/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: aea6f3efcf3740527c43b75a30caadf6b2a8b623
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "34601071"
---
# <a name="import-a-power-bi-desktop-file"></a>A Power BI Desktop-fájl importálása

Azure Analysis Services egy adatmodell a Power BI Desktop-fájl (pbix-fájlt) importálhatja. Modell metaadatait, a gyorsítótárazott adatokat és adatforrás-kapcsolatok importálása. Jelentések és a képi megjelenítések nincsenek importálva. Importálta az adatokat a Power BI Desktop modellek vannak a 1 400 kompatibilitási szinten.

**Korlátozások**   
- A pbix-modell csatlakozni tud-e **Azure SQL Database** és **Azure SQL Data Warehouse** adatforrások csak. 
- A pbix-modell nem lehet élő vagy DirectQuery kapcsolatok. 
- Importálás sikertelen lehet, ha a pbix-fájlt adatmodell Analysis Services nem támogatja a metaadatokat tartalmaz.

## <a name="to-import-from-pbix"></a>Pbix-fájlt importálása

1. A Server **áttekintése** > **webes Tervező**, kattintson a **nyitott**.

    ![A modell létrehozása Azure-portálon](./media/analysis-services-create-model-portal/aas-create-portal-overview-wd.png)

2. A **webes Tervező** > **modellek**, kattintson a **+ Hozzáadás**.

    ![A modell létrehozása Azure-portálon](./media/analysis-services-create-model-portal/aas-create-portal-models.png)

3. A **új modell**, írja be a modell neve, majd válassza ki a Power BI Desktop-fájlba.

    ![Azure-portálon az új modell párbeszédpanel](./media/analysis-services-import-pbix/aas-import-pbix-new-model.png)

4. A **importálási**, keresse meg és jelölje ki a fájlt.

     ![Csatlakozás Azure-portálon párbeszédpanel](./media/analysis-services-import-pbix/aas-import-pbix-select-file.png)

## <a name="see-also"></a>Lásd még

[A modell létrehozása Azure-portálon](analysis-services-create-model-portal.md)   
[Kapcsolódás Azure Analysis Services](analysis-services-connect.md)  
