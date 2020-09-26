---
title: Mi a Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Az Azure Form-felismerő szolgáltatás lehetővé teszi a kulcs/érték párok és a táblák adatainak azonosítását és kinyerését az űrlapos dokumentumokból, valamint a főbb információk kinyerését az értékesítési befizetések és a névjegykártyák alapján.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 09/21/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: automatizált adatfeldolgozás, dokumentumok feldolgozása, automatizált adatbevitel, űrlapok feldolgozása
ms.openlocfilehash: 5243c170e1f6b5f647057b8cfafbcac9b2fb4db3
ms.sourcegitcommit: 32c521a2ef396d121e71ba682e098092ac673b30
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/25/2020
ms.locfileid: "91318959"
---
# <a name="what-is-form-recognizer"></a>Mi a Form Recognizer?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Az Azure Form-felismerő egy olyan kognitív szolgáltatás, amely lehetővé teszi a gépi tanulási technológia használatával automatizált adatfeldolgozási szoftverek készítését. Azonosíthatja és kinyerheti a szöveges, kulcs/érték párokat és táblázatos adatokat az űrlap dokumentumaiból &mdash; a szolgáltatás olyan strukturált adatokat jelenít meg, amelyek tartalmazzák az eredeti fájl kapcsolatait. Az adott tartalomra szabott pontos eredményeket gyorsan, nehéz manuális beavatkozás vagy kiterjedt adatelemzési szakértelem nélkül érheti el. Használja az űrlap-felismerőt az alkalmazások adatbevitelének automatizálására.

Az űrlap-felismerő az egyéni dokumentum-feldolgozási modellekből, az előre összevont beérkezési és a névjegykártya modellből, valamint az elrendezési API-ból áll. Az űrlap-felismerő modelleket meghívhatja egy REST API-vagy ügyféloldali függvénytár-SDK használatával, így csökkentheti a bonyolultságot, és integrálhatja azt a munkafolyamatba vagy alkalmazásba.

