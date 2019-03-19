---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 03/11/2019
ms.openlocfilehash: 200e2dfd2dd4f9aedd9256b307491a0b207ea124
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57964169"
---
A tároló a lekérdezések számlázzuk használt Azure-erőforrás tarifacsomagja a `<ApiKey>`.

Cognitive Services-tárolók nem teszi lehetővé a számlázási végpont-méréshez való csatlakozás nélkül. Az ügyfeleknek kell ahhoz, hogy a tárolókkal való kommunikációhoz mindig számlázási végponttal számlázási adatokat. Cognitive Services-tárolók nem (például a lemezkép vagy az elemezni kívánt szöveget) a vásárlói adatokat küldeni a Microsoftnak. 

### <a name="connecting-to-azure"></a>Csatlakozás az Azure-bA

A tároló a számlázási argumentumértékként futtatni kell. Ezek az értékek lehetővé teszik a számlázási végponthoz csatlakozik a tárolóhoz. A tároló használati jelentések kapcsolatos 10 – 15 percenként. Ha a tároló nem kapcsolódik az Azure-ban engedélyezett időtartományon belül, a tároló továbbra is futtatható, de a rendszer nem-lekérdezések kiszolgálása a számlázási végpont visszaállításáig. A csatlakozási kísérlet 10 alkalommal az azonos idő 10 – 15 perces időközönként. Ha a számlázási végpont belül a 10 próbálkozás nem tud csatlakozni, a tárolót le fog állni. 

### <a name="billing-arguments"></a>A számlázás argumentumok

Mind a hármat a következők meg kell adni ahhoz, hogy érvényes értékekkel a `docker run` parancsot a tároló elindításához:

| Beállítás | Leírás |
|--------|-------------|
| `ApiKey` | Az API-kulcsot a Cognitive Services-szolgáltatás-erőforrás segítségével nyomon követhető a számlázási adatokat.<br/>Ez a beállítás értékét állítsa a kiosztott erőforrás a megadott API-kulcs `Billing`. |
| `Billing` | A végpont a Cognitive Services-szolgáltatás-erőforrás segítségével nyomon követhető a számlázási adatokat.<br/>Ez a beállítás értékét állítsa a végpontot egy üzembe helyezett LUIS Azure erőforrás URI-ját.|
| `Eula` | Azt jelzi, hogy Ön már elfogadta a tároló licencét.<br/>Ez a beállítás értékét állítsa `accept`. |


