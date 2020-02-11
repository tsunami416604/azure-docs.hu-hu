---
title: Mi a Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services űrlap-felismerő lehetővé teszi a kulcs/érték párok és a táblák adatainak azonosítását és kinyerését az űrlap dokumentumaiból.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 0d78f3cc4f2b12b2d9f45878a0c1b91263112689
ms.sourcegitcommit: 7c18afdaf67442eeb537ae3574670541e471463d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2020
ms.locfileid: "77118539"
---
# <a name="what-is-form-recognizer"></a>Mi a Form Recognizer?

Az Azure Form felismerő egy olyan kognitív szolgáltatás, amely gépi tanulási technológiával azonosítja és Kinyeri a szöveges, kulcs/érték párokat és a táblák adatait az űrlap dokumentumaiból. Betölti a szöveget az űrlapokból, és olyan strukturált adatokat ad eredményül, amelyek tartalmazzák az eredeti fájl kapcsolatait. Az adott tartalomra szabott pontos eredményeket gyorsan, nehéz manuális beavatkozás vagy kiterjedt adatelemzési szakértelem nélkül érheti el. Az űrlap-felismerő egyéni modellből, az előre elkészített beérkezési modellből és az elrendezési API-ból áll. Az űrlap-felismerő modelleket egy REST API használatával hívhatja meg, hogy csökkentse a bonyolultságot, és integrálja azt a munkafolyamatba vagy alkalmazásba.

Az űrlap-felismerő a következő szolgáltatásokból áll:
* **Egyéni modellek** – a kulcs/érték párok és a táblák adatainak kinyerése az űrlapokból. Ezek a modellek a saját adataival vannak betanítva, így az űrlapokra vannak szabva.
* Előre összevont **beérkezési modell** – az USA értékesítési bevételeiből származó adatok kinyerése egy előre elkészített modell használatával.
* **Elrendezési API** – szöveg-és táblázatos struktúrák kinyerése a dokumentumokból a határolókeret koordinátáival együtt.

<!-- add diagram -->

## <a name="custom-models"></a>Egyéni modellek

Az űrlap-felismerő egyéni modelljeinek a saját adataihoz kell betanítania, és a kezdéshez csak öt minta bemeneti űrlapra van szükség. A betanított modell olyan strukturált adatokat tud kialakítani, amelyek tartalmazzák az eredeti dokumentum kapcsolatait. A modell betanítása után tesztelheti és áttaníthatja, és végül a használatával megbízhatóan kinyerheti az adatokból az igényeknek megfelelő további formákat.

Az egyéni modellek betanításakor a következő lehetőségek állnak rendelkezésére: képzés címkézett és címkézett adatbevitel nélkül.

### <a name="train-without-labels"></a>Betanítás címkék nélkül

Alapértelmezés szerint az űrlap-felismerő nem felügyelt tanulást használ az űrlapok mezőinek és bejegyzéseinek elrendezésére és kapcsolatainak megismerésére. Ha beküldi a bemeneti űrlapokat, az algoritmus a következő típus szerint állítja be az űrlapokat, felfedi a kulcsokat és táblákat, és értékeket társít a kulcsokhoz és bejegyzésekhez a táblákhoz. Ehhez nincs szükség kézi adatcímkézésre vagy intenzív kódolásra és karbantartásra, és azt javasoljuk, hogy először próbálja meg ezt a módszert.

### <a name="train-with-labels"></a>Tanítás címkékkel

Ha címkével ellátott adatokkal látja el a képzést, a modell felügyeli a tanulást, hogy az Ön által megadott címkézett űrlapok használatával kinyerje a kívánt értékeket. Ez jobb teljesítményű modelleket eredményez, és olyan modelleket hozhat létre, amelyek olyan összetett űrlapokkal vagy űrlapokkal működnek, amelyek kulcsok nélkül tartalmaznak értékeket.

