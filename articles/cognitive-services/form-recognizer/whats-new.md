---
title: A Form Recognizer újdonságai
titleSuffix: Azure Cognitive Services
description: Ismerje meg az űrlap-felismerő API legújabb módosításait.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: conceptual
ms.date: 05/19/2020
ms.author: pafarley
ms.openlocfilehash: ad87c7e3517aad8a611c711773c5e1cc99b24f88
ms.sourcegitcommit: 73ac360f37053a3321e8be23236b32d4f8fb30cf
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/30/2020
ms.locfileid: "85562882"
---
# <a name="whats-new-in-form-recognizer"></a>A Form Recognizer újdonságai

Az űrlap-felismerő szolgáltatás folyamatosan frissül. Ebből a cikkből megtudhatja, hogyan maradhat naprakészen a funkciók fejlesztései, a javítások és a dokumentációs frissítések segítségével.

> [!NOTE]
> Az űrlap-felismerő rövid útmutatók és útmutatói mindig az API legújabb verzióját használják, kivéve, ha meg van adva.

## <a name="june-2020"></a>2020. június

### <a name="new-features"></a>Új funkciók
* **COPYMODEL API hozzáadva az ügyféloldali SDK-k számára** Mostantól az ügyfél SDK-k használatával is másolhat modelleket az egyik előfizetésből a másikba. A szolgáltatással kapcsolatos általános információkért tekintse meg a [modellek biztonsági mentése és helyreállítása](./disaster-recovery.md) című témakört.
* **Azure Active Directory integráció** Mostantól a HRE hitelesítő adataival hitelesítheti az űrlap-felismerő ügyféloldali objektumait az SDK-k használatával.
* **SDK-specifikus változások** Ez magában foglalja a másodlagos funkciók hozzáadását és a változtatások megszakítását is. További információért tekintse meg az SDK-naplókat.
  * [C# SDK előnézet 3 changelog](https://github.com/Azure/azure-sdk-for-net/blob/master/sdk/formrecognizer/Azure.AI.FormRecognizer/CHANGELOG.md)
  * [Python SDK előzetes verzió 3 – changelog](https://github.com/Azure/azure-sdk-for-python/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [Java SDK előzetes verzió 3 – changelog](https://github.com/Azure/azure-sdk-for-java/blob/master/sdk/formrecognizer/azure-ai-formrecognizer/CHANGELOG.md)
  * [JavaScript SDK előzetes verzió 3 – changelog](https://github.com/Azure/azure-sdk-for-js/blob/%40azure/ai-form-recognizer_1.0.0-preview.3/sdk/formrecognizer/ai-form-recognizer/CHANGELOG.md)

## <a name="april-2020"></a>2020. április

### <a name="new-features"></a>Új funkciók
* **SDK-támogatás az űrlap-FELISMERŐ API 2.0** -s verziójának nyilvános előzetes verziójához Ebben a hónapban kibővítettük a szolgáltatás támogatását, amely tartalmaz egy előzetes verziójú SDK-t az űrlap-felismerési v 2.0 (előzetes verzió) kiadáshoz. Használja az alábbi hivatkozásokat a választott nyelv megkezdéséhez: 
   * [.NET SDK](https://docs.microsoft.com/dotnet/api/overview/azure/formrecognizer)
   * [Java SDK](https://docs.microsoft.com/java/api/overview/azure/formrecognizer)
   * [Python SDK](https://docs.microsoft.com/python/api/overview/azure/formrecognizer)
   * [JavaScript SDK](https://docs.microsoft.com/javascript/api/overview/azure/formrecognizer)

  Az új SDK a v 2.0 REST API összes funkcióját támogatja az űrlap-felismerőhöz. Például betaníthat egy modellt címkékkel vagy anélkül, és kinyerheti a szöveget, a kulcs érték párokat és táblákat az űrlapokból, kinyerheti a nyugták adatait az előre elkészített visszaigazolási szolgáltatással, és kinyerheti a szöveget és a táblákat az elrendezési szolgáltatással a dokumentumaiból. Az [SDK-visszajelzési űrlapon](https://aka.ms/FR_SDK_v1_feedback)megoszthatja az SDK-kat.
 
* **Egyéni modell másolása** Most már másolhat modelleket régiók és előfizetések között az új másolás egyéni modell használatával. Az egyéni modell-API másolásának megkezdése előtt be kell szereznie az engedélyt, hogy a cél erőforrásba másolja a másolás engedélyezési műveletét a cél erőforrás-végponton.
   * [Másolási engedély létrehozása](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/CopyCustomFormModelAuthorization) REST API
   * [Egyéni modell másolása](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/CopyCustomFormModel) REST API 

### <a name="security-improvements"></a>Biztonsági fejlesztések

* Az ügyfél által felügyelt kulcsok mostantól elérhetők a FormRecognizer. További információ: az adattitkosítás inaktív állapotban [az űrlap-felismerő számára](https://docs.microsoft.com/azure/cognitive-services/form-recognizer/form-recognizer-encryption-of-data-at-rest).
* Felügyelt identitások használata az Azure-erőforrásokhoz való hozzáféréshez Azure Active Directory. További információ: [hozzáférés engedélyezése a felügyelt identitásokhoz](https://docs.microsoft.com/azure/cognitive-services/authentication#authorize-access-to-managed-identities).

## <a name="march-2020"></a>2020. március 

### <a name="new-features"></a>Új funkciók

* **Címkézési értékek típusai** Mostantól megadhatja, hogy milyen típusú értékeket szeretne címkézni az űrlap-felismerő minta feliratozási eszközzel. A következő típusú értékek és változatok jelenleg támogatottak:
  * `string`
    * alapértelmezett, `no-whitespaces` ,`alphanumeric`
  * `number`
    * alapértelmezett`currency`
  * `date` 
    * alapértelmezett, `dmy` , `mdy` ,`ymd`
  * `time`
  * `integer`

  A funkció használatának megismeréséhez tekintse meg a [minta címkézési eszköz](./quickstarts/label-tool.md#specify-tag-value-types) útmutatóját.


* **Táblázat vizualizációja** A minta feliratozási eszköz mostantól a dokumentumban felismert táblákat jeleníti meg. Így megtekintheti a dokumentumból felismert és kinyert táblákat a címkézés és az elemzés előtt. Ez a funkció a rétegek lehetőség használatával kapcsolható be/ki.

  Példa a táblák felismerésére és kinyerésére:

  > [!div class="mx-imgBorder"]
  > ![Táblázat vizualizációja a minta feliratozási eszköz használatával](./media/whats-new/formre-table-viz.png)

    A kibontott táblázatok a alatt található JSON-kimenetben érhetők el `"pageResults"` .

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