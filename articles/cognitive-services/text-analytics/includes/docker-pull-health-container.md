---
title: Docker-lekérés az állapotfigyelő tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker-lekérési parancs az Text Analytics for Health tárolóhoz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 4c33952ed56399b94a7db1088cb60919a4f88137
ms.sourcegitcommit: d7008edadc9993df960817ad4c5521efa69ffa9f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/08/2020
ms.locfileid: "86108981"
---
Töltse ki és küldje el a [Cognitive Services tárolók kérelem űrlapját](https://aka.ms/cognitivegate) , hogy hozzáférést Kérjen a tárolóhoz.

[!INCLUDE [Request access to the container registry](../../../../includes/cognitive-services-containers-request-access-only.md)]

A bevezetési e-mailben megadott hitelesítő adatokkal a Docker login paranccsal csatlakozhat a Cognitive Services tárolók saját tároló-beállításjegyzékéhez.

```bash
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Használja a [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) parancsot a tároló rendszerképének letöltéséhez a saját tároló beállításjegyzékből.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
