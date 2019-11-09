---
title: Az Azure Video Indexer használata a Brands modell testreszabásához
titleSuffix: Azure Media Services
description: Ez a cikk bemutatja, hogyan szabhatja testre a Brands modellt az Azure Video Indexer használatával.
services: media-services
author: anikaz
manager: johndeu
ms.service: media-services
ms.subservice: video-indexer
ms.topic: article
ms.date: 05/15/2019
ms.author: anzaman
ms.openlocfilehash: 4289c592644d7570ff0dd9ce6aed0cd77f51f25e
ms.sourcegitcommit: 35715a7df8e476286e3fee954818ae1278cef1fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/08/2019
ms.locfileid: "73838337"
---
# <a name="customize-a-brands-model-with-the-video-indexer-api"></a>Brands-modell testreszabása a Video Indexer API-val

A Video Indexer támogatja a beszédfelismerés és a vizualizáció szövegének felderítését a videó-és hangtartalom indexelése és újraindexelése során. A márka észlelési funkciója a Bing márkák adatbázisa által javasolt termékek, szolgáltatások és vállalatok megemlítését azonosítja. Ha például a Microsoft a videóban vagy a hangtartalomban szerepel, vagy ha a videó vizualizációs szövegben jelenik meg, akkor Video Indexer a tartalmat a tartalomban márkaként észleli. Az egyéni márkák modell lehetővé teszi, hogy kizárjon bizonyos márkákat a rendszerből, és olyan márkákat tartalmazzon, amelyek nem feltétlenül a Bing márkák adatbázisában találhatók.

Részletes áttekintést az [Áttekintés](customize-brands-model-overview.md)című témakörben talál.

A jelen témakörben leírtak szerint a Video Indexer API-k használatával hozhat létre, használhat és szerkeszthet egyéni márkákat tartalmazó modelleket a videóban. A Video Indexer webhelyét a [Brands Model testreszabása a video Indexer webhelyről](customize-brands-model-with-api.md)című témakörben leírtak szerint is használhatja.

## <a name="create-a-brand"></a>Márka létrehozása

Ez létrehoz egy új egyéni márkát, és hozzáadja azt a megadott fiók egyéni márkák modelljéhez.

### <a name="request-url"></a>Kérés URL-címe

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Tekintse meg a szükséges paramétereket, és tesztelje a video Indexer fejlesztői portálon](https://api-portal.videoindexer.ai/docs/services/operations/operations/Create-Brand).

### <a name="request-parameters"></a>Kérelmek paramétereinek megadása

|**Name (Név)**|**Típus**|**Szükséges**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az az Azure-régió, amelybe a hívást át kell irányítani. További információ: [Azure-régiók és video Indexer](regions.md).|
|accountId|sztring|Igen|A fiók globálisan egyedi azonosítója|
|accessToken|sztring|Igen|Hozzáférési jogkivonat (a hatókör- [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)kell lennie) a híváshoz való hitelesítéshez. A hozzáférési tokenek 1 órán belül lejárnak.|

### <a name="request-body"></a>A kérés törzse

Ezen paraméterek mellett meg kell adnia egy kérelem törzse JSON-objektumot is, amely az alábbi példa formátumát követve információt nyújt az új márkáról.

```json
{
  "name": "Example",
  "enabled": true,
  "tags": ["Tag1", "Tag2"],
  "description": "This is an example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example"
}
```

Ha a True (igaz) értékre **van** állítva, a rendszer az *include (Belefoglalás* ) listában a video Indexer az észleléshez. Ha a beállítás **engedélyezve van** , a False (hamis) értékre állítja a márka értékét a *kizárási* listán, így video Indexer nem fogja felderíteni.

A **referenceUrl** értéke a márka bármely olyan hivatkozási webhelye lehet, mint például a wikipedia oldalára mutató hivatkozás.

A **címkék** érték a márka címkéit sorolja fel. Ez a Video Indexer webhely márka *Kategória* mezőjében jelenik meg. Például az "Azure" márka címkézhető vagy kategorizálható "Cloud"-ként.

### <a name="response"></a>Válasz

A válasz információt nyújt az imént létrehozott márkáról az alábbi példa formátuma alapján.

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T14:59:52.7433333",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

## <a name="delete-a-brand"></a>Márka törlése

Eltávolít egy márkát az egyéni márkák modelljéből a megadott fiókhoz. A fiók a **accountId** paraméterben van megadva. A sikeres hívás után a márka már nem *szerepel a* Brands *(márkák)* listában.

### <a name="request-url"></a>Kérés URL-címe

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Tekintse meg a szükséges paramétereket, és tesztelje a video Indexer fejlesztői portálon](https://api-portal.videoindexer.ai/docs/services/operations/operations/Delete-Brand?).

### <a name="request-parameters"></a>Kérelmek paramétereinek megadása

|**Name (Név)**|**Típus**|**Szükséges**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az az Azure-régió, amelybe a hívást át kell irányítani. További információ: [Azure-régiók és video Indexer](regions.md).|
|accountId|sztring|Igen|A fiók globálisan egyedi azonosítója|
|id|egész szám|Igen|A márka azonosítója (amelyet a rendszer a márka létrehozásakor generált)|
|accessToken|sztring|Igen|Hozzáférési jogkivonat (a hatókör- [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)kell lennie) a híváshoz való hitelesítéshez. A hozzáférési tokenek 1 órán belül lejárnak.|

### <a name="request-body"></a>A kérés törzse

Ehhez a híváshoz nem szükséges további kérelem törzse.

### <a name="response"></a>Válasz

A márka törlését követően a rendszer nem adott vissza tartalmat.

## <a name="get-a-specific-brand"></a>Adott márka beszerzése

Így a márka azonosítójának használatával megkeresheti az egyéni márkák modellben lévő márka részleteit a megadott fiókhoz.

### <a name="request-url"></a>Kérés URL-címe

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Tekintse meg a szükséges paramétereket, és tesztelje a video Indexer fejlesztői portálon](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brand?).

