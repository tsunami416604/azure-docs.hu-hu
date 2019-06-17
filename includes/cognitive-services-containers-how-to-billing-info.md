---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: bfda8b83f1bedf11151ba89b58c95347aa35839a
ms.sourcegitcommit: 41ca82b5f95d2e07b0c7f9025b912daf0ab21909
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "67052044"
---
A tároló a lekérdezések az Azure-erőforrás szolgálja ki a tarifacsomagot számlázása a `<ApiKey>`.

Az Azure Cognitive Services-tárolók nem teszi lehetővé a számlázási végpont-méréshez való csatlakozás nélkül. Engedélyeznie kell a tárolókat való kommunikációhoz mindig a számlázási végponttal számlázási adatokat. Cognitive Services-tárolók adatai, például a lemezkép vagy a szöveg, amely elemezni, ne küldjön a Microsoftnak. 

### <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

A tároló a számlázási argumentumértékként futtatni kell. Ezek az értékek lehetővé teszik a számlázási végponthoz csatlakozik a tárolóhoz. A tároló használati jelentések kapcsolatos 10 – 15 percenként. Ha a tároló nem kapcsolódik az Azure-bA az engedélyezett időtartományon belül, a tároló továbbra is fut, de nem szolgál lekérdezéseket, a számlázási végpont visszaállításáig. A csatlakozási kísérlet 10 alkalommal az azonos idő 10 – 15 perces időközönként. Ha nem tud kapcsolódni a számlázási végpont a 10 belül próbálkozik, a tároló leáll. 

### <a name="billing-arguments"></a>A számlázás argumentumok

Az a `docker run` parancsot a tároló elindításához a következő lehetőségek közül három meg kell adni az érvényes értékek:

| Beállítás | Leírás |
|--------|-------------|
| `ApiKey` | Az API-kulcsot a Cognitive Services-erőforrás, amely segítségével nyomon követheti a számlázási adatokat.<br/>Ez a beállítás értékét állítsa a kiosztott erőforrás a megadott API-kulcs `Billing`. |
| `Billing` | A Cognitive Services-erőforrás számlázási adatok nyomon követésére használt végpont.<br/>Ez a beállítás értékét állítsa a végpontot egy üzembe helyezett Azure-erőforrás URI azonosítója.|
| `Eula` | Azt jelzi, hogy elfogadható-e a tároló a licencet.<br/>Ez a beállítás értékét állítsa **fogadja el**. |


