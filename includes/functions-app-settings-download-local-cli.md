---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "72329590"
---
Már létrehozott egy Function alkalmazást az Azure-ban, a szükséges Storage-fiókkal együtt. A fiókhoz tartozó kapcsolatok karakterlánca biztonságosan tárolódik az Azure-beli alkalmazás beállításaiban. Ebben a cikkben egy fiókba írja az üzeneteket egy tárolási várólistába. Ha a funkciót helyileg futtatja, a Storage-fiókhoz való csatlakozáshoz le kell töltenie az Alkalmazásbeállítások a local. Settings. JSON fájlra. 

A projekt gyökeréből futtassa a következő Azure Functions Core Tools parancsot a beállítások helyi. Settings. JSON fájlra való letöltéséhez, `<APP_NAME>` és cserélje le a függvényt az előző cikkből a Function alkalmazás nevére:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Előfordulhat, hogy be kell jelentkeznie az Azure-fiókjába.

> [!IMPORTANT]  
> Ez a parancs felülírja a meglévő beállításokat az Azure-beli Function alkalmazás értékeivel.  
>
> Mivel titkokat tartalmaz, a local. Settings. JSON fájl soha nem kerül közzétételre, és ki kell zárni a forrás-vezérlőelemből.  
> 

Szüksége lesz az értékre `AzureWebJobsStorage`, amely a Storage-fiókhoz tartozó kapcsolatok karakterlánca. Ezzel a kapcsolattal ellenőrizheti, hogy a kimeneti kötés a várt módon működik-e.
