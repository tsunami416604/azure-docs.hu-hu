---
title: Az Azure Data Factory az OMS figyelése |} Microsoft Docs
description: 'Útmutató: Azure Data Factory figyelje az útválasztási adatok Operations Management Suite (OMS) elemzés céljából.'
services: data-factory
documentationcenter: ''
author: douglaslMS
manager: craigg
ms.service: data-factory
ms.workload: data-services
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: conceptual
ms.date: 06/20/2018
ms.author: douglasl
ms.openlocfilehash: 4275a4ddcee51d58949b5bd83e4a898cb3dbb389
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36304422"
---
# <a name="monitor-azure-data-factory-with-operations-management-suite-oms"></a>A figyelő az Azure Data Factory Operations Management Suite (OMS)

Azure Data Factory integrációs használatával Azure figyelő útvonal adatai Operations Management Suite (OMS). Ez az integráció akkor hasznos, a következő esetekben:

1.  A Data Factory az OMS Szolgáltatáshoz által közzétett metrikák széles skáláját összetett lekérdezéseket írhat szeretné. Ezeket a lekérdezéseket, OMS keresztül is létrehozhat egyéni riasztások.

2.  Adat-előállítók között figyelni kívánt. A több adat-előállítók adatok irányíthatja a egyetlen OMS-munkaterülethez.

## <a name="get-started"></a>Első lépések

### <a name="configure-diagnostic-settings-and-oms-workspace"></a>A diagnosztikai beállításokat és az OMS-munkaterület

A data factory diagnosztikai beállítások engedélyezése.

1.  Válassza ki **Azure figyelő** -> **diagnosztikai beállítások** -> válassza a data factory -> kapcsolja a diagnosztikát.

    ![a figyelő-oms-image1.png](media/data-factory-monitor-oms/monitor-oms-image1.png)

2.  Adja meg például az OMS-munkaterület konfiguráció diagnosztikai beállításait.

    ![a figyelő-oms-image2.png](media/data-factory-monitor-oms/monitor-oms-image2.png)

### <a name="install-azure-data-factory-analytics-oms-pack-from-azure-marketplace"></a>Telepítse az Azure Data Factory Analytics OMS csomagot az Azure piactérről

![a figyelő-oms-image3.png](media/data-factory-monitor-oms/monitor-oms-image3.png)

![a figyelő-oms-image4.png](media/data-factory-monitor-oms/monitor-oms-image4.png)

Kattintson a **létrehozása** válassza ki azt az OMS-munkaterület és az OMS-munkaterület beállításait.

![a figyelő-oms-image5.png](media/data-factory-monitor-oms/monitor-oms-image5.png)

## <a name="monitor-azure-data-factory-metrics-using-oms"></a>Azure Data Factory metrikát OMS használatával

Telepíti a **Azure Data Factory Analytics** OMS-csomagot hoz létre alapértelmezett nézeteket, amely lehetővé teszi a következő metrikákat:

- Adat-előállító által ADF futtatása-1) folyamat fut

- Adat-előállító által ADF futtatása-2) tevékenység fut

- Adat-előállító által ADF futtatása-3) eseményindító fut.

- ADF hibák-1) első 10 folyamatot hibák által adat-előállító

- Hibák az ADF-2-es) első 10 tevékenység fut adat-előállító által

- ADF hibák-3) első 10 indítási hibák által adat-előállító

- Típus szerint ADF statisztika-1) tevékenység fut

- Statisztika ADF-2-es) eseményindító futtatása típus szerint

- ADF statisztika-3) a maximális feldolgozási folyamat fut időtartama

![a figyelő-oms-image6.png](media/data-factory-monitor-oms/monitor-oms-image6.png)

![a figyelő-oms-image7.png](media/data-factory-monitor-oms/monitor-oms-image7.png)

Ábrázolhatja a fenti metrikák, nézze meg a lekérdezések metrikákat mögött, a lekérdezések szerkesztése, létre riasztásokat, és így tovább.

![a figyelő-oms-image8.png](media/data-factory-monitor-oms/monitor-oms-image8.png)

## <a name="next-steps"></a>További lépések

Lásd: [figyelő programozott folyamatok kezelését és](https://docs.microsoft.com/en-us/azure/data-factory/monitor-programmatically) figyelése és folyamatok kezelése parancsfájlok futtatásával.
