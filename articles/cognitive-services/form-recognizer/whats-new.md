---
title: A Form Recognizer újdonságai
titleSuffix: Azure Cognitive Services
description: Ismerje meg az űrlap-felismerő API legújabb módosításait.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 04/14/2020
ms.author: pafarley
ms.openlocfilehash: c2b67989cbffb03eb182b4de2bf471a02ee33e7b
ms.sourcegitcommit: 1895459d1c8a592f03326fcb037007b86e2fd22f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/01/2020
ms.locfileid: "82627993"
---
# <a name="whats-new-in-form-recognizer"></a>A Form Recognizer újdonságai

Az űrlap-felismerő szolgáltatás folyamatosan frissül. Ebből a cikkből megtudhatja, hogyan maradhat naprakészen a funkciók fejlesztései, a javítások és a dokumentációs frissítések segítségével.

> [!NOTE]
> Az űrlap-felismerő rövid útmutatók és útmutatói mindig az API legújabb verzióját használják, kivéve, ha meg van adva.

## <a name="april-2020"></a>2020. április

### <a name="new-features"></a>Új funkciók
* **SDK-támogatás az űrlap-FELISMERŐ API 2.0** -s verziójának nyilvános előzetes verziójához Ebben a hónapban kibővítettük a szolgáltatás támogatását, amely tartalmaz egy előzetes verziójú SDK-t az űrlap-felismerési v 2.0 (előzetes verzió) kiadáshoz. Használja az alábbi hivatkozásokat a választott nyelv megkezdéséhez: 
   * [.NET SDK](https://github.com/Azure/azure-sdk-for-net/tree/master/sdk/formrecognizer/Azure.AI.FormRecognizer)
   * [Java SDK](https://github.com/Azure/azure-sdk-for-java/tree/master/sdk/formrecognizer/azure-ai-formrecognizer)
   * [Python SDK](https://github.com/Azure/azure-sdk-for-python/tree/master/sdk/formrecognizer/azure-ai-formrecognizer)
   * [JavaScript SDK](https://github.com/Azure/azure-sdk-for-js/tree/master/sdk/formrecognizer/ai-form-recognizer)


  Az új SDK a v 2.0 REST API összes funkcióját támogatja az űrlap-felismerőhöz. Például betaníthat egy modellt címkékkel vagy anélkül, és kinyerheti a szöveget, a kulcs érték párokat és táblákat az űrlapokból, kinyerheti a nyugták adatait az előre elkészített visszaigazolási szolgáltatással, és kinyerheti a szöveget és a táblákat az elrendezési szolgáltatással a dokumentumaiból. Az [SDK-visszajelzési űrlapon](https://aka.ms/FR_SDK_v1_feedback)megoszthatja az SDK-kat.
 
* **Egyéni modell másolása** Most már másolhat modelleket régiók és előfizetések között az új másolás egyéni modell használatával. Az egyéni modell-API másolásának megkezdése előtt be kell szereznie az engedélyt, hogy a cél erőforrásba másolja a másolás engedélyezési műveletét a cél erőforrás-végponton.
   * [Másolási engedély létrehozása](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/CopyCustomFormModelAuthorization) REST API
   * [Egyéni modell másolása](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/CopyCustomFormModel) REST API 


## <a name="march-2020"></a>2020. március 

### <a name="new-features"></a>Új funkciók

* **Címkézési értékek típusai** Mostantól megadhatja, hogy milyen típusú értékeket szeretne címkézni az űrlap-felismerő minta feliratozási eszközzel. A következő típusú értékek és változatok jelenleg támogatottak:
  * `string`
    * alapértelmezett, `no-whitespaces`,`alphanumeric`
  * `number`
    * alapértelmezett`currency`
  * `date` 
    * alapértelmezett, `dmy`, `mdy`,`ymd`
  * `time`
  * `integer`

  A funkció használatának megismeréséhez tekintse meg a [minta címkézési eszköz](./quickstarts/label-tool.md#specify-tag-value-types) útmutatóját.


* **Táblázat vizualizációja** A minta feliratozási eszköz mostantól a dokumentumban felismert táblákat jeleníti meg. Így megtekintheti a dokumentumból felismert és kinyert táblákat a címkézés és az elemzés előtt. Ez a funkció a rétegek lehetőség használatával kapcsolható be/ki.

  Példa a táblák felismerésére és kinyerésére:

  > [!div class="mx-imgBorder"]
  > ![Táblázat vizualizációja a minta feliratozási eszköz használatával](./media/whats-new/formre-table-viz.png)

    A kibontott táblázatok a alatt `"pageResults"`található JSON-kimenetben érhetők el.

  > [!IMPORTANT]
  > A címkézési táblák nem támogatottak. Ha a táblákat nem ismeri fel és nem extrated automatikusan, akkor csak kulcs/érték párokként címkézheti őket. Amikor kulcs/érték párokként címkézi a táblákat, az egyes cellákat egyedi értékként címkézze fel.

### <a name="extraction-enhancements"></a>Extrakciós fejlesztések

Ez a kiadás magában foglalja az extrakciós fejlesztéseket és a pontosságot, pontosabban a több kulcs/érték párok címkézését és kinyerését ugyanabban a sorban. 
 
### <a name="sample-labeling-tool-is-now-open-source"></a>A minta címkéző eszköz mostantól nyílt forráskódú

Az űrlap-felismerő minta feliratozási eszköze már elérhető nyílt forráskódú projektként. Integrálhatja azt a megoldásain belül, és az ügyfél-specifikus módosításokat az igényeinek megfelelően végezheti el.

Az űrlap-felismerő minta címkézési eszközével kapcsolatos további információkért tekintse át a [githubon](https://github.com/microsoft/OCR-Form-Tools/blob/master/README.md)elérhető dokumentációt.

### <a name="tls-12-enforcement"></a>A TLS 1.2 kényszerítése

A TLS 1,2 mostantól a szolgáltatáshoz tartozó összes HTTP-kérelem esetében érvénybe lép. További információ: [Azure Cognitive Services Security](../cognitive-services-security.md).

## <a name="january-2020"></a>2020. január

Ez a kiadás a 2,0 (előzetes verzió) Form felismerőt mutatja be. Az alábbi szakaszban további információkat talál az új funkciókról, a fejlesztésekről és a változásokról. 

### <a name="new-features"></a>Új funkciók

* **Egyéni modell**
  * **Tanítás címkékkel** Mostantól manuálisan címkézett adattípusú egyéni modellt is betaníthat. Ez jobb teljesítményű modelleket eredményez, és olyan modelleket hozhat létre, amelyek olyan összetett űrlapokkal vagy űrlapokkal működnek, amelyek kulcsok nélkül tartalmaznak értékeket.
  * **ASZINKRON API** Használhat aszinkron API-hívásokat a nagyméretű adatkészletek és fájlok betanításához és elemzéséhez.
  * **TIFF-fájl támogatása** Mostantól a TIFF-dokumentumokból is betaníthat és kinyerheti az adatait.
  * **A kinyerési pontosság fejlesztése**

* **Előre elkészített bevételezési modell**
  * **Tipp mennyisége** Mostantól kinyerheti a tip-mennyiségeket és az egyéb kézírásos értékeket.
  * **Vonal kibontása** A sorokból kinyerheti a tételek értékeit a nyugták közül.
  * **Megbízhatósági értékek** Megtekintheti a modell megbízhatóságát minden kinyert értékhez.
  * **A kinyerési pontosság fejlesztése**

* **Elrendezés kibontása** Mostantól az elrendezési API használatával kinyerheti a szöveges és a táblázatos adatadatokat az űrlapokból.

### <a name="custom-model-api-changes"></a>Egyéni modell API-változások

A betanításra és az egyéni modellek használatára vonatkozó összes API-t átnevezték, és néhány szinkron módszer már aszinkron módon történik. A következők jelentős változások:

* A modellek betanításának folyamata már aszinkron módon történik. A **/Custom/models** API-hívással kezdeményezheti a betanítást. Ez a hívás egy műveleti azonosítót ad vissza, amelyet az **egyéni/modellek/{ModelID}** átadhat a betanítási eredmények visszaadásához.
* A kulcs/érték kinyerését mostantól a **/Custom/models/{ModelID}/Analyze** API-hívás kezdeményezte. Ez a hívás egy műveleti azonosítót ad vissza, amelyet az **egyéni/modellek/{ModelID}/analyzeResults/{resultID}** átadhat a kinyerési eredmények visszaadásához.
* A Train művelethez tartozó műveleti azonosítók mostantól a HTTP-válaszok **hely** fejlécében találhatók, nem a **művelet – hely** fejlécben.

### <a name="receipt-api-changes"></a>Beérkezési API módosításai

Az értékesítési visszaigazolások olvasására szolgáló API-k átnevezve lettek.

* A **/prebuilt/Receipt/Analyze** API-hívás kezdeményezte a beérkezési adatbontást. Ez a hívás egy műveleti azonosítót ad vissza, amelyet átadhat a **/prebuilt/Receipt/analyzeResults/{resultID}** a kinyerési eredmények visszaadásához.

### <a name="output-format-changes"></a>Kimeneti formátum módosításai

A JSON-válaszok minden API-híváshoz új formátumok tartoznak. Egyes kulcsok és értékek hozzá lettek adva, el lettek távolítva vagy átnevezve lettek. Tekintse meg az aktuális JSON-formátumok példáit.

## <a name="next-steps"></a>További lépések

Fejezze be [a gyors](quickstarts/curl-train-extract.md) üzembe helyezési útmutatót az [űrlap-felismerő API](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)-k használatának megkezdéséhez.