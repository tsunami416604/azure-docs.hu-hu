---
title: Docker-lekérés az állapotfigyelő tárolóhoz
titleSuffix: Azure Cognitive Services
description: Docker-lekérési parancs az Text Analytics for Health tárolóhoz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.subservice: text-analytics
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 519625f6468372ec7ace523dae7648212f4f3203
ms.sourcegitcommit: ef69245ca06aa16775d4232b790b142b53a0c248
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/06/2020
ms.locfileid: "91779483"
---
Töltse ki és küldje el a [Cognitive Services tárolók kérelem űrlapját](https://aka.ms/csgate) , hogy hozzáférést Kérjen a tárolóhoz.
Az űrlap adatokat kér Önnek, a vállalatnak és a felhasználói forgatókönyvnek, amelyhez a tárolót fogja használni. Miután elküldte az űrlapot, az Azure Cognitive Services csapata ellenőrzi, hogy megfelelnek-e a Private Container registryhez való hozzáférés feltételeinek.

> [!IMPORTANT]
> * Az űrlapon az Azure-előfizetési AZONOSÍTÓhoz társított e-mail-címet kell használnia.
> * A tároló futtatásához használt Azure-erőforrást a jóváhagyott Azure-előfizetés azonosítójával kell létrehozni. 
> * A Microsofttól származó alkalmazás állapotával kapcsolatos frissítésekért keresse meg az e-mail-címét (a beérkezett fájlok és a levélszemét mappáját is).

A bevezetési e-mailben megadott hitelesítő adatokkal a Docker login paranccsal csatlakozhat a Cognitive Services tárolók saját tároló-beállításjegyzékéhez.


```Docker
docker login containerpreview.azurecr.io -u <username> -p <password>
```

Használja a [`docker pull`](https://docs.docker.com/engine/reference/commandline/pull/) parancsot a tároló rendszerképének letöltéséhez a saját tároló beállításjegyzékből.

```
docker pull containerpreview.azurecr.io/microsoft/cognitive-services-healthcare:latest
```
