---
title: Szerzői és futtatókörnyezeti kulcsok – LUIS
titleSuffix: Azure Cognitive Services
description: A LUIS két kulcsot használ, a szerzői kulcs segítségével létrehozza a modellt és a futásidejű kulcsot az előrejelzési végpont felhasználói hosszúságú kimondott szöveg való lekérdezéséhez.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 09/27/2019
ms.author: diberry
ms.openlocfilehash: 70e58077fa40ce685324cd24b447886ec3411034
ms.sourcegitcommit: 6fe40d080bd1561286093b488609590ba355c261
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/01/2019
ms.locfileid: "71703182"
---
# <a name="authoring-and-runtime-keys"></a>Tartalomkészítési és futtatókörnyezeti kulcsok


>[!NOTE]
>A folytatás előtt [telepítse át](luis-migration-authoring.md) az Azure authoring resourcet nem használó alkalmazásokat.

A LUIS két típusú Azure-erőforrást használ, minden típushoz kulcs tartozik: 
 
* [Létrehozás a](#programmatic-key) szándékok, az entitások és a címkék hosszúságú kimondott szöveg, betanításához és közzétételéhez. Ha készen áll a LUIS-alkalmazás közzétételére, szüksége lesz az alkalmazáshoz rendelt [futtatókörnyezet előrejelzési végponti kulcsára](luis-how-to-azure-subscription.md) .
* [A futtatókörnyezet előrejelzési végpontjának kulcsa](#prediction-endpoint-runtime-key). Az ügyfélalkalmazások, például a csevegési robot esetében ehhez a kulcshoz hozzá kell férniük a futásidejű **lekérdezés-előrejelzési végponthoz** . 

|Kulcs|Cél|Kognitív szolgáltatás @no__t – 0|Kognitív szolgáltatás @no__t – 0|
|--|--|--|--|
|[Kulcs létrehozási](#programmatic-key)|Szerzői műveletek, képzés, közzététel és tesztelés.|`LUIS.Authoring`|`Cognitive Services`|
|[Előrejelzési végpont futtatókörnyezetének kulcsa](#prediction-endpoint-runtime-key)| Lekérdezés-előrejelzési végpont futtatókörnyezete felhasználói Kimondás alapján a szándékok és az entitások meghatározásához.|`LUIS`|`Cognitive Services`|

A LUIS egy [alapszintű kulcsot](luis-how-to-azure-subscription.md#starter-key) is biztosít, amely egy 1000 tranzakció havonta előrejelzési végponti kvótát tartalmaz. 

Habár nem szükséges egyszerre létrehozni mindkét kulcsot, sokkal egyszerűbb, ha igen.

Fontos, hogy LUIS-alkalmazásokat hozzon létre azokon a [régiókban](luis-reference-regions.md#publishing-regions) , amelyeken közzé és lekérdezéseket szeretne közzétenni.

<a name="programmatic-key" ></a>

## <a name="authoring-key"></a>Kulcs létrehozási

A szerzői kulcsok automatikusan létrejönnek, amikor létrehoz egy LUIS-fiókot, és ingyenes. Ha a LUIS-vel kezdődik, minden egyes authoring [region](luis-reference-regions.md)esetében egy kezdő kulcs van az összes Luis-alkalmazásban. A szerzői kulcs célja, hogy hitelesítést nyújtson a LUIS-alkalmazás kezeléséhez vagy az előrejelzési végpontok lekérdezésének teszteléséhez. 

Szerzői kulcsok létrehozása a Azure Portal lehetővé teszi, hogy a szerzői erőforráshoz való jogosultságot a [közreműködő szerepkörhöz](#contributions-from-other-authors)rendeljen. A közreműködők hozzáadásához engedélyre van szüksége az Azure-előfizetési szinten. 

A szerzői kulcs megtalálásához jelentkezzen be a [Luis](luis-reference-regions.md#luis-website) -ba, és kattintson a jobb felső navigációs sávon a fiók nevére a **Fiókbeállítások**megnyitásához.

![Kulcs létrehozási](./media/luis-concept-keys/authoring-key.png)

Ha **futásidejű lekérdezéseket**szeretne készíteni, hozza létre az Azure [Luis-erőforrást](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/). 

> [!CAUTION]
> A legtöbb példa az [alapszintű kulcsot](#starter-prediction-endpoint-runtime-key) használja, mivel néhány ingyenes előrejelzési végpontot biztosít a [kvótájában](luis-boundaries.md#key-limits).  

<a name="endpoint-key"></a>

## <a name="prediction-endpoint-runtime-key"></a>Előrejelzési végpont futtatókörnyezetének kulcsa 

Ha **futásidejű végponti lekérdezésekre**van szüksége, hozzon létre egy Language UNDERSTANDING (Luis) erőforrást, majd rendelje hozzá a Luis alkalmazáshoz. 

[!INCLUDE [Azure runtime resource creation for Language Understanding and Cognitive Service resources](../../../includes/cognitive-services-luis-azure-resource-instructions.md)]

Ha az erőforrás-létrehozási folyamat elkészült, [rendelje hozzá a kulcsot](luis-how-to-azure-subscription.md) az alkalmazáshoz. 

* A futásidejű (lekérdezés-előrejelzési végpont) kulcs lehetővé teszi a végpontok látogatottságának kvótáját a futásidejű kulcs létrehozásakor megadott használati terv alapján. Lásd: [a Cognitive Services díjszabása](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/?v=17.23h) a díjszabásról.

* A futásidejű kulcs az összes LUIS-alkalmazáshoz, illetve az adott LUIS-alkalmazásokhoz is használható. 
* Ne használja a futásidejű kulcsot a LUIS-alkalmazások létrehozásához. 

### <a name="starter-prediction-endpoint-runtime-key"></a>Kezdő előrejelzési végpont futtatókörnyezetének kulcsa

Az **alapszintű** előrejelzési végpont kulcsa ingyenesen elérhető, és 1000 előrejelzési végponti lekérdezéseket tartalmaz. A lekérdezések használata után létre kell hoznia a saját előrejelzési végpontjának erőforrását a Language Understandinghoz.  

Ez egy speciális erőforrás, amelyet Ön hoz létre. Ez nem jelenik meg az Azure-erőforrások listájában, mert az egy ideiglenes kezdő kulcs. 

<a name="use-endpoint-key-in-query"></a>

### <a name="use-runtime-key-in-query"></a>Futásidejű kulcs használata a lekérdezésben
A LUIS Runtime végpont két stílust fogad el, mindkettő az előrejelzési végpont futtatókörnyezetének kulcsát használja, de különböző helyeken.

A futtatókörnyezet eléréséhez használt végpont olyan altartományt használ, amely egyedi az erőforrás régiójában, és az alábbi táblázatban `{region}` jelöli. 


#### <a name="v2-prediction-endpointtabv2"></a>[V2 előrejelzési végpont](#tab/V2)

|művelet|Példa URL-cím és a kulcs helyét|
|--|--|
|[GET](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee78)|`https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2?runtime-key=your-endpoint-key-here&verbose=true&timezoneOffset=0&q=turn%20on%20the%20lights`|
|[POST](https://westus.dev.cognitive.microsoft.com/docs/services/5819c76f40a6350ce09de1ac/operations/5819c77140a63516d81aee79)| `https://{region}.api.cognitive.microsoft.com/luis/v2.0/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2`|

#### <a name="v3-prediction-endpointtabv3"></a>[V3 előrejelzési végpont](#tab/V3)

|művelet|Példa URL-cím és a kulcs helyét|
|--|--|
|[GET](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5cb0a91e54c9db63d589f433)|`https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict?runtime-key=your-endpoint-key-here&query=turn%20on%20the%20lights`|
|[POST](https://westcentralus.dev.cognitive.microsoft.com/docs/services/luis-endpoint-api-v3-0-preview/operations/5cb0a5830f741b27cd03a061)| `https://{region}.api.cognitive.microsoft.com/luis/v3.0-preview/apps/df67dcdb-c37d-46af-88e1-8b97951ca1c2/slots/production/predict`| 

További információ a [v3 előrejelzési végpontról](luis-migration-api-v3.md).

* * * 

**BEOLVASÁS**: A végpont lekérdezés értékének módosításához a `runtime-key` kulcsból a szerzői műveletekhez részben (alapszintű), az új végpont kulcs a LUIS végponti kulcs kvóta arány használatához. Ha a kulcs létrehozása és hozzárendelése a kulcs, de ne módosítsa a lekérdezés végpontértéknek `runtime-key`, nem használja a végpont-kvótát.

**POST**: @No__t-0 fejléc értékének módosítása<br>Ha létrehozza a futásidejű kulcsot, és hozzárendeli a futásidejű kulcsot, de nem módosítja `Ocp-Apim-Subscription-Key` végpont-lekérdezési értékét, akkor nem a futásidejű kulcsot használja.

Az Alkalmazásazonosító, az előző URL-címeket, a használt `df67dcdb-c37d-46af-88e1-8b97951ca1c2`, a használt nyilvános IoT-alkalmazás a [interaktív bemutató](https://azure.microsoft.com/services/cognitive-services/language-understanding-intelligent-service/). 

## <a name="assignment-of-the-runtime-key"></a>A futásidejű kulcs hozzárendelése

A futásidejű kulcsot a [Luis-portálon](https://www.luis.ai) vagy a megfelelő API-kon keresztül [rendelheti hozzá](luis-how-to-azure-subscription.md) . 

## <a name="key-limits"></a>Kulcs korlátok

Régiónként legfeljebb 10 szerzői kulcsot hozhat létre egy előfizetéshez. 

Lásd a [legfontosabb korlátokat](luis-boundaries.md#key-limits) és az [Azure-régiókat](luis-reference-regions.md). 

Közzétételi régiója nem ugyanaz a létrehozási régiók. Győződjön meg arról, hogy létrehoz egy alkalmazást a szerzői régióban, amely ahhoz a közzétételi régióhoz tartozik, amelyet az ügyfélalkalmazás el szeretne helyezni.

## <a name="key-limit-errors"></a>Korlát hibák
Ha túllépi a tranzakció-másodpercenkénti (TPS-) kvótát, HTTP 429-es hibaüzenetet kap. Ha túllépi a havi tranzakciós (TPS) kvótát, HTTP 403-es hibaüzenetet kap. 

## <a name="contributions-from-other-authors"></a>Más szerzők hozzájárulásai



Az közreműködők hozzájárulásainak kezelése az alkalmazás aktuális állapotától függ.

**Az [áttelepített erőforrás-alkalmazások létrehozásához](luis-migration-authoring.md)** : a _közreműködők_ kezelése a Azure Portal a szerzői erőforráshoz a **hozzáférés-vezérlés (iam)** lapon. Megtudhatja, [hogyan adhat hozzá felhasználót](luis-how-to-collaborate.md)a közreműködő e-mail-címével és a _közreműködő_ szerepkörrel. 

**Még nem telepített alkalmazások esetén**: az összes _közreműködőt_ a Luis portálon kezelheti a **Manage-> közreműködők** lapról.

### <a name="contributor-roles-vs-entity-roles"></a>Közreműködő szerepkörök vs entitási szerepkörök

Az [entitás szerepkörei](luis-concept-roles.md) a Luis alkalmazás adatmodelljére vonatkoznak. A közreműködő/közreműködő szerepkörök a szerzői hozzáférés szintjeire vonatkoznak. 

## <a name="moving-or-changing-ownership"></a>Tulajdonos áthelyezése vagy módosítása

Az alkalmazást az Azure-erőforrásai határozzák meg, amelyet a tulajdonos előfizetése határoz meg. 

Elvégezheti a LUIS-alkalmazás áthelyezését. Használja az alábbi dokumentációs erőforrásokat a Azure Portal vagy az Azure CLI-ben:

* [Alkalmazás áthelyezése a LUIS authoring-erőforrások között](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Erőforrás áthelyezése új erőforráscsoporthoz vagy előfizetésbe](../../azure-resource-manager/resource-group-move-resources.md)
* [Erőforrás áthelyezése ugyanazon az előfizetésen belül vagy előfizetések között](../../azure-resource-manager/move-limitations/app-service-move-limitations.md)
* Az előfizetés [tulajdonjogának átruházása](../../billing/billing-subscription-transfer.md) 

## <a name="access-for-private-and-public-apps"></a>Hozzáférés magán-és nyilvános alkalmazásokhoz

**Privát** alkalmazások esetén a futtatókörnyezet-hozzáférés a tulajdonosokhoz és a közreműködők számára érhető el. **Nyilvános** alkalmazások esetén a futtatókörnyezet-hozzáférés mindenki számára elérhető, aki saját Azure [kognitív szolgáltatással](../cognitive-services-apis-create-account.md) vagy [Luis](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) futásidejű erőforrással rendelkezik, és rendelkezik a nyilvános alkalmazás azonosítójával. 

Jelenleg nem létezik katalógus a nyilvános alkalmazásokról.

### <a name="authoring-access"></a>Szerzői hozzáférés
Az alkalmazáshoz való hozzáférést a [Luis](luis-reference-regions.md#luis-website) portálról vagy a [szerzői API-kat](https://go.microsoft.com/fwlink/?linkid=2092087) az Azure authoring Resource vezérli. 

A tulajdonos és az összes közreműködő hozzáfér az alkalmazás létrehozásához. 

|Authoring hozzáférés tartalmaz|Megjegyzések|
|--|--|
|Adja hozzá, vagy távolítsa el a végpont kulcsok||
|Verzió exportálása||
|Végpont naplók exportálása||
|Verzió importálása||
|Az alkalmazás nyilvános tétele|Ha egy alkalmazás nyilvános, bárki egy létrehozási vagy a végpont kulcs lekérdezheti az alkalmazás.|
|Modell módosítása|
|Közzététel|
|Tekintse át a végpont utterances [aktív tanulás](luis-how-to-review-endpoint-utterances.md)|
|Betanítás|

### <a name="prediction-endpoint-runtime-access"></a>A végpont futásidejű hozzáférésének előrejelzése

Az előrejelzési végpont lekérdezéséhez a **kezelés** szakasz **alkalmazás adatai** lapján található beállítás vezérli. 

![Nyilvános csoport alkalmazás](./media/luis-concept-security/set-application-as-public.png)

|[Privát végpontjaikat](#runtime-security-for-private-apps)|[Nyilvános végponthoz](#runtime-security-for-public-apps)|
|:--|:--|
|Elérhető a tulajdonos és a közreműködők számára|Elérhető a tulajdonos, a közreműködők és bárki másnak, aki ismeri az alkalmazás AZONOSÍTÓját|

Megadhatja, hogy ki látja el a LUIS Runtime-kulcsot úgy, hogy a kiszolgálót kiszolgáló-kiszolgáló környezetben hívja meg. Ha egy robot a LUIS használ, a robot és a LUIS közötti kapcsolat már biztonságos. Ha közvetlenül, hogy a LUIS-végpontot hív, érdemes létrehoznia egy kiszolgálóoldali API-t (például az Azure-beli [függvény](https://azure.microsoft.com/services/functions/)) szabályozott hozzáféréssel rendelkező (például [AAD](https://azure.microsoft.com/services/active-directory/)). A kiszolgálóoldali API meghívása és hitelesítése, valamint az engedélyezés ellenőrzése után adja át a hívást a LUIS-nek. Habár ez a stratégia nem gátolja meg az emberen belüli támadásokat, az eltorzítja a felhasználók kulcs-és végpont-URL-címét, lehetővé teszi a hozzáférés nyomon követését, és lehetővé teszi a végponti válaszok naplózásának hozzáadását (például [Application Insights](https://azure.microsoft.com/services/application-insights/)).

#### <a name="runtime-security-for-private-apps"></a>Futásidejű biztonság privát alkalmazásokhoz

A privát alkalmazások futtatókörnyezete csak a következőkhöz érhető el:

|Kulcs és a felhasználó|Magyarázat|
|--|--|
|A tulajdonos szerzői kulcs| Legfeljebb 1000 végpont találatok|
|Közreműködő/közreműködő szerzői kulcsok| Legfeljebb 1000 végpont találatok|
|Egy szerző vagy közreműködő vagy közreműködő által a LUIShoz rendelt kulcs|Kulcshasználat réteg alapján|

#### <a name="runtime-security-for-public-apps"></a>Futtatókörnyezeti biztonság nyilvános alkalmazásokhoz

Ha egy alkalmazás van konfigurálva, nyilvános, _bármely_ szerzői vagy LUIS végponti kulcs érvényes LUIS lekérdezheti az alkalmazás mindaddig, amíg a kulcsot nem használta a teljes endpoint kvótát.

Olyan felhasználó, aki nem tulajdonos vagy közreműködő, csak akkor férhet hozzá egy nyilvános alkalmazás futtatókörnyezetéhez, ha az alkalmazás azonosítója van megadva. A LUIS nincs nyilvános _piaci_ vagy egyéb módon, egy nyilvános alkalmazás kereséséhez.  

A nyilvános alkalmazás közzé van téve az összes régióban, hogy egy felhasználó egy LUIS-erőforrás régió-alapú kulccsal hozzáférhet az alkalmazás bármelyik régióban az erőforrás-kulcs társítva.

## <a name="transfer-of-ownership"></a>A tulajdonjog átruházása

**[Erőforrás-áttelepített alkalmazások létrehozásához](luis-migration-authoring.md)** : Az erőforrás tulajdonosaként hozzáadhat egy `contributor` értéket.

**Még nem áttelepített alkalmazások esetén**: Az alkalmazás exportálása JSON-fájlként. Egy másik LUIS-felhasználó is importálhatja az alkalmazást, így az alkalmazás tulajdonosa válik. Az új alkalmazás egy másik alkalmazás-AZONOSÍTÓval fog rendelkezni.  

## <a name="securing-the-endpoint"></a>A végpont védelme 

Megadhatja, hogy ki láthatja a LUIS előrejelzési futtatókörnyezet végpontjának kulcsát úgy, hogy a kiszolgálót kiszolgáló-kiszolgáló környezetben hívja meg. Ha egy robot a LUIS használ, a robot és a LUIS közötti kapcsolat már biztonságos. Ha közvetlenül, hogy a LUIS-végpontot hív, érdemes létrehoznia egy kiszolgálóoldali API-t (például az Azure-beli [függvény](https://azure.microsoft.com/services/functions/)) szabályozott hozzáféréssel rendelkező (például [AAD](https://azure.microsoft.com/services/active-directory/)). Ha a kiszolgálói oldalon az API és a hitelesítési és engedélyezési ellenőrzése, továbbítja a LUIS-be. Amíg ez a stratégia nem man-in-the-middle támadások megelőzése, access, nyomon követheti a felhasználók számára, a végpont obfuscates, és lehetővé teszi, hogy a végpont válasz naplózás hozzáadása (például [Application Insights](https://azure.microsoft.com/services/application-insights/)).  

## <a name="next-steps"></a>További lépések

* Megismerheti [versioning](luis-concept-version.md) fogalmakat. 
* Útmutató [a kulcsok létrehozásához](luis-how-to-azure-subscription.md).