Az űrlap-felismerő a következő szolgáltatásokból áll:
* **[Egyéni modellek](#custom-models)** – a kulcs/érték párok és a táblák adatainak kinyerése az űrlapokból. Ezek a modellek a saját adataival vannak betanítva, így az űrlapokra vannak szabva.
* Előre **[elkészített modellek](#prebuilt-models)** – az egyedi űrlapokból származó adatok kinyerése előre elkészített modellek használatával. Jelenleg elérhetők az előre elkészített modellek az értékesítési visszaigazolásokhoz és az üzleti kártyákhoz angol nyelven.
* **[Elrendezési API](#layout-api)** – szöveg-és táblázatos struktúrák kinyerése a dokumentumokból a határolókeret koordinátáival együtt.

## <a name="custom-models"></a>Egyéni modellek

Az űrlap-felismerő egyéni modelljeinek a saját adataihoz kell betanítania, és a kezdéshez csak öt minta bemeneti űrlapra van szükség. A betanított dokumentum-feldolgozási modell olyan strukturált adatokat tud kialakítani, amelyek tartalmazzák az eredeti dokumentum kapcsolatait. A modell betanítása után tesztelheti és áttaníthatja, és végül a használatával megbízhatóan kinyerheti az adatokból az igényeknek megfelelő további formákat.

Az egyéni modellek betanításakor a következő lehetőségek állnak rendelkezésére: képzés címkézett és címkézett adatbevitel nélkül.

### <a name="train-without-labels"></a>Betanítás címkék nélkül

Alapértelmezés szerint az űrlap-felismerő nem felügyelt tanulást használ az űrlapok mezőinek és bejegyzéseinek elrendezésére és kapcsolatainak megismerésére. Ha beküldi a bemeneti űrlapokat, az algoritmus a következő típus szerint állítja be az űrlapokat, felfedi a kulcsokat és táblákat, és értékeket társít a kulcsokhoz és bejegyzésekhez a táblákhoz. Ehhez nincs szükség kézi adatcímkézésre vagy intenzív kódolásra és karbantartásra, és azt javasoljuk, hogy először próbálja meg ezt a módszert.

A képzési dokumentumok gyűjtésével kapcsolatos tippekért tekintse meg a [Training-adatkészlet](./build-training-data-set.md) létrehozása című témakört.

### <a name="train-with-labels"></a>Tanítás címkékkel

Ha címkével ellátott adatokkal látja el a képzést, a modell felügyeli a tanulást, hogy az Ön által megadott címkézett űrlapok használatával kinyerje a kívánt értékeket. Ez jobb teljesítményű modelleket eredményez, és olyan modelleket hozhat létre, amelyek olyan összetett űrlapokkal vagy űrlapokkal működnek, amelyek kulcsok nélkül tartalmaznak értékeket.

Az űrlap-felismerő az [elrendezési API](#layout-api) segítségével tanulja meg a nyomtatott és a kézírásos szöveges elemek várt méretét és pozícióit. Ezután a felhasználó által megadott címkéket használja a dokumentumok kulcs/érték társításának megismeréséhez. Azt javasoljuk, hogy az új modell betanítása és a modell pontosságának javítása érdekében a típus öt kézzel címkézett formáját használja az első lépésekhez.

## <a name="prebuilt-models"></a>Előre összeállított modellek

Az űrlap-felismerő olyan előre elkészített modelleket is tartalmaz, amelyek egyedi típusú adatfeldolgozást tartalmaznak.

### <a name="prebuilt-receipt-model"></a>Előre elkészített bevételezési modell

Az előkészített beérkezési modell az ausztrál, Kanada, Nagy-Britannia, India, valamint Egyesült Államok az &mdash; éttermek, a benzinkutak, a kereskedelmi és egyéb szolgáltatások által használt típus angol értékesítési nyugtáinak olvasására használatos. Ez a modell Kinyeri a legfontosabb adatokat, például a tranzakció dátumát és időpontját, a kereskedelmi adatokat, az adók mennyiségét, a sorok számát, az összesítéseket és egyebeket. Emellett az előre elkészített beérkezési modell a nyugtán lévő összes szöveg felismerésére és visszaadására van betanítva. További információért tekintse meg a [nyugták](./concept-receipts.md) fogalmi útmutatóját.

![minta nyugtája](./media/contoso-receipt-small.png)

### <a name="prebuilt-business-cards-model"></a>Előre elkészített névjegykártya-modell

A Business Cards modell lehetővé teszi olyan információk kinyerését, mint például a személy neve, a beosztás, a cím, az e-mail, a vállalat és a telefonszámok az üzleti kártyáról angol nyelven. További információért lásd a [Business Cards](./concept-business-cards.md) fogalmi útmutatóját.

![névjegykártya minta](./media/business-card-english.jpg)

## <a name="layout-api"></a>Elrendezési API

Az űrlap-felismerő a szöveg és a tábla szerkezetét is kinyerheti (a szöveghez társított sorok és oszlopok száma) a nagy felbontású optikai karakterfelismerés (OCR) használatával.

## <a name="get-started"></a>Első lépések

Egy rövid útmutató segítségével megkezdheti az űrlapok adatainak kinyerését. Javasoljuk, hogy az ingyenes szolgáltatást használja a technológia megismerése során. Ne feledje, hogy a szabad lapok száma legfeljebb 500 havonta.

* [Ügyféloldali kódtárak](./quickstarts/client-library.md) (az összes nyelv, több forgatókönyv)
* Webes felhasználói felület rövid útmutatói
  * [Vonat címkékkel – minta címkézési eszköz](quickstarts/label-tool.md)
* REST-útmutató
  * Egyéni modellek betanítása és űrlapadatok kinyerése
    * [Vonat címkék nélkül – cURL](quickstarts/curl-train-extract.md)
    * [Betanítás címkék nélkül – Python](quickstarts/python-train-extract.md)
    * [Betanítás címkékkel – Python](quickstarts/python-labeled-data.md)
  * Adatok kinyerése az értékesítési visszaigazolásokból
    * [Bevételezési adatok kinyerése – cURL](quickstarts/curl-receipts.md)
    * [Bevételezési adatok kinyerése – Python](quickstarts/python-receipts.md)
  * Adatok kinyerése névjegykártyákból
    * [Névjegykártya-adatok kinyerése – Python](quickstarts/python-business-cards.md)
  * Szöveg és táblázat struktúrájának kinyerése űrlapokból
    * [Elrendezési adatok kinyerése – Python](quickstarts/python-layout.md)


### <a name="review-the-rest-apis"></a>A REST API-k áttekintése

A következő API-kat fogja használni a modellek betanításához és a strukturált adatok űrlapokból való kinyeréséhez.

|Név |Leírás |
|---|---|
| **Egyéni modell betanítása**| Egy új modell betanításával elemezheti az űrlapokat öt azonos típusú űrlap használatával. A _useLabelFile_ paramétert beállíthatja úgy, hogy a `true` manuálisan címkézett adatként legyen betanítva. |
| **Űrlap elemzése** |Egyetlen dokumentum elemzése, amely streamként lett átadva szöveg-, kulcs/érték párok és táblák kinyeréséhez az űrlapról az egyéni modellel.  |
| **Visszaigazolás elemzése** |Egyetlen bevételezési dokumentum elemzése a legfontosabb információk és a többi bevételezési szöveg kinyeréséhez.|
| **Névjegykártya elemzése** |Egy névjegykártya elemzése a legfontosabb információk és szövegek kinyeréséhez.|
| **Elrendezés elemzése** |Egy űrlap elrendezésének elemzése szöveg és tábla szerkezetének kinyeréséhez.|

További információért olvassa el a [REST API dokumentációját](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) . Ha már ismeri az API korábbi verzióját, tekintse [meg](./whats-new.md) a újdonságokról szóló cikket a legutóbbi változások megismeréséhez.

## <a name="input-requirements"></a>Bemeneti követelmények

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Akárcsak az összes kognitív szolgáltatás esetében, az űrlap-felismerő szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft-szabályzatokat az ügyféladatok alapján. További információért tekintse meg a Microsoft adatvédelmi központjának [Cognitive Services lapját](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) .

## <a name="next-steps"></a>Következő lépések

Töltse ki az [ügyféloldali kódtár](quickstarts/client-library.md) gyors kezdését, hogy megkezdje az űrlapok feldolgozását az űrlap felismerővel az Ön által választott nyelven.