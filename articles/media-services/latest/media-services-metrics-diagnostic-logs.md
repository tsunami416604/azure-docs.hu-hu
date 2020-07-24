---
title: Media Services metrikák és diagnosztikai naplók a Azure Monitor
titleSuffix: Azure Media Services
description: Megtudhatja, hogyan figyelheti Azure Media Services metrikáit és a diagnosztikai naplókat Azure Monitor használatával.
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
ms.openlocfilehash: 7058160819c9b6ccc3f63511f86e930fcbfc413b
ms.sourcegitcommit: 3d79f737ff34708b48dd2ae45100e2516af9ed78
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/23/2020
ms.locfileid: "87011651"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-via-azure-monitor"></a>Media Services metrikák és diagnosztikai naplók figyelése Azure Monitor használatával

[Azure monitor](../../azure-monitor/overview.md) segítségével figyelheti a mérőszámokat és a diagnosztikai naplókat, amelyek segítenek megérteni az alkalmazások teljesítményét. Az Azure Monitor által összegyűjtött összes adat két alapvető típus egyikéhez illeszkedik: metrikák és naplók. Nyomon követheti Media Services diagnosztikai naplóit, és riasztásokat és értesítéseket hozhat létre az összegyűjtött metrikák és naplók számára. A metrikák adatait a [metrikák Explorerrel](../../azure-monitor/platform/metrics-getting-started.md)jelenítheti meg és elemezheti. Naplókat küldhet az [Azure Storage](https://azure.microsoft.com/services/storage/)-ba, továbbíthatja őket az [Azure Event Hubsba](https://azure.microsoft.com/services/event-hubs/), exportálhatja őket [log Analyticsba](https://azure.microsoft.com/services/log-analytics/), vagy külső szolgáltatásokat is használhat.

Részletes Áttekintés: [Azure monitor metrikák](../../azure-monitor/platform/data-platform.md) és [Azure monitor diagnosztikai naplók](../../azure-monitor/platform/platform-logs-overview.md).

Ez a témakör a támogatott [Media Services metrikákat](#media-services-metrics) és [Media Services diagnosztikai naplókat](#media-services-diagnostic-logs)ismerteti.

## <a name="media-services-metrics"></a>Media Services metrikák

A metrikák gyűjtése rendszeres időközönként történik, függetlenül attól, hogy az érték megváltozik-e. Ezek a riasztások azért hasznosak, mert gyakran mintavételezésre is képesek, és a riasztások gyorsan és viszonylag egyszerű logikával is elindíthatók. A metrikai riasztások létrehozásával kapcsolatos információkért lásd: [metrikai riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../../azure-monitor/platform/alerts-metric.md).

Media Services támogatja a következő erőforrások figyelési metrikáit:

* Fiók
* Streamvégpont

### <a name="account"></a>Fiók

A következő fiók metrikáit figyelheti.

|Metrika neve|Megjelenített név|Description|
|---|---|---|
|AssetCount|Eszközök száma|A fiókban lévő eszközök.|
|AssetQuota|Eszköz kvótája|A fiókban lévő eszköz kvótája.|
|AssetQuotaUsedPercentage|Eszköz kvótájának kihasználtsága (%)|Az eszköz kvótájának százalékos aránya már használatban van.|
|ContentKeyPolicyCount|Tartalmi kulcsokra vonatkozó szabályzatok száma|A fiókban található tartalmi kulcsokra vonatkozó szabályzatok.|
|ContentKeyPolicyQuota|Tartalmi kulcs házirend-kvótája|A fiókban található tartalmi kulcs házirendjeinek kvótája.|
|ContentKeyPolicyQuotaUsedPercentage|Tartalom kulcsára vonatkozó házirend kvótájának százalékos aránya|A tartalmi kulcs házirend-kvótájának százalékos aránya már használatban van.|
|StreamingPolicyCount|Folyamatos átviteli szabályzatok száma|Streaming-szabályzatok a fiókban.|
|StreamingPolicyQuota|Streaming Policy-kvóta|Folyamatos átviteli szabályzatok kvótája a fiókban.|
|StreamingPolicyQuotaUsedPercentage|Adatfolyam-házirend kvótájának kihasználtsága (%)|Az adatfolyam-házirend kvótájának százalékos aránya már használatban van.|

Tekintse át [a fiók kvótáit és korlátozásait](limits-quotas-constraints.md)is.

### <a name="streaming-endpoint"></a>Streamvégpont

A következő Media Services [folyamatos átviteli végpontok](/rest/api/media/streamingendpoints) metrikáit támogatja:

|Metrika neve|Megjelenített név|Description|
|---|---|---|
|Kérelmek|Kérelmek|A folyamatos átviteli végpont által kiszolgált HTTP-kérelmek teljes számát adja meg.|
|Kimenő forgalom|Kimenő forgalom|Kimenő átviteli végpontok percenkénti bájtjainak száma.|
|SuccessE2ELatency|A Befejezés végének késése|Az az időtartam, amikor a folyamatos átviteli végpont a válasz utolsó bájtjának küldésére vonatkozó kérést kapott.|

### <a name="why-would-i-want-to-use-metrics"></a>Miért érdemes mérőszámokat használni?

Íme néhány példa arra, hogy a monitorozási Media Services mérőszámai hogyan segíthetnek az alkalmazások teljesítményének megismerésében. Media Services metrikákkal foglalkozó kérdések a következők:

* Hogyan a standard folyamatos átviteli végpontját, hogy megtudjam, mikor lépték túl a korlátot?
* Hogyan tudni, hogy van-e elegendő prémium szintű streaming Endpoint skálázási egység?
* Hogyan állíthatok be egy riasztást a folyamatos átviteli végpontok vertikális felskálázásához?
* Hogyan beállítani a riasztást, hogy a fiókon beállított maximális kimenő forgalom mikor lett elérve?
* Hogyan láthatom a sikertelen kérelmek részletezését, és mi okozza a hibát?
* Honnan tudhatom meg, hogy hány HLS-vagy DASH-kérelem van lehúzva a csomagolóból?
* A Hogyan riasztást állít be, hogy a rendszer mikor találta meg a sikertelen kérelmek száma értékének küszöbértékét?

### <a name="example"></a>Példa

Lásd: [Media Services-metrikák figyelése](media-services-metrics-howto.md).

## <a name="media-services-diagnostic-logs"></a>Diagnosztikai naplók Media Services

A diagnosztikai naplók részletes és gyakori információkat biztosítanak az Azure-erőforrások működéséről. További információkért lásd: az [Azure-erőforrások naplózási adatainak gyűjtése és felhasználása](../../azure-monitor/platform/platform-logs-overview.md).

A Media Services a következő diagnosztikai naplókat támogatja:

* Kulcs kézbesítése

### <a name="key-delivery"></a>Kulcs kézbesítése

|Név|Leírás|
|---|---|
|Kulcs kézbesítési szolgáltatásának kérése|A kulcs kézbesítési szolgáltatására vonatkozó adatokat megjelenítő naplók. További információ: [sémák](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Miért érdemes diagnosztikai naplókat használni?

Néhány dolog, amit megvizsgálhat a kulcsfontosságú kézbesítési diagnosztikai naplók használatával:

* Tekintse meg a DRM-típus által szállított licencek számát.
* Tekintse meg a szabályzat által szállított licencek számát.
* A hibák a DRM vagy a házirend típusa szerint jelennek meg.
* Tekintse meg az ügyfelektől érkező jogosulatlan licencek számát.

### <a name="example"></a>Példa

Lásd: [a Media Service diagnosztikai naplóinak figyelése](media-services-diagnostic-logs-howto.md).

## <a name="next-steps"></a>További lépések

* [Az Azure-erőforrások naplózási adatainak gyűjtése és felhasználása](../../azure-monitor/platform/platform-logs-overview.md)
* [Metrikariasztások létrehozása, megtekintése és kezelése az Azure Monitorral](../../azure-monitor/platform/alerts-metric.md)
* [Media Services mérőszámok figyelése](media-services-metrics-howto.md)
* [A Media Service diagnosztikai naplóinak figyelése](media-services-diagnostic-logs-howto.md)
