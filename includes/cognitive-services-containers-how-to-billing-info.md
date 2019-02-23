---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 002/08/2019
ms.openlocfilehash: 12c86ca71b7421678b68684cccca86411d604d61
ms.sourcegitcommit: 90c6b63552f6b7f8efac7f5c375e77526841a678
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/23/2019
ms.locfileid: "56741176"
---
Cognitive Services-tárolók nem teszi lehetővé az Azure-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig a mérési szolgáltatással számlázási adatokat. Cognitive Services-tárolók nem (például a lemezkép vagy az elemezni kívánt szöveget) a vásárlói adatokat küldeni a Microsoftnak. A tároló használati jelentések kapcsolatos 10 – 15 percenként.

A `docker run` számlázási argumentumai:

| Beállítás | Leírás |
|--------|-------------|
| `ApiKey` | Az API-kulcsot a Cognitive Services-szolgáltatás-erőforrás segítségével nyomon követhető a számlázási adatokat.<br/>Ez a beállítás értékét állítsa a kiosztott erőforrás a megadott API-kulcs `Billing`. |
| `Billing` | A végpont a Cognitive Services-szolgáltatás-erőforrás segítségével nyomon követhető a számlázási adatokat.<br/>Ez a beállítás értékét állítsa a végpontot egy üzembe helyezett LUIS Azure erőforrás URI-ját.|
| `Eula` | Azt jelzi, hogy Ön már elfogadta a tároló licencét.<br/>Ez a beállítás értékét állítsa `accept`. |

> [!IMPORTANT]
> Mindhárom meg kell adni az érvényes értékek, vagy a tároló nem indul el.
