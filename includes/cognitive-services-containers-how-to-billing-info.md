---
author: IEvangelist
ms.author: dapine
ms.date: 06/25/2019
ms.service: cognitive-services
ms.topic: include
ms.openlocfilehash: a24300958c27daaaf49cc3045a5e99d77c938ab7
ms.sourcegitcommit: ce4a99b493f8cf2d2fd4e29d9ba92f5f942a754c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/28/2019
ms.locfileid: "67704279"
---
A tárolóra irányuló lekérdezések számlázása a `<ApiKey>`használt Azure-erőforrás díjszabási szintjénél történik.

Az Azure Cognitive Services-tárolók nem rendelkeznek licenccel, ha nem csatlakoznak a számlázási végponthoz a méréshez. Engedélyeznie kell a tárolókat, hogy mindig a számlázási végponttal kommunikáljanak a számlázási adatokkal. Cognitive Services tárolók nem küldenek ügyféladatokat, például az elemzett képet vagy szöveget a Microsoftnak. 

### <a name="connect-to-azure"></a>Csatlakozás az Azure szolgáltatáshoz

A tárolónak a számlázási argumentum értékeit kell futtatnia. Ezek az értékek lehetővé teszik a tárolónak a számlázási végponthoz való kapcsolódást. A tároló 10 – 15 percen belül jelentést készít a használatról. Ha a tároló nem csatlakozik az Azure-hoz az engedélyezett időtartományon belül, a tároló továbbra is futni fog, de addig nem szolgál lekérdezéseket, amíg a számlázási végpont vissza nem áll. A rendszer 10 – 15 percen belül 10 alkalommal kísérli meg a kapcsolódást. Ha a 10 próbálkozáson belül nem tud csatlakozni a számlázási végponthoz, a tároló leáll. 

### <a name="billing-arguments"></a>Számlázási argumentumok

Ahhoz, hogy a `docker run` parancs elindítsa a tárolót, az alábbi két lehetőség közül mindhárom beállítást érvényes értékekkel kell megadni:

| Lehetőség | Leírás |
|--------|-------------|
| `ApiKey` | A számlázási információk nyomon követéséhez használt Cognitive Services erőforrás API-kulcsa.<br/>Ennek a beállításnak az értékét a `Billing`ban megadott kiépített erőforráshoz tartozó API-kulcsra kell beállítani. |
| `Billing` | A számlázási információk nyomon követéséhez használt Cognitive Services erőforrás végpontja.<br/>Ennek a beállításnak az értékét egy kiépített Azure-erőforrás végpont-URI-azonosítójának kell beállítania.|
| `Eula` | Azt jelzi, hogy elfogadta a tároló licencét.<br/>Ennek a beállításnak az értékét az **elfogadás**értékre kell beállítani. |


