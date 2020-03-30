---
title: Témakörök és esemény-előfizetések figyelése – Azure Event Grid IoT Edge | Microsoft dokumentumok
description: Témakörök és esemény-előfizetések figyelése
author: banisadr
ms.author: babanisa
ms.reviewer: spelluru
ms.date: 01/09/2020
ms.topic: article
ms.service: event-grid
services: event-grid
ms.openlocfilehash: ce7c92f121fb458d528d63d0af0aad025b377386
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "77086673"
---
# <a name="monitor-topics-and-event-subscriptions"></a>Témakörök és esemény-előfizetések figyelése

Az Event Grid on Edge számos metrikát tár fel a témakörökhöz és az esemény-előfizetésekhez a [Prometheus kiállítás iszszert formátumban.](https://prometheus.io/docs/instrumenting/exposition_formats/) Ez a cikk ismerteti a rendelkezésre álló metrikákat, és hogyan engedélyezheti őket.

## <a name="enable-metrics"></a>Mérőszámok engedélyezése

Konfigurálja a modult metrikák kibocsátására úgy, hogy a környezeti változót `metrics__reporterType` `prometheus` a tárolóban létre szeretné hozni a beállításokat:

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

Metrikák lesz `5888/metrics` elérhető a modul `4438/metrics` http és https. Például `http://<modulename>:5888/metrics?api-version=2019-01-01-preview` a http. Ezen a ponton egy metrikamodul lejátszhatja a végpontot a metrikák gyűjtéséhez ebben a [példában architektúrában.](https://github.com/veyalla/ehm)

## <a name="available-metrics"></a>Rendelkezésre álló metrikák

A témakörök és az esemény-előfizetések egyaránt metrikákat bocsátanak ki, hogy betekintést nyújtson az események kézbesítésébe és a modul teljesítményébe.

### <a name="topic-metrics"></a>Témakör metrikák

| Metrika | Leírás |
| ------ | ----------- |
| Beérkezett események | A témában közzétett események száma
| Nem egyező események | A témakörben közzétett olyan események száma, amelyek nem felelnek meg egy esemény-előfizetésnek, és el vannak dobva
| SuccessRequests (Sikerkérések) | A témakör által fogadott bejövő közzétételi kérelmek száma
| SystemErrorRequests | Belső rendszerhiba miatt meghiúsult bejövő közzétételi kérelmek száma
| UserErrorRequests (UserErrorRequests) | A bejövő közzétételi kérelmek száma nem sikerült felhasználói hiba, például hibásan formázott JSON miatt
| SuccessRequestLatencyMs | Kérésválasz-késleltetés közzététele ezredmásodpercben


### <a name="event-subscription-metrics"></a>Esemény-előfizetési mutatók

| Metrika | Leírás |
| ------ | ----------- |
| DeliverySuccessCounts (DeliverySuccessCounts) | A konfigurált végpontra sikeresen kézbesített események száma
| DeliveryFailureCounts (Kézbesítési hibák száma) | A konfigurált végpontra nem kézbesített események száma
| DeliverySuccessLatencyMs | Az események késése sikeresen megadva ezredmásodpercben
| DeliveryFailureLatencyMs | Az események kézbesítési hibáinak késése ezredmásodpercben
| SystemDelayFor FirstAttemptms | Az események rendszerkésése az első kézbesítési kísérlet előtt ezredmásodpercben
| DeliveryAttemptsCount között | Eseménykézbesítési kísérletek száma – sikeres ség és sikertelenség
| ExpiredCounts (Lejárt) | Lejárt és a konfigurált végpontra nem kézbesített események száma