### <a name="request-parameters"></a>Kérelmek paramétereinek megadása

|**Name (Név)**|**Típus**|**Szükséges**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az az Azure-régió, amelybe a hívást át kell irányítani. További információ: [Azure-régiók és video Indexer](regions.md).|
|accountId|sztring|Igen|A fiók globálisan egyedi azonosítója|
|id|egész szám|Igen|A márka azonosítója (amelyet a rendszer a márka létrehozásakor generált)|
|accessToken|sztring|Igen|Hozzáférési jogkivonat (a hatókör- [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)kell lennie) a híváshoz való hitelesítéshez. A hozzáférési tokenek 1 órán belül lejárnak.|

### <a name="request-body"></a>A kérés törzse

Ehhez a híváshoz nem szükséges további kérelem törzse.

### <a name="response"></a>Válasz

A válasz az alábbi példa formátumát követve információt nyújt a keresett márkáról (a márka AZONOSÍTÓjának használatával).

```json
{
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "id": 128846,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-01-06T13:51:38.3666667",
  "lastModified": "2018-01-11T13:51:38.3666667",
  "enabled": true,
  "description": "This is an example",
  "tags": [
    "Tag1",
    "Tag2"
  ]
}
```

> [!NOTE]
> Ha az értéke **true (igaz** ) értékre van állítva, az azt jelenti, hogy a márka szerepel a *befoglalási* listán a video Indexer észleléséhez, és a hamis értékre való **engedélyezése** azt jelzi, hogy a márka szerepel a *kizárási* listán, így video Indexer nem fogja felderíteni.

## <a name="update-a-specific-brand"></a>Adott márka frissítése

Így a márka AZONOSÍTÓjának használatával megkeresheti az egyéni márkák modellben lévő márka részleteit a megadott fiókhoz.

