---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: e10de8093bf152b75cc6f262a142ff07c3d5b0d3
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72329590"
---
Már létrehozott egy Function alkalmazást az Azure-ban, a szükséges Storage-fiókkal együtt. A fiókhoz tartozó kapcsolatok karakterlánca biztonságosan tárolódik az Azure-beli alkalmazás beállításaiban. Ebben a cikkben egy fiókba írja az üzeneteket egy tárolási várólistába. Ha a funkciót helyileg futtatja, a Storage-fiókhoz való csatlakozáshoz le kell töltenie az Alkalmazásbeállítások a local. Settings. JSON fájlra. 

A projekt gyökeréből futtassa a következő Azure Functions Core Tools parancsot a beállítások helyi. Settings. JSON fájlra való letöltéséhez, és a `<APP_NAME>` helyére cserélje le a Function alkalmazás nevét az előző cikkből:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Előfordulhat, hogy be kell jelentkeznie az Azure-fiókjába.

> [!IMPORTANT]  
> Ez a parancs felülírja a meglévő beállításokat az Azure-beli Function alkalmazás értékeivel.  
>
> Mivel titkokat tartalmaz, a local. Settings. JSON fájl soha nem kerül közzétételre, és ki kell zárni a forrás-vezérlőelemből.  
> 

@No__t-0 értékre van szüksége, amely a Storage-fiókhoz tartozó kapcsolatok karakterlánca. Ezzel a kapcsolattal ellenőrizheti, hogy a kimeneti kötés a várt módon működik-e.
