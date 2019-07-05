---
title: Hogyan hozhat létre egy egyéni modell - űrlap felismerő tanítási adathalmazt
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan biztosítson a tanítási adathalmazt egy űrlap felismerő modell tanítása van optimalizálva.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 06/19/2019
ms.author: pafarley
ms.openlocfilehash: 643f0d6dd3ee073bd19f8697346689523032ad9f
ms.sourcegitcommit: ccb9a7b7da48473362266f20950af190ae88c09b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/05/2019
ms.locfileid: "67592645"
---
# <a name="build-a-training-data-set-for-a-custom-model"></a>Egy egyéni modell a tanítási adathalmazt létrehozása

Az űrlap felismerő egyéni modell használata esetén, adja meg a saját betanítási adatok, a modell betaníthatja az iparág-specifikus űrlapokon. Emellett két ki vannak töltve űrlap betaníthatja öt ki vannak töltve űrlapok vagy egy üres képernyő (szerepelnie kell az "üres" szót a fájlnév) modell. Még ha van elegendő ki vannak töltve űrlapok a taníthat be, a tanítási adathalmazt ad hozzá egy üres képernyő javíthatja a modell pontosságát.

## <a name="training-data-tips"></a>Betanítási adatok tippek

Fontos egy adatkészlet, amely optimalizált használata képzéshez. Az alábbi tippek segítségével győződjön meg arról, hogy a legjobb eredmények elérése érdekében a get használatát a [tanítási modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) műveletet:

* Ha lehetséges használjon szöveges PDF-dokumentumok lemezkép-alapú dokumentumok helyett. A beolvasott PDF-fájlok, képek kezeli.
* Egy üres képernyő és a két ki vannak töltve űrlapok használja, ha azok érhető el.
* Kitöltött űrlapok használja a példa, amelyek az összes saját kitölti a mezőket.
* Egyes mezők eltérő értékek űrlapokat használnak.
* Ha az űrlap lemezképek alacsonyabb minőségű, használjon nagyobb (például a 10 – 15 képek).

## <a name="general-input-requirements"></a>Általános bemeneti követelmények

Ügyeljen, hogy a tanítási adathalmazt is minden űrlap felismerő tartalom bemeneti követelményeinek. 

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="upload-your-training-data"></a>A betanítási adatok feltöltése

Ha Ön összeállítottunk űrlap dokumentumok szeretné használni a betanítási készlete, töltse fel az Azure blob storage-tárolókat az kell. Ha nem ismeri egy tárolót az Azure storage-fiók létrehozása, a következő a [Azure Storage a rövid útmutató az Azure portal](https://docs.microsoft.com/azure/storage/blobs/storage-quickstart-blobs-portal).

### <a name="organize-your-data-in-subfolders-optional"></a>Almappák (nem kötelező) az adatok rendezése

Alapértelmezés szerint a [tanítási modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) API csak arra használja a storage-tároló gyökérmappájában található űrlap dokumentumok. Azonban betaníthatja almappákban lévő adatokkal, ha megadja azt az API-hívással. Normális esetben törzse a [tanítási modell](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api/operations/TrainCustomModel) hívást tartalmaz a következő formátumot követi, ahol `<SAS URL>` közös hozzáférési aláírás URL-címe a tárolóhoz van:

```json
{
  "source":"<SAS URL>"
}
```

A következő tartalmat ad hozzá a kérelem törzsében, ha az API-t az almappákban dokumentumok fog betanításához. A `"prefix"` mező nem kötelező, és korlátozza a tanítási adathalmazt, fájlok, amelyeknek elérési utak a megadott karakterlánc kezdődhet. Ezért érték `"Test"`, például miatt csak azokat a fájlokat vagy mappákat, a word "Test" karakterrel kezdődő tekintse meg az API-t.

```json
{
  "source": "<SAS URL>",
  "sourceFilter": {
    "prefix": "<prefix string>",
    "includeSubFolders": true
  }
}
```

## <a name="next-steps"></a>További lépések

Most, hogy megismerte, hogyan hozhat létre a tanítási adathalmazt, kövesse a gyors útmutató: egyéni űrlap felismerő modellek betanítása és vegye használatba az űrlapokon.

* [Rövid útmutató: A modell betanítását, és bontsa ki az adatokat a cURL használatával](./quickstarts/curl-train-extract.md)
* [Rövid útmutató: A modell betanítását, és bontsa ki az űrlapadatok REST API használatával a Python használatával](./quickstarts/python-train-extract.md)

