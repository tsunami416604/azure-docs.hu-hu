---
title: 'Rövid útmutató: A cURL & REST használatával kezelheti a tudásbázist - QnA Maker'
description: Ez a rövid útmutató bemutatja, hogyan hozhat létre, tehet közzé és kérdezheti le a tudásbázist a REST API-k használatával.
ms.date: 02/27/2020
ROBOTS: NOINDEX,NOFOLLOW
ms.custom: RESTCURL2020FEB27
ms.topic: quickstart
ms.openlocfilehash: 00ec52fe20fb0e6a976f3e7142386e835713c98c
ms.sourcegitcommit: 9ee0cbaf3a67f9c7442b79f5ae2e97a4dfc8227b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "78851208"
---
# <a name="quickstart-use-curl-and-rest-to-manage-knowledge-base"></a>Rövid útmutató: A cURL és a REST használata a tudásbázis kezeléséhez

Ez a rövid útmutató végigvezeti a tudásbázis létrehozásán, közzétételén és lekérdezésén. A QnA Maker automatikusan nyer ki kérdéseket és válaszokat a félig strukturált tartalmak, például a gyakori kérdések közül, az [adatforrásokból](../Concepts/knowledge-base.md). A tudásbázis modelljét az API-kérés törzsében küldött JSON definiálja.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisites"></a>Előfeltételek

