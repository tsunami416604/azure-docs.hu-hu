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
ms.openlocfilehash: c6780d952b9ce6ea58fc6c8a2509a4526add7149
ms.sourcegitcommit: 0100d26b1cac3e55016724c30d59408ee052a9ab
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/07/2020
ms.locfileid: "86028256"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Betanítási adatkészlet létrehozása egyéni modellhez

Ha az űrlap-felismerő egyéni modelljét használja, saját betanítási adatait is megadhatja, így a modell az iparágra jellemző űrlapokra is betanítható. 

Ha manuális címkék nélkül végez betanítást, öt kitöltött űrlapot vagy egy üres űrlapot is használhat (a fájlnévben a "Empty" szót kell megadnia), valamint két kitöltött űrlapot is. Még ha elegendő kitöltött űrlap is van, a betanítási adatkészletbe üres űrlap hozzáadásával növelheti a modell pontosságát.

Ha manuálisan címkézett betanítási adatmennyiséget szeretne használni, legalább öt kitöltött, azonos típusú kitöltésű űrlapon kell kezdődnie. Továbbra is használhat címkézetlen űrlapokat és egy üres űrlapot a szükséges adatkészleten kívül.

## <a name="training-data-tips"></a>Az adatképzéssel kapcsolatos tippek

Fontos, hogy olyan adatkészletet használjon, amely betanításra van optimalizálva. A következő tippek segítségével biztosíthatja, hogy a legjobb eredményt kapja a [vonat egyéni modellje](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) műveletből:

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

Ha manuálisan címkézett adatokkal kívánja használni, akkor is fel kell töltenie a *.labels.jst* , és *.ocr.jsa* betanítási dokumentumoknak megfelelő fájlokra. A fájlok létrehozásához használhatja a [minta feliratozási eszközt](./quickstarts/label-tool.md) (vagy a saját felhasználói felületét).

### <a name="organize-your-data-in-subfolders-optional"></a>Az Ön adatait almappákban rendszerezheti (opcionális)

Alapértelmezés szerint az [Egyéni modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) -API-k csak a Storage-tároló gyökerében található űrlap-dokumentumokat használják. Ha azonban az API-hívásban megadhatja, az almappákban is betaníthatja az adatkészletet. A [vonat egyéni modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/TrainCustomModelAsync) -hívásának törzse általában a következő formátumú, ahol `<SAS URL>` a a tároló közös hozzáférésű aláírásának URL-címe:

```json
{
  "source":"<SAS URL>"
}
```

Ha az alábbi tartalmat adja hozzá a kérelem törzséhez, az API az almappákban található dokumentumokkal fog betanítani. A `"prefix"` mező nem kötelező, és a betanítási adatkészletet a megadott karakterlánccal kezdődő elérési úttal rendelkező fájlokra korlátozza. Így `"Test"` például az API csak azokat a fájlokat vagy mappákat fogja megtekinteni, amelyek a "test" kifejezéssel kezdődnek.

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

* [Modellek betanítása és űrlapos adatok kinyerése a cURL használatával](./quickstarts/curl-train-extract.md)
* [Modellek betanítása és űrlapos adatok kinyerése a REST API és a Python használatával](./quickstarts/python-train-extract.md)
* [Betanítás címkék használatával a minta feliratozási eszközzel](./quickstarts/label-tool.md)
* [Betanítás címkékkel a REST API és Python használatával](./quickstarts/python-labeled-data.md)
