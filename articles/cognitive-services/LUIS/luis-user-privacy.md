---
title: Exportálás és az adatok törlése
titleSuffix: Azure Cognitive Services
description: Adatvédelem és megfelelőség biztosítása érdekében a vásárlói adatokat törli.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: eb125133138c6de173fdeb90024a9e5d961a929d
ms.sourcegitcommit: c174d408a5522b58160e17a87d2b6ef4482a6694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2019
ms.locfileid: "58895156"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportálás és az ügyféladatokat, a Language Understanding (LUIS) a Cognitive Services törlése

Adatvédelem és megfelelőség biztosítása érdekében a vásárlói adatokat törli. 

## <a name="summary-of-customer-data-request-features"></a>Vásárlói adatok funkciók összefoglalása
Language Understanding Intelligent Service (LUIS) őrzi meg a szolgáltatás ügyfél-tartalmat, de a LUIS felhasználó keresztül megtekintése, exportálását és törlését a teljes körű vezérléssel rendelkezik. Ezt megteheti a LUIS weben keresztül [portál](luis-reference-regions.md) vagy a [LUIS szerzői (más néven programozott) API-k](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Ügyfél tartalom titkosítása a Microsoft regionális Azure storage-ban tárolt, és tartalmazza:

- Regisztráció során gyűjtött felhasználói fiók tartalom
- Betanítási adatok, a modellek létrehozásához szükséges
- Naplózza a felhasználói lekérdezések által használt [aktív tanulás](luis-concept-review-endpoint-utterances.md) a modell továbbfejlesztése érdekében
  - Bármikor kikapcsolhatják lekérdezések naplózása hozzáfűzésével `&log=false` a kérést, részletesen [Itt](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Vásárlói adatok törlése
A LUIS felhasználók törölni a tartalom, a LUIS webes portálon vagy a LUIS szerzői (más néven Programmatic) API-k keresztül, bármilyen felhasználói teljes hozzáféréssel rendelkeznek. A következő táblázat mindkét segítő hivatkozásokat jeleníti meg:

| | **Felhasználói fiók** | **Alkalmazás** | **Példa Utterance(s)** | **A végfelhasználói lekérdezések** |
| --- | --- | --- | --- | --- |
| **Portál** | [Hivatkozás](luis-concept-data-storage.md#delete-an-account) | [Hivatkozás](luis-how-to-start-new-app.md#delete-app) | [Hivatkozás](luis-concept-data-storage.md#utterances-in-an-intent) | [Aktív tanulás kimondott szöveg](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[A naplózott kimondott szöveg](luis-concept-data-storage.md#disable-logging-utterances) |
| **API-k** | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Vásárlói adatok exportálása
A LUIS-felhasználók az adatok megtekintése a portálon, a teljes hozzáféréssel rendelkeznek, azonban a LUIS szerzői (más néven programozott) API-kon keresztül kell exportálni. Az alábbi táblázat a LUIS szerzői (más néven programozott) API-kon keresztül adatexportálást segítő hivatkozásokat jeleníti meg:

| | **Felhasználói fiók** | **Alkalmazás** | **Utterance(s)** | **A végfelhasználói lekérdezések** |
| --- | --- | --- | --- | --- |
| **API-k** | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Aktív tanulás helye

Ahhoz, hogy [aktív tanulás](luis-how-to-review-endpoint-utterances.md#enable-active-learning), felhasználók naplózott utterances kézbesítve a közzétett LUIS-végpont, a következő Azure-régiócsoportok vannak tárolva:

* [Európa](#europe)
* [Ausztrália](#australia)
* [Egyesült Államok](#united-states)

Aktív tanulás adatok (lásd alább), kivéve a LUIS követi a [tárolási gyakorlatokat a regionális szolgáltatások esetében](http://azuredatacentermap.azurewebsites.net/). 

### <a name="europe"></a>Európa

A [eu.luis.ai](https://eu.luis.ai) portál és az Európai (más néven programozható API-k készítése) az Azure Európa földrajzi üzemelnek. A eu.luis.ai portál és az Európai (más néven programozható API-k készítése) a következő Azure-régiócsoportok végpontok telepítésének támogatása:

* Európa
* Franciaország
* Egyesült Királyság

Ezen Azure-régiócsoportok telepítésekor a végpontot a végfelhasználók számára az alkalmazás által fogadott megcímkézzen tárolva az Azure Európa térségen aktív tanulás. Tiltsa le az aktív tanulás, lásd: [tiltsa le az aktív tanulás](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Tárolt utterances kezelése, tekintse meg a [törli az utterance (kifejezés)](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="australia"></a>Ausztrália

A [au.luis.ai](https://au.luis.ai) portál és Ausztrália szerzői (más néven programozható API-k) az Azure ausztráliai földrajzi üzemelnek. A au.luis.ai portál- és Ausztrália (más néven programozható API-k készítése) a következő Azure-régiócsoportok végpontok telepítésének támogatása:

* Ausztrália

Ezen Azure-régiócsoportok telepítésekor a végpontot a végfelhasználók számára az alkalmazás által fogadott megcímkézzen tárolva az Azure ausztráliai földrajzi hely, aktív tanulás. Tiltsa le az aktív tanulás, lásd: [tiltsa le az aktív tanulás](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Tárolt utterances kezelése, tekintse meg a [törli az utterance (kifejezés)](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="united-states"></a>Egyesült Államok

A [luis.ai](https://www.luis.ai) portál és az Egyesült Államok szerzői (más néven programozható API-k) az Azure-Egyesült Államok földrajzi üzemelnek. A luis.ai portál és az Egyesült Államok szerzői (más néven programozható API-k) a következő Azure-régiócsoportok végpontok telepítésének támogatása:

* Európa vagy Ausztrália régiókban szerzői által nem támogatott Azure-régiócsoportok

Ezen Azure-régiócsoportok telepítésekor a végpontot a végfelhasználók számára az alkalmazás által fogadott megcímkézzen tárolva az Azure Egyesült államokbeli térségen aktív tanulás. Tiltsa le az aktív tanulás, lásd: [tiltsa le az aktív tanulás](luis-how-to-review-endpoint-utterances.md#disable-active-learning). Tárolt utterances kezelése, tekintse meg a [törli az utterance (kifejezés)](luis-how-to-review-endpoint-utterances.md#delete-utterance). 


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A LUIS régiók referencia](./luis-reference-regions.md)