* A [cURL](https://curl.haxx.se/)aktuális verziója . A rövid útmutatókban számos parancssori kapcsoló t használ, amelyek a [cURL dokumentációjában](https://curl.haxx.se/docs/manpage.html)találhatók.
* Rendelkeznie kell egy [QnA Maker erőforrással.](../How-To/set-up-qnamaker-service-azure.md) A kulcs és az erőforrás nevének beolvasásához válassza az erőforrás **gyorsútmutatóját** az Azure Portalon. Az erőforrás neve a végpont URL-címének első része:

    `https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0`

> [!CAUTION]
> A következő BASH `\` példák a sor folytatási karakterét használják. Ha a konzol vagy a terminál más sorfolytatási karaktert használ, ezt a karaktert használja.

## <a name="create-a-knowledge-base"></a>Tudásbázis létrehozása

Ha tudásbázist szeretne létrehozni a REST API-kkal és a cURL-lel, a következő információkkal kell rendelkeznie:

|Információ|cURL konfiguráció|Cél|
|--|--|--|
|QnA Maker erőforrás neve|URL-cím|URL-cím létrehozásához|
|QnA Maker erőforráskulcs|`-h`param `Ocp-Apim-Subscription-Key` fejléchez|Hitelesítés a QnA Maker szolgáltatásnak|
|JSON leíró tudásbázis|`-d`Param|[Példák](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/create#examples) a JSON-ra|
|A JSON mérete bájtban|`-h`param `Content-Size` fejléchez||

A cURL parancs végrehajtása BASH rendszerhéjból történik. A parancs szerkesztése saját erőforrásnevével, erőforráskulcsával és JSON-értékekkel és JSON-értékekkel.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/create \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:107" \
-d '{ name: "QnA Maker FAQ",urls: [ "https://docs.microsoft.com/en-in/azure/cognitive-services/qnamaker/faqs"]}'
```

A QnA Maker cURL `operationId` válasza tartalmazza a , amely [a művelet állapotának kéréséhez](#get-status-of-operation)szükséges .

```json
{
  "operationState": "NotStarted",
  "createdTimestamp": "2020-02-27T04:11:22Z",
  "lastActionTimestamp": "2020-02-27T04:11:22Z",
  "userId": "9596077b3e0441eb93d5080d6a15c64b",
  "operationId": "95a4f700-9899-4c98-bda8-5449af9faef8"
}
```

## <a name="get-status-of-operation"></a>A működés állapotának beszereznie

Amikor létrehoz egy tudásbázist, mert a művelet aszinkron, a válasz információkat tartalmaz az állapot meghatározásához.

|Információ|cURL konfiguráció|Cél|
|--|--|--|
|QnA Maker erőforrás neve|URL-cím|URL-cím létrehozásához|
|Műveleti azonosító|URL-útvonal|`/operations/REPLACE-WITH-YOUR-OPERATION-ID`|
|QnA Maker erőforráskulcs|`-h`param `Ocp-Apim-Subscription-Key` fejléchez|Hitelesítés a QnA Maker szolgáltatásnak|

A cURL parancs végrehajtása BASH rendszerhéjból történik. A parancs szerkesztése saját erőforrásnevével, erőforráskulcsával és műveleti azonosítójával.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/operations/REPLACE-WITH-YOUR-OPERATION-ID \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

A cURL válasz tartalmazza az állapotot. Ha a művelet állapota sikeres, `resourceLocation` akkor a tudásbázis-azonosítót tartalmazza.

```json
{
   "operationState": "Succeeded",
   "createdTimestamp": "2020-02-27T04:54:07Z",
   "lastActionTimestamp": "2020-02-27T04:54:19Z",
   "resourceLocation": "/knowledgebases/fe3971b7-cfaa-41fa-8d9f-6ceb673eb865",
   "userId": "f596077b3e0441eb93d5080d6a15c64b",
   "operationId": "f293f218-d080-48f0-a766-47993e9b26a8"
}
```

## <a name="publish-knowledge-base"></a>Tudásbázis közzététele

A tudásbázis lekérdezése előtt a következőket kell a következőkre tudnia:
* Tudásbázis közzététele
* A futásidejű végpontkulcs betöltése

Ez a feladat közzéteszi a tudásbázist. A futásidejű végpontkulcs beszerzése [külön feladat.](#get-published-knowledge-bases-runtime-endpoint-key)

|Információ|cURL konfiguráció|Cél|
|--|--|--|
|QnA Maker erőforrás neve|URL-cím|URL-cím létrehozásához|
|QnA Maker erőforráskulcs|`-h`param `Ocp-Apim-Subscription-Key` fejléchez|Hitelesítés a QnA Maker szolgáltatásnak|
|Tudásbázis azonosítója|URL-útvonal|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

A cURL parancs végrehajtása BASH rendszerhéjból történik. A parancs szerkesztése saját erőforrásnevével, erőforráskulcsával és tudásbázis-azonosítójával.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-v \
-X POST \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY" \
--data-raw ''
```

A válasz állapota 204 eredmények nélkül. A `-v` parancssori paraméterrel megtekintheti a cURL parancs részletes kimenetét. Ez magában foglalja a HTTP állapotot.

## <a name="get-published-knowledge-bases-runtime-endpoint-key"></a>Közzétett tudásbázis futásidejű végpontkulcsának beszereznie

A tudásbázis lekérdezése előtt a következőket kell a következőkre tudnia:
* Tudásbázis közzététele
* A futásidejű végpontkulcs betöltése

Ez a feladat learatja a futásidejű végpontkulcsot. A tudásbázis közzététele [külön feladat.](#publish-knowledge-base)

A futásidejű végpontkulcs ugyanaz a kulcs a QnA Maker erőforrást használó összes tudásbázishoz.

|Információ|cURL konfiguráció|Cél|
|--|--|--|
|QnA Maker erőforrás neve|URL-cím|URL-cím létrehozásához|
|QnA Maker erőforráskulcs|`-h`param `Ocp-Apim-Subscription-Key` fejléchez|Hitelesítés a QnA Maker szolgáltatásnak|

A cURL parancs végrehajtása BASH rendszerhéjból történik. A parancs szerkesztése saját erőforrásnevével, erőforráskulcsával.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/endpointkeys \
-X GET \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```


A cURL-válasz tartalmazza a futásidejű végpontkulcsokat. A lekérdezéssorán csak az egyik kulcsot használja, hogy választ kapjon a tudásbázistól.

```json
{
  "primaryEndpointKey": "93e88a14-694a-44d5-883b-184a68aa8530",
  "secondaryEndpointKey": "92c98c16-ca31-4294-8626-6c57454a5063",
  "installedVersion": "4.0.5",
  "lastStableVersion": "4.0.6"
}
```

## <a name="query-for-answer-from-published-knowledge-base"></a>Közzétett tudásbázis válaszának lekérdezése

A tudás válaszának beszerzése külön futásidejű, mint a tudásbázis kezelése. Mivel ez egy külön futásidejű, egy futásidejű kulccsal kell hitelesítenie.

|Információ|cURL konfiguráció|Cél|
|--|--|--|
|QnA Maker erőforrás neve|URL-cím|URL-cím létrehozásához|
|QnA Maker futásidejű kulcs|`-h`param `Authorization` fejléchez|A kulcs egy karakterlánc része, `Endpointkey `amely tartalmazza a szót. Hitelesítés a QnA Maker szolgáltatásnak|
|Tudásbázis azonosítója|URL-útvonal|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|
|JSON leírás lekérdezés|`-d`Param|[Törzsparaméterek](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#request-body) és a JSON [példái](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/generateanswer#examples)|
|A JSON mérete bájtban|`-h`param `Content-Size` fejléchez||

A cURL parancs végrehajtása BASH rendszerhéjból történik. A parancs szerkesztése saját erőforrásnevével, erőforráskulcsával és tudásbázis-azonosítójával.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.azurewebsites.net/qnamaker/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID/generateAnswer \
-X POST \
-H "Authorization: EndpointKey REPLACE-WITH-YOUR-RUNTIME-KEY" \
-H "Content-Type:application/json" \
-H "Content-Size:159" \
-d '{"question": "How are QnA Maker and LUIS used together?","top": 6,"isTest": true,  "scoreThreshold": 20, "strictFilters": [], "userId": "sd53lsY="}'
```

A sikeres válasz tartalmazza a felső választ, valamint az egyéb információkat, amelyeket az ügyfélalkalmazásnak, például egy csevegőrobotnak meg kell adnia a felhasználónak.

## <a name="delete-knowledge-base"></a>Tudásbázis törlése

Ha végzett a tudásbázissal, törölje azt.

|Információ|cURL konfiguráció|Cél|
|--|--|--|
|QnA Maker erőforrás neve|URL-cím|URL-cím létrehozásához|
|QnA Maker erőforráskulcs|`-h`param `Ocp-Apim-Subscription-Key` fejléchez|Hitelesítés a QnA Maker szolgáltatásnak|
|Tudásbázis azonosítója|URL-útvonal|`/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID`|

A cURL parancs végrehajtása BASH rendszerhéjból történik. A parancs szerkesztése saját erőforrásnevével, erőforráskulcsával és tudásbázis-azonosítójával.

```bash
curl https://REPLACE-WITH-YOUR-RESOURCE-NAME.cognitiveservices.azure.com/qnamaker/v4.0/knowledgebases/REPLACE-WITH-YOUR-KNOWLEDGE-BASE-ID \
-X DELETE \
-v \
-H "Ocp-Apim-Subscription-Key: REPLACE-WITH-YOUR-RESOURCE-KEY"
```

A válasz állapota 204 eredmények nélkül. A `-v` parancssori paraméterrel megtekintheti a cURL parancs részletes kimenetét. Ez magában foglalja a HTTP állapotot.

## <a name="additional-resources"></a>További források

* [Szerzői jog](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase) Referenciadokumentáció
* [Futási idő](https://docs.microsoft.com/rest/api/cognitiveservices/qnamakerruntime/runtime/) Referenciadokumentáció
* [Példa BASH parancsfájlok segítségével cURL](https://github.com/Azure-Samples/cognitive-services-quickstart-code/tree/master/curl/QnAMaker)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [QnA Maker (V4) REST API-referencia](https://go.microsoft.com/fwlink/?linkid=2092179)
