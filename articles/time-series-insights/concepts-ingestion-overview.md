---
title: A betöltés áttekintése – Azure Time Series Insights | Microsoft Docs
description: Ismerkedjen meg a Azure Time Series Insightsba való adatfeldolgozással.
author: lyrana
ms.author: lyhughes
manager: deepakpalled
ms.workload: big-data
ms.service: time-series-insights
services: time-series-insights
ms.topic: conceptual
ms.date: 06/03/2020
ms.custom: seodec18
ms.openlocfilehash: ecee6817b58639d1b60264c818ab9517e5d133e0
ms.sourcegitcommit: e132633b9c3a53b3ead101ea2711570e60d67b83
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86049958"
---
# <a name="azure-time-series-insights-data-ingestion-overview"></a>Azure Time Series Insights adatfeldolgozás áttekintése

Az Azure Time Series Insights-környezet egy betöltési *motort* tartalmaz a streaming Time Series-adatok gyűjtéséhez, feldolgozásához és tárolásához. Mivel az adatforrása (ke) t beérkeznek, Azure Time Series Insights az adatait közel valós időben fogja felhasználni és tárolni.

[![A betöltés áttekintése](media/concepts-ingress-overview/ingress-overview.png)](media/concepts-ingress-overview/ingress-overview.png#lightbox)

## <a name="ingestion-topics"></a>Betöltési témakörök

Az alábbi cikkek részletesen ismertetik az adatfeldolgozást, beleértve az alábbi ajánlott eljárásokat:

* Olvassa el [az eseményforrás és az](concepts-streaming-ingestion-event-sources.md) eseményforrás időbélyegének kiválasztására vonatkozó útmutatást.

* A támogatott [adattípusok](concepts-supported-data-types.md) áttekintése

* Megtudhatja, hogyan alkalmazza a betöltési motor a JSON-tulajdonságok [szabályait](./concepts-json-flattening-escaping-rules.md) a Storage-fiók oszlopainak létrehozásához.

* Tekintse át a környezet [átviteli sebességének korlátozásait](concepts-streaming-throughput-limitations.md) a méretezési igények megtervezéséhez.

## <a name="next-steps"></a>További lépések

* Folytassa tovább a Azure Time Series Insights-környezethez tartozó [eseményforrás](concepts-streaming-ingestion-event-sources.md) megismeréséhez. 
