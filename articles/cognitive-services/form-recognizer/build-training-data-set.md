---
title: Betanítási adatkészlet létrehozása egyéni modellhez – Űrlapfelismerő
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan biztosíthatja, hogy a betanítási adatkészlet a Form Recognizer modell betanítására legyen optimalizálva.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 71ad7c5dd3ad74082da552cd3c45142bc0c2d624
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "75380626"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Betanítási adatkészlet létrehozása egyéni modellhez

Az Űrlapfelismerő egyéni modell használatakor saját betanítási adatokat kell megadnia, hogy a modell betaníthassa az iparág-specifikus űrlapokat. Betaníthat egy modellt öt kitöltött űrlappal vagy egy üres űrlappal (a fájlnévben az "üres" szót, valamint két kitöltött űrlapot kell betanítania. Még akkor is, ha elegendő kitöltött űrlapvan a betanításhoz, egy üres űrlap hozzáadása a betanítási adatkészlethez javíthatja a modell pontosságát.

Ha manuálisan címkézett betanítási adatokat szeretne használni, legalább öt azonos típusú űrlapokkal kell kezdenie. Továbbra is használhatunk címkézetlen űrlapokat és üres űrlapokat ugyanabban az adatkészletben.

## <a name="training-data-tips"></a>Betanítási adatokkal kapcsolatos tippek

Fontos, hogy egy betanításra optimalizált adatkészletet használjon. Az alábbi tippek segítségével biztosíthatja, hogy a legjobb eredményeket kapja az [egyéni modell betanítása](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) műveletből:

* Ha lehetséges, használjon szövegalapú PDF-dokumentumokat a képalapú dokumentumok helyett. A beolvasott PDF-fájlokat a program képként kezeli.
* Kitöltött űrlapok esetén használjon olyan példákat, amelyek mindegyik mezőjét kitöltik.
* Minden mezőben más értékkel rendelkező űrlapot használjon.
* Ha az űrlapképek gyengébb minőségűek, használjon nagyobb adatkészletet (például 10–15 képet).
* A betanítási adatkészlet teljes mérete legfeljebb 500 oldal lehet.

## <a name="general-input-requirements"></a>Általános bemeneti követelmények

Győződjön meg arról, hogy a betanítási adatkészlet az összes űrlapfelismerő tartalom bemeneti követelményeit is megfelel. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>A betanítási adatok feltöltése

Ha összeállította a betanításhoz használni kívánt űrlapdokumentumok készletét, fel kell töltenie egy Azure blob storage-tárolóba. Ha nem tudja, hogyan hozhat létre egy Azure storage-fiókot egy tárolóval, kövesse az [Azure Storage rövid útmutatóját az Azure Portalhoz.](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal)

### <a name="organize-your-data-in-subfolders-optional"></a>Az adatok rendezése almappákban (nem kötelező)

Alapértelmezés szerint a [Train Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) API csak a tárolótároló gyökerében található űrlapdokumentumokat fogja használni. Az almappákban lévő adatokat azonban betanítása, ha megadja azokat az API-hívásban. Általában a Train [Custom Model](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/TrainCustomModelAsync) hívás törzse a `<SAS URL>` következő űrlappal rendelkezik, ahol a tároló megosztott hozzáférésű aláírásurl-címe található:

```json
{
  "source":"<SAS URL>"
}
```

Ha hozzáadja a következő tartalmat a kérelem törzséhez, az API betanítása az almappákban található dokumentumokkal történik. A `"prefix"` mező nem kötelező, és korlátozza a betanítási adatkészletet azokra a fájlokra, amelyek elérési útjai az adott karakterlánccal kezdődnek. Így például `"Test"`az API értéke csak a "Teszt" szóval kezdődő fájlokat vagy mappákat vizsgálja meg.

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

Most, hogy megtanulta, hogyan hozhat létre betanítási adatkészletet, kövesse a rövid útmutatót egy egyéni űrlapfelismerő modell betanításához és használatának megkezdéséhez az űrlapokon.

* [Rövid útmutató: Modell betanítása és űrlapadatok kinyerése cURL használatával](./quickstarts/curl-train-extract.md)
* [Rövid útmutató: Modell betanítása és űrlapadatok kinyerése a REST API-val pythonnal](./quickstarts/python-train-extract.md)
* [Betanítás címkékkel a REST API és Python használatával](./quickstarts/python-labeled-data.md)