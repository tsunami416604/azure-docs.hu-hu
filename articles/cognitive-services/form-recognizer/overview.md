---
title: Mi a Form Recognizer?
titleSuffix: Azure Cognitive Services
description: Az Azure Form-felismerő szolgáltatás lehetővé teszi a kulcs/érték párok és a táblák adatainak azonosítását és kinyerését az űrlapos dokumentumokból, valamint a főbb információk kinyerését az értékesítési befizetések és a névjegykártyák alapján.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: overview
ms.date: 11/23/2020
ms.author: pafarley
ms.custom: cog-serv-seo-aug-2020
keywords: automatizált adatfeldolgozás, dokumentumok feldolgozása, automatizált adatbevitel, űrlapok feldolgozása
ms.openlocfilehash: 39243f4428d055ee5d4491c3e87b4b91213f63f1
ms.sourcegitcommit: 9eda79ea41c60d58a4ceab63d424d6866b38b82d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/30/2020
ms.locfileid: "96353680"
---
# <a name="what-is-form-recognizer"></a>Mi a Form Recognizer?

[!INCLUDE [TLS 1.2 enforcement](../../../includes/cognitive-services-tls-announcement.md)]

Az Azure Form-felismerő egy olyan kognitív szolgáltatás, amely lehetővé teszi a gépi tanulási technológia használatával automatizált adatfeldolgozási szoftverek készítését. Szöveg, kulcs/érték párok, kiválasztási jelek, táblák és struktúra azonosítása a dokumentumokból &mdash; a szolgáltatás olyan strukturált adatokat tartalmaz, amelyek tartalmazzák az eredeti fájlban, a határolókeretban, a megbízhatóságban és egyebekben lévő kapcsolatokat. Az adott tartalomra szabott pontos eredményeket gyorsan, nehéz manuális beavatkozás vagy kiterjedt adatelemzési szakértelem nélkül érheti el. Az űrlap-felismerő segítségével automatizálhatja az alkalmazásaiban lévő adatbevitelt, és bővítheti a dokumentumok keresési funkcióit.

Az űrlap-felismerő egyéni dokumentum-feldolgozási modellekből, előre elkészített modellből áll a számlákhoz, a bevételezésekhez és az üzleti kártyákhoz, valamint az elrendezési modellhez. Az űrlap-felismerő modelleket meghívhatja egy REST API-vagy ügyféloldali függvénytár-SDK használatával, így csökkentheti a bonyolultságot, és integrálhatja azt a munkafolyamatba vagy alkalmazásba.

