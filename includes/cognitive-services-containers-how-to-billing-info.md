---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: 05961f8dd2788179a15e6bfe094484cf4770067b
ms.sourcegitcommit: 778e7376853b69bbd5455ad260d2dc17109d05c1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/23/2019
ms.locfileid: "66124362"
---
A tároló a lekérdezések számlázzuk használt Azure-erőforrás tarifacsomagja a `<ApiKey>`.

Az Azure Cognitive Services-tárolók nem teszi lehetővé a számlázási végpont-méréshez való csatlakozás nélkül. Engedélyeznie kell a tárolókat való kommunikációhoz mindig a számlázási végponttal számlázási adatokat. Cognitive Services-tárolók adatai, például a lemezkép vagy a szöveg, amely elemezni, ne küldjön a Microsoftnak. 

### <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

A tároló a számlázási argumentumértékként futtatni kell. Ezek az értékek lehetővé teszik a számlázási végponthoz csatlakozik a tárolóhoz. A tároló használati jelentések kapcsolatos 10 – 15 percenként. Ha a tároló nem kapcsolódik az Azure-bA az engedélyezett időtartományon belül, a tároló továbbra is fut, de nem szolgál lekérdezéseket, a számlázási végpont visszaállításáig. A csatlakozási kísérlet 10 alkalommal az azonos idő 10 – 15 perces időközönként. Ha nem tud kapcsolódni a számlázási végpont a 10 belül próbálkozik, a tároló leáll. 

### <a name="billing-arguments"></a>A számlázás argumentumok

Mind a hármat a következők meg kell adni ahhoz, hogy érvényes értékekkel a `docker run` parancsot a tároló elindításához.

| Beállítás | Leírás |
|--------|-------------|
| `ApiKey` | Az API-kulcsot a Cognitive Services-erőforrás számlázási adatok nyomon követésére szolgál.<br/>Ez a beállítás értékét állítsa a kiosztott erőforrás a megadott API-kulcs `Billing`. |
| `Billing` | A végpont a Cognitive Services-erőforrás számlázási adatok nyomon követésére szolgál.<br/>Ez a beállítás értékét állítsa a végpontot egy üzembe helyezett Azure-erőforrás URI azonosítója.|
| `Eula` | Azt jelzi, hogy elfogadható-e a tároló a licencet.<br/>Ez a beállítás értékét állítsa `accept`. |


