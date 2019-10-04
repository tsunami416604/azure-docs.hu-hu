---
title: Azure Media Services metrikák és diagnosztikai naplók figyelése Azure Monitor használatával | Microsoft Docs
description: Ez a cikk áttekintést nyújt arról, hogyan figyelhetők meg Azure Media Services metrikák és diagnosztikai naplók a Azure Monitor használatával.
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
ms.openlocfilehash: 1c77cdf57978af81accc7802575d262b97d08fe2
ms.sourcegitcommit: 55f7fc8fe5f6d874d5e886cb014e2070f49f3b94
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2019
ms.locfileid: "71261076"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>Media Services metrikák és diagnosztikai naplók figyelése

[Azure monitor](../../azure-monitor/overview.md) lehetővé teszi a metrikák és diagnosztikai naplók figyelését, amelyek segítenek megérteni az alkalmazások teljesítményét. Az Azure Monitor által összegyűjtött összes adat a két alapvető típus, a metrikák és a naplók egyikére illeszkedik. Nyomon követheti Media Services diagnosztikai naplóit, és riasztásokat és értesítéseket hozhat létre az összegyűjtött metrikák és naplók számára. A metrikák adatait a [metrikák Explorerrel](../../azure-monitor/platform/metrics-getting-started.md)jelenítheti meg és elemezheti. Naplókat küldhet az [Azure Storage](https://azure.microsoft.com/services/storage/)-ba, továbbíthatja őket az [Azure Event Hubsba](https://azure.microsoft.com/services/event-hubs/), és exportálhatja őket [log Analyticsba](https://azure.microsoft.com/services/log-analytics/), vagy külső szolgáltatásokhoz is használhatja őket.

Részletes Áttekintés: [Azure monitor metrikák](../../azure-monitor/platform/data-platform.md) és [Azure monitor diagnosztikai naplók](../../azure-monitor/platform/resource-logs-overview.md).

Ez a témakör a támogatott [Media Services metrikákat](#media-services-metrics) és [Media Services diagnosztikai naplókat](#media-services-diagnostic-logs)ismerteti.

## <a name="media-services-metrics"></a>Media Services metrikák

Rendszeres időközönként metrikákat gyűjt az érték-e. Hasznos, mert azok gyakran kell mintát, és riasztást gyorsan is fired viszonylag egyszerű logikával riasztási zajlik. A metrikai riasztások létrehozásával kapcsolatos információkért lásd: [metrikai riasztások létrehozása, megtekintése és kezelése Azure monitor használatával](../../azure-monitor/platform/alerts-metric.md).

Media Services támogatja a következő erőforrások figyelési metrikáit:

* Fiók
* Streamvégpont
 
### <a name="account"></a>Fiók

A következő fiók metrikáit figyelheti. 

|Metrika neve|Display name|Leírás|
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

A következő Media Services [folyamatos átviteli végpontok](https://docs.microsoft.com/rest/api/media/streamingendpoints) metrikáit támogatja:

|Metrika neve|Display name|Leírás|
|---|---|---|
|Kérelmek|Kérelmek|A folyamatos átviteli végpont által kiszolgált HTTP-kérelmek teljes számát adja meg.|
|Kimenő forgalom|Kimenő forgalom|A kimenő bájtok teljes száma. Például a streaming végpont által továbbított bájtok.|
|SuccessE2ELatency|A Befejezés végének késése|Az az időtartam, amikor a folyamatos átviteli végpont a válasz utolsó bájtjának küldésére vonatkozó kérést kapott.|

### <a name="why-would-i-want-to-use-metrics"></a>Miért érdemes mérőszámokat használni? 

Íme néhány példa arra, hogy a figyelési Media Services mérőszámai hogyan segíthetnek az alkalmazások teljesítményének megismerésében. Media Services metrikákkal foglalkozó kérdések a következők:

* Hogyan a standard folyamatos átviteli végpontját, hogy megtudjam, mikor lépték túl a korlátot?
* Hogyan tudni, hogy van-e elegendő prémium szintű streaming Endpoint skálázási egység? 
* Hogyan állíthatok be egy riasztást a folyamatos átviteli végpontok vertikális felskálázásához?
* Hogyan beállítani a riasztást, hogy a fiókon beállított maximális kimenő forgalom mikor lett elérve?
* Hogyan láthatom a sikertelen kérelmek részletezését, és mi okozza a hibát?
* Honnan tudhatom meg, hogy hány HLS-vagy DASH-kérelem van lehúzva a csomagolóból?
* A Hogyan riasztást állít be, hogy a rendszer mikor találta meg a sikertelen kérelmek száma értékének küszöbértékét? 

### <a name="example"></a>Példa

Lásd: [Media Services-metrikák figyelése](media-services-metrics-howto.md)

## <a name="media-services-diagnostic-logs"></a>Diagnosztikai naplók Media Services

A diagnosztikai naplók részletes és gyakori információkat biztosítanak az Azure-erőforrások működéséről. További információkért lásd: az [Azure-erőforrások naplózási adatainak gyűjtése és felhasználása](../../azure-monitor/platform/resource-logs-overview.md).

A Media Services a következő diagnosztikai naplókat támogatja:

* Kulcs kézbesítése

### <a name="key-delivery"></a>Kulcs kézbesítése

|Name (Név)|Leírás|
|---|---|
|Kulcs kézbesítési szolgáltatásának kérése|A kulcs kézbesítési szolgáltatására vonatkozó adatokat megjelenítő naplók. További részletekért lásd: [sémák](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Miért érdemes diagnosztikai naplókat használni? 

Néhány dolog, amit megvizsgálhat a kulcsfontosságú kézbesítési diagnosztikai naplók használatával:

* A DRM-típus által szállított licencek száma
* A szabályzat által szállított licencek számának megjelenítése 
* A hibák a DRM vagy a szabályzat típusa szerint jelennek meg
* Az ügyfelektől érkező jogosulatlan licencek számának megjelenítése

### <a name="example"></a>Példa

Lásd: [a Media Service diagnosztikai naplóinak figyelése](media-services-diagnostic-logs-howto.md)

## <a name="next-steps"></a>További lépések 

* [Az Azure-erőforrások naplózási adatainak gyűjtése és felhasználása](../../azure-monitor/platform/resource-logs-overview.md)
* [Metrikai riasztások létrehozása, megtekintése és kezelése Azure Monitor használatával](../../azure-monitor/platform/alerts-metric.md)
* [Media Services mérőszámok figyelése](media-services-metrics-howto.md)
* [A Media Service diagnosztikai naplóinak figyelése](media-services-diagnostic-logs-howto.md)
