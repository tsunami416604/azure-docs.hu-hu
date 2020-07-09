---
title: Témakörök és esemény-előfizetések figyelése – Azure Event Grid IoT Edge | Microsoft Docs
description: Témakörök és esemény-előfizetések figyelése
author: femila
ms.author: femila
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: d4fbc5232722bfb08bde9be51d44e8e8d7514570
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "84554358"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Témakörök és esemény-előfizetések figyelése

A Event Grid on Edge számos mérőszámot nyújt a témakörökhöz és az esemény-előfizetésekhez a [Prometheus kiállítási formátumában](https://prometheus.io/docs/instrumenting/exposition_formats/). Ez a cikk a rendelkezésre álló mérőszámokat és azok engedélyezésének módját ismerteti.

## <a name="enable-metrics"></a>Metrikák engedélyezése

Konfigurálja a modult a metrikák kibocsátására úgy, hogy beállítja a `metrics__reporterType` környezeti változót `prometheus` a tároló létrehozási beállításai között:

 ```json
        {
          "Env": [
            "metrics__reporterType=prometheus"
          ],
          "HostConfig": {
            "PortBindings": {
              "4438/tcp": [
                {
                    "HostPort": "4438"
                }
              ]
            }
          }
        }
 ```    

A metrikák `5888/metrics` a http és a https esetében a modulban lesznek elérhetők `4438/metrics` . Http esetében például: `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` . Ezen a ponton a metrikák modulja lekérdezheti a végpontot, hogy összegyűjtse a mérőszámokat az ebben a [példában szereplő architektúrában](https://github.com/veyalla/ehm).

## <a name="available-metrics"></a>Rendelkezésre álló metrikák

A témakörök és az esemény-előfizetések mérőszámokat bocsátanak ki, hogy betekintést nyújtson az események kézbesítésére és a modulok teljesítményére.

### <a name="topic-metrics"></a>Témakör metrikái

| Metric | Leírás |
| ------ | ----------- |
| EventsReceived | A témakörben közzétett események száma
| UnmatchedEvents | A témakörben közzétett események száma, amelyek nem felelnek meg az esemény-előfizetésnek, és el lettek dobva
| SuccessRequests | A témakörben fogadott bejövő közzétételi kérelmek száma
| SystemErrorRequests | A bejövő közzétételi kérelmek száma belső rendszerhiba miatt sikertelen volt.
| UserErrorRequests | A bejövő közzétételi kérelmek száma felhasználói hiba miatt meghiúsult, például helytelenül formázott JSON
| SuccessRequestLatencyMs | Kérelemre adott válasz késésének közzététele ezredmásodpercben


### <a name="event-subscription-metrics"></a>Esemény-előfizetési metrikák

| Metric | Leírás |
| ------ | ----------- |
| DeliverySuccessCounts | A konfigurált végponthoz sikeresen küldött események száma
| DeliveryFailureCounts | A konfigurált végpontnak sikertelenül küldött események száma
| DeliverySuccessLatencyMs | Az események késése (ezredmásodpercben) sikeresen lefutott
| DeliveryFailureLatencyMs | Az események kézbesítési hibáinak késése ezredmásodpercben
| SystemDelayForFirstAttemptMs | Események rendszerkésleltetése az első kézbesítési kísérlet előtt ezredmásodpercben
| DeliveryAttemptsCount | Esemény kézbesítési kísérletek száma – sikeres és sikertelen
| ExpiredCounts | A lejárt és a beállított végpontnak nem továbbított események száma