---
title: Beszédfelismerési tárolók telepítése
titleSuffix: Azure Cognitive Services
description: A beszéd – szöveg Helm diagram konfigurációs beállításainak részletei.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: trbye
ms.openlocfilehash: f7ca8fdaddab9757292939c4f7e658179d6e517c
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "81421746"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Beszéd – szöveg (Aldiagram: diagramok/speechToText)

Az "esernyő" diagram felülbírálásához adja hozzá az előtagot `speechToText.` bármelyik paraméterhez, hogy pontosabb legyen. Például felülbírálja a megfelelő paramétert például `speechToText.numberOfConcurrentRequest` : felülbírálások. `numberOfConcurrentRequest`

|Paraméter|Leírás|Alapértelmezett|
| -- | -- | -- |
| `enabled` | Azt határozza meg, hogy a **beszédfelismerési** szolgáltatás engedélyezve van-e. | `false` |
| `numberOfConcurrentRequest` | A **beszédfelismerési** szolgáltatáshoz való egyidejű kérelmek száma. Ez a diagram automatikusan kiszámítja a processzor-és memória-erőforrásokat ezen érték alapján. | `2` |
| `optimizeForAudioFile`| Azt határozza meg, hogy a szolgáltatásnak hangfájlokon keresztül kell-e optimalizálnia a hangbemenetet. Ha `true`ez a diagram több CPU-erőforrást fog kiosztani a szolgáltatás számára. | `false` |
| `image.registry`| A **beszéd-szöveg** Docker-rendszerkép beállításjegyzéke. | `containerpreview.azurecr.io` |
| `image.repository` | A **beszéd-szöveg** Docker-rendszerkép tárháza. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | A **beszéd – szöveg** Docker-rendszerkép címkéje. | `latest` |
| `image.pullSecrets` | A **hang-szöveg** Docker-rendszerkép kihúzásának képtitka. | |
| `image.pullByHash`| Azt jelzi, hogy a Docker-rendszerképet kivonattal húzta-e. `image.hash` Ha `true`kötelező megadni. | `false` |
| `image.hash`| A **beszéd – szöveg** Docker-rendszerkép kivonata. Csak akkor használatos, ha `image.pullByHash: true`.  | |
| `image.args.eula`szükséges | Azt jelzi, hogy elfogadta a licencet. Az egyetlen érvényes érték a`accept` | |
| `image.args.billing`szükséges | A számlázási végpont URI-ja a Azure Portal beszédének áttekintése oldalon érhető el. | |
| `image.args.apikey`szükséges | A számlázási információk nyomon követésére szolgál. ||
| `service.type` | A **beszéd-szöveg** típusú szolgáltatás Kubernetes. További részletekért tekintse meg a [Kubernetes-szolgáltatások típusaival kapcsolatos útmutatót](https://kubernetes.io/docs/concepts/services-networking/service/) , és ellenőrizze a felhőalapú szolgáltatók támogatását. | `LoadBalancer` |
| `service.port`|  A **beszédfelismerési** szolgáltatás portja. | `80` |
| `service.annotations` | A szolgáltatás metaadatainak **beszéd-szöveg** típusú megjegyzései. A jegyzetek a kulcs érték párok. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Azt jelzi, hogy engedélyezve van-e a [horizontális Pod automéretező](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) . Ha `true`a, `speech-to-text-autoscaler` a a Kubernetes-fürtben lesz telepítve. | `true` |
| `service.podDisruption.enabled` | Azt jelzi, hogy engedélyezve van-e a [Pod-megszakadási költségvetés](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) . Ha `true`a, `speech-to-text-poddisruptionbudget` a a Kubernetes-fürtben lesz telepítve. | `true` |