---
title: Beszédtárolók telepítése
titleSuffix: Azure Cognitive Services
description: Részletek a beszéd-szöveg helm diagram konfigurációs lehetőségeket.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: 3f390affe7badb401277aa86d1867c763aa0ae3b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "69971325"
---
### <a name="speech-to-text-sub-chart-chartsspeechtotext"></a>Beszéd-szöveg (aldiagram: diagramok/beszédSzöveg)

Az "esernyő" diagram felülbírálásához adja `speechToText.` hozzá az előtagot bármely paraméterhez, hogy pontosabb legyen. Például felülbírálja a megfelelő paramétert, `speechToText.numberOfConcurrentRequest` például felülbírálja a . `numberOfConcurrentRequest`

|Paraméter|Leírás|Alapértelmezett|
| -- | -- | -- |
| `enabled` | Azt **jelzi, hogy a beszéd-szöveg** szolgáltatás engedélyezve van-e. | `false` |
| `numberOfConcurrentRequest` | A **beszéd-szöveg** szolgáltatás egyidejű kéréseinek száma. Ez a diagram automatikusan kiszámítja a PROCESSZOR- és memóriaerőforrásokat ezen érték alapján. | `2` |
| `optimizeForAudioFile`| Azt jelzi, hogy a szolgáltatásnak optimalizálnia kell-e a hangbemenetet hangfájlokon keresztül. Ha `true`a diagram több CPU-erőforrást rendel a szolgáltatáshoz. | `false` |
| `image.registry`| A **beszéd-szöveg** docker lemezkép-beállításjegyzék. | `containerpreview.azurecr.io` |
| `image.repository` | A **beszéd-szöveg** docker rendszertárház. | `microsoft/cognitive-services-speech-to-text` |
| `image.tag` | A **beszéd-szöveg** docker image tag. | `latest` |
| `image.pullSecrets` | A **szöveggéb-olvasási docker-lemezkép** lehúzásának képtitkai. | |
| `image.pullByHash`| Azt jelzi, hogy a docker-lemezképet kivonat oltja-e le. Ha `true` `image.hash` szükséges, hogy a. | `false` |
| `image.hash`| A **beszéd-szöveg** docker kép kivonat. Csak akkor `image.pullByHash: true`használható, ha .  | |
| `image.args.eula`(kötelező) | Azt jelzi, hogy elfogadta a licencet. Az egyetlen érvényes érték`accept` | |
| `image.args.billing`(kötelező) | A számlázási végpont URI-értéke elérhető az Azure Portal beszédfelismerési áttekintése lapon. | |
| `image.args.apikey`(kötelező) | Számlázási adatok nyomon követésére szolgál. ||
| `service.type` | A **beszéd-szöveg** szolgáltatás Kubernetes szolgáltatástípusa. További részletekért tekintse meg a [Kubernetes-szolgáltatástípusokra vonatkozó utasításokat,](https://kubernetes.io/docs/concepts/services-networking/service/) és ellenőrizze a felhőszolgáltató támogatását. | `LoadBalancer` |
| `service.port`|  A **beszéd-szöveg** szolgáltatás portja. | `80` |
| `service.annotations` | A szolgáltatás metaadatainak **szöveggéi.** A jegyzetek kulcsértékpárok. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Azt jelzi, hogy a [vízszintes pod automatikus skálázó](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) engedélyezve van-e. Ha `true`a `speech-to-text-autoscaler` lesz telepítve a Kubernetes-fürtben. | `true` |
| `service.podDisruption.enabled` | Azt jelzi, hogy a [pod megszakítási költségvetés](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) engedélyezve van-e. Ha `true`a `speech-to-text-poddisruptionbudget` lesz telepítve a Kubernetes-fürtben. | `true` |