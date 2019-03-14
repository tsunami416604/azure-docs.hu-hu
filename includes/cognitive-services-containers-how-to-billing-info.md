---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/06/2019
ms.openlocfilehash: b20ee2df1f468323c3298854371c97950c017e49
ms.sourcegitcommit: 5fbca3354f47d936e46582e76ff49b77a989f299
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/12/2019
ms.locfileid: "57788858"
---
A tároló a lekérdezések számlázzuk használt Azure-erőforrás tarifacsomagja a `<ApiKey>`.

Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services-tárolók nem (például a lemezkép vagy az elemezni kívánt szöveget) a vásárlói adatokat küldeni a Microsoftnak. A tároló használati jelentések kapcsolatos 10 – 15 percenként.

A `docker run` számlázási argumentumai:

| Beállítás | Leírás |
|--------|-------------|
| `ApiKey` | Az API-kulcsot a Cognitive Services-szolgáltatás-erőforrás segítségével nyomon követhető a számlázási adatokat.<br/>Ez a beállítás értékét állítsa a kiosztott erőforrás a megadott API-kulcs `Billing`. |
| `Billing` | A végpont a Cognitive Services-szolgáltatás-erőforrás segítségével nyomon követhető a számlázási adatokat.<br/>Ez a beállítás értékét állítsa a végpontot egy üzembe helyezett LUIS Azure erőforrás URI-ját.|
| `Eula` | Azt jelzi, hogy Ön már elfogadta a tároló licencét.<br/>Ez a beállítás értékét állítsa `accept`. |

> [!IMPORTANT]
> Mindhárom meg kell adni az érvényes értékek, vagy a tároló nem indul el.
