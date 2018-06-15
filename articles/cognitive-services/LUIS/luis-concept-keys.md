---
title: A LUIS kulcsok - Azure megértése |} Microsoft Docs
description: Az alkalmazás és a endpoing lekérdezési nyelv ismertetése (LUIS) kulcsok használatára.
services: cognitive-services
author: v-geberr
manager: kaiqb
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 03/23/2018
ms.author: v-geberr
ms.openlocfilehash: bf131877080f46781e74991ef627922384cc4c08
ms.sourcegitcommit: 266fe4c2216c0420e415d733cd3abbf94994533d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/01/2018
ms.locfileid: "35349779"
---
# <a name="keys-in-luis"></a>A LUIS kulcsok
LUIS két kulcsokat használ: [szerzői](#programmatic-key) és [végpont](#endpoint-key). A szerzői műveletekhez kulcs, automatikusan létrejön a LUIS fiók létrehozásakor. Amikor készen áll a LUIS alkalmazás közzététele, meg kell [a végpont-kulcs létrehozása](luis-how-to-azure-subscription.md#create-luis-endpoint-key), [rendelje hozzá](Manage-keys.md#assign-endpoint-key) a LUIS alkalmazáshoz, és [a végpont lekérdezés együtt használja azt](#use-endpoint-key-in-query). 

|Kulcs|Cél|
|--|--|
|[Szerzői kulcs](#programmatic-key)|Szerzői, kezelése közreműködők, a közzététel versioning|
|[Végpontkulcs](#endpoint-key)| Lekérdezése|

Fontos, hogy LUIS alkalmazásokat hozhatnak létre [régiók](luis-reference-regions.md#publishing-regions) hol szeretné közzétenni, és lekérdezi a.

<a name="programmatic-key" ></a>
## <a name="authoring-key"></a>Szerzői kulcs

Egy szerzői kulcs, más néven egy alapszintű, automatikusan létrejön, amikor LUIS fiók létrehozásához, és ingyenes. Minden egyes tartalomkészítéshez LUIS alkalmazások között van egy szerzői kulcsa [régió](luis-reference-regions.md). A szerzői műveletekhez kulcsra azért van ahhoz, hogy az LUIS alkalmazás vagy a végpont lekérdezések teszteléséhez. 

A szerzői műveletekhez kulcs található, jelentkezzen be [LUIS] [ LUIS] , majd kattintson a jobb felső navigációs sáv megnyitása a fiók nevére a **Fiókbeállítások**.

![szerzői kulcs](./media/luis-concept-keys/programatic-key.png)

Ha azt szeretné, hogy **éles végpont lekérdezések**, hozzon létre egy Azure [LUIS előfizetés](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). 

> [!CAUTION]
> Kényelmi célokat szolgál, a minták számos billentyűvel a szerzői műveletek, mert néhány végpont-hívások biztosít a [kvóta](luis-boundaries.md#key-limits).  

## <a name="endpoint-key"></a>Végpontkulcs
 Ha van szükség **éles végpont lekérdezések**, hozzon létre egy [LUIS kulcs](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) az Azure portálon. Ne feledje, a kulcs létrehozásához használt név, amikor az alkalmazás hozzá a kulcsot...

A LUIS előfizetési folyamat befejezésekor [vegye fel a kulcs](Manage-keys.md#assign-endpoint-key) az alkalmazás a **közzététel** lap. 

A végpont kulcs lehetővé teszi, hogy a használati terv a kulcs létrehozásakor megadott alapján végpont találatok tartozó kvóta. Lásd: [kognitív árképzési](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) díjszabási információkat.

A végpont kulcs a LUIS alkalmazásokhoz vagy adott LUIS alkalmazásokhoz használható. 

Ne használja a végpontkulcs LUIS apps-szerzésre vonatkozó információ. 

## <a name="use-endpoint-key-in-query"></a>A lekérdezés végpont kulcs használata
A LUIS végpont lekérdezés két stílusok fogad el, a végpont kulcs, de mindkettő használja a különböző helyeken:

|Művelet|Példa URL-cím és a kulcs helyét|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/98998dcf-66d2-468e-840a-7c7c57549b5a?subscription-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn a fény a<br><br>a lekérdezési karakterláncokra vonatkozó értéket `subscription-key`<br><br>A végpont lekérdezés értékének módosításához a `subscription-key` kulcsból a szerzői műveletekhez (alapszintű), az új végpont kulcshoz LUIS végpont kulcs kvóta sebessége használatához. Ha akkor hozza létre a kulcsot, és rendelje hozzá a kulcs, de nem módosítja a végpont lekérdezés előfizetés-kulcs a következő ", nem használja a végpont kulcs kvótát.|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| https://westus.api.cognitive.microsoft.com/luis/v2.0/apps/98998dcf-66d2-468e-840a-7c7c57549b5a<br><br> a fejléc értéke `Ocp-Apim-Subscription-Key`<br><br>A végpont lekérdezés értékének módosításához a `Ocp-Apim-Subscription-Key` kulcsból a szerzői műveletekhez (alapszintű), az új végpont kulcshoz LUIS végpont kulcs kvóta sebessége használatához. Ha a kulcs létrehozása és hozzárendelése a kulcsot, de ne változtassa meg a végpont lekérdezés értéke `Ocp-Apim-Subscription-Key`, nem használja a végpont kulcs kvótát.|

## <a name="api-usage-of-ocp-apim-subscription-key"></a>API-használati Ocp-Apim-előfizetés-kulcs
A LUIS API-k használata a fejléc `Ocp-Apim-Subscription-Key`. A fejléc neve nem változik alapján mely kulcs és az API-készlet használ. A fejléc be a szerzői műveletekhez kulcsot szerzői API-k. Ha használ a végponthoz, a végpont key értékre állítani a fejlécet. 

A végpont kulcs API-szerzésre vonatkozó információ nem adható át. Ha így tesz, a 401-es hiba – a hozzáférés megtagadva a miatt érvénytelen előfizetés kulcs kap. 

## <a name="key-limits"></a>Korlátozza a kulcs
Lásd: [korlátok kulcs](luis-boundaries.md#key-limits) és [Azure-régiók](luis-reference-regions.md). A szerzői műveletekhez fontos, szabad és használt szerzésre vonatkozó információ. A LUIS előfizetés kulcs van egy ingyenes szint, de kell hozták létre, és a az LUIS alkalmazáshoz társított a **közzététel** lap. A szerzői, de csak a végpont-lekérdezések nem használható.

Közzétételi régiók régiók szerzői eltérnek. Ellenőrizze, hogy a szerzői műveletek terület megfelelő kívánt közzétételi régió alkalmazást hoz létre.

## <a name="key-limit-errors"></a>Korlát hibák
Ha az túllépi a második kvóta / HTTP 429 hibaüzenetet kap. Ha az túllépi a havi kvótát, egy HTTP 403-as hibaüzenetet kap. Javítsa ki a hibákat olvasson be egy LUIS [végpont](#endpoint-key) kulcs, [hozzárendelése](Manage-keys.md#assign-endpoint-key) a kulcs az alkalmazás a **közzététel** oldalán a [LUIS] [ LUIS] webhelyet.

## <a name="next-steps"></a>További lépések

* Ismerje meg, [fogalmak](Manage-Keys.md#assign-endpoint-key) jelentéskészítő és -végpont kulcsok.

[LUIS]:luis-reference-regions.md#luis-website
