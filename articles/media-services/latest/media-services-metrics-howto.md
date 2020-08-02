---
title: Metrikák megtekintése Azure Monitor
description: Ez a cikk bemutatja, hogyan figyelheti a metrikákat a Azure Portal-diagramokkal és az Azure CLI-vel.
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
ms.custom: devx-track-azurecli
ms.openlocfilehash: 154e5b5d9639203810e9d16dec4e2907fe5ee80a
ms.sourcegitcommit: 11e2521679415f05d3d2c4c49858940677c57900
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/31/2020
ms.locfileid: "87504296"
---
# <a name="monitor-media-services-metrics"></a>A Media Services metrikáinak monitorozása

[Azure monitor](../../azure-monitor/overview.md) lehetővé teszi a metrikák és diagnosztikai naplók figyelését, amelyek segítenek megérteni az alkalmazások teljesítményét. A funkció részletes ismertetését és a Azure Media Services metrikák és diagnosztikai naplók használatának okát lásd: [Media Services metrikák és diagnosztikai naplók figyelése](media-services-metrics-diagnostic-logs.md).

A Azure Monitor számos lehetőséget kínál a metrikák használatára, például a portálon való ábrázolásra, a REST API való hozzáférésre, vagy az Azure CLI használatával történő lekérdezésre. Ez a cikk bemutatja, hogyan figyelheti a metrikákat a Azure Portal-diagramokkal és az Azure CLI-vel.

## <a name="prerequisites"></a>Előfeltételek

- [Media Services-fiók létrehozása](./create-account-howto.md)
- [Figyelő Media Services mérőszámok és diagnosztikai naplók](media-services-metrics-diagnostic-logs.md) áttekintése

## <a name="view-metrics-in-azure-portal"></a>Metrikák megtekintése Azure Portal

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
1. Navigáljon a Azure Media Services-fiókjához, és válassza a **metrikák**lehetőséget.
1. Kattintson az **erőforrás** mezőre, és válassza ki azt az erőforrást, amelynek a metrikáit figyelni szeretné.

    Az **erőforrás kiválasztása** ablak jobb oldalán megjelenik az elérhető erőforrások listája. Ebben az esetben a következőket látja:

    * &lt;Media Services fiók neve&gt;
    * &lt;Media Services fiók neve &gt; / &lt; streaming Endpoint neve&gt;
    * &lt;Storage-fiók neve&gt;

    Válassza ki az erőforrást, és nyomja le az **alkalmaz**gombot. A támogatott erőforrásokkal és mérőszámokkal kapcsolatos részletekért lásd: [Media Services mérőszámok figyelése](media-services-metrics-diagnostic-logs.md).

    ![Mérőszámok](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > Ha váltani szeretne a metrikákat figyelő erőforrások között, kattintson ismét az **erőforrás** mezőre, és ismételje meg ezt a lépést.
1. (Opcionálisan) adjon nevet a diagramnak (szerkessze a nevet a tetején lévő ceruza megnyomásával).
1. Adja meg a megtekinteni kívánt metrikákat.

    ![Mérőszámok](media/media-services-metrics/metrics03.png)
1. A diagramot rögzítheti az irányítópulton.

## <a name="view-metrics-with-azure-cli"></a>Metrikák megtekintése az Azure CLI-vel

Az Azure CLI-vel való "kimenő" metrikák eléréséhez futtassa a következő `az monitor metrics` parancsot:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Más mérőszámok beszerzéséhez helyettesítse be a "kimenő forgalom" kifejezést a metrika neve érdekli.

## <a name="see-also"></a>További információ

* [Azure Monitor metrikák](../../azure-monitor/platform/data-platform.md)
* [Metrikai riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../../azure-monitor/platform/alerts-metric.md).

## <a name="next-steps"></a>További lépések

[Diagnosztikai naplók](media-services-diagnostic-logs-howto.md)
