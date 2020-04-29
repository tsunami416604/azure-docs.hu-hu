---
title: Betanítási adatkészlet létrehozása egyéni modell-űrlap felismerőhöz
titleSuffix: Azure Cognitive Services
description: Megtudhatja, hogyan biztosíthatja, hogy a betanítási adatkészletet az űrlap-felismerő modell betanítására optimalizálták.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 71ad7c5dd3ad74082da552cd3c45142bc0c2d624
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "75380626"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Betanítási adatkészlet létrehozása egyéni modellhez

Ha az űrlap-felismerő egyéni modelljét használja, saját betanítási adatait is megadhatja, így a modell az iparágra jellemző űrlapokra is betanítható. A modelleket öt kitöltött űrlapon vagy egy üres űrlapon is betaníthatja (a fájlnévben a "Empty" szót kell megadnia), valamint két kitöltött űrlapot is. Még ha elegendő kitöltött űrlap is van a betanításhoz, a betanítási adatkészletbe üres űrlap hozzáadásával növelheti a modell pontosságát.

Ha manuálisan címkézett betanítási adatmennyiséget szeretne használni, legalább öt azonos típusú formát kell kezdenie. Továbbra is használhat címkézetlen űrlapokat és egy üres űrlapot ugyanabban az adatkészletben.

## <a name="training-data-tips"></a>Az adatképzéssel kapcsolatos tippek

Fontos, hogy olyan adatkészletet használjon, amely betanításra van optimalizálva. A következő tippek segítségével biztosíthatja, hogy a legjobb eredményt kapja a [vonat egyéni modellje](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) műveletből:

* Ha lehetséges, a képeken alapuló dokumentumok helyett szöveges alapú PDF-dokumentumokat használjon. A beolvasott PDF-fájlok képként vannak kezelve.
* A kitöltött űrlapok esetében olyan példákat használjon, amelyekben az összes mező kitöltése megtörtént.
* Minden mezőben más értékkel rendelkező űrlapot használjon.
* Ha az űrlapon lévő képek gyengébb minőségűek, használjon nagyobb adatkészletet (például 10-15-es lemezképeket).
* A betanítási adatkészlet teljes mérete legfeljebb 500 oldal lehet.

## <a name="general-input-requirements"></a>Általános bemeneti követelmények

Győződjön meg arról, hogy a betanítási adatkészlet az összes űrlap-felismerő tartalomra vonatkozó bemeneti követelményeket is követi. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>A betanítási adatok feltöltése

Ha összeállította a betanításhoz használni kívánt űrlap-dokumentumokat, fel kell töltenie egy Azure Blob Storage-tárolóba. Ha nem tudja, hogyan hozhat létre egy tárolóval rendelkező Azure Storage-fiókot, kövesse a [Azure Portal Azure Storage](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)gyors üzembe helyezési útmutatóját.

### <a name="organize-your-data-in-subfolders-optional"></a>Az Ön adatait almappákban rendszerezheti (opcionális)

Alapértelmezés szerint az [Egyéni modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) -API-k csak a Storage-tároló gyökerében található űrlap-dokumentumokat használják. Ha azonban az API-hívásban megadhatja, az almappákban is betaníthatja az adatkészletet. Az [Egyéni modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) -hívás törzsének általában a következő űrlapja van, ahol `<SAS URL>` az a tároló közös hozzáférési aláírásának URL-címe:

```json
{
  "source":"<SAS URL>"
}
```

Ha az alábbi tartalmat adja hozzá a kérelem törzséhez, az API az almappákban található dokumentumokkal fog betanítani. A `"prefix"` mező nem kötelező, és a betanítási adatkészletet a megadott karakterlánccal kezdődő elérési úttal rendelkező fájlokra korlátozza. `"Test"`Így például az API csak azokat a fájlokat vagy mappákat fogja megtekinteni, amelyek a "test" kifejezéssel kezdődnek.

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  },
  "useLabelFile": false
}
```

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan hozhat létre egy betanítási adatkészletet, egy rövid útmutató segítségével betaníthat egy egyéni űrlap-felismerő modellt, és megkezdheti az űrlapokon való használatát.

* [Gyors útmutató: modell betanítása és űrlap-adatok kinyerése a cURL használatával](./quickstarts/curl-train-extract.md)
* [Gyors útmutató: modell betanítása és űrlap-adatok kinyerése a REST API és a Python használatával](./quickstarts/python-train-extract.md)
* [Betanítás címkékkel a REST API és Python használatával](./quickstarts/python-labeled-data.md)