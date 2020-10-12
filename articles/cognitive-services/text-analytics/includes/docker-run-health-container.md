---
title: Tároló futtatása példa a Docker Run parancsra
titleSuffix: Azure Cognitive Services
description: Docker-futtatási parancs az állapotfigyelő tárolóhoz
services: cognitive-services
author: aahill
manager: nitinme
ms.service: cognitive-services
ms.topic: include
ms.date: 07/07/2020
ms.author: aahi
ms.openlocfilehash: 72a877ab8cfda126016376017c0a7eb42b1186d6
ms.sourcegitcommit: 829d951d5c90442a38012daaf77e86046018e5b9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/09/2020
ms.locfileid: "86108932"
---
A tároló futtatásához először keresse meg a rendszerkép AZONOSÍTÓját:
 
```bash
docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
```

Hajtsa végre a következő `docker run` parancsot. Cserélje le az alábbi helyőrzőket a saját értékeire:

| Helyőrző | Érték | Formátum vagy példa |
|-------------|-------|---|
| **{API_KEY}** | A Text Analytics erőforrás kulcsa. A Azure Portal az erőforrás **kulcs és végpont** lapján található. |`xxxxxxxxxxxxxxxxxxxxxxxxxxxxxxxx`|
| **{ENDPOINT_URI}** | A Text Analytics API elérésére szolgáló végpont. A Azure Portal az erőforrás **kulcs és végpont** lapján található. | `https://<your-custom-subdomain>.cognitiveservices.azure.com` |
| **{IMAGE_ID}** | A tároló rendszerkép-azonosítója. | `1.1.011300001-amd64-preview` |
| **{INPUT_DIR}** | A tároló bemeneti könyvtára. | Windows `C:\healthcareMount` <br> Linux/MacOS: `/home/username/input` |

```bash
docker run --rm -it -p 5000:5000 --cpus 6 --memory 12g \
--mount type=bind,src={INPUT_DIR},target=/output {IMAGE_ID} \
Eula=accept \
Billing={ENDPOINT_URI} \
ApiKey={API_KEY} \
Logging:Disk:Format=json
```

A parancs a következőket hajtja végre:

- Feltételezi, hogy a bemeneti könyvtár létezik a gazdagépen.
- Text Analytics futtat az állapotfigyelő tárolóból a tároló képéből
- 6 CPU-mag és 12 gigabájt (GB) memória foglalása
- Elérhetővé teszi a 5000-es TCP-portot, és kiosztja a tárolóhoz tartozó pszeudo-TTY-t
- A automatikusan eltávolítja a tárolót a kilépés után. A tároló rendszerképe továbbra is elérhető a gazdaszámítógépen.
