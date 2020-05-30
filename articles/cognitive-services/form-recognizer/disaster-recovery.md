---
title: Vész-helyreállítási útmutató az Azure Form felismerőhöz
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan készíthet biztonsági másolatot az űrlap-felismerő erőforrásairól a copy Model API használatával.
author: PatrickFarley
manager: nitinme
ms.service: cognitive-services
ms.subservice: forms-recognizer
ms.topic: how-to
ms.date: 05/27/2020
ms.author: pafarley
ms.openlocfilehash: 2e5b32421a04e09bd32d2bba21ff4faf920d84dd
ms.sourcegitcommit: 12f23307f8fedc02cd6f736121a2a9cea72e9454
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/30/2020
ms.locfileid: "84221844"
---
# <a name="back-up-and-recover-your-form-recognizer-models"></a>Az űrlap-felismerő modelljeinek biztonsági mentése és helyreállítása

Amikor létrehoz egy űrlap-felismerő erőforrást a Azure Portal, meg kell adnia egy régiót. Ettől kezdve az erőforrás és a hozzá tartozó összes művelet az adott Azure-kiszolgáló régiójához tartozik. Ritka, de nem lehetetlen, hogy olyan hálózati problémát tapasztaljon, amely egy teljes régiót üt. Ha a megoldásnak mindig elérhetőnek kell lennie, akkor tervezze meg a feladatátvételt egy másik régióba, vagy Ossza szét a munkaterhelést két vagy több régió között. Mindkét módszer esetében legalább két, különböző régiókban található felismerő erőforrásra van szükség, és az [egyéni modellek](./quickstarts/curl-train-extract.md) szinkronizálhatók a régiók között.

A copy API ezt a forgatókönyvet teszi lehetővé azáltal, hogy lehetővé teszi az egyéni modellek másolását az egyik űrlap-felismerő fiókból vagy másokba, amelyek bármely támogatott földrajzi régióban létezhetnek. Ez az útmutató bemutatja, hogyan használható a copy REST API a cURL használatával. HTTP-kérelmeket is használhat, például a Poster szolgáltatást a kérések kiadásához.

## <a name="business-scenarios"></a>Üzleti forgatókönyvek

Ha az alkalmazás vagy az üzlet egy űrlap-felismerő egyéni modelltől függ, akkor azt javasoljuk, hogy egy másik régióban is másolja a modellt egy másik űrlap-felismerő fiókba. Ha regionális leállás következik be, a modelljét a másolt régióban is elérheti.

##  <a name="prerequisites"></a>Előfeltételek

1. Két űrlap-felismerő Azure-erőforrás különböző Azure-régiókban. Ha nem rendelkezik velük, lépjen a Azure Portalra, és <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesFormRecognizer" title=" hozzon létre egy új űrlap-felismerő erőforrást, és " target="_blank"> hozzon létre egy új űrlap-felismerő erőforrást <span class="docon docon-navigate-external x-hidden-focus"></span> </a> .
1. Az előfizetési kulcs, a végpont URL-címe és az űrlap-felismerő erőforrás előfizetés-azonosítója. Ezek az értékek az erőforrás **Áttekintés** lapján találhatók a Azure Portal.


## <a name="copy-api-overview"></a>Az API másolása – áttekintés

Az egyéni modellek másolásának folyamata a következő lépésekből áll:

1. Először egy másolási engedélyezési kérelmet kell kiadnia a célként megadott erőforráshoz &mdash; , amely a másolt modellt fogadó erőforrás. A rendszer visszaküldi az újonnan létrehozott cél modell URL-címét, amely megkapja a másolt adattípust.
1. Ezután elküldi a másolási kérést a forrás erőforrásnak &mdash; , amely a másolandó modellt tartalmazó erőforrást tartalmazza. Egy URL-címet fog visszakapni, amelyet a művelet előrehaladásának nyomon követéséhez tud lekérdezni.
1. Ha a művelet sikeres, a forrás-erőforrás hitelesítő adataival kérdezheti le a folyamatjelző URL-címet. Az új modell állapotának lekéréséhez az új modell AZONOSÍTÓját is lekérdezheti a cél erőforrásban.

## <a name="generate-copy-authorization-request"></a>Másolási engedélyezési kérelem előállítása

A következő HTTP-kérelem átmásolja az engedélyt a cél erőforrásból. A célként megadott erőforrás végpontját és kulcsát fejlécként kell megadnia.

