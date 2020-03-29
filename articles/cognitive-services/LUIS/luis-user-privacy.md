---
title: Adatok exportálása & törlése - LUIS
titleSuffix: Azure Cognitive Services
description: Teljes mértékben szabályozhatja az adatok megtekintését, exportálását és törlését. Az ügyféladatok törlése az adatvédelem és a megfelelőség biztosítása érdekében.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: reference
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 4e3e0d04b0086905b80e26fb4f838c36b5b5545e
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78273362"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Az ügyféladatok exportálása és törlése a Nyelvi megértés (LUIS) szolgáltatásban a Cognitive Servicesben

Az ügyféladatok törlése az adatvédelem és a megfelelőség biztosítása érdekében.

## <a name="summary-of-customer-data-request-features"></a>Az ügyféladat-igénylési funkciók összefoglalása
A Language Understanding Intelligent Service (LUIS) megőrzi az ügyfél tartalmait a szolgáltatás működtetéséhez, de a LUIS-felhasználó teljes mértékben szabályozhatja az adatok megtekintését, exportálását és törlését. Ez a LUIS [webportálon](luis-reference-regions.md) vagy a [LUIS Authoring (más néven Programmatic) API-kon](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)keresztül végezhető el.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Az ügyféltartalmat a Microsoft regionális Azure-tárolójában titkosítva tárolják, és a következőket tartalmazzák:

- A regisztrációkor gyűjtött felhasználói fiók tartalma
- A modellek létrehozásához szükséges betanítási adatok
- Az [aktív tanulás](luis-concept-review-endpoint-utterances.md) által a modell fejlesztéséhez használt naplózott felhasználói lekérdezések
  - A felhasználók kikapcsolhatják a `&log=false` lekérdezésnaplózást a kéréshez fűzött, [itt](troubleshooting.md#how-can-i-disable-the-logging-of-utterances) részletezve

## <a name="deleting-customer-data"></a>Ügyféladatok törlése
A LUIS-felhasználók teljes hozzáféréssel rendelkeznek a felhasználói tartalom törléséhez, akár a LUIS webportálon, akár a LUIS Authoring (más néven Programmatic) API-kon keresztül. Az alábbi táblázat a következő hivatkozásokat jeleníti meg, amelyek mindkettőt segítik:

| | **Felhasználói fiók** | **Alkalmazás** | **Példa utterance(s)** | **Végfelhasználói lekérdezések** |
| --- | --- | --- | --- | --- |
| **Portál** | [Hivatkozás](luis-concept-data-storage.md#delete-an-account) | [Hivatkozás](luis-how-to-start-new-app.md#delete-app) | [Hivatkozás](luis-concept-data-storage.md#utterances-in-an-intent) | [Aktív tanulási kimondott szöveg](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Naplózott kimondott szöveg](luis-concept-data-storage.md#disable-logging-utterances) |
| **API-k** | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Vevői adatok exportálása
A LUIS-felhasználók teljes hozzáféréssel rendelkeznek a portálon lévő adatok megtekintéséhez, azonban a LUIS Authoring (más néven Programmatic) API-kon keresztül kell exportálni. Az alábbi táblázat a LUIS Authoring (más néven Programmatic) API-kon keresztül iratják az adatexportálást segítő hivatkozásokat jeleníti meg:

| | **Felhasználói fiók** | **Alkalmazás** | **Kimondott szöveg(ek)** | **Végfelhasználói lekérdezések** |
| --- | --- | --- | --- | --- |
| **API-k** | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Az aktív tanulás helye

Az [aktív tanulás](luis-how-to-review-endpoint-utterances.md#enable-active-learning)engedélyezéséhez a közzétett LUIS-végpontokon kapott felhasználói naplózott utterances a következő Azure-földrajzi helyeken tárolódik:

* [Európa](#europe)
* [Ausztrália](#australia)
* [Egyesült Államok](#united-states)

Az aktív tanulási adatok kivételével (lásd alább), a LUIS a [regionális szolgáltatások adattárolási gyakorlatát](https://azuredatacentermap.azurewebsites.net/)követi.

### <a name="europe"></a>Európa

A [eu.luis.ai](https://eu.luis.ai) portál és az Európa-szerzői (más néven programozott API-k) az Azure Európa földrajzi adottságain található. A eu.luis.ai portál és az Európa-szerzői (más néven programozott API-k) támogatja a végpontok üzembe helyezését a következő Azure-földrajzi területeken:

* Európa
* Franciaország
* Egyesült Királyság

Ezekben az Azure-földrajzi területeken üzembe helyezésekor az alkalmazás végfelhasználóitól kapott kimondott szövegeket az Azure Európa földrajzi helyéről tárolják az aktív tanuláshoz. Letilthatja az aktív tanulást, [lásd: Aktív tanulás letiltása](luis-how-to-review-endpoint-utterances.md#disable-active-learning). A tárolt utterances kezelése, [lásd: Utterance (kifejezés) törlése.](luis-how-to-review-endpoint-utterances.md#delete-utterance)

### <a name="australia"></a>Ausztrália

A [au.luis.ai](https://au.luis.ai) portál és az Ausztrália authoring (más néven programozott API-k) az Azure ausztráliai földrajzi adottságaiban találhatók. A au.luis.ai portál és az Ausztrália szerzői (más néven programozott API-k) támogatja a végpontok üzembe helyezését a következő Azure-földrajzi területeken:

* Ausztrália

Ezekben az Azure-földrajzi területeken üzembe helyezésekor az alkalmazás végfelhasználóitól kapott kimondott szövegeket az Azure ausztráliai földrajzi helyén tárolják az aktív tanuláshoz. Letilthatja az aktív tanulást, [lásd: Aktív tanulás letiltása](luis-how-to-review-endpoint-utterances.md#disable-active-learning). A tárolt utterances kezelése, [lásd: Utterance (kifejezés) törlése.](luis-how-to-review-endpoint-utterances.md#delete-utterance)

### <a name="united-states"></a>Egyesült Államok

A [luis.ai](https://www.luis.ai) portál és az Egyesült Államok szerzői (más néven programozott API-k) az Azure Egyesült Államok földrajzi helyén találhatók. A luis.ai portál és az Egyesült Államok szerzői (más néven programozott API-k) támogatja a végpontok üzembe helyezését a következő Azure-földrajzi területeken:

* Az Azure földrajzi területeit nem támogatják az európai vagy ausztráliai szerzői régiók

Ezekben az Azure-földrajzi területeken üzembe helyezésekor az alkalmazás végfelhasználóitól kapott kimondott szövegeket az Azure Egyesült Államok földrajzi helyéről az aktív tanuláshoz tárolja a rendszer. Letilthatja az aktív tanulást, [lásd: Aktív tanulás letiltása](luis-how-to-review-endpoint-utterances.md#disable-active-learning). A tárolt utterances kezelése, [lásd: Utterance (kifejezés) törlése.](luis-how-to-review-endpoint-utterances.md#delete-utterance)


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [LUIS-régiók hivatkozása](./luis-reference-regions.md)
