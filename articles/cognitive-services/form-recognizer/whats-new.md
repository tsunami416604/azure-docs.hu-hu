---
title: A Form Recognizer újdonságai
titleSuffix: Azure Cognitive Services
description: Ismerje meg az űrlap-felismerő API legújabb módosításait.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 12/12/2019
ms.author: pafarley
ms.openlocfilehash: cb5639dcf0e13ea03d34604816b3939085674c2e
ms.sourcegitcommit: f4f626d6e92174086c530ed9bf3ccbe058639081
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/25/2019
ms.locfileid: "75456365"
---
# <a name="whats-new-in-form-recognizer"></a>A Form Recognizer újdonságai

Ez a cikk az űrlap-felismerő API új verzióival kapcsolatos főbb változásokat emeli ki.

> [!NOTE]
> Az ebben a dokumentumban található rövid útmutatók és útmutatók mindig az API legújabb verzióját használják, kivéve, ha másként vannak megadva.

## <a name="form-recognizer-20-preview"></a>Űrlap-felismerő 2,0 (előzetes verzió)

> [!IMPORTANT]
> Az 2,0-es űrlap-felismerő jelenleg a `West US 2`-és `West Europe`-régiókban lévő előfizetésekhez érhető el. Ha az előfizetése nem ebben a régióban van, használja a 1,0 API-t. Az egyéni modell betanítására és használatára szolgáló rövid útmutatók az v 1.0 és a v 2.0 esetében is elérhetők.

### <a name="new-features"></a>ÚJ funkciók

* **Egyéni modell**
  * **Tanítás címkékkel** Mostantól manuálisan címkézett adattípusú egyéni modellt is betaníthat. Ez jobb teljesítményű modelleket eredményez, és olyan modelleket hozhat létre, amelyek olyan összetett űrlapokkal vagy űrlapokkal működnek, amelyek kulcsok nélkül tartalmaznak értékeket.
  * **ASZINKRON API** Használhat aszinkron API-hívásokat a nagyméretű adatkészletek és fájlok betanításához és elemzéséhez.
  * **TIFF-fájl támogatása** Mostantól a TIFF-dokumentumokból is betaníthat és kinyerheti az adatait.
  * **A kinyerési pontosság fejlesztése**

* **Előre elkészített bevételezési modell**
  * **Tipp mennyisége** Mostantól kinyerheti a tip-mennyiségeket és az egyéb kézírásos értékeket.
  * **Vonal kibontása** A sorokból kinyerheti a tételek értékeit a nyugták közül.
  * **Megbízhatósági értékek** Megtekintheti a modell megbízhatóságát minden kinyert értékhez.
  * **A kinyerési pontosság fejlesztése**

* **Elrendezés kibontása** Mostantól az elrendezési API használatával kinyerheti a szöveges és a táblázatos adatadatokat az űrlapokból.

### <a name="custom-model-api-changes"></a>Egyéni modell API-változások

A betanításra és az egyéni modellek használatára vonatkozó összes API-t átnevezték, és néhány szinkron módszer már aszinkron módon történik. A következők jelentős változások:

* A modellek betanításának folyamata már aszinkron módon történik. A **/Custom/models** API-hívással kezdeményezheti a betanítást. Ez a hívás egy műveleti azonosítót ad vissza, amelyet az **egyéni/modellek/{ModelID}** átadhat a betanítási eredmények visszaadásához.
* A kulcs/érték kinyerését mostantól a **/Custom/models/{ModelID}/Analyze** API-hívás kezdeményezte. Ez a hívás egy műveleti azonosítót ad vissza, amelyet az **egyéni/modellek/{ModelID}/analyzeResults/{resultID}** átadhat a kinyerési eredmények visszaadásához.
* A Train művelethez tartozó műveleti azonosítók mostantól a HTTP-válaszok **hely** fejlécében találhatók, nem a **művelet – hely** fejlécben.

### <a name="receipt-api-changes"></a>Beérkezési API módosításai

Az értékesítési visszaigazolások olvasására szolgáló API-k átnevezve lettek.

* A **/prebuilt/Receipt/Analyze** API-hívás kezdeményezte a beérkezési adatbontást. Ez a hívás egy műveleti azonosítót ad vissza, amelyet átadhat a **/prebuilt/Receipt/analyzeResults/{resultID}** a kinyerési eredmények visszaadásához.

### <a name="output-format-changes"></a>Kimeneti formátum módosításai

A JSON-válaszok minden API-híváshoz új formátumok tartoznak. Egyes kulcsok és értékek hozzá lettek adva, el lettek távolítva vagy átnevezve lettek. Tekintse meg az aktuális JSON-formátumok példáit.

## <a name="next-steps"></a>Következő lépések

Fejezze be [a gyors](quickstarts/curl-train-extract.md) üzembe helyezési útmutatót az [űrlap-felismerő API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)-k használatának megkezdéséhez.