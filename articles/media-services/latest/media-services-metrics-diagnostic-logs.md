---
title: Media Services-mutatók és diagnosztikai naplók az Azure Monitor segítségével
titleSuffix: Azure Media Services
description: Ismerje meg, hogyan figyelheti az Azure Media Services metrikák at és diagnosztikai naplók at az Azure Monitoron keresztül.
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
ms.openlocfilehash: 75363212684bb09e84a4bc4881af26e6d2a8e7e3
ms.sourcegitcommit: 3c318f6c2a46e0d062a725d88cc8eb2d3fa2f96a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/02/2020
ms.locfileid: "80585271"
---
# <a name="monitor-media-services-metrics-and-diagnostic-logs-via-azure-monitor"></a>Media Services-mérőszámok és diagnosztikai naplók figyelése az Azure Monitoron keresztül

[Az Azure Monitor](../../azure-monitor/overview.md) segítségével figyelheti a metrikákat és a diagnosztikai naplókat, amelyek segítenek megérteni, hogyan teljesítenek az alkalmazások. Az Azure Monitor által gyűjtött összes adat két alapvető típus egyikébe illeszkedik: metrikák és naplók. Figyelheti a Media Services diagnosztikai naplókat, és riasztásokat és értesítéseket hozhat létre az összegyűjtött metrikákhoz és naplókhoz. A metrikák adatait a [Metrics Explorer](../../azure-monitor/platform/metrics-getting-started.md)segítségével jelenítheti meg és elemezheti. Naplókat küldhet az [Azure Storage-ba,](https://azure.microsoft.com/services/storage/)streamelheti őket [az Azure Event Hubs-ba,](https://azure.microsoft.com/services/event-hubs/)exportálhatja őket a [Log Analytics](https://azure.microsoft.com/services/log-analytics/)szolgáltatásba, vagy külső szolgáltatásokat használhat.

A részletes áttekintést az [Azure Monitor metrikák](../../azure-monitor/platform/data-platform.md) és az Azure Monitor diagnosztikai naplók című [témakörben találja.](../../azure-monitor/platform/platform-logs-overview.md)

Ez a témakör a media [services metrikáit](#media-services-metrics) és [a Media Services diagnosztikai naplóit](#media-services-diagnostic-logs)ismerteti.

## <a name="media-services-metrics"></a>A Media Services mutatói

A mérőszámok rendszeres időközönként kerülnek összegyűjtésre, függetlenül attól, hogy az érték megváltozik-e vagy sem. Ezek akkor hasznosak, riasztási, mert gyakran mintavételezhetők, és egy riasztást lehet gyorsan kilőni viszonylag egyszerű logika. A metrikariasztások létrehozásáról az [Azure Monitor használatával metrikariasztások létrehozása, megtekintése és kezelése című](../../azure-monitor/platform/alerts-metric.md)témakörben talál.

A Media Services a következő erőforrások figyelési mutatóit támogatja:

* Fiók
* Streamvégpont

### <a name="account"></a>Fiók

A következő fiókmutatókat figyelheti.

|Metrika neve|Megjelenített név|Leírás|
|---|---|---|
|Eszközszáma|Eszközdarabszáma|Vagyontárgyak a számláján.|
|Eszközkvóta|Eszközkvóta|Eszközkvóta a fiókjában.|
|AssetQuotaUsedSzázalék|Használt eszközkvóta százalék|A már felhasznált eszközkvóta százaléka.|
|ContentKeyPolicyCount (Tartalomkulcs-házirendszáma)|Tartalomkulcs-házirendek száma|Tartalomkulcs-házirendek a fiókjában.|
|ContentKeyPolicyQuota (ContentKeyPolicyQuota)|Tartalomkulcs-házirendkvóta|A tartalomkulcs-házirendek kvótája a fiókjában.|
|ContentKeyPolicyQuotaUsedSzázalék|A tartalomkulcs-házirend használt kvótája százalékban|A tartalomkulcs-házirend kvóta már használt százaléka.|
|StreamingPolicyCount (StreamPolicyCount)|Streamelési irányelvek száma|Streamelési szabályzatok a fiókjában.|
|StreamingPolicyQuota (StreamingPolicyQuota)|Streamelési házirend kvóta|A streamelési irányelvek kvótája a fiókjában.|
|StreamingPolicyQuotaUsedSzázalék|A streamelési szabályzat használt kvótája százalékban|A streamelési szabályzat kvóta már használt százaléka.|

Tekintse át a [fiókkvótákat és -korlátozásokat](limits-quotas-constraints.md)is.

### <a name="streaming-endpoint"></a>Streamvégpont

A media services [streamelési végpontjainak](https://docs.microsoft.com/rest/api/media/streamingendpoints) alábbi mérőszámai támogatottak:

|Metrika neve|Megjelenített név|Leírás|
|---|---|---|
|Kérelmek|Kérelmek|A streamelési végpont által kiszolgált HTTP-kérelmek teljes számát adja meg.|
|Kimenő forgalom|Kimenő forgalom|A kimenő bájtok teljes száma. Például a streamelési végpont által streamelt bájtok.|
|SuccessE2ELatency (SuccessE2ELatency)|Sikeres befejezés a késés végéig|Időtartam attól, hogy a streamelési végpont mikor kapta meg a kérést, a válasz utolsó bájtjának elküldésének időpontja.|

### <a name="why-would-i-want-to-use-metrics"></a>Miért szeretnék metrikákat használni?

Íme néhány példa arra, hogy a Media Services-mérőszámok figyelése hogyan segíthet az alkalmazások teljesítményének megértésében. A Media Services mutatóival megválaszolható kérdések a következők:

* Hogyan figyelhetem a standard streamelési végpontomat, hogy megtudjam, mikor léptem túl a határértékeket?
* Honnan tudhatom, hogy elegendő prémium szintű streamelési végpont-méretezési egységgel rendelkezem-e?
* Hogyan állíthatok be riasztást, hogy tudja, mikor kell felskálázni a streamelési végpontokat?
* Hogyan állíthatok be riasztást, hogy mikor lett elérhető a fiókon konfigurált maximális kimenő forgalom?
* Hogyan láthatom a kérelmek meghibásodását, és mi okozza a hibát?
* Hogyan láthatom, hogy hány HLS- vagy DASH-kérést kér le a csomagoló?
* Hogyan állíthatok be riasztást, hogy tudja, mikor a # a sikertelen kérelmek küszöbértékét?

### <a name="example"></a>Példa

Lásd: [A Media Services mutatóinak figyelése.](media-services-metrics-howto.md)

## <a name="media-services-diagnostic-logs"></a>Media Services diagnosztikai naplók

A diagnosztikai naplók gazdag és gyakori adatokat nyújtanak egy Azure-erőforrás működéséről. További információ: [Naplóadatok gyűjtése és felhasználása az Azure-erőforrásokból.](../../azure-monitor/platform/platform-logs-overview.md)

A Media Services a következő diagnosztikai naplókat támogatja:

* Kulcs kézbesítése

### <a name="key-delivery"></a>Kulcs kézbesítése

|Név|Leírás|
|---|---|
|Kulcskézbesítési szolgáltatás kérése|Naplók, amelyek a kulcskézbesítési szolgáltatás kérési adatait jelenítik meg. További információ: [sémák](media-services-diagnostic-logs-schema.md).|

### <a name="why-would-i-want-to-use-diagnostics-logs"></a>Miért szeretném használni a diagnosztikai naplókat?

Néhány dolog, amit a kulcskézbesítési diagnosztikai naplókkal vizsgálhat meg:

* Tekintse meg a DRM-típus szerint szállított licencek számát.
* Tekintse meg a szabályzat által kézbesített licencek számát.
* Tekintse meg a hibákat drm vagy házirendtípus szerint.
* Tekintse meg az ügyfelektől érkező jogosulatlan licenckérelmek számát.

### <a name="example"></a>Példa

Lásd: [A Media Service diagnosztikai naplóinak figyelése](media-services-diagnostic-logs-howto.md).

## <a name="next-steps"></a>További lépések

* [Naplóadatok gyűjtése és felhasználása az Azure-erőforrásokból](../../azure-monitor/platform/platform-logs-overview.md)
* [Metrikariasztások létrehozása, megtekintése és kezelése az Azure Monitorral](../../azure-monitor/platform/alerts-metric.md)
* [A Media Services mutatóinak figyelése](media-services-metrics-howto.md)
* [A Media Service diagnosztikai naplóinak figyelése](media-services-diagnostic-logs-howto.md)
