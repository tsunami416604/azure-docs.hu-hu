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
ms.openlocfilehash: b9452baf0301233a1cd6e05621a89d013d32c9e0
ms.sourcegitcommit: 53acd9895a4a395efa6d7cd41d7f78e392b9cfbe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/22/2020
ms.locfileid: "90906000"
---
Töltse ki és küldje el a [Cognitive Services tárolók kérelem űrlapját](https://aka.ms/cognitivegate) , hogy hozzáférést Kérjen a tárolóhoz.
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