### <a name="request-url"></a>Kérés URL-címe

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands/{id}?accessToken={accessToken}
```

[Tekintse meg a szükséges paramétereket, és tesztelje a video Indexer fejlesztői portálon](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brand?).

### <a name="request-parameters"></a>Kérelmek paramétereinek megadása

|**Name (Név)**|**Típus**|**Szükséges**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az az Azure-régió, amelybe a hívást át kell irányítani. További információ: [Azure-régiók és video Indexer](regions.md).|
|accountId|sztring|Igen|A fiók globálisan egyedi azonosítója|
|id|egész szám|Igen|A márka azonosítója (amelyet a rendszer a márka létrehozásakor generált)|
|accessToken|sztring|Igen|Hozzáférési jogkivonat (a hatókör- [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)kell lennie) a híváshoz való hitelesítéshez. A hozzáférési tokenek 1 órán belül lejárnak.|

### <a name="request-body"></a>A kérés törzse

Ezen paraméterek mellett meg kell adnia egy kérelem törzse JSON-objektumot, amely naprakész információkat biztosít a frissíteni kívánt márkáról az alábbi példa formátuma alapján.

```json
{
  "name": "Example",
  "enabled": false,
  "tags": ["Tag1", "NewTag2"],
  "description": "This is an update example",
  "referenceUrl": "https://en.wikipedia.org/wiki/Example",
  "lastModifierUserName": "SampleUserName",
  "created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-28T15:52:22.3413983",
}
```

> [!NOTE]
> Ebben a példában a márka **létrehozása** szakasz példa kérés törzsében létrehozott márka új címkével és új leírással frissül. Az **engedélyezett** érték hamisra módosult, hogy a *kizárási* listára kerüljön.

### <a name="response"></a>Válasz

A válasz az alábbi példa formátumát követve frissített információt nyújt a frissített adatokról.

```json
{
  "referenceUrl": null,
  "id": 97974,
  "name": "Example",
  "accountId": "SampleAccountId",
  "lastModifierUserName": "SampleUserName",
  "Created": "2018-04-25T14:59:52.7433333",
  "lastModified": "2018-04-25T15:37:50.67",
  "enabled": false,
  "description": "This is an update example",
  "tags": [
    "Tag1",
    "NewTag2"
  ]
}
```

## <a name="get-all-of-the-brands"></a>Az összes márka beszerzése

Ez visszaadja az egyéni márkák modellben lévő összes márkát a megadott fiókhoz, függetlenül attól, hogy a márka a *befoglalási* vagy *kizárási* márkák listájában szerepel-e.

### <a name="request-url"></a>Kérés URL-címe

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Tekintse meg a szükséges paramétereket, és tesztelje a video Indexer fejlesztői portálon](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands?).

### <a name="request-parameters"></a>Kérelmek paramétereinek megadása

|**Name (Név)**|**Típus**|**Szükséges**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az az Azure-régió, amelybe a hívást át kell irányítani. További információ: [Azure-régiók és video Indexer](regions.md).|
|accountId|sztring|Igen|A fiók globálisan egyedi azonosítója|
|accessToken|sztring|Igen|Hozzáférési jogkivonat (a hatókör- [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)kell lennie) a híváshoz való hitelesítéshez. A hozzáférési tokenek 1 órán belül lejárnak.|

### <a name="request-body"></a>A kérés törzse

Ehhez a híváshoz nem szükséges további kérelem törzse.

### <a name="response"></a>Válasz

A válasz tartalmazza a fiókban lévő összes márka listáját, és az alábbi példa formátumát követve mindegyik részletet.

```json
[
    {
        "ReferenceUrl": null,
        "id": 97974,
        "name": "Example",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-25T14:59:52.7433333",
        "LastModified": "2018-04-25T14:59:52.7433333",
        "enabled": true,
        "description": "This is an example",
        "tags": ["Tag1", "Tag2"]
    },
    {
        "ReferenceUrl": null,
        "id": 97975,
        "name": "Example2",
        "accountId": "AccountId",
        "lastModifierUserName": "UserName",
        "Created": "2018-04-26T14:59:52.7433333",
        "LastModified": "2018-04-26T14:59:52.7433333",
        "enabled": false,
        "description": "This is another example",
        "tags": ["Tag1", "Tag2"]
    },
]
```

> [!NOTE]
> A *példaként* megadott márka a *befoglalások* listájában szerepel a video Indexer észleléséhez, és a *pelda2* nevű márka a *kizárási* listán szerepel, így video Indexer nem fogja felderíteni.

## <a name="get-brands-model-settings"></a>A Brands modell beállításainak beolvasása

Ez a beállítás a márkák modell beállításait adja vissza a megadott fiókban. A márkák modell beállításai azt jelzik, hogy engedélyezve van-e a Bing Brands adatbázisának észlelése. Ha a Bing-márkák nincsenek engedélyezve, a Video Indexer csak a megadott fiók egyéni márkák modelljéből származó márkákat fogja felderíteni.

### <a name="request-url"></a>Kérés URL-címe

```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/Brands?accessToken={accessToken}
```

[Tekintse meg a szükséges paramétereket, és tesztelje a video Indexer fejlesztői portálon](https://api-portal.videoindexer.ai/docs/services/operations/operations/Get-Brands).

### <a name="request-parameters"></a>Kérelmek paramétereinek megadása

|**Name (Név)**|**Típus**|**Szükséges**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az az Azure-régió, amelybe a hívást át kell irányítani. További információ: [Azure-régiók és video Indexer](regions.md).|
|accountId|sztring|Igen|A fiók globálisan egyedi azonosítója|
|accessToken|sztring|Igen|Hozzáférési jogkivonat (a hatókör- [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)kell lennie) a híváshoz való hitelesítéshez. A hozzáférési tokenek 1 órán belül lejárnak.|

### <a name="request-body"></a>A kérés törzse

Ehhez a híváshoz nem szükséges további kérelem törzse.

### <a name="response"></a>Válasz

A válasz azt jelzi, hogy a Bing márkák engedélyezve vannak-e az alábbi példa formátuma szerint.

```json
{
  "state": true,
  "useBuiltIn": true
}
```

> [!NOTE]
> a **useBuiltIn** értéke TRUE (igaz) értékre van állítva, hogy a Bing-márkák engedélyezve vannak. Ha a *useBuiltin* hamis, a Bing-márkák le vannak tiltva. Az **állapot** értéke figyelmen kívül hagyható, mert elavult.

## <a name="update-brands-model-settings"></a>A Brands modell beállításainak frissítése

Ezzel frissíti a márkák modell beállításait a megadott fiókban. A márkák modell beállításai azt jelzik, hogy engedélyezve van-e a Bing Brands adatbázisának észlelése. Ha a Bing-márkák nincsenek engedélyezve, a Video Indexer csak a megadott fiók egyéni márkák modelljéből származó márkákat fogja felderíteni.

### <a name="request-url"></a>Kérelem URL-címe:
```
https://api.videoindexer.ai/{location}/Accounts/{accountId}/Customization/BrandsModelSettings?accessToken={accessToken}
```

[Tekintse meg a szükséges paramétereket, és tesztelje a video Indexer fejlesztői portálon](https://api-portal.videoindexer.ai/docs/services/operations/operations/Update-Brands-Model-Settings?).

### <a name="request-parameters"></a>Kérelmek paramétereinek megadása

|**Name (Név)**|**Típus**|**Szükséges**|**Leírás**|
|---|---|---|---|
|location|sztring|Igen|Az az Azure-régió, amelybe a hívást át kell irányítani. További információ: [Azure-régiók és video Indexer](regions.md).|
|accountId|sztring|Igen|A fiók globálisan egyedi azonosítója|
|accessToken|sztring|Igen|Hozzáférési jogkivonat (a hatókör- [fiók hozzáférési jogkivonatának](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token?)kell lennie) a híváshoz való hitelesítéshez. A hozzáférési tokenek 1 órán belül lejárnak.|

### <a name="request-body"></a>A kérés törzse

Ezen paraméterek mellett meg kell adnia egy kérelem törzse JSON-objektumot is, amely az alábbi példa formátumát követve információt nyújt az új márkáról.

```json
{
    "useBuiltIn":true
}
```

> [!NOTE]
> a **useBuiltIn** értéke TRUE (igaz) értékre van állítva, hogy a Bing-márkák engedélyezve vannak. Ha a *useBuiltin* hamis, a Bing-márkák le vannak tiltva.

### <a name="response"></a>Válasz

A Brands modell beállításának frissítése nem történt meg a visszaadott tartalomban.

## <a name="next-steps"></a>További lépések

[A Brands modell testreszabása webhely használatával](customize-brands-model-with-website.md)
