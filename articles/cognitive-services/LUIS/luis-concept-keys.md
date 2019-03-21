---
title: Előfizetői azonosítók
titleSuffix: Language Understanding - Azure Cognitive Services
description: A LUIS használ két kulcsot, az ingyenes szerzői a modell létrehozásához és a forgalmi díjas végpont kulcs lekérdezése az előrejelzési végpont felhasználói kimondott szöveg.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 01/18/2019
ms.author: diberry
ms.openlocfilehash: 1f5aab607c5046df0dee4db5caf36b0b7de53c4d
ms.sourcegitcommit: 2d0fb4f3fc8086d61e2d8e506d5c2b930ba525a7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/18/2019
ms.locfileid: "57998741"
---
# <a name="authoring-and-query-prediction-endpoint-keys-in-luis"></a>Szerzői műveletek és a lekérdezés előrejelzési végpont kulcsok a LUIS
A LUIS használ két kulcsot: [szerzői](#programmatic-key) és [végpont](#endpoint-key). Az Authoring Tool kulcs az Ön számára automatikusan létrejön a LUIS-fiók létrehozásakor. Amikor készen áll a LUIS-alkalmazás közzététele, meg kell [a végpont kulcs létrehozása](luis-how-to-azure-subscription.md), [rendelje hozzá](luis-how-to-azure-subscription.md) , a LUIS-alkalmazás és [használhatja a végpont lekérdezés](#use-endpoint-key-in-query). 

|Kulcs|Cél|
|--|--|
|[Kulcs létrehozási](#programmatic-key)|Szerzői műveletek, a kezelése közreműködők, a közzététel verziószámozás|
|[Végponti kulcs](#endpoint-key)| Lekérdezés|

Fontos, hogy a LUIS-alkalmazások készítése [régiók](luis-reference-regions.md#publishing-regions) ahol is szeretné közzétenni, és lekérdezéseket.

<a name="programmatic-key" ></a>
## <a name="authoring-key"></a>Kulcs létrehozási

Egy szerzői kulcs, más néven egy alapszintű kulcs, automatikusan létrejön, amikor létrehoz egy LUIS-fiókot, és ingyenes. Az egyes szerzői valamennyi LUIS alkalmazás rendelkezik egy szerzői kulccsal [régió](luis-reference-regions.md). A LUIS-alkalmazás létrehozásához, vagy végpont tesztlekérdezések az Authoring Tool kulcsot biztosítunk. 

Az Authoring Tool kulcs megkereséséhez jelentkezzen be [LUIS](luis-reference-regions.md#luis-website) , majd kattintson a jobb felső navigációs sávon, nyissa meg a fiók nevét a **fiókbeállításokat**.

![Kulcs létrehozási](./media/luis-concept-keys/programatic-key.png)

Ha szeretné, hogy **éles végpontot lekérdezések**, hozzon létre az Azure [LUIS előfizetés](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). 

> [!CAUTION]
> Az egyszerűség kedvéért a minták számos szerzői műveletek kulcs használata óta biztosít néhány végpont hívások annak [kvóta](luis-boundaries.md#key-limits).  

## <a name="endpoint-key"></a>Végponti kulcs
Amikor kell **éles végpontot lekérdezések**, hozzon létre egy Azure-erőforrást, majd rendelje hozzá a LUIS-alkalmazás. 

[!INCLUDE [Azure resource creation for Language Understanding and Cognitive Service resources](../../../includes/cognitive-services-luis-azure-resource-instructions.md)]

Ha elkészült a Azure-erőforrások létrehozását, [rendelje hozzá a kulcs](luis-how-to-azure-subscription.md) az alkalmazáshoz. 

    * A végpont kulcs lehetővé teszi egy végpont a találatok alapján a használati terv a kulcs létrehozásakor megadott kvótát. Lásd: [a Cognitive Services díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) a díjszabásról.

    * A végpont kulcs használható a LUIS-alkalmazások, vagy konkrét LUIS-alkalmazások esetén. 

    * A végpont kulcs ne használja a LUIS-alkalmazások készítéséhez. 

## <a name="use-endpoint-key-in-query"></a>A lekérdezés a végpont kulcs használata
A LUIS-végpont lekérdezés két stílusok fogad el, a végpont kulcs, de mindkettő használja a különböző helyeken:

|művelet|Példa URL-cím és a kulcs helyét|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`<br><br>a lekérdezési karakterlánc értéke `subscription-key`<br><br>A végpont lekérdezés értékének módosításához a `subscription-key` kulcsból a szerzői műveletekhez részben (alapszintű), az új végpont kulcs a LUIS végponti kulcs kvóta arány használatához. Ha a kulcs létrehozása és hozzárendelése a kulcs, de ne módosítsa a lekérdezés végpontértéknek `subscription-key`, nem használja a végpont-kvótát.|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`<br><br> a fejléc értéke `Ocp-Apim-Subscription-Key`<br><br>A végpont lekérdezés értékének módosításához a `Ocp-Apim-Subscription-Key` kulcsból a szerzői műveletekhez részben (alapszintű), az új végpont kulcs a LUIS végponti kulcs kvóta arány használatához. Ha a kulcs létrehozása és hozzárendelése a kulcs, de ne módosítsa a lekérdezés végpontértéknek `Ocp-Apim-Subscription-Key`, nem használja a végpont-kvótát.|

Az Alkalmazásazonosító, az előző URL-címeket, a használt `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, a használt nyilvános IoT-alkalmazás a [interaktív bemutató](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). 

## <a name="api-usage-of-ocp-apim-subscription-key"></a>API-használat az Ocp-Apim-Subscription-Key
Az intelligens HANGFELISMERÉSI API-k használata a fejléc `Ocp-Apim-Subscription-Key`. A fejléc neve nem változik alapján mely kulcs- és API-k készlete használ. A fejléc beállítása a szerzői műveletekhez részben kulcsot az Authoring Tool API-k. A végpontot használja, ha a végpont kulcs beállítása a fejlécet. 

Az API-k készítése a végpont kulcs nem adhatók át. Ha így tesz, kap egy 401-es hiba – a hozzáférés érvénytelen végponti kulcs miatt megtagadva. 

## <a name="key-limits"></a>Kulcs korlátok
Lásd: [korlátok kulcs](luis-boundaries.md#key-limits) és [Azure-régiók](luis-reference-regions.md). A szerzői műveletekhez részben kulcs a szabad és használt készítéséhez. A LUIS-végpont kulcs rendelkezik egy ingyenes szinttel, de kell Ön által létrehozott és hozzárendelt a LUIS-alkalmazás a a **közzététel** lapot. A szerzői műveletek, de csak a végpont lekérdezések nem használható.

Közzétételi régiója nem ugyanaz a létrehozási régiók. Ellenőrizze, hogy az Authoring Tool régió régióhoz tartozó, a közzétételi szeretné az alkalmazást hoz létre.

## <a name="key-limit-errors"></a>Korlát hibák
Ha túllépi a második kvótája per HTTP 429-es hibaüzenetet kap. Ha túllépi a havi kvóta, egy HTTP 403-as hibaüzenetet kap. Javítsa ki a hibákat a LUIS lekérésével [végpont](#endpoint-key) kulcs [hozzárendelése](luis-how-to-azure-subscription.md) a kulcsot az alkalmazás a **közzététel** lapján a [LUIS](luis-reference-regions.md#luis-website) webhely.

## <a name="assignment-of-the-endpoint-key"></a>A végpont kulcs hozzárendelése

Is [hozzárendelése](luis-how-to-azure-subscription.md) a végponti kulcsot a [LUIS portál](https://www.luis.ai) vagy a megfelelő API-kon keresztül. 


## <a name="next-steps"></a>További lépések

* Ismerje meg, [fogalmak](luis-how-to-azure-subscription.md) létrehozási és -végpont kulcsokkal kapcsolatos.
