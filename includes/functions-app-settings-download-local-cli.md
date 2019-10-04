---
author: ggailey777
ms.service: azure-functions
ms.topic: include
ms.date: 09/26/2019
ms.author: glenga
ms.openlocfilehash: 801613f4e3fb1e754856d716c6815895ea5da3aa
ms.sourcegitcommit: 15e3bfbde9d0d7ad00b5d186867ec933c60cebe6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/03/2019
ms.locfileid: "71839143"
---
Már létrehozott egy Function alkalmazást az Azure-ban, a szükséges Storage-fiókkal együtt. A fiókhoz tartozó kapcsolatok karakterlánca biztonságosan tárolódik az Azure-beli alkalmazás beállításaiban. Ebben a cikkben egy fiókba írja az üzeneteket egy tárolási várólistába. Ha a funkciót helyileg futtatja, a Storage-fiókhoz való csatlakozáshoz le kell töltenie az Alkalmazásbeállítások a local. Settings. JSON fájlra. A következő Azure Functions Core Tools parancs futtatásával töltse le a beállításokat a local. Settings. JSON fájlra, és cserélje le a `<APP_NAME>` értéket az előző cikkből származó Function alkalmazás nevével:

```bash
func azure functionapp fetch-app-settings <APP_NAME>
```

Előfordulhat, hogy be kell jelentkeznie az Azure-fiókjába.

> [!IMPORTANT]  
> Mivel titkokat tartalmaz, a local. Settings. JSON fájl soha nem kerül közzétételre, és ki kell zárni a forrás-vezérlőelemből.

@No__t-0 értékre van szüksége, amely a Storage-fiókhoz tartozó kapcsolatok karakterlánca. Ezzel a kapcsolattal ellenőrizheti, hogy a kimeneti kötés a várt módon működik-e.
