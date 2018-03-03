---
title: "A Power BI Desktop fájlt importálja az Azure Analysis Services |} Microsoft Docs"
description: "Ismerteti, hogyan importálhatja a Power BI Desktop-fájl (pbix-fájlt) az Azure portál használatával."
services: analysis-services
documentationcenter: 
author: minewiskan
manager: kfile
editor: 
tags: 
ms.assetid: 
ms.service: analysis-services
ms.devlang: NA
ms.topic: article
ms.tgt_pltfrm: NA
ms.workload: na
ms.date: 02/26/2018
ms.author: owend
ms.openlocfilehash: e0be0c69b501d7e93c65bcf23d4dd1b6bfa89caf
ms.sourcegitcommit: 782d5955e1bec50a17d9366a8e2bf583559dca9e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/02/2018
---
# <a name="import-a-power-bi-desktop-file"></a>A Power BI Desktop-fájl importálása

Létrehozhat egy új modell az Azure-AS importálásával egy Power BI Desktop-fájl (pbix-fájlt). Modell metaadatait, a gyorsítótárazott adatokat és adatforrás-kapcsolatok importálása. Jelentések és a képi megjelenítések nincsenek importálva.

**Korlátozások**   
- A pbix-modell csatlakoztatni kell [Analysis Services támogatott adatforrások](analysis-services-datasource.md) csak. 
- A pbix-modell nem lehet élő vagy DirectQuery kapcsolatok. 
- Ha a helyszíni adatforrások, csatlakozik a pbix-modell egy [helyszíni adatátjáró](analysis-services-gateway.md) az Analysis Services-kiszolgálónak kell konfigurálni.
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
