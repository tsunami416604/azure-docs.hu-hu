---
title: A LUIS kulcsok ismertetése
titleSuffix: Azure Cognitive Services
description: LUIS két kulcsot, a szerzői és a végpontot használja. Az Authoring Tool kulcs az Ön számára automatikusan létrejön a LUIS-fiók létrehozásakor. Amikor elkészült, a LUIS-alkalmazás közzététele, kell a végpont kulcs létrehozásához rendelje hozzá a LUIS-alkalmazás, és használhatja a végpont lekérdezés.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: a4bd20f9f8a6a8317e161b1d84e948391cd08140
ms.sourcegitcommit: 4ecc62198f299fc215c49e38bca81f7eb62cdef3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "47034926"
---
# <a name="keys-in-luis"></a>A LUIS kulcsok
A LUIS használ két kulcsot: [szerzői](#programmatic-key) és [végpont](#endpoint-key). Az Authoring Tool kulcs az Ön számára automatikusan létrejön a LUIS-fiók létrehozásakor. Amikor készen áll a LUIS-alkalmazás közzététele, meg kell [a végpont kulcs létrehozása](luis-how-to-azure-subscription.md#create-luis-endpoint-key), [rendelje hozzá](luis-how-to-manage-keys.md#assign-endpoint-key) , a LUIS-alkalmazás és [használhatja a végpont lekérdezés](#use-endpoint-key-in-query). 

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
 Amikor kell **éles végpontot lekérdezések**, hozzon létre egy [LUIS kulcs](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) az Azure Portalon. Ne feledje, a kulcs létrehozásához használt név, szüksége lesz rá a kulcsot az alkalmazáshoz való hozzáadásakor.

A LUIS-előfizetés folyamat befejezésekor [rendelje hozzá a kulcs](luis-how-to-manage-keys.md#assign-endpoint-key) az alkalmazáshoz. 

A végpont kulcs lehetővé teszi egy végpont a találatok alapján a használati terv a kulcs létrehozásakor megadott kvótát. Lásd: [a Cognitive Services díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) a díjszabásról.

A végpont kulcs használható a LUIS-alkalmazások, vagy konkrét LUIS-alkalmazások esetén. 

A végpont kulcs ne használja a LUIS-alkalmazások készítéséhez. 

## <a name="use-endpoint-key-in-query"></a>A lekérdezés a végpont kulcs használata
A LUIS-végpont lekérdezés két stílusok fogad el, a végpont kulcs, de mindkettő használja a különböző helyeken:

|művelet|Példa URL-cím és a kulcs helyét|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?subscription-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`<br><br>a lekérdezési karakterlánc értéke `subscription-key`<br><br>A végpont lekérdezés értékének módosításához a `subscription-key` kulcsból a szerzői műveletekhez részben (alapszintű), az új végpont kulcs a LUIS végponti kulcs kvóta arány használatához. Ha, hozza létre a kulcsot, és rendelje hozzá a kulcsot, de nem módosítja az előfizetői végpont lekérdezés értéke ", nem használja a végpont-kvótát.|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`<br><br> a fejléc értéke `Ocp-Apim-Subscription-Key`<br><br>A végpont lekérdezés értékének módosításához a `Ocp-Apim-Subscription-Key` kulcsból a szerzői műveletekhez részben (alapszintű), az új végpont kulcs a LUIS végponti kulcs kvóta arány használatához. Ha a kulcs létrehozása és hozzárendelése a kulcs, de ne módosítsa a lekérdezés végpontértéknek `Ocp-Apim-Subscription-Key`, nem használja a végpont-kvótát.|

Az Alkalmazásazonosító, az előző URL-címeket, a használt `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, a használt nyilvános IoT-alkalmazás a [interaktív bemutató](https://azure.microsoft.com/en-us/services/cognitive-services/language-understanding-intelligent-service/). 

## <a name="api-usage-of-ocp-apim-subscription-key"></a>API-használat az Ocp-Apim-Subscription-Key
Az intelligens HANGFELISMERÉSI API-k használata a fejléc `Ocp-Apim-Subscription-Key`. A fejléc neve nem változik alapján mely kulcs- és API-k készlete használ. A fejléc beállítása a szerzői műveletekhez részben kulcsot az Authoring Tool API-k. A végpontot használja, ha a végpont kulcs beállítása a fejlécet. 

Az API-k készítése a végpont kulcs nem adhatók át. Ha így tesz, kap egy 401-es hiba – a hozzáférés érvénytelen végponti kulcs miatt megtagadva. 

## <a name="key-limits"></a>Kulcs korlátok
Lásd: [korlátok kulcs](luis-boundaries.md#key-limits) és [Azure-régiók](luis-reference-regions.md). A szerzői műveletekhez részben kulcs a szabad és használt készítéséhez. A LUIS-végpont kulcs rendelkezik egy ingyenes szinttel, de kell Ön által létrehozott és hozzárendelt a LUIS-alkalmazás a a **közzététel** lapot. A szerzői műveletek, de csak a végpont lekérdezések nem használható.

Közzétételi régiója nem ugyanaz a létrehozási régiók. Ellenőrizze, hogy az Authoring Tool régió régióhoz tartozó, a közzétételi szeretné az alkalmazást hoz létre.

## <a name="key-limit-errors"></a>Korlát hibák
Ha túllépi a második kvótája per HTTP 429-es hibaüzenetet kap. Ha túllépi a havi kvóta, egy HTTP 403-as hibaüzenetet kap. Javítsa ki a hibákat a LUIS lekérésével [végpont](#endpoint-key) kulcs [hozzárendelése](luis-how-to-manage-keys.md#assign-endpoint-key) a kulcsot az alkalmazás a **közzététel** lapján a [LUIS](luis-reference-regions.md#luis-website) webhely.

## <a name="automating-assignment-of-the-endpoint-key"></a>A végpont kulcs hozzárendelés automatizálása

Annak érdekében, hogy a végpont kulcs rendel a LUIS-alkalmazások, a megfelelő szerzői és a közzététel a LUIS webhelyet kell használnia [régiók](luis-reference-regions.md). Nincs **nincs** automatikus módszer ezzel, függetlenül attól, mechanizmust, például egy Azure resource manager, parancsfájl Azure CLI-vel, programozott SDK-t, vagy az API-khoz.

## <a name="next-steps"></a>További lépések

* Ismerje meg, [fogalmak](luis-how-to-manage-keys.md#assign-endpoint-key) létrehozási és -végpont kulcsokkal kapcsolatos.