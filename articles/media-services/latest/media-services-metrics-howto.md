---
title: Metrikák megtekintése az Azure Monitor segítségével
description: Ez a cikk bemutatja, hogyan figyelheti a metrikák az Azure Portal diagramok és az Azure CLI.
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
ms.openlocfilehash: c230e1e950bb924631032940642a6202acf4ade8
ms.sourcegitcommit: e040ab443f10e975954d41def759b1e9d96cdade
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2020
ms.locfileid: "80382936"
---
# <a name="monitor-media-services-metrics"></a>A Media Services metrikáinak monitorozása

[Az Azure Monitor](../../azure-monitor/overview.md) lehetővé teszi, hogy a metrikák és diagnosztikai naplók, amelyek segítenek megérteni, hogyan teljesítenek az alkalmazások. A szolgáltatás részletes leírását és azt, hogy miért szeretné használni az Azure Media Services metrikák és diagnosztikai naplók, [lásd: Media Services-metrikák és diagnosztikai naplók figyelése.](media-services-metrics-diagnostic-logs.md)

Az Azure Monitor számos lehetőséget kínál a metrikák kal való interakcióra, beleértve a portálon való ábrázolásukat, a REST API-n keresztüli hozzáférésüket vagy az Azure CLI használatával történő lekérdezésüket. Ez a cikk bemutatja, hogyan figyelheti a metrikák az Azure Portal diagramok és az Azure CLI.

## <a name="prerequisites"></a>Előfeltételek

- [Media Services-fiók létrehozása](create-account-cli-how-to.md)
- [A Media Services monitorának mutatóinak és diagnosztikai naplóinak áttekintése](media-services-metrics-diagnostic-logs.md)

## <a name="view-metrics-in-azure-portal"></a>Metrikák megtekintése az Azure Portalon

1. Jelentkezzen be az Azure Portalra a https://portal.azure.com webhelyen.
1. Nyissa meg az Azure Media Services-fiókját, és válassza **a Metrikák**lehetőséget.
1. Kattintson az **ERŐFORRÁS** mezőre, és válassza ki azt az erőforrást, amelynek a metrikákat figyelni szeretné.

    Az **Erőforrás kiválasztása** ablak a jobb oldalon jelenik meg, és az erőforrások listája elérhető. Ebben az esetben a következőket látja:

    * &lt;Media Services-fiók neve&gt;
    * &lt;Media Services-fiók neve&gt;/&lt;streamelési végpont neve&gt;
    * &lt;tárfiók neve&gt;

    Jelölje ki az erőforrást, és nyomja **le az Apply billentyűt.** A támogatott erőforrásokkal és mérőszámokkal kapcsolatos részleteka [Media Services-mutatók figyelése című témakörben talál.](media-services-metrics-diagnostic-logs.md)

    ![Mérőszámok](media/media-services-metrics/metrics02.png)

    > [!NOTE]
    > Ha váltani szeretne az erőforrások között, amelyeknél figyelni szeretné a mutatókat, kattintson ismét az **ERŐFORRÁS** mezőre, és ismételje meg ezt a lépést.
1. (Opcionálisan) adjon nevet a diagramnak (a név szerkesztése a tetején lévő ceruza megnyomásával).
1. Adja hozzá a megtekinteni kívánt mutatókat.

    ![Mérőszámok](media/media-services-metrics/metrics03.png)
1. A diagramot rögzítheti az irányítópulton.

## <a name="view-metrics-with-azure-cli"></a>Metrikák megtekintése az Azure CLI-vel

Az "Egres" metrikák beszerezéséhez az `az monitor metrics` Azure CLI-vel a következő parancsot kell futtatnia:

```azurecli-interactive
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Más metrikák lekérni, helyettesítse a "Kimenő" a metrika neve érdekli.

## <a name="see-also"></a>Lásd még

* [Az Azure Monitor metrikák](../../azure-monitor/platform/data-platform.md)
* [Metrikariasztásoklétrehozása, megtekintése és kezelése az Azure Monitor használatával.](../../azure-monitor/platform/alerts-metric.md)

## <a name="next-steps"></a>További lépések

[Diagnosztikai naplók](media-services-diagnostic-logs-howto.md)
