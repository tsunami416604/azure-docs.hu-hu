---
title: Az exportálás és ügyféladatok - LUIS - törlési kognitív Azure-szolgáltatások. Microsoft Docs
description: Exportálás és az ügyfél adatok törlését nyelvi ismertetése szolgáltatásból (LUIS) hivatkozás.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.technology: luis
ms.topic: article
ms.date: 05/23/2018
ms.author: v-geberr;
ms.openlocfilehash: f684b8ab875e2fbb774dc4a29bce25be41b24e6d
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35349582"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportálni, és törölje az ügyféladatokat, a nyelvi ismertetése (LUIS) kognitív szolgáltatásban

## <a name="summary-of-customer-data-request-features"></a>Felhasználói adatok kérelem szolgáltatásainak összefoglalása
Nyelvi ismertetése intelligens szolgáltatás (LUIS) őrzi meg a szolgáltatás működtetéséhez ügyfél tartalmat, de a LUIS felhasználó rendelkezik-e teljes hozzáféréssel megtekintése, exportálás és az adatok törlése. Ezt megteheti a LUIS webböngészőnek a segítségével [portal](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-reference-regions) vagy a [LUIS programozott API-k](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Ügyfél tartalmat a rendszer a Microsoft regionális az Azure storage titkosítva tárolja, és tartalmazza:

- Regisztráció során gyűjtött felhasználói fiók tartalomhoz
- A modellek (azaz leképezés & entitások) létrehozásához szükséges betanítási adatok
- Futásidőben a felhasználó modellek javítása érdekében a rendszer naplózza a felhasználói lekérdezések
  - Felhasználók bármikor kikapcsolhatják az lekérdezések naplózása hozzáfűzésével `&log=false` a kérést, a részletek [Itt](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-resources-faq#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Felhasználói adatok törlése
LUIS felhasználók törlése a felhasználó tartalma, akár a LUIS webes portál vagy a LUIS programozott API-k teljes hozzáféréssel rendelkeznek. A következő táblázat mindkét védelmével hivatkozások jeleníti meg:

| | **Felhasználói fiók** | **Alkalmazás** | **Utterance(s)** | **Végfelhasználói lekérdezések** |
| --- | --- | --- | --- | --- |
| **Portal** | [Hivatkozás](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/luis-how-to-account-settings) | [Hivatkozás](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#delete-app) | [Hivatkozás](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#delete-app) | [Hivatkozás](https://docs.microsoft.com/en-us/azure/cognitive-services/luis/create-new-app#delete-app) |
| **API-k** | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Felhasználói adatok exportálása
LUIS felhasználók a portálon, az adatok megtekintéséhez teljes hozzáféréssel rendelkeznek, azonban a LUIS programozott API-k segítségével kell exportálni. Az alábbi táblázatban láthatók az adatok exportálja a LUIS programozott API-k segítségével védelmével hivatkozásokat:

| | **Felhasználói fiók** | **Alkalmazás** | **Utterance(s)** | **Végfelhasználói lekérdezések** |
| --- | --- | --- | --- | --- |
| **API-k** | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [LUIS régiók referencia](./luis-reference-regions.md)
