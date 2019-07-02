---
title: Mi a Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan használhatja az űrlap felismerő elemezni az űrlap és a táblák adatait.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: form-recognizer
ms.topic: overview
ms.date: 07/01/2019
ms.author: pafarley
ms.openlocfilehash: a17e47fb059c23ab2e6eb69f3cfe6f2f8d0e51b2
ms.sourcegitcommit: 837dfd2c84a810c75b009d5813ecb67237aaf6b8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2019
ms.locfileid: "67502899"
---
# <a name="what-is-form-recognizer"></a>Mi a Form Recognizer?

Azure űrlap felismerő az kognitív szolgáltatás, amely gépi tanulási technológia használatával azonosíthatja és kulcs-érték párok, és a táblák adatainak kinyerése űrlap dokumentumok. Ezután strukturált adatokat ad vissza, amelyek tartalmazzák az eredeti fájlban lévő kapcsolatokat. Az egyéni űrlap felismerő modell egy egyszerű REST API használatával csökkenthető, és egyszerűen integrálhatja a munkafolyamat vagy alkalmazás hívása. A kezdéshez csak kell öt kitöltött dokumentumok vagy két ki vannak töltve űrlap plusz egy ugyanolyan típusú, mint a bemeneti anyagot találhat az üres formájában. Gyorsan hozzáférhet pontos eredményeket, amelyek az adott tartalom (nagy erőforrásigényű) kézi beavatkozás vagy kiterjedt data science szakértelem nélkül.

## <a name="custom-models"></a>Egyéni modellek

Az űrlap felismerő egyéni modell betanítja a saját adataihoz, és csak akkor kell öt minta beviteli űrlapokat elindításához. A bemeneti adatok elküldésekor, az algoritmus típus szerint a képernyők fürtök, felderíti, mely kulcsok és a táblák jelen, és kulcsok értékek és táblák bejegyzések társítja. Ezután strukturált adatokat ad vissza, amelyek tartalmazzák az eredeti fájlban lévő kapcsolatokat. Után a modell betanítását, tesztelése és újratanítása azt, és végül, amellyel igény szerint további űrlapok adatok megbízhatóan kinyerése.

A nem felügyelt tanítás lehetővé teszi, hogy a modell manuális adatcímkézé, valamint intenzív kódolás és karbantartás nélkül megértse az elrendezést, illetve a mezők és bejegyzések közötti kapcsolatokat. Ezzel szemben az előre betanított gépi tanulási modellek szükséges szabványosított adatok. A bemeneti anyagot, amely eltér a hagyományos formátumok, mint például az iparág-specifikus űrlapok kevésbé pontos zajlik.

## <a name="pre-built-receipt-model"></a>Előre elkészített beérkezési modell

Űrlap felismerő egy értékesítési Nyugtával rendelkezik olvasási modellt is tartalmaz. Ez a modell a kulcsadatokat dátumát és időpontját a tranzakciót, kereskedő információ, olyan adókat, és az összegek és több mint adja eredményül. Emellett az előre elkészített visszaigazolások modell tanítása felismerni és a nyugta vissza a teljes szöveg.

## <a name="what-it-includes"></a>Mit tartalmaz?

Űrlap felismerő REST API-ként érhető el. Hozzon létre, betanítást, és a egy egyéni modell pontozása vagy eléréséhez a kész modell API-k meghívásával. Ha azt szeretné, betanítása, és egyéni modellek futtatása helyi Docker-tárolóban.

## <a name="input-requirements-custom-model"></a>A bemeneti követelmények (egyéni modell)

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="request-access"></a>Hozzáférés kérése

Űrlap felismerő egy korlátozott hozzáférésű előzetes verzióban érhető el. Töltse ki az előzetes verzió eléréséhez, és küldje el a [űrlap felismerő hozzáférési kérelem](https://aka.ms/FormRecognizerRequestAccess) űrlap. Az űrlap kér információt, a vállalat és a felhasználói forgatókönyvet, amely űrlap felismerő fog használni. Ha a kérést az Azure Cognitive Services-csapat jóváhagyja, kap egy e-mailt a szolgáltatás eléréséhez szükséges utasításokat.

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

**1. lépés:** Űrlap felismerő erőforrás létrehozása az Azure Portalon.

**2. lépés:** Hajtsa végre a gyors útmutató: a REST API-val:
* [Rövid útmutató: Űrlap felismerő modellek betanítása és űrlap adatokat nyerhet ki a REST API-val a curl használatával](quickstarts/curl-train-extract.md)
* [Rövid útmutató: Űrlap felismerő modellek betanítása és űrlap adatokat nyerhet ki a REST API a pythonnal](quickstarts/python-train-extract.md)
* [Rövid útmutató: A cURL használatával beérkezési adatokat nyerhet ki](quickstarts/curl-receipts.md)
* [Rövid útmutató: Python használatával beérkezési adatokat nyerhet ki](quickstarts/python-receipts.md)

Azt javasoljuk, hogy az ingyenes szolgáltatás használja, ha Ön már technológia megismerésére. Ne feledje, hogy ingyenes oldalak számát havi 500 korlátozódik.

**3. lépés:** A REST API-k áttekintése

A következő API-k használatával betanítása és strukturált adatok kinyerése az űrlapok.

|||
|---|---|
| Modell betanítása| Az űrlapok elemzése ugyanolyan típusú öt űrlapok használatával új modell betanításához. Vagy egy üres képernyő és a két ki vannak töltve űrlap betanításához.  |
| Űrlap elemzése |Egyetlen dokumentum, kulcs/érték párok és táblák kibontani az űrlapot a egyéni modell adatfolyamként átadott elemzése.  |
| Nyugta elemzése |A fontos információk és egyéb beérkezési szöveg kinyerése egyetlen értesítés a dokumentum elemzése.|

Fedezze fel a [REST API forrásdokumentáció](https://aka.ms/form-recognizer/api) további. 

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Ez a szolgáltatás érhető el egy [előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) alatt Azure-szolgáltatás a [Online szolgáltatási feltételek](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31). Csakúgy, mint a cognitive services összes szolgáltatása, az űrlap felismerő szolgáltatással a fejlesztők célszerű tisztában lennie a Microsoft-házirendek a vásárlói adatokat. Tekintse meg a [Cognitive Services-lapra](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) a további Microsoft Trust Center.

## <a name="next-steps"></a>További lépések

Végezze el a [rövid](quickstarts/curl-train-extract.md) való használatának első lépései a [felismerő API](https://aka.ms/form-recognizer/api).
