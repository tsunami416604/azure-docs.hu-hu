---
title: Mi a Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Az Azure Cognitive Services űrlapfelismerő lehetővé teszi kulcs-és értékpárok és táblázatadatok azonosítását és kinyerését űrlapdokumentumokból.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 12/05/2019
ms.author: pafarley
ms.openlocfilehash: 6b19dc11438274ecf6218d5c0bd8c9ef3dafbf01
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "80052433"
---
# <a name="what-is-form-recognizer"></a>Mi a Form Recognizer?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Az Azure Form Recognizer egy kognitív szolgáltatás, amely gépi tanulási technológiát használ a szöveg, a kulcs/érték párok és az űrlapdokumentumok táblázatadatainak azonosítására és kinyerésére. Az űrlapokszöveget beadja, és strukturált adatokat ad ki, amelyek tartalmazzák az eredeti fájlban lévő kapcsolatokat. Gyorsan kap pontos eredményeket, amelyek az adott tartalomhoz igazodnak, nehéz manuális beavatkozás vagy kiterjedt adatelemzési szakértelem nélkül. A Formafelismerő egyéni modellekből, az előre összeállított bevételezési modellből és az elrendezési API-ból áll. A FORM Recognizer modelleket rest API használatával hívhatja meg, hogy csökkentse az összetettséget, és integrálja azokat a munkafolyamatba vagy alkalmazásba.

A Formafelismerő a következő szolgáltatásokból áll:
* **Egyéni modellek** – Kulcs-/értékpárok és táblaadatok kinyerése űrlapokról. Ezek a modellek a saját adataival vannak betanítva, így az űrlapokhoz vannak igazítva.
* **Előre összeállított bevételezési modell** – Adatok kinyerése az USA értékesítési nyugtáiból egy előre összeállított modell használatával.
* **Elrendezési API** – Szöveg- és táblázatszerkezetek kinyerése a határolókeret koordinátáival együtt a dokumentumokból.

<!-- add diagram -->

## <a name="custom-models"></a>Egyéni modellek

A Form Recognizer egyéni modelljei betanítása a saját adataira, és csak öt mintabeviteli űrlapra van szükség a kezdéshez. A betanított modell olyan strukturált adatokat adhat ki, amelyek tartalmazzák az eredeti űrlapdokumentumban lévő kapcsolatokat. A modell betanítása után tesztelheti és újrataníthatja, és végül használhatja azt, hogy megbízhatóan kinyerje az adatokat több űrlapról az Igényeinek megfelelően.

Egyéni modellek betanításakor a következő lehetőségek közül választhat: betanítás címkézett adatokkal és címkézett adatok nélkül.

### <a name="train-without-labels"></a>Vonat címkék nélkül

Alapértelmezés szerint az Űrlapfelismerő felügyelet nélküli tanulással érti az űrlapok mezői és bejegyzései közötti elrendezést és kapcsolatokat. A bemeneti űrlapok elküldésekor az algoritmus típus szerint csoportosítja az űrlapokat, felderíti, hogy milyen kulcsok és táblák vannak jelen, és értékeket társít a kulcsokhoz és a táblák bejegyzéseihez. Ehhez nincs szükség manuális adatcímkézésre vagy intenzív kódolásra és karbantartásra, ezért azt javasoljuk, hogy először próbálja ki ezt a módszert.

### <a name="train-with-labels"></a>Vonat címkékkel

A címkézett adatokkal való betanításkor a modell felügyelt tanulással kinyeri az érdeklődési értékeket a megadott címkézett űrlapok használatával. Ez jobban teljesítő modelleket eredményez, és olyan modelleket képes előállítani, amelyek összetett űrlapokkal vagy kulcsok nélküli értékeket tartalmazó űrlapokkal dolgoznak.

