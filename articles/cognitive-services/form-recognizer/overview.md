---
title: Mi a Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services űrlap-felismerő lehetővé teszi a kulcs/érték párok és a táblák adatainak azonosítását és kinyerését az űrlap dokumentumaiból.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 07/25/2019
ms.author: pafarley
ms.openlocfilehash: 86e0494ded84e9711b2283ae5fad77ff626cf792
ms.sourcegitcommit: bc193bc4df4b85d3f05538b5e7274df2138a4574
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/10/2019
ms.locfileid: "73904483"
---
# <a name="what-is-form-recognizer"></a>Mi a Form Recognizer?

Az Azure Form felismerő egy olyan kognitív szolgáltatás, amely gépi tanulási technológiával azonosítja és Kinyeri a kulcs/érték párokat és a táblák adatait az űrlap dokumentumaiból. Ezután strukturált adatokat ad vissza, amelyek tartalmazzák az eredeti fájlban lévő kapcsolatokat. Az egyéni űrlap felismerő modelljét egyszerű REST API használatával meghívhatja, hogy csökkentse a bonyolultságot, és egyszerűen integrálja azt a munkafolyamatba vagy alkalmazásba. A kezdéshez mindössze öt kitöltött űrlapra vagy két kitöltött űrlapra, valamint egy olyan üres formára van szükség, amely ugyanolyan típusú, mint a bemeneti anyag. Az adott tartalomra szabott pontos eredményeket gyorsan, nehéz manuális beavatkozás vagy kiterjedt adatelemzési szakértelem nélkül érheti el.

## <a name="custom-models"></a>Egyéni modellek

Az űrlap-felismerő egyéni modellje a saját adataihoz is vonatok, és a kezdéshez csak öt minta bemeneti űrlap szükséges. Amikor beküldi a bemeneti adatait, az algoritmus a következő típus szerint fürtbe állítja az űrlapokat, felfedi a kulcsokat és táblákat, és értékeket társít a kulcsokhoz és bejegyzésekhez a táblákhoz. Ezután strukturált adatokat ad vissza, amelyek tartalmazzák az eredeti fájlban lévő kapcsolatokat. A modell betanítása után tesztelheti és áttaníthatja, és végül a használatával megbízhatóan kinyerheti az adatokból az igényeknek megfelelő további formákat.

A nem felügyelt tanítás lehetővé teszi, hogy a modell manuális adatcímkézé, valamint intenzív kódolás és karbantartás nélkül megértse az elrendezést, illetve a mezők és bejegyzések közötti kapcsolatokat. Ezzel szemben az előre betanított gépi tanulási modellek szabványosított adattípust igényelnek. Kevésbé pontosak azok a bemeneti anyagok, amelyek eltérnek a hagyományos formátumtól, például az iparág-specifikus formáktól.

## <a name="prebuilt-receipt-model"></a>Előre elkészített bevételezési modell

Az űrlap-felismerő az értékesítési visszaigazolások olvasására szolgáló modellt is tartalmaz. Ez a modell kigyűjti a legfontosabb adatokat, például a tranzakció dátumát és időpontját, a kereskedelmi adatokat, az adókat és az összegeket, valamint egyebeket. Emellett az előre elkészített beérkezési modell a nyugtán lévő összes szöveg felismerésére és visszaadására van betanítva.

## <a name="what-it-includes"></a>Mit tartalmaz?

Az űrlap-felismerő REST APIként érhető el. Az API-k meghívásával létrehozhat, betaníthat és kialakíthat egy egyéni modellt, vagy elérheti az előre elkészített modellt. Ha szeretné, a helyi Docker-tárolóban egyéni modelleket is betaníthat és futtathat.

## <a name="input-requirements"></a>Bemeneti követelmények
### <a name="custom-model"></a>Egyéni modell

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Előre elkészített bevételezési modell

A beérkezési modellhez tartozó bemeneti követelmények némileg eltérőek.

* A formátumnak JPEG, PNG, BMP, PDF (Text vagy beszkennelt) vagy TIFF formátumúnak kell lennie.
* A fájlméretnek 20 MB-nál kisebbnek kell lennie.
* A képdimenziónak 50 x 50 képpont és 10000 x 10000 képpont közé kell esnie. 
* A PDF-dimenzióknak legfeljebb 17 x 17 hüvelyknek kell lenniük, amely a jogi vagy az A3-as papírméretnek felel meg, és kisebb.
* A PDF és a TIFF esetében csak az első 200 oldal lesz feldolgozva (ingyenes rétegbeli előfizetéssel csak az első két oldal feldolgozása történik).

## <a name="request-access"></a>Hozzáférés kérése

Az űrlap-felismerő korlátozott hozzáférésű előzetes verzióban érhető el. Az előzetes verzió eléréséhez töltse ki és küldje el az [űrlap-felismerő hozzáférési kérelmének](https://aka.ms/FormRecognizerRequestAccess) űrlapját. Az űrlap adatokat kér Önnek, a vállalatnak és a felhasználói forgatókönyvnek, amellyel az űrlap-felismerőt fogja használni. Ha az Azure Cognitive Services csapata jóváhagyja a kérést, a szolgáltatás elérésére vonatkozó utasításokat tartalmazó e-mailt fog kapni.

## <a name="where-do-i-start"></a>Hogyan kezdjek hozzá?

**1. lépés:** Hozzon létre egy űrlap-felismerő erőforrást a Azure Portal.

**2. lépés:** A REST API használatának lépései:
* [Gyors útmutató: űrlap-felismerő modell betanítása és űrlap-adatok kinyerése a REST API és a cURL használatával](quickstarts/curl-train-extract.md)
* [Gyors útmutató: űrlap-felismerő modell betanítása és adatok kinyerése a REST API és a Python használatával](quickstarts/python-train-extract.md)
* [Gyors útmutató: bevételezési adatok kinyerése a cURL használatával](quickstarts/curl-receipts.md)
* [Gyors útmutató: bevételezési adatok kinyerése a Python használatával](quickstarts/python-receipts.md)

Javasoljuk, hogy az ingyenes szolgáltatást használja a technológia megismerése során. Ne feledje, hogy a szabad lapok száma legfeljebb 500 havonta.

**3. lépés:** A REST API-k áttekintése

A következő API-k segítségével végezheti el a strukturált adatok űrlapokból való betanítását és kinyerését.

|||
|---|---|
| Modell betanítása| Egy új modell betanításával elemezheti az űrlapokat öt azonos típusú űrlap használatával. Vagy egy üres űrlap és két kitöltött űrlap betanítása.  |
| Űrlap elemzése |Egyetlen dokumentum elemzése, amelyet adatfolyamként kapott a kulcs/érték párok és táblák az egyéni modellel való kinyeréséhez.  |
| Visszaigazolás elemzése |Egyetlen bevételezési dokumentum elemzése a legfontosabb információk és a többi bevételezési szöveg kinyeréséhez.|

További információért olvassa el a [REST API dokumentációját](https://aka.ms/form-recognizer/api) . 

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Ezt a szolgáltatást az [online szolgáltatási feltételekben](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)elérhető Azure-szolgáltatás [előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verziója kínálja. Akárcsak az összes kognitív szolgáltatás esetében, az űrlap-felismerő szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft-szabályzatokat az ügyféladatok alapján. További információért tekintse meg a Microsoft adatvédelmi központjának [Cognitive Services lapját](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) .

## <a name="next-steps"></a>Következő lépések

Fejezze be [a gyors](quickstarts/curl-train-extract.md) üzembe helyezési útmutatót az [űrlap-felismerő API](https://aka.ms/form-recognizer/api)-k használatának megkezdéséhez.