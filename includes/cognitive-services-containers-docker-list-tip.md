---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 02/11/2019
ms.openlocfilehash: 480cec39e42e075ad83ad3e32b88c638094db64f
ms.sourcegitcommit: 3e98da33c41a7bbd724f644ce7dedee169eb5028
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67179395"
---
> [!TIP]
> Használhatja a [docker-rendszerképek](https://docs.docker.com/engine/reference/commandline/images/) paranccsal listát készíthet a letöltött tárolólemezképek. Például a következő parancs megjeleníti az azonosítója, a tárházat, és a címke az egyes letöltött tárolórendszerképet, és táblázatként vannak formázva:
>
>  ```
>  docker images --format "table {{.ID}}\t{{.Repository}}\t{{.Tag}}"
>
>  IMAGE ID            REPOSITORY              TAG
>  ebbee78a6baa       <container-name>         latest
>  ``` 