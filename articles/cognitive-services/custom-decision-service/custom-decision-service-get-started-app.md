---
title: API hívása egy alkalmazásból – Custom Decision Service
titlesuffix: Azure Cognitive Services
description: A Custom Decision Service API-k meghívása egy okostelefon-alkalmazásból.
services: cognitive-services
author: slivkins
manager: nitinme
ms.service: cognitive-services
ms.subservice: custom-decision-service
ms.topic: conceptual
ms.date: 05/10/2018
ms.author: slivkins
ROBOTS: NOINDEX
ms.openlocfilehash: 08fbc1716d402c83bc2c33be82cba143c1737a55
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68707256"
---
# <a name="call-api-from-an-app"></a>API hívása egy alkalmazásból

Hívásokat indíthat az Azure Custom Decision Service API-jai számára egy okostelefon-alkalmazásból. Ez a cikk bemutatja, hogyan kezdheti el az első lépéseket néhány alapvető lehetőséggel.

Ügyeljen arra, hogy először [regisztrálja az alkalmazást](custom-decision-service-get-started-register.md).

Két API-hívás áll rendelkezésre a smartphone-alkalmazásból a Custom Decision Service: a rangsorolási API meghívása a tartalom rangsorolt listájának beszerzéséhez, valamint a jutalom API meghívása a jutalom jelentéséhez. Itt láthatók a fürtökön [](https://en.wikipedia.org/wiki/CURL)megjelenő példák.

További információ: Reference [API](custom-decision-service-api-reference.md).

Kezdje a rangsor API meghívásával. Hozza létre a `<request.json>`fájlt, amely a műveleti készlet azonosítója. Ez az azonosító a portálon megadott megfelelő RSS-vagy Atom-hírcsatorna neve:

```json
{"decisions":
     [{ "actionSets":[{"id":{"id":"<actionSetId>"}}] }]}
```

Számos Művelettípus megadható a következő módon:

```json
{"decisions":
    [{ "actionSets":[{"id":{"id":"<actionSetId1>"}},
                     {"id":{"id":"<actionSetId2>"}}] }]}
```

Ezt a JSON-fájlt a rendszer a rangsorolási kérelem részeként küldi el:

```shell
curl -d @<request.json> -X POST https://ds.microsoft.com/api/v2/<appId>/rank --header "Content-Type: application/json"
```

`<appId>` Itt a portálon regisztrált alkalmazás neve. Az alkalmazásban megjelenített tartalmi elemek rendezett készletét kell kapnia. A minta visszaadása A következőképpen néz ki:

```json
[{ "ranking":[{"id":"actionId3"}, {"id":"actionId1"}, {"id":"actionId2"}],
   "eventId":"<opaque event string>",
   "appId":"<your app id>",
   "rewardAction":"actionId3",
   "actionSets":[{"id":"<actionSetId1>","lastRefresh":"2017-04-29T22:34:25.3401438Z"},
                 {"id":"<actionSetId2>","lastRefresh":"2017-04-30T22:34:25.3401438Z"}]}]
```

A Return első részében szerepel a megrendelt műveletek listája, amelyet a műveleti azonosítóik határoznak meg. Egy cikk esetében a műveleti azonosító egy URL-cím. A teljes kérelemhez egyedi `<eventId>`, a rendszer által létrehozott fájl is tartozik.

Később megadhatja, hogy az esemény `<actionId3>`első tartalmi elemére kattintva megfigyelt-e. Ezt `<eventId>` követően a jutalmazási API-n keresztül Custom Decision Service jutalmat, amely egy másik kérelem, például:

```shell
curl -v https://ds.microsoft.com/api/v2/<appId>/reward/<eventId> -X POST
```

Végül meg kell adnia a műveleti set API-t, amely visszaadja a Custom Decision Service által megfontolandó cikkek (műveletek) listáját. Az API-t RSS-hírcsatornaként implementálhatja, ahogy az itt látható:

```xml
<rss version="2.0">
<channel>
   <item>
      <title><![CDATA[title (possibly with url) ]]></title>
      <link>url</link>
      <pubDate>Thu, 27 Apr 2017 16:30:52 GMT</pubDate>
    </item>
   <item>
       ....
   </item>
</channel>
</rss>
```

Itt minden legfelső szintű `<item>` elem egy cikket ír le. A `<link>` kötelező, és Custom Decision Service alapján műveleti azonosítóként használja. Ha `<date>` több mint 15 cikket használ, válassza a (szabványos RSS-formátumban). A 15 legutóbb használt cikk. A `<title>` nem kötelező, és szöveggel kapcsolatos szolgáltatások létrehozására szolgál a cikkhez.

## <a name="next-steps"></a>További lépések

* Részletes példa az [oktatóanyagra](custom-decision-service-tutorial-news.md) .
* A megadott funkciókkal kapcsolatos további információkért tekintse meg a hivatkozási [API](custom-decision-service-api-reference.md) -t.
