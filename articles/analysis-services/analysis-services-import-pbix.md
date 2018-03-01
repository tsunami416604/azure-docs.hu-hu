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
ms.openlocfilehash: 43eab587a1e5209069a248f1e2e1f57af158a2b8
ms.sourcegitcommit: 088a8788d69a63a8e1333ad272d4a299cb19316e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/27/2018
---
# <a name="import-a-power-bi-desktop-file"></a>A Power BI Desktop-fájl importálása

Létrehozhat egy új modell az Azure-AS importálásával egy Power BI Desktop-fájl (pbix-fájlt). Modell metaadatait, a gyorsítótárazott adatokat és adatforrás-kapcsolatok importálása. Jelentések és a képi megjelenítések nincsenek importálva. Egyszer kiszolgálóját, a modell módosítható frissítése és importintézkedésekkel a pbix-fájlt, a portál webes designer (előzetes verzió) szolgáltatásának használatát, vagy SQL Server Management Studio (SSMS) használatával. Importált modellek nem megnyitható vagy Visual Studio exportált.

> [!NOTE]
> Ha a helyszíni adatforrások, csatlakozik a pbix-modell egy [helyszíni átjáró](analysis-services-gateway.md) kiszolgálóját konfigurálni kell.

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
