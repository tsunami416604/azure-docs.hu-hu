---
title: Szerzői és futásidejű kulcsok használata a LUIS-szal
titleSuffix: Azure Cognitive Services
description: A LUIS két kulcsot használ, a szerzői kulcsot a modell létrehozásához, és a futásidejű kulcsot az előrejelzési végpont lekérdezéséhez a felhasználói utterances.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 9d213c8fa03ad2ca5e5fd7e620e52aa502749be2
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220962"
---
# <a name="authoring-and-runtime-keys"></a>Tartalomkészítési és futtatókörnyezeti kulcsok

A Language Understanding (LUIS) két szolgáltatás- és API-készlettel rendelkezik: 

* Szerzői (korábbi nevén _programozott)_
* Előrejelzési futásidő

Számos kulcstípus létezik, attól függően, hogy milyen szolgáltatással szeretne dolgozni, és hogyan szeretne vele dolgozni.

## <a name="non-azure-resources-for-luis"></a>Nem Azure-beli erőforrások a LUIS-hoz

### <a name="starter-key"></a>Kezdő gomb

Amikor először kezdi el használni a LUIS-t, létrejön egy **kezdőkulcs.** Ez az erőforrás a következőket biztosítja:

* ingyenes szerzői szolgáltatás kérések a LUIS portálon vagy API-kon keresztül (beleértve az SDK-kat is)
* ingyenes 1000 előrejelzési végpontkérés havonta böngészőn, API-n vagy SDK-n keresztül

## <a name="azure-resources-for-luis"></a>Azure-erőforrások a LUIS-hoz

<a name="programmatic-key" ></a>
<a name="endpoint-key"></a>
<a name="authoring-key"></a>

A LUIS háromféle Azure-erőforrást engedélyez: 
 
