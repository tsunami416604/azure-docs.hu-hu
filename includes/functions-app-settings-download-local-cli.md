---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "72329590"
---
Már létrehozott egy függvényalkalmazást az Azure-ban, valamint a szükséges storage-fiókot. A fiók kapcsolati karakterlánca biztonságosan tárolódik az Azure-beli alkalmazásbeállításokban. Ebben a cikkben üzeneteket ír egy storage-várólistába ugyanabban a fiókban. Ha a funkció helyi futtatásakor szeretne csatlakozni a tárfiókhoz, le kell töltenie az alkalmazásbeállításokat a local.settings.json fájlba. 

A projekt gyökeréből futtassa a következő Azure Functions Core Tools parancsot `<APP_NAME>` a beállítások letöltéséhez a local.settings.json fájlba, és helyettesítse a függvényalkalmazás nevét az előző cikkből:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Előfordulhat, hogy be kell jelentkeznie az Azure-fiókjába.

> [!IMPORTANT]  
> Ez a parancs felülírja a meglévő beállításokat az Azure-beli függvényalkalmazás értékeivel.  
>
> Mivel titkos kulcsokat tartalmaz, a local.settings.json fájl soha nem lesz közzétéve, és ki kell zárni a forrásellenőrzésből.  
> 

Szüksége van `AzureWebJobsStorage`az értékre, amely a Storage-fiók kapcsolati karakterlánca. Ezzel a kapcsolattal ellenőrizheti, hogy a kimeneti kötés a várt módon működik-e.
