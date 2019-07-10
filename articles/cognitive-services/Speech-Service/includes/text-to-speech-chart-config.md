---
title: Telepítse a beszédfelismerési tárolókhoz
titleSuffix: Azure Cognitive Services
description: A szöveg-hang transzformációs helm-diagram konfigurációs beállításokat ismerteti.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 06/26/2019
ms.author: dapine
ms.openlocfilehash: e6c7dcd3015b0b8ab5b3c719ebd2397bc814b81a
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2019
ms.locfileid: "67717253"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Szöveg-hang transzformációs (alárendelt diagram: diagramok/textToSpeech)

Bírálja felül az "összevonó" diagram, adja hozzá az előtagot `textToSpeech.` , hogy pontosabb bármely paraméter. Például az felül fogja írni a megfelelő paraméter például `textToSpeech.numberOfConcurrentRequest` felülbírálások `numberOfConcurrentRequest`.

|Paraméter|Leírás|Alapértelmezett|
| -- | -- | -- |
| `enabled` | -E a **szöveg-hang transzformációs** szolgáltatás engedélyezve van. | `false` |
| `numberOfConcurrentRequest` | Az egyidejű kérelmek számát a **szöveg-hang transzformációs** szolgáltatás. Ez a diagram automatikusan a CPU és memória-erőforrások, ez az érték alapján számítja ki. | `2` |
| `optimizeForTurboMode`| A szolgáltatás e kell optimalizálása a szövegbevitel keresztül szöveges fájlok. Ha `true`, ez a diagram szolgáltatásnak több Processzor-erőforrást foglal le. | `false` |
| `image.registry`| A **szöveg-hang transzformációs** docker regisztrációs adatbázisba. | `containerpreview.azurecr.io` |
| `image.repository` | A **szöveg-hang transzformációs** docker lemezképtárban. | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | A **szöveg-hang transzformációs** docker rendszerkép címke. | `latest` |
| `image.pullSecrets` | A kép titkos kódjainak lekérése a **szöveg-hang transzformációs** docker-rendszerképet. | |
| `image.pullByHash`| Hogy a docker-rendszerkép kivonata alapján kéri le. Ha `true`, `image.hash` megadása kötelező. | `false` |
| `image.hash`| A **szöveg-hang transzformációs** docker rendszerkép kivonat. Csak a használt `image.pullByHash: true`.  | |
| `image.args.eula` (kötelező) | Azt jelzi, hogy Ön már elfogadta a licencet. Az egyetlen érvényes érték: `accept` | |
| `image.args.billing` (kötelező) | A számlázási végpont URI azonosítóját az Azure Portalon beszédfelismerés – Áttekintés lapon érhető el. | |
| `image.args.apikey` (kötelező) | Számlázási adatok nyomon követésére szolgál. ||
| `service.type` | A Kubernetes szolgáltatás típusa az **szöveg-hang transzformációs** szolgáltatás. Tekintse meg a [Kubernetes szolgáltatástípusok utasításokat](https://kubernetes.io/docs/concepts/services-networking/service/) további részletekért és a felhő által támogatott szolgáltatók ellenőrzése. | `LoadBalancer` |
| `service.port`|  A port, a **szöveg-hang transzformációs** szolgáltatás. | `80` |
| `service.autoScaler.enabled` | -E a [vízszintes Pod méretező](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) engedélyezve van. Ha `true`, a `text-to-speech-autoscaler` lesz telepítve, a Kubernetes-fürt. | `true` |
| `service.podDisruption.enabled` | -E a [Pod megszakítás költségvetés](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) engedélyezve van. Ha `true`, a `text-to-speech-poddisruptionbudget` lesz telepítve, a Kubernetes-fürt. | `true` |