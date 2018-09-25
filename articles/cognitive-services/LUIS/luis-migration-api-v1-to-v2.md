---
title: V2 a v1 útmutató API-k áttelepítése
titleSuffix: Azure Cognitive Services
description: Ismerje meg, hogyan áttelepítése a legújabb API-ra állította.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/06/2018
ms.author: diberry
ms.openlocfilehash: b936b42671c15a77a901f321ed3c51f1ba3639cd
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47036592"
---
# <a name="api-v2-migration-guide"></a>API v2-Migrálási útmutató
Az 1. verziójának [végpont](https://aka.ms/v1-endpoint-api-docs) és [szerzői](https://aka.ms/v1-authoring-api-docs) API-k elavulttá válik. Ez az útmutató segítségével megtudhatja, hogyan 2. verzióban át [végpont](https://aka.ms/luis-endpoint-apis) és [szerzői](https://aka.ms/luis-authoring-apis) API-k. 

## <a name="new-azure-regions"></a>Új Azure-régió
A LUIS rendelkezik új [régiók](https://aka.ms/LUIS-regions) biztosított az intelligens HANGFELISMERÉSI API-t. A LUIS régiócsoportok biztosít egy másik webhelyre. Az alkalmazás lekérdezése várhatóan ugyanabban a régióban kell hozhatóak létre. Alkalmazások nem telepíthetők át automatikusan régióban. Az alkalmazás exportálhat egy adott régióban, majd importálása másik ahhoz, hogy egy új régióban érhető el.

## <a name="authoring-route-changes"></a>Módosítások. útvonal szerzői műveletekhez részben.
Az Authoring Tool API útvonal változása: használja a **programazonosítója** útvonal használatával az **api** útvonalat.


| verzió: | útvonal |
|--|--|
|1|/Luis/V1.0/**programazonosítója**/apps|
|2|/Luis/**api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>Végpont útvonal módosítások
A végpont API új lekérdezési karakterlánc paraméterei, valamint a különböző választ tartalmaz. A részletes jelző értéke igaz, ha minden leképezések, függetlenül a pontszám, feltételobjektumot leképezések mellett a topScoringIntent nevű tömbben.

| verzió: | Útvonal LEKÉRÉSE |
|--|--|
|1|/Luis/V1/Application? azonosítója = {appId} & q = {q}|
|2|/ luis/v2.0/apps/{appId}?q={q} [& timezoneOffset] [& részletes] [& helyesírás-ellenőrzés] [& átmeneti] [& bing – helyesírás-ellenőrzés-subscription-key] [& log]|


V1 végpont sikeres válasz:
```JSON
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

v2 végpont sikeres válasz:
```JSON
{
  "query": "forward to frank 30 dollars through HSBC",
  "topScoringIntent": {
    "intent": "give",
    "score": 0.3964121
  },
  "entities": [
    {
      "entity": "30",
      "type": "builtin.number",
      "startIndex": 17,
      "endIndex": 18,
      "resolution": {
        "value": "30"
      }
    },
    {
      "entity": "frank",
      "type": "frank",
      "startIndex": 11,
      "endIndex": 15,
      "score": 0.935219169
    },
    {
      "entity": "30 dollars",
      "type": "builtin.currency",
      "startIndex": 17,
      "endIndex": 26,
      "resolution": {
        "unit": "Dollar",
        "value": "30"
      }
    },
    {
      "entity": "hsbc",
      "type": "Bank",
      "startIndex": 36,
      "endIndex": 39,
      "resolution": {
        "values": [
          "BankeName"
        ]
      }
    }
  ]
}
```

## <a name="key-management-no-longer-in-api"></a>API-ban már nem kulcskezelés
Az előfizetési végpont kulcs API-k elavultak, 410-es GONE visszaadása.

| verzió: | útvonal |
|--|--|
|1|/Luis/V1.0/prog/Subscriptions|
|1|/ luis/v1.0/prog/subscriptions/{subscriptionKey}|

Azure [végpont kulcsok](luis-how-to-azure-subscription.md) jönnek létre az Azure Portalon. A LUIS-alkalmazások a kulcsot rendel a **[közzététel](luis-how-to-manage-keys.md)** lap. Nem kell tudni, hogy a tényleges kulcs értékét. LUIS, hogy a hozzárendelés előfizetést a nevet használja. 

## <a name="new-versioning-route"></a>Új versioning útvonal
A v2-modell most már szerepel egy [verzió](luis-how-to-manage-versions.md). A verziónév érték 10 karakter hosszúságú, az útvonal. Az alapértelmezett verziószáma "0.1".

| verzió: | útvonal |
|--|--|
|1|/Luis/V1.0/**programazonosítója**/apps/ {appId} / entitás|
|2|/Luis/**api**/v2.0/apps/{appId}/**verziók**/ {versionId} / entitás|

## <a name="metadata-renamed"></a>A metaadat neve
A LUIS metaadatokat visszaadni több API-k új névvel rendelkeznek.

| V1 útvonal neve | v2 útvonal neve |
|--|--|
|PersonalAssistantApps |Kiemeltek|
|applicationcultures|kulturális környezetek|
|applicationdomains|tartományok|
|applicationusagescenarios|usagescenarios|


## <a name="sample-renamed-to-suggest"></a>"Sample", "javaslat" nevet kapott:
A LUIS javasol utterances meglévő [végpont utterances](luis-how-to-review-endoint-utt.md) , előfordulhat, hogy növelje a modell. A korábbi verzióban ez nevű **minta**. Az új verzióban neve megváltozik a mintából **javaslat**. Ezt nevezzük **[tekintse át a végpont utterances](luis-how-to-review-endoint-utt.md)** a LUIS-webhelyen.

| verzió: | útvonal |
|--|--|
|1|/Luis/V1.0/**programazonosítója**/apps/ {appId} /entities/ {entityId} /**minta**|
|1|/Luis/V1.0/**programazonosítója**/apps/ {appId} /intents/ {intentId} /**minta**|
|2|/Luis/**api**/v2.0/apps/{appId}/**verziók**/ {versionId} /entities/ {entityId} /**javaslat**|
|2|/Luis/**api**/v2.0/apps/{appId}/**verziók**/ {versionId} /intents/ {intentId} /**javaslat**|


## <a name="create-app-from-prebuilt-domains"></a>Alkalmazás létrehozása előre összeállított tartományok
[Előre összeállított tartományok](luis-how-to-use-prebuilt-domains.md) előre meghatározott tartományi modell biztosítása. Előre összeállított tartományok engedélyezése gyorsan üzembe helyezheti a LUIS alkalmazás gyakori tartományokhoz. Ez az API lehetővé teszi, hogy hozzon létre egy új alkalmazást, egy előre elkészített tartomány alapján. A válasz új alkalmazásazonosító.

|v2 útvonal|művelet|
|--|--|
|/Luis/API/v2.0/Apps/customprebuiltdomains  |GET, post|
|/ luis/api/v2.0/apps/customprebuiltdomains/{culture}  |Get|

## <a name="importing-1x-app-into-2x"></a>2.x 1.x alkalmazás importálása
Az exportált 1.x alkalmazás JSON-FÁJLBAN szerepel, amely importálása előtt módosítani kell néhány területe [LUIS] [ LUIS] 2.0. 

### <a name="prebuilt-entities"></a>Előre összeállított entitások 
A [előre összeállított entitások](luis-prebuilt-entities.md) megváltoztak. Ellenőrizze, hogy a V2 használata előre összeállított entitások. Ez magában foglalja a használatával [datetimeV2](luis-prebuilt-entities.md#use-a-prebuilt-datetimev2-entity), dátum és idő helyett. 

### <a name="actions"></a>Műveletek
A műveleti tulajdonság már nem érvényes. Akkor érdemes lehet üres. 

### <a name="labeled-utterances"></a>Címkézett kimondott szöveg
V1 engedélyezett címkével ellátott kimondott szöveg legyen benne szóköz elején vagy végén szó vagy kifejezés. A szóközök törlődnek. 

## <a name="common-reasons-for-http-response-status-codes"></a>HTTP-állapotkódok leggyakoribb okai
Lásd: [intelligens HANGFELISMERÉSI API válaszkódok](luis-reference-response-codes.md).

## <a name="next-steps"></a>További lépések

A v2 API-dokumentáció frissítésére a meglévő REST-hívások LUIS használatát [végpont](https://aka.ms/luis-endpoint-apis) és [szerzői](https://aka.ms/luis-authoring-apis) API-k. 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions