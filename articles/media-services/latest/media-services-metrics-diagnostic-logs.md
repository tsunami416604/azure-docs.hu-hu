---
title: Azure Media Services-metrikák és diagnosztikai naplók Azure monitoron keresztül figyelése |} A Microsoft Docs
description: Ez a cikk áttekintést nyújt az Azure Media Services-metrikák és diagnosztikai naplók az Azure monitoron keresztül figyelése.
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
ms.openlocfilehash: 6d26cd809d78bf05f66c9fa03be5063ca4d2d5e4
ms.sourcegitcommit: 47ce9ac1eb1561810b8e4242c45127f7b4a4aa1a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/11/2019
ms.locfileid: "67805999"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs"></a>A Media Services-metrikák és diagnosztikai naplók figyelése

[Az Azure Monitor](../../azure-monitor/overview.md) lehetővé teszi, hogy a figyelő metrikák és diagnosztikai naplók, amelyek segítenek megérteni, hogyan az alkalmazások hajt végre. Az Azure Monitor által gyűjtött összes adat megfelel a két alapvető típus, metrikák és naplók egyikébe. Figyelheti a Media Services diagnosztikai naplók és riasztások és értesítések a gyűjtött metrikák és naplók létrehozása. Megjelenítheti és elemezheti a metrikák adatait az [metrikaböngésző](../../azure-monitor/platform/metrics-getting-started.md). A naplókat elküldheti [Azure Storage](https://azure.microsoft.com/services/storage/), azokat a stream [Azure Event Hubs](https://azure.microsoft.com/services/event-hubs/), és exportálhatja őket [Log Analytics](https://azure.microsoft.com/services/log-analytics/), vagy használja a 3. fél által nyújtott szolgáltatások.

Részletes ismertetőt talál [Azure Monitor-metrikák](../../azure-monitor/platform/data-platform.md) és [Azure Monitor-diagnosztikai naplók](../../azure-monitor/platform/diagnostic-logs-overview.md).

Ebben a témakörben ismertetett támogatott [Media Services-metrikák](#media-services-metrics) és [Media Services diagnosztikai naplók](#media-services-diagnostic-logs).

## <a name="media-services-metrics"></a>A Media Services-metrikák

Rendszeres időközönként metrikákat gyűjt az érték-e. Hasznos, mert azok gyakran kell mintát, és riasztást gyorsan is fired viszonylag egyszerű logikával riasztási zajlik. Metrikákhoz kapcsolódó riasztások létrehozásával kapcsolatos információkért lásd: [létrehozása, megtekintése és kezelése az Azure Monitor használatával metrikákhoz kapcsolódó riasztások](../../azure-monitor/platform/alerts-metric.md).

A Media Services támogatja a figyelési metrikákat a következő erőforrásokhoz:

* Fiók
* Streamvégpont
 
### <a name="account"></a>Fiók

A következő mérőszámai követheti nyomon. 

|Metrika neve|Display name|Leírás|
|---|---|---|
|AssetCount|Eszközök száma|Eszközök a fiókban.|
|AssetQuota|Az eszközintelligencia-kvóta|Az eszközintelligencia kvóta-fiókjában található.|
|AssetQuotaUsedPercentage|Az eszközintelligencia felhasznált kvóta százalékos aránya|A százalékos aránya az Eszközintelligencia kvóta már használatban van.|
|ContentKeyPolicyCount|Tartalmi kulcs szabályzatok száma|Tartalmi kulcs házirendjei-fiókjában található.|
|ContentKeyPolicyQuota|Tartalmi kulcs házirend kvóta|Tartalmi kulcs házirendjei kvóta-fiókjában található.|
|ContentKeyPolicyQuotaUsedPercentage|Tartalmi kulcs házirend kvóta használt százalékos aránya|A százalékos aránya a tartalom kulcs házirend kvóta már használatban van.|
|StreamingPolicyCount|Streamelési szabályzatok száma|Streamelési házirendek a fiókjában.|
|StreamingPolicyQuota|Adatfolyam-továbbítási szabályzat kvóta|Streamelési házirendek kvóta-fiókjában található.|
|StreamingPolicyQuotaUsedPercentage|Adatfolyam-továbbítási szabályzat kvóta használt százalékos aránya|A százalékos aránya a házirend folyamatos átviteli kvóta már használatban van.|
 
Olvassa el a [kvóták és korlátozások fiók](limits-quotas-constraints.md).

### <a name="streaming-endpoint"></a>Streamvégpont

A következő Media Services [adatfolyam-továbbítási végpontok](https://docs.microsoft.com/rest/api/media/streamingendpoints) metrikák támogatottak:

|Metrika neve|Display name|Leírás|
|---|---|---|
|Kérelmek|Kérelmek|Biztosítja a folyamatos átviteli végponton, melyet a HTTP-kérések száma.|
|Kimenő forgalom|Kimenő forgalom|Kimenő bájtok teljes száma. Ha például bájt streameli a folyamatos átviteli végponton.|
|SuccessE2ELatency|Sikeres végpontok közötti késése|A folyamatos átviteli végponton, ha a kérés érkezésekor az időtartam a válasz utolsó bájtját lett elküldve.|

### <a name="why-would-i-want-to-use-metrics"></a>Miért érdemes a mérőszámok használatát? 

Az alábbiakban példát, hogyan Media Services-metrikák figyelése segíthet megérteni, hogyan az alkalmazások hajt végre. A Media Services-metrikák lehet megoldani a következő kérdések a következők:

* Hogyan figyelhetem a Standard folyamatos átviteli végponton, ha szeretnék túllépte a korlátok?
* Honnan tudhatom meg, ha van elegendő prémium szintű Streamvégpont skálázási egység? 
* Hogyan állíthatok be egy riasztást, hogy mikor vertikális felskálázása az adatfolyam-továbbítási végpontok?
* Hogyan állíthatok be egy riasztást tudni, hogy a fiók konfigurálva a maximális kimenő elérésekor?
* Hogyan tekinthetem meg a sikertelen kérelmek áttekintését, és mi okozza a hibát?
* Hogyan tekintheti meg, hogy hány HLS, DASH vagy kérelmek a csomagoló lekért van folyamatban?
* Hogyan állíthatok be egy riasztást, ha a sikertelen kérelmek száma a küszöbérték észlelte? 

### <a name="example"></a>Példa

Lásd: [Media Services-metrikák figyelése](media-services-metrics-howto.md)

## <a name="media-services-diagnostic-logs"></a>A Media Services diagnosztikai naplók

Diagnosztikai naplók nyújtanak részletes, gyakori adatokat egy Azure-erőforrás a művelet. További információkért lásd: [gyűjtése és felhasználása a naplófájlok adatait az Azure-erőforrások](../../azure-monitor/platform/diagnostic-logs-overview.md).

Media Services támogatja a következő diagnosztikai naplók kapcsolódnak:

* Kulcskézbesítési

### <a name="key-delivery"></a>Kulcskézbesítési

|Name (Név)|Leírás|
|---|---|
|Kulcskézbesítési szolgáltatáskérés|Naplók megjelenítése a kulcstovábbítást adatok kérése. További részletekért lásd: [sémák](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Miért érdemes használni a diagnosztikai naplók? 

Néhány dolgot, hogy a kulcs kézbesítési diagnosztikai naplókkal tekintse meg a következők:

* DRM-típus által kínált licencek számának megtekintéséhez
* Tekintse meg a szabályzat által kínált licencek számát 
* Hibába ütközik, DRM-mel vagy a házirend típusa szerint
* Tekintse meg a jogosulatlan licenc ügyfelektől érkező kérelmek száma

### <a name="example"></a>Példa

Lásd: [figyelése a Media Services diagnosztikai naplók](media-services-diagnostic-logs-howto.md)

## <a name="next-steps"></a>További lépések 

* [Gyűjtése és felhasználása a naplófájlok adatait az Azure-erőforrások](../../azure-monitor/platform/diagnostic-logs-overview.md)
* [Létrehozása, megtekintése és kezelése az Azure Monitor használatával metrikákhoz kapcsolódó riasztások](../../azure-monitor/platform/alerts-metric.md)
* [A Media Services-metrikák figyelése](media-services-metrics-howto.md)
* [Media Services diagnosztikai naplók figyelése](media-services-diagnostic-logs-howto.md)