```
POST https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

Választ kaphat a `201\Created` `modelId` törzsben található értékekre. Ez a karakterlánc az újonnan létrehozott (üres) modell azonosítója. A `accessToken` szükséges ahhoz, hogy az API átmásolja az adatforrást az erőforrásba, és az `expirationDateTimeTicks` érték a jogkivonat lejárata. Mentse mindhárom értéket egy biztonságos helyre.

```
HTTP/1.1 201 Created
Location: https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d
{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","accessToken":"1855fe23-5ffc-427b-aab2-e5196641502f","expirationDateTimeTicks":637233481531659440}
```

## <a name="start-copy-operation"></a>Másolási művelet indítása

A következő HTTP-kérelem elindítja a másolási műveletet a forrás erőforráson. A forrás erőforrás végpontját és kulcsát fejlécként kell megadnia. Figyelje meg, hogy a kérelem URL-címe tartalmazza a másolni kívánt forrásoldali modell AZONOSÍTÓját.

```
POST https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copy HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

A kérelem törzsének a következő formátumúnak kell lennie. Meg kell adnia a cél erőforrás erőforrás-AZONOSÍTÓját és régiójának nevét. Szüksége lesz az előző lépésből másolt modell-AZONOSÍTÓra, hozzáférési jogkivonatra és lejárati értékre is.

```json
{
   "targetResourceId": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}",  
   "targetResourceRegion": "{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}",
   "copyAuthorization": {"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","accessToken":"1855fe23-5ffc-427b-aab2-e5196641502f","expirationDateTimeTicks":637233481531659440}
}
```

Választ kap egy `202\Accepted` műveleti hely fejlécére. Ez az érték az az URL-cím, amelyet a művelet előrehaladásának nyomon követéséhez fog használni. Másolja a következő lépéshez egy ideiglenes helyre.

```
HTTP/1.1 202 Accepted
Operation-Location: https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1
```

## <a name="track-copy-progress"></a>A másolási folyamat nyomon követése

Nyomon követheti a folyamat előrehaladását a forrás-erőforrás végpontján a **másolási modell eredményének lekérése** API lekérdezésével.

```
GET https://{SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/eccc3f13-8289-4020-ba16-9f1d1374e96f/copyresults/02989ba8-1296-499f-aaf4-55cfff41b8f1 HTTP/1.1
Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

A válasz a művelet állapotától függően változhat. Keresse meg a `"status"` mezőt a JSON-törzsben. Ha egy parancsfájlban automatizálja ezt az API-hívást, javasoljuk, hogy másodpercenként egyszer kérdezze le a műveletet.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"status":"succeeded","createdDateTime":"2020-04-23T18:18:01.0275043Z","lastUpdatedDateTime":"2020-04-23T18:18:01.0275048Z","copyResult":{}}
```

### <a name="optional-track-the-target-model-id"></a>Választható A cél modell AZONOSÍTÓjának nyomon követése 

A cél modell lekérdezésével a művelet állapotának nyomon követéséhez használhatja az **Egyéni modell beolvasása** API-t is. Hívja meg ezt az API-t az első lépésben lemásolt cél modell-azonosító használatával.

```
GET https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/33f4d42c-cd2f-4e74-b990-a1aeafab5a5d HTTP/1.1
Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}
```

A válasz törzsében a modellre vonatkozó információk láthatók. Keresse `"status"` meg a modell állapotának mezőjét.

```
HTTP/1.1 200 OK
Content-Type: application/json; charset=utf-8
{"modelInfo":{"modelId":"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d","status":"ready","createdDateTime":"2020-02-26T16:59:28Z","lastUpdatedDateTime":"2020-02-26T16:59:34Z"},"trainResult":{"trainingDocuments":[{"documentName":"0.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"1.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"2.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"3.pdf","pages":1,"errors":[],"status":"succeeded"},{"documentName":"4.pdf","pages":1,"errors":[],"status":"succeeded"}],"errors":[]}}
```

## <a name="curl-sample-code"></a>Fürt mintájának kódja

A következő kódrészletek a cURL használatával teszik elérhetővé az API-hívásokat a fenti lépésekben. Továbbra is ki kell töltenie a saját erőforrásaihoz tartozó modell-azonosítókat és előfizetési adatokat.

### <a name="generate-copy-authorization-request"></a>Másolási engedélyezési kérelem előállítása

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" 
```

### <a name="start-copy-operation"></a>Másolási művelet indítása

```bash
curl -i -X POST "https://{TARGET_FORM_RECOGNIZER_RESOURCE_ENDPOINT}/formrecognizer/v2.0-preview/custom/models/copyAuthorization" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {TARGET_FORM_RECOGNIZER_RESOURCE_API_KEY}" --data-ascii "{ \"targetResourceId\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_ID}\",   \"targetResourceRegion\": \"{TARGET_AZURE_FORM_RECOGNIZER_RESOURCE_REGION_NAME}\", \"copyAuthorization\": "{\"modelId\":\"33f4d42c-cd2f-4e74-b990-a1aeafab5a5d\",\"accessToken\":\"1855fe23-5ffc-427b-aab2-e5196641502f\",\"expirationDateTimeTicks\":637233481531659440}"}"
```

### <a name="track-copy-progress"></a>A másolási folyamat nyomon követése

```bash
curl -i GET "https://<SOURCE_FORM_RECOGNIZER_RESOURCE_ENDPOINT>/formrecognizer/v2.0-preview/custom/models/{SOURCE_MODELID}/copyResults/{RESULT_ID}" -H "Content-Type: application/json" -H "Ocp-Apim-Subscription-Key: {SOURCE_FORM_RECOGNIZER_RESOURCE_API_KEY}"
```

## <a name="next-steps"></a>További lépések

Ebben az útmutatóban megtanulta, hogyan használhatja a copy API-t az egyéni modellek másodlagos űrlap-felismerő erőforrásra történő biztonsági mentésére. Ezután tekintse meg az API-referenciák dokumentációját, hogy megtudja, mit tehet az űrlap-felismerővel.
* [REST API dokumentáció](https://westus2.dev.cognitive.microsoft.com/docs/services/form-recognizer-api-v2-preview/operations/AnalyzeWithCustomForm)