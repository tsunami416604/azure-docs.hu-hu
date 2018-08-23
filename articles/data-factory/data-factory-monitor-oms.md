---
title: Az OMS-SZEL az Azure Data Factory monitorozása |} A Microsoft Docs
description: Ismerje meg, hogyan figyelheti az Azure Data Factory útválasztási adatokat az Operations Management Suite (OMS) elemzés céljából.
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 08/20/2018
ms.author: douglasl
ms.openlocfilehash: fb5da75d0e42dd0700cad008d348ff846d602409
ms.sourcegitcommit: 3f8f973f095f6f878aa3e2383db0d296365a4b18
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/20/2018
ms.locfileid: "42055749"
---
# <a name="monitor-azure-data-factory-with-operations-management-suite-oms"></a>A figyelő az Azure Data Factory az Operations Management Suite (OMS)

Használhatja az Azure Data Factory integrációs és az Azure Monitor adatátirányításhoz az Operations Management Suite (OMS). Ez az integráció a következő esetekben hasznos:

1.  A Data Factory az OMS által közzétett mérőszámok széles választékának összetett lekérdezéseket írni szeretne. Ezeket a lekérdezéseket, OMS-n keresztül az egyéni riasztásokat is létrehozhat.

2.  Adat-előállítók különböző figyelni kívánt. A több adat-előállítók adatok irányíthatja egy OMS-munkaterülethez.

A 7 perces bevezető és a funkció bemutatójáért tekintse meg a következő videót:

> [!VIDEO https://channel9.msdn.com/Shows/Azure-Friday/Monitor-Data-Factory-pipelines-using-Operations-Management-Suite-OMS/player]

## <a name="get-started"></a>Első lépések

### <a name="configure-diagnostic-settings-and-oms-workspace"></a>Diagnosztikai beállítások és az OMS-munkaterület konfigurálása

Diagnosztikai beállítások engedélyezése a data Factory.

1.  Válassza ki **Azure Monitor** -> **diagnosztikai beállítások** -> Válassza ki a data factory -> kapcsolja be a diagnosztikát.

    ![a figyelő-oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  Adja meg a diagnosztikai beállítások, beleértve a az OMS-munkaterületet.

    ![a figyelő-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-oms-pack-from-azure-marketplace"></a>Azure Data Factory Analytics OMS-csomag telepítése az Azure Marketplace-ről

![a figyelő-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![a figyelő-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Kattintson a **létrehozás** , és válassza az OMS-munkaterület és az OMS-munkaterület beállításai.

![a figyelő-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

## <a name="monitor-azure-data-factory-metrics-using-oms"></a>A figyelő az Azure Data Factory Metrikái OMS használatával

Telepíti a **az Azure Data Factory Analytics** OMS csomagot létrehozza az alapértelmezett készletét, nézetek, amely lehetővé teszi a következő metrikákat:

- A Folyamatfuttatások ADF futtatások-1) az adat-előállítók által

- ADF-futtatások-2) tevékenység-végrehajtás a Data Factory által

- Adat-előállítók által eseményindító-futtatások ADF futtatások-3)

- ADF-hibák-1) első 10 folyamatot hibák adat-előállítók által

- ADF-hibák-2) első 10 tevékenység-végrehajtás a Data Factory által

- ADF-hibák-3) első 10 eseményindító hibák adat-előállítók által

- Tevékenység-végrehajtás statisztikai ADF-1) típus szerint

- Eseményindító-futtatások ADF statisztika-2) típus szerint

- ADF-statisztikák-3) maximális Folyamatfuttatások időtartama

![a figyelő-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![a figyelő-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Megjelenítheti a fenti metrikák, tekintse meg a lekérdezések mögötti ezeket a metrikákat, a lekérdezések szerkesztése, riasztásokat hozhat létre, és így tovább.

![a figyelő-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="next-steps"></a>További lépések

Lásd: [figyelő programozással felügyelheti a folyamatokat és](https://docs.microsoft.com/en-us/azure/data-factory/monitor-programmatically) figyelése és kezelése a folyamatok parancsprogramok futtatásával kapcsolatos.
