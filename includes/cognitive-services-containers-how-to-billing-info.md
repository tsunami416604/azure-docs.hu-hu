---
author: diberry
ms.author: diberry
ms.service: cognitive-services
ms.topic: include
ms.date: 04/16/2019
ms.openlocfilehash: e92d1c65d9601c23e7e785f07e2de3e43ea6612b
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59684650"
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
| `Billing` | A végpont a Cognitive Services-szolgáltatás-erőforrás segítségével nyomon követhető a számlázási adatokat.<br/>Ez a beállítás értékét állítsa a végpontot egy üzembe helyezett Azure-erőforrás URI azonosítója.|
| `Eula` | Azt jelzi, hogy Ön már elfogadta a tároló licencét.<br/>Ez a beállítás értékét állítsa `accept`. |


