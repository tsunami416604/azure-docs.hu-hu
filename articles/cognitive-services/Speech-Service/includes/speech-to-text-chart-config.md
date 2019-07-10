---
title: Telepítse a beszédfelismerési tárolókhoz
titleSuffix: Azure Cognitive Services
description: A hang-szöveg transzformációs helm-diagram konfigurációs beállításokat ismerteti.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: 1b46c58d3f3c804052e637f7bde2e1a456764dba
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717224"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Hang-szöveg transzformációs (alárendelt diagram: diagramok/speechToText)

Bírálja felül az "összevonó" diagram, adja hozzá az előtagot `speechToText.` , hogy pontosabb bármely paraméter. Például az felül fogja írni a megfelelő paraméter például `speechToText.numberOfConcurrentRequest` felülbírálások `numberOfConcurrentRequest`.

|Paraméter|Leírás|Alapértelmezett|
| -- | -- | -- |
| `enabled` | -E a **hang-szöveg transzformációs** szolgáltatás engedélyezve van. | `false` |
| `numberOfConcurrentRequest` | Az egyidejű kérelmek számát a **hang-szöveg transzformációs** szolgáltatás. Ez a diagram automatikusan a CPU és memória-erőforrások, ez az érték alapján számítja ki. | `2` |
| `optimizeForAudioFile`| A szolgáltatás e kell hangbemeneti keresztül hangfájlok optimalizálása. Ha `true`, ez a diagram szolgáltatásnak több Processzor-erőforrást foglal le. | `false` |
| `image.registry`| A **hang-szöveg transzformációs** docker regisztrációs adatbázisba. | `containerpreview.azurecr.io` |
| `image.repository` | A **hang-szöveg transzformációs** docker lemezképtárban. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | A **hang-szöveg transzformációs** docker rendszerkép címke. | `latest` |
| `image.pullSecrets` | A kép titkos kódjainak lekérése a **hang-szöveg transzformációs** docker-rendszerképet. | |
| `image.pullByHash`| Hogy a docker-rendszerkép kivonata alapján kéri le. Ha `true`, `image.hash` megadása kötelező. | `false` |
| `image.hash`| A **hang-szöveg transzformációs** docker rendszerkép kivonat. Csak a használt `image.pullByHash: true`.  | |
| `image.args.eula` (kötelező) | Azt jelzi, hogy Ön már elfogadta a licencet. Az egyetlen érvényes érték: `accept` | |
| `image.args.billing` (kötelező) | A számlázási végpont URI azonosítóját az Azure Portalon beszédfelismerés – Áttekintés lapon érhető el. | |
| `image.args.apikey` (kötelező) | Számlázási adatok nyomon követésére szolgál. ||
| `service.type` | A Kubernetes szolgáltatás típusa az **hang-szöveg transzformációs** szolgáltatás. Tekintse meg a [Kubernetes szolgáltatástípusok utasításokat](https://kubernetes.io/docs/concepts/services-networking/service/) további részletekért és a felhő által támogatott szolgáltatók ellenőrzése. | `LoadBalancer` |
| `service.port`|  A port, a **hang-szöveg transzformációs** szolgáltatás. | `80` |
| `service.autoScaler.enabled` | -E a [vízszintes Pod méretező](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) engedélyezve van. Ha `true`, a `speech-to-text-autoscaler` lesz telepítve, a Kubernetes-fürt. | `true` |
| `service.podDisruption.enabled` | -E a [Pod megszakítás költségvetés](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) engedélyezve van. Ha `true`, a `speech-to-text-poddisruptionbudget` lesz telepítve, a Kubernetes-fürt. | `true` |