A Formafelismerő az [Elrendezés API-t](#layout-api) használja a nyomtatott és kézzel írt szövegelemek várható méretének és helyének megismerésére. Ezután a felhasználó által megadott címkéket használja a dokumentumokkulcs/érték társítások megismeréséhez. Azt javasoljuk, hogy öt manuálisan címkézett azonos típusú űrlapok az első lépések hez, amikor egy új modell betanítása, és adjunk hozzá további címkézett adatokat, ha szükséges, hogy javítsa a modell pontosságát.

## <a name="prebuilt-receipt-model"></a>Előre összeállított bevételezési modell

A Form Recognizer egy modellt is tartalmaz az&mdash;Egyesült Államokból származó angol értékesítési nyugták olvasására az éttermek, benzinkutak, kiskereskedelmi és így tovább[(mintabevételezés)](./media/contoso-receipt-small.png)típusra. Ez a modell olyan kulcsfontosságú információkat nyer ki, mint a tranzakció időpontja és dátuma, a kereskedői adatok, az adók és az összegek összege stb. Emellett az előre összeállított bevételezési modell be van tanítva a bevételezés teljes szövegének felismerésére és visszaküldésére.

## <a name="layout-api"></a>Elrendezési API

Az Űrlapfelismerő nagy felbontású optikai karakterfelismerés (OCR) segítségével szöveget és táblázatszerkezetet (a szöveghez társított sor- és oszlopszámokat) is kinyerheti.

## <a name="get-started"></a>Bevezetés

Rövid útmutatóként ismerkedjen meg az űrlapokból való adatok kinyerésével. Javasoljuk, hogy használja az ingyenes szolgáltatást, amikor a technológia tanulását tanulja. Ne feledje, hogy az ingyenes oldalak száma havonta legfeljebb 500 lehet.

* Egyéni – modell betanítása az űrlapokra
  * Vonat címkék nélkül
    * [Rövid útmutató: Űrlapfelismerő modell betanítása és űrlapadatok kinyerése a REST API cURL használatával](quickstarts/curl-train-extract.md)
    * [Rövid útmutató: Űrlapfelismerő modell betanítása és űrlapadatok kinyerése a REST API pythonnal való használatával](quickstarts/python-train-extract.md)
  * Vonat címkékkel
    * [Űrlapfelismerő modell betanítása címkékkel a mintacímkéző eszközzel](quickstarts/label-tool.md)
    * [Űrlapfelismerő modell betanítása feliratokkal REST API és Python használatával](quickstarts/python-labeled-data.md)
* Előre összeállított bevételezések - adatok kinyerése az USA értékesítési bevételeiből
  * [Rövid útmutató: Nyugtaadatok kinyerése cURL használatával](quickstarts/curl-receipts.md)
  * [Rövid útmutató: Bevételezési adatok kinyerése python használatával](quickstarts/python-receipts.md)
* Elrendezés – szöveg és táblázat szerkezetének kibontása űrlapokból
  * [Rövid útmutató: Elrendezési adatok kinyerése python használatával](quickstarts/python-layout.md)

### <a name="review-the-rest-apis"></a>A REST API-k áttekintése

A következő API-k at fogja használni a modellek betanításához és a strukturált adatok űrlapokból való kinyeréséhez.

|Név |Leírás |
|---|---|
| **Egyéni modell betanítása**| Új modell betanítása az űrlapok elemzéséhez öt azonos típusú űrlap használatával. Állítsa be a _useLabelFile_ paramétert `true` a manuálisan címkézett adatokkal való betanításhoz. |
| **Űrlap elemzése** |Elemezze az adatfolyamként átadott egyetlen dokumentumot, és bontsa ki a szöveget, a kulcs-/értékpárokat és a táblázatokat az űrlapról az egyéni modellel.  |
| **Bevételezés elemzése** |Egyetlen bevételezési bizonylat elemzése a legfontosabb információk és egyéb bevételezési szöveg kinyeréséhez.|
| **Elrendezés elemzése** |Az űrlap elrendezésének elemzése a szöveg és a táblázat szerkezetének kibontásához.|

További információkért tekintse meg a [REST API referenciadokumentációját.](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm) Ha ismeri az API egy korábbi verzióját, tekintse meg az [Újdonságok](./whats-new.md) című cikket a legutóbbi változásokról.

## <a name="input-requirements"></a>Bemeneti követelmények
### <a name="custom-model"></a>Egyéni modell

[!INCLUDE [input requirements](./includes/input-requirements.md)]

### <a name="prebuilt-receipt-model"></a>Előre összeállított bevételezési modell

A bevételezési modell bemeneti követelményei némileg eltérnek.

* A formátumnak JPEG, PNG, BMP, PDF (szöveg vagy beolvasott) vagy TIFF formátumnak kell lennie.
* A fájlméretnek 20 MB-nál kisebbnek kell lennie.
* A kép méretének 50 x 50 és 10000 x 10000 képpont között kell lennie.
* A PDF-méretek legbőlegelhetőek lehetnek 17 x 17 hüvelyk, amely jogi vagy A3-as papírméretnek felel meg, és kisebbek.
* PDF és TIFF esetén csak az első 200 oldal feldolgozása történik (ingyenes szintű előfizetéssel csak az első két oldal lesz feldolgozva).

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Ez a szolgáltatás az [Online szolgáltatási feltételek](https://www.microsoftvolumelicensing.com/DocumentSearch.aspx?Mode=3&DocumentTypeId=31)értelmében egy Azure-szolgáltatás előzetes [verziójaként](https://azure.microsoft.com/support/legal/preview-supplemental-terms/) érhető el. Mint minden kognitív szolgáltatás, a Form Recognizer szolgáltatást használó fejlesztőknek is tisztában kell lenniük az ügyféladatokra vonatkozó Microsoft-irányelvekkel. További információért tekintse meg a [Cognitive Services lapot](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) a Microsoft Adatvédelmi központban.

## <a name="next-steps"></a>További lépések

Az [űrlapfelismerő API-k](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)első lépésekhez rövid [útmutatót](quickstarts/curl-train-extract.md) kell végeznie.
