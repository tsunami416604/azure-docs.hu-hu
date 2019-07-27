---
title: Beszédfelismerési tárolók telepítése
titleSuffix: Azure Cognitive Services
description: A beszéd – szöveg Helm diagram konfigurációs beállításainak részletei.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1b46c58d3f3c804052e637f7bde2e1a456764dba
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "67717224"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Beszéd – szöveg (Aldiagram: diagramok/speechToText)

Az "esernyő" diagram felülbírálásához adja hozzá az előtagot `speechToText.` bármelyik paraméterhez, hogy pontosabb legyen. Például felülbírálja a megfelelő paramétert, például `speechToText.numberOfConcurrentRequest` `numberOfConcurrentRequest`felülbírálásokat.

|Paraméter|Leírás|Alapértelmezett|
| -- | -- | -- |
| `enabled` | Azt határozza **meg** , hogy a beszédfelismerési szolgáltatás engedélyezve van-e. | `false` |
| `numberOfConcurrentRequest` | A beszédfelismerési szolgáltatáshoz való egyidejű  kérelmek száma. Ez a diagram automatikusan kiszámítja a processzor-és memória-erőforrásokat ezen érték alapján. | `2` |
| `optimizeForAudioFile`| Azt határozza meg, hogy a szolgáltatásnak hangfájlokon keresztül kell-e optimalizálnia a hangbemenetet. Ha `true`ez a diagram több CPU-erőforrást fog kiosztani a szolgáltatás számára. | `false` |
| `image.registry`| A **beszéd-szöveg** Docker-rendszerkép beállításjegyzéke. | `containerpreview.azurecr.io` |
| `image.repository` | A **beszéd-szöveg** Docker-rendszerkép tárháza. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | A **beszéd – szöveg** Docker-rendszerkép címkéje. | `latest` |
| `image.pullSecrets` | A **hang-szöveg** Docker-rendszerkép kihúzásának képtitka. | |
| `image.pullByHash`| Azt jelzi, hogy a Docker-rendszerképet kivonattal húzta-e. Ha `true`kötelezőmegadni. `image.hash` | `false` |
| `image.hash`| A **beszéd – szöveg** Docker-rendszerkép kivonata. Csak akkor használatos, ha `image.pullByHash: true`.  | |
| `image.args.eula`szükséges | Azt jelzi, hogy elfogadta a licencet. Az egyetlen érvényes érték a`accept` | |
| `image.args.billing`szükséges | A számlázási végpont URI-ja a Azure Portal beszédének áttekintése oldalon érhető el. | |
| `image.args.apikey`szükséges | Számlázási adatok nyomon követésére szolgál. ||
| `service.type` | A **beszéd-szöveg** típusú szolgáltatás Kubernetes. További részletekért tekintse meg a [Kubernetes-szolgáltatások típusaival kapcsolatos útmutatót](https://kubernetes.io/docs/concepts/services-networking/service/) , és ellenőrizze a felhőalapú szolgáltatók támogatását. | `LoadBalancer` |
| `service.port`|  A beszédfelismerési szolgáltatás  portja. | `80` |
| `service.autoScaler.enabled` | Azt jelzi, hogy engedélyezve van-e a [horizontális Pod automéretező](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) . Ha `true`a, `speech-to-text-autoscaler` a a Kubernetes-fürtben lesz telepítve. | `true` |
| `service.podDisruption.enabled` | Azt jelzi, hogy engedélyezve van-e a pod-megszakadási [költségvetés](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) . Ha `true`a, `speech-to-text-poddisruptionbudget` a a Kubernetes-fürtben lesz telepítve. | `true` |