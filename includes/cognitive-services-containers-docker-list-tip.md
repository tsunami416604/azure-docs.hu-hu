---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 01/24/2019
ms.openlocfilehash: e7960bd288e87c4e3c6b395322a5ab92f3040d88
ms.sourcegitcommit: a7331d0cc53805a7d3170c4368862cad0d4f3144
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/30/2019
ms.locfileid: "55302311"
---
> [!TIP]
> Használhatja a [docker-rendszerképek](https://docs.docker.com/engine/reference/commandline/images/) paranccsal listát készíthet a letöltött tárolólemezképek. Például a következő parancs megjeleníti az azonosítója, a tárházat, és a címke az egyes letöltött tárolórendszerképet, és táblázatként vannak formázva:
>
>  ```Docker
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID            REPOSITORY                                                                TAG
>  ebbee78a6baa        mcr.microsoft.com/azure-cognitive-services/luis                           latest
>  ``` 