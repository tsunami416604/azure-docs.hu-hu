---
title: API áttelepítése útmutató a v1 v2 |} Microsoft Docs
titleSuffix: Azure
description: Ismerje meg a legújabb API az áttelepítés beállításának módját.
services: cognitive-services
author: v-geberr
manager: kamran.iqbal
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/01/2018
ms.author: v-geberr
ms.openlocfilehash: 45b6c2eda77668616a7e49ecd5ea2715af3cd3ce
ms.sourcegitcommit: 5a7f13ac706264a45538f6baeb8cf8f30c662f8f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/29/2018
ms.locfileid: "37111586"
---
# <a name="api-v2-migration-guide"></a>API v2 áttelepítési útmutató
1-es verziójával [végpont](https://aka.ms/v1-endpoint-api-docs) és [szerzői](https://aka.ms/v1-authoring-api-docs) API-k elavulttá válik. Ez az útmutató segítségével megtudhatja, hogyan 2 verziójára való áttérést [végpont](https://aka.ms/luis-endpoint-apis) és [szerzői](https://aka.ms/luis-authoring-apis) API-k. 

## <a name="new-azure-regions"></a>Új Azure-régiók
Új rendelkezik LUIS [régiók](https://aka.ms/LUIS-regions) előírt a LUIS API-k. LUIS régió csoportok kínál egy másik webhelyre. Az alkalmazás lekérdezése várt ugyanabban a régióban kell hozhatóak létre. Alkalmazások nem telepíthetők át automatikusan régiók. Az alkalmazás exportálása egy régió tartozik, majd importálása másik hozzá egy új régióban elérhető legyen.

## <a name="authoring-route-changes"></a>Útvonal-változtatásokat készítése
A szerzői műveletekhez API útvonal változása használatával a **program** útvonal használatára a **api** útvonal.


| verzió: | útvonal |
|--|--|
|1|/Luis/V1.0/**program**/apps|
|2|/Luis/**api**/v2.0/apps|


## <a name="endpoint-route-changes"></a>Végpont útvonal változások
A végpont API rendelkezik a lekérdezési karakterlánc új paramétereket, valamint a különböző választ. Ha a részletes jelző értéke true, pontszám, attól függetlenül, minden leképezések leképezések mellett a topScoringIntent nevű tömbben is megjelennek.

| verzió: | Útvonal beolvasása |
|--|--|
|1|/Luis/V1/Application? azonosítója = {appId} & q = {q}|
|2|/ luis/v2.0/apps/{appId}?q={q} [& timezoneOffset] [& részletes] [& a helyesírás-ellenőrzési] [& átmeneti] [& bing helyesírás-ellenőrzés-előfizetés-kulcs] [& napló]|


V1 végpont sikerességi válasz:
```JSON
{
  "odata.metadata":"https://dialogice.cloudapp.net/odata/$metadata#domain","value":[
    {
      "id":"bccb84ee-4bd6-4460-a340-0595b12db294","q":"turn on the camera","response":"[{\"intent\":\"OpenCamera\",\"score\":0.976928055},{\"intent\":\"None\",\"score\":0.0230718572}]"
    }
  ]
}
```

v2 végpont sikerességi válasz:
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

## <a name="key-management-no-longer-in-api"></a>Kulcskezelés már nem API
Az előfizetés végpontkulcs API-k elavultak, 410 GONE ad vissza.

| verzió: | útvonal |
|--|--|
|1|/Luis/V1.0/prog/Subscriptions|
|1|/ luis/v1.0/prog/subscriptions/{subscriptionKey}|

Azure [végpont kulcsok](luis-how-to-azure-subscription.md) akkor jönnek létre, az Azure portálon. A kulcs LUIS alkalmazásokhoz rendelje hozzá a a **[közzététel](manage-keys.md)** lap. Nem kell tudni, hogy a tényleges kulcs értékét. LUIS az előfizetés nevét használja, a hozzárendelés létrehozásához. 

## <a name="new-versioning-route"></a>Új versioning útvonal
A v2 modell most már szerepel egy [verzió](luis-how-to-manage-versions.md). Az érték 10 karakter az útvonal egy verzió neve. Az alapértelmezett verziója "0,1".

| verzió: | útvonal |
|--|--|
|1|/Luis/V1.0/**program**/apps/ {appId} / entitások|
|2|/Luis/**api**/v2.0/apps/{appId}/**verziók**/ {versionId} / entitások|

## <a name="metadata-renamed"></a>Átnevezett metaadatok
Több API-k, amelyek LUIS metaadatok visszaadását új neve lehet.

| V1 útvonal neve | v2 útvonal neve |
|--|--|
|PersonalAssistantApps |Segédek|
|applicationcultures|kulturális környezetek|
|applicationdomains|tartományok|
|applicationusagescenarios|usagescenarios|


## <a name="sample-renamed-to-suggest"></a>"Javaslat" "Sample" átnevezése
LUIS javasol meglévő utterances [végpont utterances](label-suggested-utterances.md) , előfordulhat, hogy a modell javítása érdekében. Az előző verziójában ez nevű **minta**. Az új verzióban a neve módosul a minta **javaslat**. Ez a lehetőség **[tekintse át a végpont utterances](https://docs.microsoft.com/azure/cognitive-services/LUIS/label-suggested-utterances)** a LUIS webhelyen.

| verzió: | útvonal |
|--|--|
|1|/Luis/V1.0/**program**/apps/ {appId} /entities/ {entityid beállítást} /**minta**|
|1|/Luis/V1.0/**program**/apps/ {appId} /intents/ {intentId} /**minta**|
|2|/Luis/**api**/v2.0/apps/{appId}/**verziók**/ {versionId} /entities/ {entityid beállítást} /**javaslat**|
|2|/Luis/**api**/v2.0/apps/{appId}/**verziók**/ {versionId} /intents/ {intentId} /**javaslat**|


## <a name="create-app-from-prebuilt-domains"></a>Előre elkészített tartományokból-alkalmazás létrehozása
[Előre elkészített tartományok](luis-how-to-use-prebuilt-domains.md) adjon meg egy előre meghatározott modell. Előre elkészített tartományok gyorsan fejlesztése az általános tartományok LUIS alkalmazását teszik lehetővé. Ez az API lehetővé teszi, hogy hozzon létre egy új alkalmazást egy előre elkészített tartomány alapján. A rendszer az új appID a választ.

|v2 útvonal|Művelet|
|--|--|
|/Luis/API/v2.0/Apps/customprebuiltdomains  |GET, post|
|/ luis/api/v2.0/apps/customprebuiltdomains/{culture}  |Get|

## <a name="importing-1x-app-into-2x"></a>2.x 1.x alkalmazás importálása
Az exportált 1.x alkalmazás JSON rendelkezik olyan területek, amelyek importálása előtt módosítani kell [LUIS] [ LUIS] 2.0. 

### <a name="prebuilt-entities"></a>Előre elkészített entitások 
A [előre elkészített entitások](Pre-builtEntities.md) megváltoztak. Ellenőrizze, hogy a V2 használ előre elkészített entitásokat. Ez magában foglalja, használatával [datetimeV2](pre-builtentities.md?#use-a-prebuilt-datetimev2-entity), a DateTime típusú érték helyett. 

### <a name="actions"></a>Műveletek
A műveletek tulajdonság már nem érvényes. Meg kell egy üres 

### <a name="labeled-utterances"></a>Címkézett utterances
1-es verzió engedélyezett címkézett utterances elején vagy a szó vagy kifejezés záró szóközöket tartalmazza. Eltávolítja a szóközöket. 

## <a name="common-reasons-for-http-response-status-codes"></a>Általános oka a következő HTTP-állapotkódok
Lásd: [LUIS API válaszkódot](luis-reference-response-codes.md).

## <a name="next-steps"></a>További lépések

A v2 frissíteni a meglévő REST API-dokumentáció hívások LIUS használata [végpont](https://aka.ms/luis-endpoint-apis) és [szerzői](https://aka.ms/luis-authoring-apis) API-k. 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions