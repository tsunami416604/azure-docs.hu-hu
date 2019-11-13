---
title: Exportálás & adattörlés – LUIS
titleSuffix: Azure Cognitive Services
description: Teljes körűen szabályozhatja az adatmegjelenítését, exportálását és törlését. Az adatvédelem és a megfelelőség biztosítása érdekében törölje a vásárlói adatokat.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 061f019fe36e4d5495a41fc81e56d9673ad595fc
ms.sourcegitcommit: 44c2a964fb8521f9961928f6f7457ae3ed362694
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/12/2019
ms.locfileid: "73953463"
---
# <a name="export-and-delete-your-customer-data-in-language-understanding-luis-in-cognitive-services"></a>Exportálás és az ügyféladatokat, a Language Understanding (LUIS) a Cognitive Services törlése

Az adatvédelem és a megfelelőség biztosítása érdekében törölje a vásárlói adatokat. 

## <a name="summary-of-customer-data-request-features"></a>Vásárlói adatok funkciók összefoglalása
Language Understanding Intelligent Service (LUIS) őrzi meg a szolgáltatás ügyfél-tartalmat, de a LUIS felhasználó keresztül megtekintése, exportálását és törlését a teljes körű vezérléssel rendelkezik. Ezt a LUIS [webportálon](luis-reference-regions.md) vagy a [Luis authoring (más néven programozott) API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)-kon keresztül teheti meg.

[!INCLUDE [GDPR-related guidance](../../../includes/gdpr-intro-sentence.md)]

Ügyfél tartalom titkosítása a Microsoft regionális Azure storage-ban tárolt, és tartalmazza:

- Regisztráció során gyűjtött felhasználói fiók tartalom
- A modellek létrehozásához szükséges betanítási adatgyűjtés
- Az [aktív tanulás](luis-concept-review-endpoint-utterances.md) által használt naplózott felhasználói lekérdezések segítenek a modell fejlesztésében
  - Bármikor kikapcsolhatják lekérdezések naplózása hozzáfűzésével `&log=false` a kérést, részletesen [Itt](troubleshooting.md#how-can-i-disable-the-logging-of-utterances)

## <a name="deleting-customer-data"></a>Vásárlói adatok törlése
A LUIS-felhasználók teljes hozzáféréssel rendelkeznek a felhasználói tartalmak törléséhez, akár a LUIS webportálon, akár a LUIS authoring (más néven programozott) API-kon keresztül. A következő táblázat mindkét segítő hivatkozásokat jeleníti meg:

| | **Felhasználói fiók** | **Alkalmazás** | **Példa a Kimondás (ok) ra** | **A végfelhasználói lekérdezések** |
| --- | --- | --- | --- | --- |
| **Portál** | [Hivatkozás](luis-concept-data-storage.md#delete-an-account) | [Hivatkozás](luis-how-to-start-new-app.md#delete-app) | [Hivatkozás](luis-concept-data-storage.md#utterances-in-an-intent) | [Aktív tanulási hosszúságú kimondott szöveg](luis-how-to-review-endpoint-utterances.md#disable-active-learning)<br>[Naplózott hosszúságú kimondott szöveg](luis-concept-data-storage.md#disable-logging-utterances) |
| **API-k** | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c4c) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c39) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0b) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/58b6f32139e2bb139ce823c9) |


## <a name="exporting-customer-data"></a>Vásárlói adatok exportálása
A LUIS-felhasználók teljes hozzáféréssel rendelkeznek a portálon tárolt adat megtekintéséhez, azonban azt a LUIS authoring (más néven programozott) API-k használatával kell exportálni. Az alábbi táblázat a LUIS authoring (más néven programozott) API-k használatával folytatott adatexportálási hivatkozásokat tartalmazza:

| | **Felhasználói fiók** | **Alkalmazás** | **Utterance(s)** | **A végfelhasználói lekérdezések** |
| --- | --- | --- | --- | --- |
| **API-k** | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c48) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c40) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c0a) | [Hivatkozás](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c36) |

## <a name="location-of-active-learning"></a>Az aktív tanulás helye

Az [aktív tanulás](luis-how-to-review-endpoint-utterances.md#enable-active-learning)engedélyezéséhez a közzétett Luis-végpontokon fogadott felhasználók naplózott hosszúságú kimondott szöveg a következő Azure-régiókban tárolódnak:

* [Európa](#europe)
* [Ausztrália](#australia)
* [Egyesült Államok](#united-states)

Az aktív tanulási adatmennyiség (alább részletezve) kivételével a LUIS a [regionális szolgáltatások adattárolási gyakorlatát](https://azuredatacentermap.azurewebsites.net/)követi. 

### <a name="europe"></a>Európa

Az [EU.Luis.ai](https://eu.luis.ai) -portál és Európa authoring (más néven programozási API-k) az Azure Európa-földrajzában található. A eu.luis.ai portál és Európa authoring (más néven programozási API-k) támogatják a végpontok következő Azure földrajzi területekre történő telepítését:

* Európa
* Franciaország
* Egyesült Királyság

Ezen Azure földrajzi területek telepítésekor a végpont által az alkalmazás végfelhasználói által fogadott hosszúságú kimondott szöveg az Azure Európa-földrajzában az aktív tanuláshoz lesz tárolva. Az aktív tanulás letiltásával kapcsolatban lásd: az [aktív tanulás letiltása](luis-how-to-review-endpoint-utterances.md#disable-active-learning). A tárolt hosszúságú kimondott szöveg kezeléséhez lásd: a [teljes törlés](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="australia"></a>Ausztrália

Az [au.Luis.ai](https://au.luis.ai) -portál és az ausztráliai szerzői műveletek (más néven programozási API-k) az Azure Ausztráliában földrajzán futnak. A au.luis.ai-portál és az ausztráliai szerzői műveletek (más néven programozási API-k) támogatják a végpontok következő Azure földrajzi területekre történő telepítését:

* Ausztrália

Ezen Azure földrajzi területek telepítésekor a végpont által az alkalmazás végfelhasználói által kapott hosszúságú kimondott szöveg az Azure ausztráliai földrajza az aktív tanuláshoz. Az aktív tanulás letiltásával kapcsolatban lásd: az [aktív tanulás letiltása](luis-how-to-review-endpoint-utterances.md#disable-active-learning). A tárolt hosszúságú kimondott szöveg kezeléséhez lásd: a [teljes törlés](luis-how-to-review-endpoint-utterances.md#delete-utterance). 

### <a name="united-states"></a>Egyesült Államok

A [Luis.ai](https://www.luis.ai) portál és a Egyesült Államok authoring (más néven programozott API-k) az Azure Egyesült Államok-földrajzában találhatók. A luis.ai-portál és a Egyesült Államok authoring (más néven programozási API-k) támogatják a végpontok következő Azure földrajzi területekre történő telepítését:

* Az Európai vagy ausztráliai szerzői régiók nem támogatják az Azure földrajzi területeit

Ezen Azure földrajzi területek telepítésekor a végpont által az alkalmazás végfelhasználói által fogadott hosszúságú kimondott szöveg az Azure Egyesült Államok-földrajzában az aktív tanuláshoz lesz tárolva. Az aktív tanulás letiltásával kapcsolatban lásd: az [aktív tanulás letiltása](luis-how-to-review-endpoint-utterances.md#disable-active-learning). A tárolt hosszúságú kimondott szöveg kezeléséhez lásd: a [teljes törlés](luis-how-to-review-endpoint-utterances.md#delete-utterance). 


## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [A LUIS régiók referencia](./luis-reference-regions.md)