Az űrlap-felismerő a következő szolgáltatásokból áll:
* **[Layout API](#layout-api)** – szöveg-, kiválasztási és táblázatos struktúrák kinyerése, valamint a hozzájuk tartozó mezők koordinátái a dokumentumokból.
* **[Egyéni modellek](#custom-models)** – szöveg, kulcs/érték párok, kiválasztási jelek és táblák adatainak kinyerése űrlapokból. Ezek a modellek a saját adataival vannak betanítva, így az űrlapokra vannak szabva.
* Előre **[elkészített modellek](#prebuilt-models)** – az egyedi űrlapokból származó adatok kinyerése előre elkészített modellek használatával. Jelenleg a következő előre elkészített modellek érhetők el
    * [Számlák](./concept-invoices.md)
    * [Értékesítési nyugták](./concept-receipts.md)
    * [Névjegykártyák](./concept-business-cards.md)


## <a name="try-it-out"></a>Próbálja ki

Az űrlap-felismerő szolgáltatás kipróbálásához nyissa meg az online minta felhasználói felület eszközét:


# <a name="v20"></a>[2.0-s verzió](#tab/v2-0)
> [!div class="nextstepaction"]
> [Előre elkészített modellek kipróbálása](https://fott.azurewebsites.net/)

# <a name="v21-preview"></a>[v 2.1 előzetes verzió](#tab/v2-1)
> [!div class="nextstepaction"]
> [Előre elkészített modellek kipróbálása](https://fott-preview.azurewebsites.net/)

---

Az űrlap-felismerő szolgáltatás kipróbálásához szüksége lesz egy Azure-előfizetésre ([hozzon létre egyet ingyen](https://azure.microsoft.com/free/cognitive-services)) és egy [űrlap-felismerő erőforrás](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer) -végpontot és kulcsot.

## <a name="layout-api"></a>Elrendezési API

Az űrlap-felismerő kinyerheti a szöveg, a kiválasztási jelek és a tábla szerkezetét (a szöveghez társított sorok és oszlopok számát) a nagy felbontású optikai karakterfelismerés (OCR) és a dokumentumokból kibővített mély tanulási modell használatával. További információért tekintse meg az [elrendezés](./concept-layout.md) fogalmi útmutatóját.

:::image type="content" source="./media/tables-example.jpg" alt-text="táblák – példa" lightbox="./media/tables-example.jpg":::

## <a name="custom-models"></a>Egyéni modellek

Az űrlap-felismerő egyéni modelljeinek a saját adataihoz kell betanítania, és a kezdéshez csak öt minta bemeneti űrlapra van szükség. A betanított dokumentum-feldolgozási modell olyan strukturált adatokat tud kialakítani, amelyek tartalmazzák az eredeti dokumentum kapcsolatait. A modell betanítása után tesztelheti és áttaníthatja, és végül a használatával megbízhatóan kinyerheti az adatokból az igényeknek megfelelő további formákat.

Az egyéni modellek betanításakor a következő lehetőségek állnak rendelkezésére: képzés címkézett és címkézett adatbevitel nélkül.

### <a name="train-without-labels"></a>Betanítás címkék nélkül

Alapértelmezés szerint az űrlap-felismerő nem felügyelt tanulást használ az űrlapok mezőinek és bejegyzéseinek elrendezésére és kapcsolatainak megismerésére. Ha beküldi a bemeneti űrlapokat, az algoritmus a következő típus szerint állítja be az űrlapokat, felfedi a kulcsokat és táblákat, és értékeket társít a kulcsokhoz és bejegyzésekhez a táblákhoz. Ehhez nincs szükség kézi adatcímkézésre vagy intenzív kódolásra és karbantartásra, és azt javasoljuk, hogy először próbálja meg ezt a módszert.

A képzési dokumentumok gyűjtésével kapcsolatos tippekért tekintse meg a [Training-adatkészlet](./build-training-data-set.md) létrehozása című témakört.

### <a name="train-with-labels"></a>Tanítás címkékkel

Ha címkével ellátott adatokkal látja el a képzést, a modell felügyeli a tanulást, hogy az Ön által megadott címkézett űrlapok használatával kinyerje a kívánt értékeket. Ez jobb teljesítményű modelleket eredményez, és olyan modelleket hozhat létre, amelyek olyan összetett űrlapokkal vagy űrlapokkal működnek, amelyek kulcsok nélkül tartalmaznak értékeket.

Az űrlap-felismerő az [elrendezési API](#layout-api) segítségével tanulja meg a nyomtatott és a kézírásos szöveges elemek várt méretét és pozícióit. Ezután a felhasználó által megadott címkéket használja a dokumentumok kulcs/érték társításának megismeréséhez. Azt javasoljuk, hogy az új modell betanítása és a modell pontosságának javítása érdekében használjon öt, azonos típusú manuálisan címkézett űrlapot (azonos struktúrát).

[Ismerkedés a vonattal címkékkel](./quickstarts/label-tool.md)


> [!VIDEO https://channel9.msdn.com/Shows/Docs-Azure/Azure-Form-Recognizer/player]


## <a name="prebuilt-models"></a>Előre összeállított modellek

Az űrlap-felismerő olyan előre elkészített modelleket is tartalmaz, amelyek egyedi típusú adatfeldolgozást tartalmaznak.

### <a name="prebuilt-invoice-model"></a>Előre elkészített számla modellje
Az előre elkészített számla modell számos formátumban kinyeri az adatait a számlákból, és visszaadja a strukturált adatok mennyiségét. Ez a modell Kinyeri a legfontosabb adatokat, például a számla AZONOSÍTÓját, az ügyfél adatait, a szállító adatait, a szállítás, a számla, a teljes, az adó, a részösszeg és egyebek számát. Emellett az előre elkészített számla modell a számla összes szövegének és táblájának felismerésére és visszaadására van betanítva. További információért tekintse meg a [számlák](./concept-invoices.md) fogalmi útmutatóját.

:::image type="content" source="./media/overview-invoices.jpg" alt-text="minta számla" lightbox="./media/overview-invoices.jpg":::

### <a name="prebuilt-receipt-model"></a>Előre elkészített bevételezési modell

Az előkészített beérkezési modell az ausztrál, Kanada, Nagy-Britannia, India, valamint Egyesült Államok az &mdash; éttermek, a benzinkutak, a kereskedelmi és egyéb szolgáltatások által használt típus angol értékesítési nyugtáinak olvasására használatos. Ez a modell Kinyeri a legfontosabb adatokat, például a tranzakció dátumát és időpontját, a kereskedelmi adatokat, az adók mennyiségét, a sorok számát, az összesítéseket és egyebeket. Emellett az előre elkészített beérkezési modell a nyugtán lévő összes szöveg felismerésére és visszaadására van betanítva. További információért tekintse meg a [nyugták](./concept-receipts.md) fogalmi útmutatóját.

:::image type="content" source="./media/overview-receipt.jpg" alt-text="minta nyugtája" lightbox="./media/overview-receipt.jpg":::

### <a name="prebuilt-business-cards-model"></a>Előre elkészített névjegykártya-modell

A Business Cards modell lehetővé teszi olyan információk kinyerését, mint például a személy neve, a beosztás, a cím, az e-mail, a vállalat és a telefonszámok az üzleti kártyáról angol nyelven. További információért lásd a [Business Cards](./concept-business-cards.md) fogalmi útmutatóját.

:::image type="content" source="./media/overview-business-card.jpg" alt-text="névjegykártya minta" lightbox="./media/overview-business-card.jpg":::


## <a name="get-started"></a>Bevezetés

Használja a [minta űrlap-felismerő eszközt](https://fott.azurewebsites.net/) , vagy kövessen egy rövid útmutatót az adatok űrlapokból való kinyerésének megkezdéséhez. Javasoljuk, hogy az ingyenes szolgáltatást használja a technológia megismerése során. Ne feledje, hogy a szabad lapok száma legfeljebb 500 havonta.

* [Ügyféloldali kódtárak](./quickstarts/client-library.md) (az összes nyelv, több forgatókönyv)
* Webes felhasználói felület rövid útmutatói
  * [Vonat címkékkel – minta címkézési eszköz](quickstarts/label-tool.md)
* REST-útmutató
 * Szöveg, kiválasztási jelek és tábla szerkezetének kinyerése dokumentumokból
    * [Elrendezési adatok kinyerése – Python](quickstarts/python-layout.md)
  * Egyéni modellek betanítása és űrlapadatok kinyerése
    * [Vonat címkék nélkül – cURL](quickstarts/curl-train-extract.md)
    * [Betanítás címkék nélkül – Python](quickstarts/python-train-extract.md)
    * [Betanítás címkékkel – Python](quickstarts/python-labeled-data.md)
  * Adatok kinyerése a számlákból
    * [Számlázási adatok kinyerése – Python](quickstarts/python-invoices.md)
  * Adatok kinyerése az értékesítési visszaigazolásokból
    * [Bevételezési adatok kinyerése – cURL](quickstarts/curl-receipts.md)
    * [Bevételezési adatok kinyerése – Python](quickstarts/python-receipts.md)
  * Adatok kinyerése névjegykártyákból
    * [Névjegykártya-adatok kinyerése – Python](quickstarts/python-business-cards.md)

### <a name="review-the-rest-apis"></a>A REST API-k áttekintése

A következő API-kat fogja használni a modellek betanításához és a strukturált adatok űrlapokból való kinyeréséhez.

|Név |Leírás |
|---|---|
| **Elrendezés elemzése** | Egy adatfolyamként átadott dokumentum elemzése szöveg, kiválasztási jelek, táblák és struktúra kinyeréséhez a dokumentumból |
| **Egyéni modell betanítása**| Egy új modell betanításával elemezheti az űrlapokat öt azonos típusú űrlap használatával. A _useLabelFile_ paramétert beállíthatja úgy, hogy a `true` manuálisan címkézett adatként legyen betanítva. |
| **Űrlap elemzése** |Egy adatfolyamként átadott űrlap elemzése szöveg, kulcs/érték párok és táblák az egyéni modellel való kinyeréséhez.  |
| **Számla elemzése** | Egy számla elemzése a legfontosabb információk, táblák és egyéb számlázási szöveg kinyeréséhez.|
| **Visszaigazolás elemzése** | Egy bevételezési dokumentum elemzése a legfontosabb információk kinyeréséhez és a más bevételezési szövegekhez.|
| **Névjegykártya elemzése** | Egy névjegykártya elemzése a legfontosabb információk és szövegek kinyeréséhez.|


# <a name="v20"></a>[2.0-s verzió](#tab/v2-0)
További információért olvassa el a [REST API dokumentációját](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2/operations/AnalyzeWithCustomForm) . Ha már ismeri az API korábbi verzióját, tekintse [meg](./whats-new.md) a újdonságokról szóló cikket a legutóbbi változások megismeréséhez.

# <a name="v21"></a>[v 2.1](#tab/v2-1)
További információért olvassa el a [REST API dokumentációját](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-1-preview-2/operations/AnalyzeWithCustomForm) . Ha már ismeri az API korábbi verzióját, tekintse [meg](./whats-new.md) a újdonságokról szóló cikket a legutóbbi változások megismeréséhez.

---

## <a name="input-requirements"></a>Bemeneti követelmények

[!INCLUDE [input requirements](./includes/input-requirements.md)]

## <a name="deploy-on-premises-using-docker-containers"></a>Helyszíni üzembe helyezés Docker-tárolók használatával

A helyszíni API-funkciók üzembe helyezéséhez [használjon űrlap-felismerő tárolókat (előzetes verzió)](form-recognizer-container-howto.md) . Ez a Docker-tároló lehetővé teszi, hogy a szolgáltatás a megfelelőségi, biztonsági vagy egyéb működési okokból közelebb kerüljön az adataihoz.

## <a name="data-privacy-and-security"></a>Adatvédelem és biztonság

Akárcsak az összes kognitív szolgáltatás esetében, az űrlap-felismerő szolgáltatást használó fejlesztőknek ismerniük kell a Microsoft-szabályzatokat az ügyféladatok alapján. További információért tekintse meg a Microsoft adatvédelmi központjának [Cognitive Services lapját](https://www.microsoft.com/trustcenter/cloudservices/cognitiveservices) .

## <a name="next-steps"></a>További lépések

Töltse ki az [ügyféloldali kódtár](quickstarts/client-library.md) gyors kezdését, hogy megkezdje az űrlapok feldolgozását az űrlap felismerővel az Ön által választott nyelven.