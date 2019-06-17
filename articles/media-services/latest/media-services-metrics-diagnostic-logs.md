---
title: Media Services-metrikák és diagnosztikai naplók Azure monitoron keresztül figyelése |} A Microsoft Docs
description: Ez a cikk áttekintést nyújt a Media Services-metrikák és diagnosztikai naplók az Azure monitoron keresztül figyelése.
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
ms.date: 03/05/2019
ms.author: juliako
ms.openlocfilehash: bbf43ecb07947fad8cc1ee064d2038e4a21d4444
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65964767"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>A Media Services-metrikák és diagnosztikai naplók figyelése

[Az Azure Monitor](../../azure-monitor/overview.md) lehetővé teszi, hogy a figyelő metrikák és diagnosztikai naplók, amelyek segítenek megérteni, hogyan az alkalmazások hajt végre. Az Azure Monitor által gyűjtött összes adat megfelel a két alapvető típus, metrikák és naplók egyikébe. Figyelheti a Media Services diagnosztikai naplók és riasztások és értesítések a gyűjtött metrikák és naplók létrehozása. Megjelenítheti és elemezheti a metrikák adatait az [metrikaböngésző](../../azure-monitor/platform/metrics-getting-started.md). A naplókat elküldheti [Azure Storage](https://azure.microsoft.com/services/storage/), azokat a stream [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), és exportálhatja őket [Log Analytics](https://azure.microsoft.com/services/log-analytics/), vagy használja a 3. fél által nyújtott szolgáltatások.

Részletes ismertetőt talál [Azure Monitor-metrikák](../../azure-monitor/platform/data-platform.md) és [Azure Monitor-diagnosztikai naplók](../../azure-monitor/platform/diagnostic-logs-overview.md).

Ebben a témakörben ismertetett jelenleg elérhető [Media Services-metrikák](#media-services-metrics) és [Media Services diagnosztikai naplók](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>A Media Services-metrikák

Rendszeres időközönként metrikákat gyűjt az érték-e. Hasznos, mert azok gyakran kell mintát, és riasztást gyorsan is fired viszonylag egyszerű logikával riasztási zajlik.

Jelenleg a következő Media Services [adatfolyam-továbbítási végpontok](https://docs.microsoft.com/rest/api/media/streamingendpoints) metrikák vannak kibocsátva az Azure-ban:

|Metrika|Display name|Leírás|
|---|---|---|
|Kérelmek|Kérelmek|A folyamatos átviteli végponton által feldolgozott kérelmek teljes száma körül részleteket nyújt.|
|Kimenő forgalom|Kimenő forgalom|Kimenő bájtok teljes száma. Ha például bájt streameli a folyamatos átviteli végponton.|
|SuccessE2ELatency|Sikeres végpontok közötti késése| Sikeres kérések végpontok közötti késés kapcsolatos információkat biztosít.|

Például a CLI-vel "Kimenő" metrikáinak beolvasása, futtatná a következő `az monitor metrics` parancssori felületi parancsot:

```cli
az monitor metrics list --resource \
   "/subscriptions/<subscription id>/resourcegroups/<resource group name>/providers/Microsoft.Media/mediaservices/<Media Services account name>/streamingendpoints/<streaming endpoint name>" \
   --metric "Egress"
```

Metrikákhoz kapcsolódó riasztások létrehozásával kapcsolatos információkért lásd: [létrehozása, megtekintése és kezelése az Azure Monitor használatával metrikákhoz kapcsolódó riasztások](../../azure-monitor/platform/alerts-metric.md).

## <a name="media-services-diagnostic-logs"></a>A Media Services diagnosztikai naplók

Jelenleg a következő diagnosztikai naplók kaphat:

|Name (Név)|Leírás|
|---|---|
|Kulcskézbesítési szolgáltatáskérés|Naplók megjelenítése a kulcstovábbítást adatok kérése. További részletekért lásd: [sémák](media-services-diagnostic-logs-schema.md).|

Ahhoz, hogy a diagnosztikai naplókat egy tárfiókban, a következőt futtatná `az monitor diagnostic-settings` parancssori felületi parancsot: 

```cli
az monitor diagnostic-settings create --name <diagnostic name> \
    --storage-account <name or ID of storage account> \
    --resource <target resource object ID> \
    --resource-group <storage account resource group> \
    --logs '[
    {
        "category": <category name>,
        "enabled": true,
        "retentionPolicy": {
            "days": <# days to retain>,
            "enabled": true
        }
    }]'
```

Példa:

```cli
az monitor diagnostic-settings create --name amsv3diagnostic \
    --storage-account storageaccountforamsv3  \
    --resource "/subscriptions/00000000-0000-0000-0000-0000000000/resourceGroups/amsv3ResourceGroup/providers/Microsoft.Media/mediaservices/amsv3account" \
    --resource-group "amsv3ResourceGroup" \
    --logs '[{"category": "KeyDeliveryRequests",  "enabled": true, "retentionPolicy": {"days": 3, "enabled": true }}]'
```

## <a name="next-steps"></a>További lépések 

[Gyűjtése és felhasználása a naplófájlok adatait az Azure-erőforrások](../../azure-monitor/platform/diagnostic-logs-overview.md).
