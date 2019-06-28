---
title: Mi a Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja az űrlap felismerő elemezni az űrlap és a táblák adatait.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 04/08/2019
ms.author: pafarley
ms.openlocfilehash: 82ee2aa5627ac5fa4584f5af6b6b80cc2813c667
ms.sourcegitcommit: f56b267b11f23ac8f6284bb662b38c7a8336e99b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/28/2019
ms.locfileid: "67441833"
---
# <a name="what-is-form-recognizer"></a>Mi a Form Recognizer?

Az Azure Form Recognizer egy Cognitive Services-szolgáltatás, amely gépi tanulási technológiával azonosítja és nyeri ki a kulcs-érték párokat és táblaadatokat az űrlapdokumentumokból. Ezután strukturált adatokat ad vissza, amelyek tartalmazzák az eredeti fájlban lévő kapcsolatokat. Az egyéni űrlap felismerő modell egy egyszerű REST API használatával csökkenthető, és egyszerűen integrálhatja a munkafolyamat vagy alkalmazás hívása. A kezdéshez csak kell öt kitöltött dokumentumok vagy két ki vannak töltve űrlap plusz egy ugyanolyan típusú, mint a bemeneti anyagot találhat az üres formájában. Gyorsan hozzáférhet pontos eredményeket, amelyek az adott tartalom (nagy erőforrásigényű) kézi beavatkozás vagy kiterjedt data science szakértelem nélkül.

## <a name="request-access"></a>Hozzáférés kérése
Űrlap felismerő egy korlátozott hozzáférésű előzetes verzióban érhető el. Töltse ki az előzetes verzió eléréséhez, és küldje el a [űrlap felismerő hozzáférési kérelem](https://aka.ms/FormRecognizerRequestAccess) űrlap. Az űrlap kér információt, a vállalat és a felhasználói forgatókönyvet, amely űrlap felismerő fog használni. Ha a kérést az Azure Cognitive Services-csapat jóváhagyja, kap egy e-mailt a szolgáltatás eléréséhez szükséges utasításokat.

## <a name="what-it-does"></a>Művelet

Amikor a bemeneti adatokat küld, az algoritmus, betanítja, fürtök típusok, az űrlapok felderíti, mely kulcsok és a táblák jelen, és megtanulja kulcsok értékek és táblák bejegyzések. A nem felügyelt tanítás lehetővé teszi, hogy a modell manuális adatcímkézé, valamint intenzív kódolás és karbantartás nélkül megértse az elrendezést, illetve a mezők és bejegyzések közötti kapcsolatokat. Ezzel szemben az előre betanított machine learning-modellek szabványos igényelnek, és kevésbé pontos bemeneti anyagot, amely eltér a hagyományos formátumok, mint például az iparág-specifikus űrlapok együtt használva.

Után a modell betanítását, tesztelése és újratanítása azt, és végül, amellyel igény szerint további űrlapok adatok megbízhatóan kinyerése.

## <a name="what-it-includes"></a>Mit tartalmaz?

Űrlap felismerő REST API-ként érhető el. Létrehozását, betanítását és pontozását egy modellt az API meghívásával. Ha azt szeretné, a helyi Docker-tárolóban futtathatja a modellt.

## <a name="input-requirements"></a>A bemeneti követelmények

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

**1. lépés:** Űrlap felismerő erőforrás létrehozása az Azure Portalon.

**2. lépés:** Próbálja ki a rövid útmutató a gyakorlatban használható tapasztalatot:
* [Rövid útmutató: Űrlap felismerő modellek betanítása és űrlap adatokat nyerhet ki a REST API-val a curl használatával](quickstarts/curl-train-extract.md)
* [Rövid útmutató: Űrlap felismerő modellek betanítása és űrlap adatokat nyerhet ki a REST API a pythonnal](quickstarts/python-train-extract.md)

Azt javasoljuk, hogy az ingyenes szolgáltatást használja, ha Ön éppen technológia megismerésére, de ne feledje, hogy ingyenes oldalak számát havi 500 oldalak korlátozódik.

**3. lépés:** A REST API-k áttekintése

A következő API-k használatával betanítása és strukturált adatok kinyerése az űrlapok.

| REST API | Leírás |
|-----|-------------|
| Betanítás | Az űrlapok elemzése ugyanolyan típusú öt űrlapok használatával új modell betanításához. Vagy egy üres képernyő és a két ki vannak töltve űrlap betanításához.  |
| Elemzés  |Egyetlen dokumentum, kulcs-érték párok és táblák kibontani az űrlapot a egyéni modell adatfolyamként átadott elemzése.  |

Fedezze fel a [REST API-referencia dokumentumban](https://aka.ms/form-recognizer/api). 

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Ez a szolgáltatás érhető el egy [előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) alatt Azure-szolgáltatás a [Online szolgáltatási feltételek](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Csakúgy, mint a cognitive services összes szolgáltatása, az űrlap felismerő szolgáltatással a fejlesztők célszerű tisztában lennie a Microsoft-házirendek a vásárlói adatokat. Tekintse meg a [Cognitive Services-lapra](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) a további Microsoft Trust Center.

## <a name="next-steps"></a>További lépések

Végezze el a [rövid](quickstarts/curl-train-extract.md) való használatának első lépései a [felismerő API](https://aka.ms/form-recognizer/api).