Az űrlap-felismerő az [elrendezési API](#layout-api) segítségével tanulja meg a nyomtatott és a kézírásos szöveges elemek várt méretét és pozícióit. Ezután a felhasználó által megadott címkéket használja a dokumentumok kulcs/érték társításának megismeréséhez. Azt javasoljuk, hogy az új modell betanítása és a modell pontosságának javítása érdekében a típus öt kézzel címkézett formáját használja az első lépésekhez.

## <a name="prebuilt-receipt-model"></a>Előre elkészített bevételezési modell

Az űrlap-felismerő olyan modellt is tartalmaz, amely a Egyesült Államok angol értékesítési nyugtákat olvas be&mdash;az éttermek, a benzinkutak, a kiskereskedelmi és így tovább (a[minta beérkezése](./media/contoso-receipt-small.png)) által használt típust. Ez a modell kigyűjti a legfontosabb adatokat, például a tranzakció dátumát és időpontját, a kereskedelmi adatokat, az adókat és az összegeket, valamint egyebeket. Emellett az előre elkészített beérkezési modell a nyugtán lévő összes szöveg felismerésére és visszaadására van betanítva.

## <a name="layout-api"></a>Elrendezési API

Az űrlap-felismerő a szöveg és a tábla szerkezetét is kinyerheti (a szöveghez társított sorok és oszlopok száma) a nagy felbontású optikai karakterfelismerés (OCR) használatával. 

## <a name="get-started"></a>Első lépések

Egy rövid útmutató segítségével megkezdheti az űrlapok adatainak kinyerését. Javasoljuk, hogy az ingyenes szolgáltatást használja a technológia megismerése során. Ne feledje, hogy a szabad lapok száma legfeljebb 500 havonta.

* Egyéni – modell betanítása az űrlapokra
  * Betanítás címkék nélkül
    * [Gyors útmutató: űrlap-felismerő modell betanítása és űrlap-adatok kinyerése a REST API és a cURL használatával](quickstarts/curl-train-extract.md)
    * [Gyors útmutató: űrlap-felismerő modell betanítása és adatok kinyerése a REST API és a Python használatával](quickstarts/python-train-extract.md)
  * Tanítás címkékkel 
    * [Űrlap-felismerő modell betanítása címkékkel a minta feliratozási eszköz használatával](quickstarts/label-tool.md)
    * [Űrlap-felismerő modell betanítása címkékkel REST API és Python használatával](quickstarts/python-labeled-data.md) 
* Előre elkészített visszaigazolások – adatok kinyerése az USA értékesítési bevételeiből
  * [Gyors útmutató: bevételezési adatok kinyerése a cURL használatával](quickstarts/curl-receipts.md)
  * [Gyors útmutató: bevételezési adatok kinyerése a Python használatával](quickstarts/python-receipts.md)
* Elrendezés – szöveg és tábla szerkezetének kinyerése űrlapokból
  * [Gyors útmutató: elrendezési adatok kinyerése a Python használatával](quickstarts/python-layout.md)

### <a name="review-the-rest-apis"></a>A REST API-k áttekintése

A következő API-kat fogja használni a modellek betanításához és a strukturált adatok űrlapokból való kinyeréséhez.

|Name (Név) |Leírás |
|---|---|
| **Egyéni modell betanítása**| Egy új modell betanításával elemezheti az űrlapokat öt azonos típusú űrlap használatával. Állítsa be a _useLabelFile_ paramétert úgy, hogy `true` a manuálisan címkézett adattal. |
| **Űrlap elemzése** |Egyetlen dokumentum elemzése, amely streamként lett átadva szöveg-, kulcs/érték párok és táblák kinyeréséhez az űrlapról az egyéni modellel.  |
| **Visszaigazolás elemzése** |Egyetlen bevételezési dokumentum elemzése a legfontosabb információk és a többi bevételezési szöveg kinyeréséhez.|
| **Elrendezés elemzése** |Egy űrlap elrendezésének elemzése szöveg és tábla szerkezetének kinyeréséhez.|

További információért olvassa el a [REST API dokumentációját](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) . Ha már ismeri az API korábbi verzióját, tekintse [meg](./whats-new.md) a újdonságokról szóló cikket a legutóbbi változások megismeréséhez.

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

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Ezt a szolgáltatást az [online szolgáltatási feltételekben](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)elérhető Azure-szolgáltatás [előzetes](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) verziója kínálja. Akárcsak az összes kognitív szolgáltatás esetében, az űrlap-felismerő szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft-szabályzatokat az ügyféladatok alapján. További információért tekintse meg a Microsoft adatvédelmi központjának [Cognitive Services lapját](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) .

## <a name="next-steps"></a>Következő lépések

Fejezze be [a gyors](quickstarts/curl-train-extract.md) üzembe helyezési útmutatót az [űrlap-felismerő API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)-k használatának megkezdéséhez.
