---
title: Az Azure Monitor metrikáinak megtekintése
description: Ez a cikk bemutatja az Azure portal diagramok és az Azure CLI-metrikák figyelése.
services: media-services
documentationcenter: ''
author: Juliako
manager: femila
editor: ''
ms.service: media-services
ms.workload: media
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 07/08/2019
ms.author: juliako
ms.openlocfilehash: d331dc4eb0c6668426e1ab1a01a1dd1dcebe0c85
ms.sourcegitcommit: 66237bcd9b08359a6cce8d671f846b0c93ee6a82
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67795833"
---
# <a name="monitor-media-services-metrics"></a>A Media Services-metrikák figyelése 

[Az Azure Monitor](../../azure-monitor/overview.md) lehetővé teszi, hogy a figyelő metrikák és diagnosztikai naplók, amelyek segítenek megérteni, hogyan az alkalmazások hajt végre. Ez a részletes ismertetését a funkciót, és miért érdemes az Azure Media Services-metrikák és diagnosztikai naplók megtekintéséhez lásd: [figyelő Media Services-metrikák és diagnosztikai naplók](media-services-metrics-diagnostic-logs.md).

Az Azure Monitor használatával kommunikálhat a metrikák, beleértve a diagramkészítési őket a portálon, a hozzájuk férni a REST API-n keresztül vagy a lekérdezési őket több módot nyújt parancssori felület használatával. Ez a cikk bemutatja az Azure portal diagramok és az Azure CLI-metrikák figyelése.

## <a name="prerequisites"></a>Előfeltételek

- [A Media Services-fiók létrehozása](create-account-cli-how-to.md)
- Felülvizsgálat [figyelő Media Services-metrikák és diagnosztikai naplók](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Metrikák megtekintése az Azure Portalon

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
1. Az Azure Media Services-fiók keresse meg és válassza **metrikák**.
1. Kattintson a **erőforrás** mezőbe, majd válassza ki az erőforrást, amelynek meg szeretné figyelheti a mérőszámokat. 

    A **válasszon ki egy erőforrást** ablak jelenik meg, hogy a rendelkezésre álló erőforrások listáját a jobb oldalon. Ebben az esetben azt láthatja 

    * &lt;A Media Services-fiók neve&gt;
    * &lt;A Media Services-fiók neve&gt;/&lt;adatfolyam-végpont neve&gt;
    * &lt;Tárfiók neve&gt;

    Válassza ki az erőforrást, majd kattintson **alkalmaz**. További információk a támogatott erőforrások és a metrikák: [figyelő Media Services-metrikák](media-services-metrics-diagnostic-logs.md).
 
    ![Mérőszámok](media/media-services-metrics/metrics02.png)
    
    > [!NOTE]
    > Erőforrások, amelynek meg szeretné figyelheti a mérőszámokat közötti váltáshoz kattintson a a **erőforrás** újra mezőbe, majd ismételje meg ezt a lépést.
1. (Opcionális) nevezze el a diagram (Szerkesztés a nevét, az oldal tetején a Ceruza billentyű lenyomásával).
1. Adja hozzá a megtekinteni kívánt mérőszámokat.

    ![Mérőszámok](media/media-services-metrics/metrics03.png)
1. A diagram rögzítheti az irányítópulton.

## <a name="view-metrics-with-azure-cli"></a>Metrikák megtekintése az Azure CLI-vel

CLI-vel "Kimenő" metrikák beolvasásához a következőt futtatná `az monitor metrics` parancssori felületi parancsot:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Más metrikák beolvasásához cserélje le az Önt érdeklő metrika neve "Kimenő".

## <a name="see-also"></a>Lásd még

* [Az Azure Monitor-metrikák](../../azure-monitor/platform/data-platform.md)
* [Létrehozása, megtekintése és kezelése az Azure Monitor használatával metrikákhoz kapcsolódó riasztások](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>További lépések

[Diagnosztikai naplók](media-services-diagnostic-logs-howto.md)
