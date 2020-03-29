---
title: Beszédtárolók telepítése
titleSuffix: Azure Cognitive Services
description: Részletek a szöveg-beszéd helm diagram konfigurációs lehetőségeket.
services: cognitive-services
author: IEvangelist
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: include
ms.date: 08/22/2019
ms.author: dapine
ms.openlocfilehash: c87132c3ae41dec82e3493f9a0ee2397455ff881
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "69971348"
---
### <a name="text-to-speech-sub-chart-chartstexttospeech"></a>Szövegfelolvasás (aldiagram: diagramok/szövegBeszédszó)

Az "esernyő" diagram felülbírálásához adja `textToSpeech.` hozzá az előtagot bármely paraméterhez, hogy pontosabb legyen. Például felülbírálja a megfelelő paramétert, `textToSpeech.numberOfConcurrentRequest` például felülbírálja a . `numberOfConcurrentRequest`

|Paraméter|Leírás|Alapértelmezett|
| -- | -- | -- |
| `enabled` | Azt jelzi, hogy engedélyezve **van-e a szövegfelolvasó** szolgáltatás. | `false` |
| `numberOfConcurrentRequest` | A **szövegfelolvasó** szolgáltatás egyidejű kéréseinek száma. Ez a diagram automatikusan kiszámítja a PROCESSZOR- és memóriaerőforrásokat ezen érték alapján. | `2` |
| `optimizeForTurboMode`| Azt jelzi, hogy a szolgáltatásnak optimalizálnia kell-e a szövegbevitelt szövegfájlokon keresztül. Ha `true`a diagram több CPU-erőforrást rendel a szolgáltatáshoz. | `false` |
| `image.registry`| A **szövegfelolvasó** docker rendszerkép-beállításjegyzék. | `containerpreview.azurecr.io` |
| `image.repository` | A **szövegfelolvasó** docker-rendszertár. | `microsoft/cognitive-services-text-to-speech` |
| `image.tag` | A **szövegfelolvasó** docker-képcímke. | `latest` |
| `image.pullSecrets` | A **szövegfelolvasó docker-lemezkép** lehúzásának titkai. | |
| `image.pullByHash`| Azt jelzi, hogy a docker-lemezképet kivonat oltja-e le. Ha `true` `image.hash` szükséges, hogy a. | `false` |
| `image.hash`| A **szövegfelolvasó docker-kép** kivonata. Csak akkor `image.pullByHash: true`használható, ha .  | |
| `image.args.eula`(kötelező) | Azt jelzi, hogy elfogadta a licencet. Az egyetlen érvényes érték`accept` | |
| `image.args.billing`(kötelező) | A számlázási végpont URI-értéke elérhető az Azure Portal beszédfelismerési áttekintése lapon. | |
| `image.args.apikey`(kötelező) | Számlázási adatok nyomon követésére szolgál. ||
| `service.type` | A **szövegfelolvasó** szolgáltatás Kubernetes szolgáltatástípusa. További részletekért tekintse meg a [Kubernetes-szolgáltatástípusokra vonatkozó utasításokat,](https://kubernetes.io/docs/concepts/services-networking/service/) és ellenőrizze a felhőszolgáltató támogatását. | `LoadBalancer` |
| `service.port`|  A **szövegfelolvasó** szolgáltatás portja. | `80` |
| `service.annotations` | A **text-to-speech** szolgáltatás metaadatainak szövegfelolvasó-valancításai. A jegyzetek kulcsértékpárok. <br>`annotations:`<br>&nbsp;&nbsp;`some/annotation1: value1`<br>&nbsp;&nbsp;`some/annotation2: value2` | |
| `service.autoScaler.enabled` | Azt jelzi, hogy a [vízszintes pod automatikus skálázó](https://kubernetes.io/docs/tasks/run-application/horizontal-pod-autoscale/) engedélyezve van-e. Ha `true`a `text-to-speech-autoscaler` lesz telepítve a Kubernetes-fürtben. | `true` |
| `service.podDisruption.enabled` | Azt jelzi, hogy a [pod megszakítási költségvetés](https://kubernetes.io/docs/concepts/workloads/pods/disruptions/) engedélyezve van-e. Ha `true`a `text-to-speech-poddisruptionbudget` lesz telepítve a Kubernetes-fürtben. | `true` |