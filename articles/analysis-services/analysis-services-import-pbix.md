---
title: A Power BI Desktop fájlt importálja az Azure Analysis Services |} Microsoft Docs
description: Ismerteti, hogyan importálhatja a Power BI Desktop-fájl (pbix-fájlt) az Azure portál használatával.
author: minewiskan
manager: kfile
ms.service: analysis-services
ms.topic: conceptual
ms.date: 04/12/2018
ms.author: owend
ms.reviewer: minewiskan
ms.openlocfilehash: 35bf2ba85017de43788f802b6244d61ed2bb62df
ms.sourcegitcommit: 9cdd83256b82e664bd36991d78f87ea1e56827cd
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2018
---
# <a name="import-a-power-bi-desktop-file"></a>A Power BI Desktop-fájl importálása

Létrehozhat egy új modell az Azure-AS importálásával egy Power BI Desktop-fájl (pbix-fájlt). Modell metaadatait, a gyorsítótárazott adatokat és adatforrás-kapcsolatok importálása. Jelentések és a képi megjelenítések nincsenek importálva.

**Korlátozások**   
- A pbix-fájlt modellt az Azure SQL Database és az Azure SQL Data Warehouse adatforrások csak csatlakozhat. 
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
