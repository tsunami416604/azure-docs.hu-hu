---
title: V2 Azure videó indexelő API v1 át |} Microsoft Docs
description: Ez a témakör azt ismerteti, hogyan telepíthetők át az Azure videó indexelő API v1 v2.
services: cognitive services
documentationcenter: ''
author: juliako
manager: erikre
ms.service: cognitive-services
ms.topic: article
ms.date: 05/13/2018
ms.author: juliako
ms.openlocfilehash: 00f5a0d7c9a37c24866cd5e1259800d5e431ccc3
ms.sourcegitcommit: 6f6d073930203ec977f5c283358a19a2f39872af
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/11/2018
ms.locfileid: "35350103"
---
# <a name="migrate-from-the-video-indexer-api-v1-to-v2"></a>A videó indexelő API v1 v2 áttelepítése

> [!Note]
> A videó indexelő V1-es API-k mára elavult, és eltávolítja a 2018 augusztus 1-jétől. El kell kezdenie a videó indexelő v2 API-k használatával zavarokat elkerülése érdekében.
>
> Fejlesztéséhez videó indexelő v2 API-khoz, tekintse meg a útmutatását [Itt](https://api-portal.videoindexer.ai/). 

Ez a cikk ismerteti a v2 bevezetett módosítások.  

## <a name="api-changes"></a>API-változások

### <a name="authorization-and-operations"></a>Engedélyezési és műveletek

A v2 verziójú videó indexelő módosította a hitelesítési és engedélyezési modell API. API-k két csoportjára van: 

* Engedélyezés 
* Műveletek

A **engedélyezési** API hívása jogkivonatainak beszerzésére szolgál a **műveletek** API. A **műveletek** API minden a videó indexelő API-kat tartalmaz, például a feltöltési videó, Get insights és egyéb műveletekhez.

Egyszer, [előfizetés](video-indexer-get-started.md) számára a **engedélyezési** API-t fogja tudni hozzáférési tokenek beszerzése érdekében úgy, hogy az Előfizetés kulcs (ahogy az 1-es verzió.)

## <a name="getting-and-using-the-access-token-for-operations-apis"></a>Első és a hozzáférési jogkivonat segítségével műveleteihez API-k

Ha előhívja a **műveletek** API-k, az Előfizetés kulcs már nem használható. Ehelyett a kapott hozzáférési jogkivonatok fogja továbbítani a **engedélyezési** API. 

Minden kérésnek tartalmaznia kell egy érvényes tokent, az API-hívás a hozzáférési szint megfelelő. A felhasználó, például a fiókok első műveletek megkövetelni például, egy felhasználói jogkivonat. A fiók műveleteket, például a lista összes videók szinten kötelező, egy fiók hozzáférési jogkivonatot. Videók, például az ismételt indexelése videó, a rendszer működéséhez szükségesek, vagy a fiók hozzáférési tokent, vagy a videó jogkivonatot.

Egyszerűbbé, használhatja a **engedélyezési** API > **GetAccounts** a fiókok nem a felhasználó jogkivonatának beszerzéséhez először. Is kérhet, lekérni a fiókok érvényes jogkivonatokkal, így lehetővé teszi egy további, a fiók tokent irányuló hívás kihagyása.

A különböző hozzáférési jogkivonatok kapcsolatos további információkért lásd: [használata Azure videó indexelő API](video-indexer-use-apis.md).

### <a name="locations"></a>Helyek

Minden egyes hívásakor az API-t tartalmaznia kell a videó indexelő fiók helyét. A hely nélkül, vagy egy rossz helyen lévő API-hívások meghiúsulnak.

A következő táblázat ismerteti az értékek érvényesek. A **Param érték** az API-t használ az értéket, ha át.

|**Name (Név)**|**Param érték**|**Leírás**|
|---|---|---|
|Próbaverzió|Próbaverzió|Próba fiókok használatos. Például: https://api.videoindexer.ai/trial/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|USA nyugati régiója|westus2|Az Azure nyugati Velünk 2 régió használatos.  Például: https://api.videoindexer.ai/westus2/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|
|Észak-Európa |northeurope|Használja az Észak-Európa Azure-régió. Például: https://api.videoindexer.ai/northeurope/Accounts/{accountId}/Videos/{videoId}/Index?language=English. |
|Kelet-Ázsia|eastasia|Az Azure Kelet-Ázsia régió használatos. Például: https://api.videoindexer.ai/eastasia/Accounts/{accountId}/Videos/{videoId}/Index?language=English.|

### <a name="data-model"></a>Adatmodell

Videó indexelő most már képes biztosítani a sok tisztább insights egyszerűsített adatmodellt. A v2 API által létrehozott kimenet kapcsolatos további információkért lásd: [vizsgálja meg a videó indexelő kimenetét a v2 API által előállított](video-indexer-output-json-v2.md).

### <a name="swagger"></a>Swagger

A videó indexelő API-definíciók ennek megfelelően frissültek-e, és keresztül, töltse le a [API portal](https://api-portal.videoindexer.ai/docs/services/authorization/operations/Get-Account-Access-Token).


### <a name="v1-vs-v2-examples"></a>A V1 és V2 példák

Az új V2 API-kat tartalmaz, amely 3 fő paraméterekkel:

1. [Hely] - fent leírt módon. Próbaverzió, westus2, northeurope vagy eastasia.
2. [YOUR_ACCOUNT_ID] - fiókját A Guid azonosítóját. Lekéri a fiókokhoz (lásd lejjebb) beolvasásakor.
3. [YOUR_VIDEO_ID] - a videó (pl. "d4fa369abc") azonosítóját. Videó feltöltésekor vagy videók keresésekor adott vissza.

#### <a name="uploading-a-video-in-v1"></a>A V1 videó feltöltése:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns?name=some_name&description=some_description&privacy=private&videoUrl=http://URL_TO_YOUR_VIDEO
```

#### <a name="uploading-a-video-in-v2"></a>A V2 videó feltöltése:

1. Hozzáférés-token beszerzése a feltöltési kérelem:

  Minden fiókot és a hozzáférési jogkivonatok vagy beolvasása:

    ```
    https://api.videoindexer.ai/auth/[LOCATION]/Accounts?generateAccessTokens=true&allowEdit=true
    ```

  Vagy a meghatározott fiók access token beszerzése:
  
  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/AccessToken?allowEdit=true
  ```
2. Töltse fel a videót:

  ```
  POST https://api.videoindexer.ai/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos?name=MySample&description=MySampleDescription&videoUrl=[URL_ENCODED_VIDEO_URL]&accessToken=eyJ0eXAiOiJ...
  ```

#### <a name="getting-insights-in-v1"></a>A V1 nyerek betekintést az adatokba:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]
```
  
#### <a name="getting-insights-in-v2"></a>A V2 nyerek betekintést az adatokba:

1. A fiók hozzáférési jogkivonatot használja, vagy egy videó szintű hozzáférési jogkivonat beszerzése:

  ```
  https://api.videoindexer.ai/auth/[LOCATION]/Accounts/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/AccessToken
  ```
  
2. Elemzések lekérése:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

#### <a name="getting-video-processing-state-in-v1"></a>A V1 videó feldolgozási állapot beolvasása:

```
https://videobreakdown.azure-api.net/Breakdowns/Api/Partner/Breakdowns/[VIDEO_ID]/State
```
  
#### <a name="getting-video-processing-state-in-v2"></a>A V2 videó feldolgozási állapot beolvasása:

Az API 2-es feldolgozási állapotát az beszerzése videó Index API részeként adja vissza.

1. A fiók hozzáférési jogkivonatot használja, vagy egy videó szintű hozzáférési jogkivonat beszerzése:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```
  
2. Elemzések lekérése:

  ```
  https://api.videoindexer.ai/trial/[LOCATION]/[YOUR_ACCOUNT_ID]/Videos/[VIDEO_ID]/Index?accessToken=eyJ0eXA...
  ```

## <a name="next-steps"></a>További lépések

[Használja az Azure videó indexelőt API](video-indexer-use-apis.md)