|Kulcs|Cél|Kognitív szolgáltatás`kind`|Kognitív szolgáltatás`type`|
|--|--|--|--|
|[Szerzői kulcs](#programmatic-key)|Az alkalmazás adatainak elérése és kezelése szerzői, betanítási, közzétételi és tesztelési adatokkal. Hozzon létre egy LUIS-szerzői kulcsot, ha programozott módon kívánja létrehozni a LUIS-alkalmazásokat.<br><br>A `LUIS.Authoring` kulcs célja, hogy lehetővé tegye:<br>* programozott módon kezeli kondikai ismeretek alkalmazások és modellek, beleértve a képzés, és a közzététel<br> * ellenőrzési engedélyeket a szerzői erőforrás hozzárendelésével az emberek [a közreműködői szerepkör](#contributions-from-other-authors).|`LUIS.Authoring`|`Cognitive Services`|
|[Előrejelzési kulcs](#prediction-endpoint-runtime-key)| Lekérdezés-előrejelzési végpont-kérelmek. Hozzon létre egy LUIS-előrejelzési kulcsot, mielőtt az ügyfélalkalmazás az indító erőforrás által biztosított 1000 kérelemen túli előrejelzéseket kér. |`LUIS`|`Cognitive Services`|
|[Cognitive Service többszolgáltatásos erőforráskulcs](../cognitive-services-apis-create-account-cli.md?tabs=windows#create-a-cognitive-services-resource)|Lekérdezés előrejelzési végpont kérelmek megosztott LUIS és más támogatott Cognitive Services.|`CognitiveServices`|`Cognitive Services`|

Amikor az erőforrás-létrehozási folyamat befejeződött, [rendelje hozzá a kulcsot az](luis-how-to-azure-subscription.md) alkalmazáshoz a LUIS-portálon.

Fontos, hogy a LUIS-alkalmazások [olyan régiókban,](luis-reference-regions.md#publishing-regions) ahol közzé szeretné tenni és le szeretné kérdezni.

> [!CAUTION]
> A kényelem érdekében a minták közül sok a [Starter kulcsot](#starter-key) használja, mert néhány ingyenes előrejelzési végponthívást biztosít a [kvótájában.](luis-boundaries.md#key-limits)  


### <a name="query-prediction-resources"></a>Lekérdezés-előrejelzési erőforrások

* A futásidejű kulcs az összes LUIS-alkalmazáshoz vagy adott LUIS-alkalmazásokhoz használható. 
* Ne használja a futásidejű kulcsot a LUIS-alkalmazások szerkesztéséhez. 

A LUIS futásidejű végpont két lekérdezési stílust fogad el, mindkettő az előrejelzési végpont futásidejű kulcsát használja, de különböző helyeken.

A futásidejű eléréséhez használt végpont olyan altartományt használ, amely az erőforrás `{region}` régiójában egyedi, és amely az alábbi táblázatban szerepel. 

## <a name="assignment-of-the-key"></a>A kulcs hozzárendelése

A futásidejű kulcsot a [LUIS-portálon](https://www.luis.ai) vagy a megfelelő API-kon keresztül [rendelheti hozzá.](luis-how-to-azure-subscription.md) 

## <a name="key-limits"></a>Kulcskorlátok

Régiónként és előfizetésenként legfeljebb 10 szerzői kulcsot hozhat létre. 

Lásd: [Kulcskorlátok](luis-boundaries.md#key-limits) és [Azure-régiók.](luis-reference-regions.md) 

A közzétételi régiók eltérnek a szerzői régióktól. Győződjön meg arról, hogy hozzon létre egy alkalmazást a szerzői régióban, amely megfelel az ügyfélalkalmazás által kívánt közzétételi régióban található.

## <a name="key-limit-errors"></a>Kulcskorlát-hibák
Ha túllépi a másodpercenkénti tranzakciós (TPS) kvótát, HTTP 429-es hibaüzenetet kap. Ha túllépi a havi tranzakciós (TPS) kvótát, HTTP 403-as hibaüzenetjelenik meg. 

## <a name="contributions-from-other-authors"></a>Más szerzők hozzájárulásai

**Az [erőforrások áttelepítése](luis-migration-authoring.md) alkalmazások szerzői:** _közreműködők_ kezelik az Azure Portalon a szerzői erőforrás, a **hozzáférés-vezérlés (IAM)** lap használatával. [Ismerje meg, hogyan adhat hozzá egy felhasználót](luis-how-to-collaborate.md)a közreműködő e-mail-címével és a _közreműködői szerepkörrel._ 

**A még nem áttelepített alkalmazások esetében:** az összes _közreműködő_ a LUIS-portálon a **-> közreműködők kezelése** lapon kezelhető.

## <a name="move-transfer-or-change-ownership"></a>Tulajdonosáthelyezés, áthelyezés vagy tulajdonosváltás

Az alkalmazást az Azure-erőforrások határozzák meg, amelyet a tulajdonos előfizetése határoz meg. 

Áthelyezheti a LUIS-alkalmazást. Használja a következő dokumentációs erőforrásokat az Azure Portalon vagy az Azure CLI-ben:

* [Alkalmazás áthelyezése a LUIS szerzői erőforrásai között](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/apps-move-app-to-another-luis-authoring-azure-resource)
* [Erőforrás áthelyezése új erőforráscsoportba vagy előfizetésbe](../../azure-resource-manager/management/move-resource-group-and-subscription.md)
* [Erőforrás áthelyezése ugyanazon előfizetésen belül vagy előfizetések között](../../azure-resource-manager/management/move-limitations/app-service-move-limitations.md)

Az előfizetés [tulajdonjogának](../../cost-management-billing/manage/billing-subscription-transfer.md) átruházása: 

**Az áttelepített felhasználók számára - [az erőforrás-áttelepítési](luis-migration-authoring.md) alkalmazások**: Az erőforrás `contributor`tulajdonosaként hozzáadhat egy .

**Azoknak a felhasználóknak, akik még nem telepedtek át:** Exportálja az alkalmazást JSON-fájlként. Egy másik LUIS-felhasználó importálhatja az alkalmazást, így az alkalmazás tulajdonosa lesz. Az új alkalmazás egy másik alkalmazásazonosítóval fog rendelkezni.  

## <a name="access-for-private-and-public-apps"></a>Hozzáférés magán- és nyilvános alkalmazásokhoz

**Privát** alkalmazás esetén a futásidejű hozzáférés a tulajdonosok és a közreműködők számára érhető el. Egy **nyilvános** alkalmazás, futásidejű hozzáférés mindenki számára elérhető, amely saját Azure [Cognitive Service](../cognitive-services-apis-create-account.md) vagy [LUIS](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal) futásidejű erőforrással rendelkezik, és rendelkezik a nyilvános alkalmazás azonosítóját. 

Jelenleg nincs katalógus a nyilvános alkalmazások.

### <a name="authoring-access"></a>Szerzői hozzáférés
Az alkalmazáshoz való [LUIS](luis-reference-regions.md#luis-website) hozzáférés a LUIS-portálról vagy a [szerzői API-k](https://go.microsoft.com/fwlink/?linkid=2092087) az Azure szerzői erőforrás által szabályozott. 

A tulajdonos és az összes közreműködő hozzáférhet az alkalmazás hoz. 

|A szerzői hozzáférés tartalmazza|Megjegyzések|
|--|--|
|Végpontkulcsok hozzáadása és eltávolítása||
|Verzió exportálása||
|Végpontnaplók exportálása||
|Verzió importálása||
|Az alkalmazás nyilvánossá tévődése|Ha egy alkalmazás nyilvános, bárki, aki rendelkezik szerzői vagy végponti kulccsal, lekérdezheti az alkalmazást.|
|Modell módosítása|
|Közzététel|
|Végpontkimondott szöveg áttekintése [az aktív tanuláshoz](luis-how-to-review-endpoint-utterances.md)|
|Betanítás|

<a name="prediction-endpoint-runtime-key"></a>

### <a name="prediction-endpoint-runtime-access"></a>Előrejelzés végpont futásidejű hozzáférés

Az előrejelzési végpont lekérdezéséhez való hozzáférést a Kezelés szakasz **Alkalmazásinformációk** lapjának egyik beállítása **szabályozza.** 

|[Privát végpont](#runtime-security-for-private-apps)|[Nyilvános végpont](#runtime-security-for-public-apps)|
|:--|:--|
|A tulajdonos és a közreműködők számára elérhető|Elérhető a tulajdonos, a közreműködők és mindenki más számára, aki ismeri az alkalmazásazonosítót|

Szabályozhatja, hogy ki láthatja a LUIS futásidejű kulcs áthívja azt a kiszolgálók között környezetben. Ha a LUIS egy robot, a kapcsolat a robot és a LUIS már biztonságos. Ha közvetlenül hívja a LUIS-végpontot, hozzon létre egy szabályozott hozzáféréssel (például [AAD)](https://azure.microsoft.com/services/active-directory/)rendelkező kiszolgálóoldali API-t (például egy [Azure-függvényt).](https://azure.microsoft.com/services/functions/) Amikor a kiszolgálóoldali API-t hívják és hitelesítik, és az engedélyezést ellenőrzik, adja át a hívást a LUIS-nak. Bár ez a stratégia nem akadályozza meg a nem-a-középső támadásokat, elhomályosítja a kulcsot és a végpont URL-címét a felhasználóktól, lehetővé teszi a hozzáférés nyomon követését, és lehetővé teszi a végpontválasz naplózásának hozzáadását (például [az Application Insights).](https://azure.microsoft.com/services/application-insights/)

#### <a name="runtime-security-for-private-apps"></a>Futásidejű biztonság magánjellegű alkalmazásokhoz

A privát alkalmazások futásideje csak a következő képpen érhető el:

|Kulcs és felhasználó|Magyarázat|
|--|--|
|Tulajdonos szerzői kulcsa| Akár 1000 végpontlekérés|
|Közreműködő/közreműködő szerzői kulcsai| Akár 1000 végpontlekérés|
|Bármely kulcs, amelyet egy szerző vagy közreműködő rendel a LUIS-hoz|Kulcshasználati szint alapján|

#### <a name="runtime-security-for-public-apps"></a>Futásidejű biztonság nyilvános alkalmazásokhoz

Ha egy alkalmazás nyilvánosként van konfigurálva, _bármely_ érvényes LUIS-szerzői kulcs vagy LUIS-végpontkulcs lekérdezheti az alkalmazást, feltéve, hogy a kulcs nem használta a teljes végpontkvótát.

Az a felhasználó, aki nem tulajdonos vagy közreműködő, csak akkor férhet hozzá egy nyilvános alkalmazás futási idejéhez, ha megvan az alkalmazásazonosítója. A LUIS nem rendelkezik nyilvános _piaccal_ vagy más módon egy nyilvános alkalmazás keresésére.  

Egy nyilvános alkalmazás minden régióban közzé van téve, így a régióalapú LUIS erőforráskulccsal rendelkező felhasználó hozzáférhet az alkalmazáshoz abban a régióban, amely az erőforráskulcshoz van társítva.

## <a name="transfer-of-ownership"></a>Tulajdonjog átruházása

A LUIS nem rendelkezik az erőforrás tulajdonjogának átruházásának koncepciójával. 

## <a name="securing-the-endpoint"></a>A végpont rögzítése 

Szabályozhatja, hogy ki láthatja a LUIS előrejelzési futásidejű végpontkulcsát, ha kiszolgálók ról kiszolgálóra környezetben hívja meg. Ha a LUIS egy robot, a kapcsolat a robot és a LUIS már biztonságos. Ha közvetlenül hívja a LUIS-végpontot, hozzon létre egy szabályozott hozzáféréssel (például [AAD)](https://azure.microsoft.com/services/active-directory/)rendelkező kiszolgálóoldali API-t (például egy [Azure-függvényt).](https://azure.microsoft.com/services/functions/) A kiszolgálóoldali API hívása és a hitelesítés és az engedélyezés ellenőrzése esetén adja át a hívást a LUIS-nak. Bár ez a stratégia nem akadályozza meg a nem-a-középső támadásokat, elhomályosítja a végpontot a felhasználóktól, lehetővé teszi a hozzáférés nyomon követését, és lehetővé teszi a végpontválasz naplózásának hozzáadását (például [az Application Insights).](https://azure.microsoft.com/services/application-insights/)  

## <a name="next-steps"></a>További lépések

* A [verziószámozási](luis-concept-version.md) fogalmak ismertetése. 
* További információ [a kulcsok létrehozásáról.](luis-how-to-azure-subscription.md)
