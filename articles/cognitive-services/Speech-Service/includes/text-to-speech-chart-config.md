---
title: Beszédfelismerési tárolók telepítése
titleSuffix: Azure Cognitive Services
description: A szöveg-beszéd Helm diagram konfigurációs beállításainak részletei.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: e6c7dcd3015b0b8ab5b3c719ebd2397bc814b81a
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "67717253"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Szöveg-beszéd (Aldiagram: diagramok/textToSpeech)

Az "esernyő" diagram felülbírálásához adja hozzá az előtagot `textToSpeech.` bármelyik paraméterhez, hogy pontosabb legyen. Például felülbírálja a megfelelő paramétert, például `textToSpeech.numberOfConcurrentRequest` `numberOfConcurrentRequest`felülbírálásokat.

|Paraméter|Leírás|Alapértelmezett|
| -- | -- | -- |
| `enabled` | Azt jelzi, hogy engedélyezve van-e a **szöveg-beszéd** szolgáltatás. | `false` |
| `numberOfConcurrentRequest` | A **szöveg-beszéd** típusú szolgáltatás egyidejű kérelmeinek száma. Ez a diagram automatikusan kiszámítja a processzor-és memória-erőforrásokat ezen érték alapján. | `2` |
| `optimizeForTurboMode`| Azt határozza meg, hogy a szolgáltatásnak szöveges fájlok használatával kell-e optimalizálnia a szövegbevitelt. Ha `true`ez a diagram több CPU-erőforrást fog kiosztani a szolgáltatás számára. | `false` |
| `image.registry`| A **szöveg-beszéd** Docker-rendszerkép beállításjegyzéke. | `containerpreview.azurecr.io` |
| `image.repository` | A **szöveg-beszéd** Docker-rendszerkép tárháza. | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | A **szöveg és a beszéd** Docker-rendszerkép címkéje. | `latest` |
| `image.pullSecrets` | A **szöveg-beszéd** Docker-rendszerkép kihúzásának képtitka. | |
| `image.pullByHash`| Azt jelzi, hogy a Docker-rendszerképet kivonattal húzta-e. Ha `true`kötelezőmegadni. `image.hash` | `false` |
| `image.hash`| A **szöveg-beszéd** Docker-rendszerkép kivonata. Csak akkor használatos, ha `image.pullByHash: true`.  | |
| `image.args.eula`szükséges | Azt jelzi, hogy elfogadta a licencet. Az egyetlen érvényes érték a`accept` | |
| `image.args.billing`szükséges | A számlázási végpont URI-ja a Azure Portal beszédének áttekintése oldalon érhető el. | |
| `image.args.apikey`szükséges | Számlázási adatok nyomon követésére szolgál. ||
| `service.type` | A **szöveg-beszéd** típusú szolgáltatás Kubernetes. További részletekért tekintse meg a [Kubernetes-szolgáltatások típusaival kapcsolatos útmutatót](https://kubernetes.io/docs/concepts/services-networking/service/) , és ellenőrizze a felhőalapú szolgáltatók támogatását. | `LoadBalancer` |
| `service.port`|  A **szöveg-beszéd** típusú szolgáltatás portja. | `80` |
| `service.autoScaler.enabled` | Azt jelzi, hogy engedélyezve van-e a [horizontális Pod automéretező](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) . Ha `true`a, `text-to-speech-autoscaler` a a Kubernetes-fürtben lesz telepítve. | `true` |
| `service.podDisruption.enabled` | Azt jelzi, hogy engedélyezve van-e a pod-megszakadási [költségvetés](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) . Ha `true`a, `text-to-speech-poddisruptionbudget` a a Kubernetes-fürtben lesz telepítve. | `true` |