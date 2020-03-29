---
title: v1 a v2 API áttelepítése
titleSuffix: Azure Cognitive Services
description: Az 1-es verziójú végpont és a nyelvi megértésapi-k szerzői nyelvének példái elavultak. Ez az útmutató a 2-es verziójú végpontra való áttelepítés és a szerzői API-k átgrésének megismeréséhez használható.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 04/02/2019
ms.author: diberry
ms.openlocfilehash: 2f67bf0951ef8928297c71e8fc9f924cf05c63f4
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68932694"
---
# <a name="api-v1-to-v2-migration-guide-for-luis-apps"></a>API-v1 a v2 áttelepítési útmutató LUIS-alkalmazások
Az 1-es [verziójú végpont](https://aka.ms/v1-endpoint-api-docs) és [a szerzői](https://aka.ms/v1-authoring-api-docs) API-k elavultak. Ez az útmutató a 2-es [verziójú végpontra](https://go.microsoft.com/fwlink/?linkid=2092356) való áttelepítés és a szerzői API-k átgrésének [megismeréséhez](https://go.microsoft.com/fwlink/?linkid=2092087) használható. 

## <a name="new-azure-regions"></a>Új Azure-régiók
A LUIS új [régiókat](https://aka.ms/LUIS-regions) biztosít a LUIS API-khoz. A LUIS egy másik portált biztosít a régiócsoportokszámára. Az alkalmazásnak ugyanabban a régióban kell szerződnie, amelyet le szeretne kérdezni. Az alkalmazások nem telepítik át automatikusan a régiókat. Exportálja az alkalmazást az egyik régióból, majd importálja egy másikba, hogy elérhető legyen egy új régióban.

## <a name="authoring-route-changes"></a>Útvonalmódosítások készítése
A szerzői API-útvonal a **prog** útvonalról az **API-útvonal** használatára változott.


| version | útvonal |
|--|--|
|1|/luis/v1.0/**prog**/apps|
|2|/luis/**api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>Végpontútvonal-változások
A végpont API új lekérdezési karakterlánc-paraméterek, valamint egy másik válasz. Ha a részletes jelző igaz, a rendszer a topScoringIntent mellett a rendszer a pontszámtól függetlenül minden leképezést visszaad egy szándéknak nevezett tömbben.

| version | GET útvonal |
|--|--|
|1|/luis/v1/application?ID={appId}&q={q}|
|2|/luis/v2.0/apps/{appId}?q={q}[&timezoneOffset][&verbose][&spellCheck][&átmeneti][&bing-spell-check-subscription-key][&log]|


v1 végpont sikeres válasz:
```json
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

v2 végpont sikeres válasz:
```json
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

## <a name="key-management-no-longer-in-api"></a>A kulcskezelés már nem az API-ban
Az előfizetési végpontkulcs API-k elavultak, és 410 GONE-t adnak vissza.

| version | útvonal |
|--|--|
|1|/luis/v1.0/prog/subscriptions|
|1|/luis/v1.0/prog/subscriptions/{subscriptionKey}|

Az [Azure-végpontkulcsok](luis-how-to-azure-subscription.md) az Azure Portalon jönnek létre. A kulcsot hozzárendeli egy LUIS-alkalmazáshoz a **[Közzététel](luis-how-to-azure-subscription.md)** lapon. Nem kell tudnia a tényleges kulcsértékét. A LUIS az előfizetés nevét használja a hozzárendelés hez. 

## <a name="new-versioning-route"></a>Új verziószámozási útvonal
A v2 modell most már tartalmaz egy [verziót](luis-how-to-manage-versions.md). A verziónév 10 karakter ből áll az útvonalban. Az alapértelmezett verzió a "0.1".

| version | útvonal |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities|
|2|/luis/**api**/v2.0/apps/{appId}/**verziók**/{versionId}/entitások|

## <a name="metadata-renamed"></a>Átnevezett metaadatok
A LUIS metaadatait visszaadó számos API új névvel rendelkezik.

| v1 útvonal neve | v2 útvonal neve |
|--|--|
|PersonalAssistantApps |Asszisztensek|
|alkalmazások|kulturális környezetek|
|alkalmazástartományok|Tartományok|
|alkalmazáshasználati forgatókönyvek|használati forgatókönyvek|


## <a name="sample-renamed-to-suggest"></a>"Minta" átnevezve "suggest" névre
A LUIS azt javasolja, hogy a meglévő [végpontkimondott szövegek utterances,](luis-how-to-review-endpoint-utterances.md) amely javíthatja a modell. Az előző verzióban ez a **minta**nevet kapta. Az új verzióban a név mintáról **a javaslatra változik.** Ezt nevezzük a LUIS-webhelyen található **[végpontkimondott szöveg áttekintésének.](luis-how-to-review-endpoint-utterances.md)**

| version | útvonal |
|--|--|
|1|/luis/v1.0/**prog**/apps/{appId}/entities/{entityId}/**minta**|
|1|/luis/v1.0/**prog**/apps/{appId}/intents/{intentId}/**minta**|
|2|/luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/entities/{entityId}/**suggest**|
|2|/luis/**api**/v2.0/apps/{appId}/**versions**/{versionId}/intents/{intentId}/**suggest**|


## <a name="create-app-from-prebuilt-domains"></a>Alkalmazás létrehozása előre összeállított tartományokból
Az előre összeállított tartományok előre definiált tartománymodellt [biztosítanak.](luis-how-to-use-prebuilt-domains.md) Az előre összeállított tartományok lehetővé teszik a LUIS-alkalmazás gyakori tartományokhoz való gyors fejlesztését. Ez az API lehetővé teszi, hogy egy előre összeállított tartományon alapuló új alkalmazást hozzon létre. A válasz az új appID.

|v2 útvonal|Ige|
|--|--|
|/luis/api/v2.0/apps/customprebuiltdomains  |kap, felad|
|/luis/api/v2.0/apps/customprebuiltdomains/{culture}  |get|

## <a name="importing-1x-app-into-2x"></a>1.x alkalmazás importálása 2.x-be
Az exportált 1.x alkalmazás JSON néhány olyan terület, amelyet módosítanikell importálás előtt a LUIS 2.0.The exported 1.x app's JSON has a bizonyos területek, amelyeket módosítani kell a [LUIS][LUIS] 2.0 importálása előtt. 

### <a name="prebuilt-entities"></a>Előre összeállított entitások 
Az [előre összeállított entitások](luis-prebuilt-entities.md) megváltoztak. Győződjön meg arról, hogy a V2 előre összeállított entitásokat használja. Ez magában foglalja a [datetimeV2](luis-reference-prebuilt-datetimev2.md), helyett datetime. 

### <a name="actions"></a>Műveletek
A actions tulajdonság már nem érvényes. Meg kell egy üres 

### <a name="labeled-utterances"></a>Címkézett kimondott szöveg
V1 lehetővé tette a címkézett utterances tartalmaznia szóközök elején vagy végén a szó vagy kifejezés. Eltávolította a szóközöket. 

## <a name="common-reasons-for-http-response-status-codes"></a>A HTTP-válasz állapotkódjainak gyakori okai
Lásd: [LUIS API válaszkódok](luis-reference-response-codes.md).

## <a name="next-steps"></a>További lépések

A v2 API-dokumentáció segítségével frissítse a [LUIS-végpontra](https://go.microsoft.com/fwlink/?linkid=2092356) irányuló meglévő REST-hívásokat és a szerzői API-kat. [authoring](https://go.microsoft.com/fwlink/?linkid=2092087) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions
