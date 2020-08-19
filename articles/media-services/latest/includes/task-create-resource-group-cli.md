---
author: IngridAtMicrosoft
ms.service: media-services
ms.topic: include
ms.date: 08/17/2020
ms.author: inhenkel
ms.custom: CLI
ms.openlocfilehash: d567a4f7d9b9429887d6396cb2b03dfc34c6ac93
ms.sourcegitcommit: 37afde27ac137ab2e675b2b0492559287822fded
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/18/2020
ms.locfileid: "88602352"
---
<!-- Create a resource group -->

Az alábbi parancs használatával hozzon létre egy erőforráscsoportot. Válassza ki azt a földrajzi régiót, amelyet a rendszer a Media Services-fiókhoz tartozó média és metaadat-rekordok tárolására fog használni. Ez a régió fogja használni az adathordozó feldolgozását és továbbítását.

```azurecli
az group create --name amsResourceGroup --location westus2
```
