---
title: Együttműködésen alapuló fordítás Framework (CTF) jelentéskészítés – Translator Text API
titlesuffix: Azure Cognitive Services
description: Hogyan használható a reporting által biztosított együttműködési környezettel Translation Framework (CTF).
services: cognitive-services
author: Jann-Skotdal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: e1af2ef3be2b6cf4b75357107caf9d74c7199e8d
ms.sourcegitcommit: bd15a37170e57b651c54d8b194e5a99b5bcfb58f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/07/2019
ms.locfileid: "57539782"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>A Collaborative Translation Framework (CTF) jelentéskészítés használata

> [!NOTE]
> Tato metoda se zamítá. Nem érhető a 3.0-s verzió, a Translator Text API.

> Az együttműködésen alapuló fordítások Framework (CTF), 2.0-s verziójú, a Translator Text API, a korábban elérhető volt elavult 2018. február 1. A AddTranslation és AddTranslationArray funkciók lehetővé teszik a felhasználók engedélyezhetik a javításokat az együttműködésen alapuló fordítás keretrendszeren keresztül. 2018. január 31. után két függvényekhez nem fogadta el új mondat jelentkezés, és a felhasználók hibaüzenetet kapnak. Ezek a függvények is kivezetjük, és nem pótolhatók.

>Elérhető a Translator Hub API, lehetővé téve a terminológia és stílus, egyéni fordítási rendszer hasonló funkciókat és hívhatja meg a kategória-azonosítója használatával a Translator Text API. Translator Hub: [ https://hub.microsofttranslator.com ](https://hub.microsofttranslator.com). Translator Hub API: [ https://hub.microsofttranslator.com/swagger ](https://hub.microsofttranslator.com/swagger).

Az együttműködési Translation Framework (CTF) Reporting API CTF áruházbeli statisztikák és a tényleges tartalmat adja vissza. Ez az API eltér a GetTranslations() metódus mert azt:
* A fordított tartalomban és a számuk csak a fiókból (appId vagy az Azure Marketplace-fiók) adja vissza.
* A fordított tartalomban és a számuk anélkül, hogy a forrás mondat egyezést adja vissza.
* Az automatikus fordítás (gépi fordítás) nem ad vissza.

## <a name="endpoint"></a>Végpont
A CTF Reporting API-végpont https://api.microsofttranslator.com/v2/beta/ctfreporting.svc


## <a name="methods"></a>Metódusok
| Name (Név) |    Leírás|
|:---|:---|
| GetUserTranslationCounts Method | A megjelenő fordításokat, a felhasználó által létrehozott számát beolvasása. |
| GetUserTranslations Method | A megjelenő fordításokat, a felhasználó által létrehozott kérdezi le. |

Ezek a metódusok lehetővé teszi:
* Felhasználói fordítások és javításokat a Letöltés most a Fiókazonosítójával teljes készletének beolvasása.
* Szerezze be a rendszeres közreműködők listáját. Győződjön meg arról, hogy a megfelelő felhasználónév AddTranslation() megtalálható-e.
* Korlátozni a helyet, az URI-előtag alapján egy része, amely lehetővé teszi a megbízható felhasználók számára az összes rendelkezésre álló jelöltek, szükség esetén felhasználói felület (UI) létrehozása.

> [!NOTE]
> Mindkét a módszereket viszonylag lassú és drága feladat. Javasoljuk, hogy azok takarékosan használatát.

## <a name="getusertranslationcounts-method"></a>GetUserTranslationCounts metódus

Ez a módszer lekérdezi a felhasználó által létrehozott fordításainak száma. A fordítási számát a uriPrefix szerint csoportosítva, a felhasználó minRating és maxRating kérelem paramétereinek listáját biztosít.

**Syntax**

> [!div class="tabbedCodeSnippets"]
```cs
UserTranslationCount[]GetUserTranslationCounts(
           string appId,
           string uriPrefix,
           string from,
           string to,
           int? minRating,
           int? maxRating,
           string user,
           string category
           DateTime? minDateUtc,
           DateTime? maxDateUtc,
           int? skip,
           int? take);
```

**Paraméterek**

| Paraméter | Leírás |
|:---|:---|
| appId | **Szükséges** az engedélyezési fejléc használata esetén a appid mezőt üresen hagyja más esetben adja meg egy karakterlánc, amely tartalmazza a "Tulajdonos" + "" + a hozzáférési jogkivonatot.|
| uriPrefix | **Nem kötelező** URI-ját a fordítás előtagot tartalmazó karakterlánc.|
| forrás: | **Nem kötelező** egy karakterlánc, amely a fordítandó szöveg nyelvkódja. |
| erre: | **Nem kötelező** karakterláncként lefordítani a szöveget a nyelvi kódot.|
| minRating| **Nem kötelező** a minőségi besorolása a lefordított szöveg jelölő egész szám. Az érvényes érték -10- és 10 között. Az alapértelmezett érték az 1.|
| maxRating| **Nem kötelező** a legmagasabb minőségű besorolása a lefordított szöveg jelölő egész szám. Az érvényes érték -10- és 10 között. Az alapértelmezett érték az 1.|
| Felhasználó | **Nem kötelező** egy karakterlánc, amellyel szűrheti az eredményeket a Küldés létrehozója alapján. |
| category| **Nem kötelező** a kategóriát vagy a tartomány a fordítás tartalmazó karakterlánc. Ez a paraméter csak az alapértelmezett beállítás általános támogatja.|
| minDateUtc| **Nem kötelező** a dátum, amikor szeretné beolvasni a fordításokat. A dátum az UTC formátumban kell lennie. |
| maxDateUtc| **Nem kötelező** : Ha szeretné a fordítások beolvasni a dátumot. A dátum az UTC formátumban kell lennie. |
| kihagyás| **Nem kötelező** oldalon hagyja ki a kívánt eredmények száma. Például ha azt szeretné, a kihagyása a első 20 sort az eredmények és a 21-én eredményrekord nézetet, adja meg az ehhez a paraméterhez 20. Ez a paraméter alapértelmezett értéke 0.|
| hajtsa végre a megfelelő | **Nem kötelező** a lekérni kívánt eredmények számát. Az egyes kérelmek maximális száma pedig a 100. Az alapértelmezett érték 100.|

> [!NOTE]
> A Kihagyás gombra, és végezze el a kérelem paramétereinek nagy számú rekordot a tördelés engedélyezése.

**Vrácená hodnota**

Az eredményhalmaz tömbjét tartalmazza a **UserTranslationCount**. Minden egyes UserTranslationCount a következő elemekből áll:

| Mező | Leírás |
|:---|:---|
| Darabszám| Beolvasott eredmények száma|
| Ettől: | A Forrásnyelv|
| Minősítés| A minősítés az alkalmazott a küldőtől AddTranslation() metódushívás|
| Művelet| A Célnyelv|
| URI| Az URI-t a AddTranslation() metódus meghívása a alkalmazni|
| Felhasználó| A felhasználó neve|

**Kivételek**

| Kivétel | Üzenet | Feltételek |
|:---|:---|:---|
| ArgumentOutOfRangeException | A paraméter "**maxDateUtc**"kell lennie, nagyobb vagy egyenlő"**minDateUtc**".| A paraméter értékének **maxDateUtc** kisebb, mint a paraméter értékének **minDateUtc**.|
| TranslateApiException | IP-cím része fölé a kvótát.| <ul><li>Percenkénti kérések száma a határérték elérése.</li><li>A kérelem mérete legfeljebb 10000 karakter marad.</li><li>Óránként történik, és a egy napi kvóta korlátozza, amely elfogadja a Microsoft Translator API karakterek száma.</li></ul>|
| TranslateApiException | Identifikátor AppId kvóta felett van.| Az Alkalmazásazonosítót meghaladta az óránkénti vagy napi kvótáját.|

> [!NOTE]
> A kvóta növeléséhez, győződjön meg, hogy a szolgáltatás az összes felhasználó használ igazságos lehetőség.

**A GitHib hitelesítésikód-Példák megtekintése**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>GetUserTranslations metódus

Ez a módszer a fordítások, a felhasználó által létrehozott kérdezi le. A fordítások szerint csoportosítva a uriPrefix, to, felhasználói, és minRating és maxRating kérelem paramétereket biztosít.

**Syntax**

> [!div class="tabbedCodeSnippets"]
```cs
UserTranslation[] GetUserTranslations (
            string appId,
            string uriPrefix,
            string from,
            string to,
            int? minRating,
            int? maxRating,
            string user,
            string category
            DateTime? minDateUtc,
            DateTime? maxDateUtc,
            int? skip,
            int? take);
```

**Paraméterek**

| Paraméter | Leírás |
|:---|:---|
| appId | **Szükséges** az engedélyezési fejléc használata esetén a appid mezőt üresen hagyja más esetben adja meg egy karakterlánc, amely tartalmazza a "Tulajdonos" + "" + a hozzáférési jogkivonatot.|
| uriPrefix| **Nem kötelező** URI-ját a fordítás előtagot tartalmazó karakterlánc.|
| forrás:| **Nem kötelező** egy karakterlánc, amely a fordítandó szöveg nyelvkódja.|
| erre:| **Nem kötelező** karakterláncként lefordítani a szöveget a nyelvi kódot.|
| minRating| **Nem kötelező** a minőségi besorolása a lefordított szöveg jelölő egész szám. Az érvényes érték -10- és 10 között. Az alapértelmezett érték az 1.|
| maxRating| **Nem kötelező** a legmagasabb minőségű besorolása a lefordított szöveg jelölő egész szám. Az érvényes érték -10- és 10 között. Az alapértelmezett érték az 1.|
| Felhasználó| **Nem kötelező. Egy karakterlánc, amellyel szűrheti az eredményeket a Küldés létrehozója alapján**|
| category| **Nem kötelező** a kategóriát vagy a tartomány a fordítás tartalmazó karakterlánc. Ez a paraméter csak az alapértelmezett beállítás általános támogatja.|
| minDateUtc| **Nem kötelező** a dátum, amikor szeretné beolvasni a fordításokat. A dátum az UTC formátumban kell lennie.|
| maxDateUtc| **Nem kötelező** : Ha szeretné a fordítások beolvasni a dátumot. A dátum az UTC formátumban kell lennie.|
| kihagyás| **Nem kötelező** oldalon hagyja ki a kívánt eredmények száma. Például ha azt szeretné, a kihagyása a első 20 sort az eredmények és a 21-én eredményrekord nézetet, adja meg az ehhez a paraméterhez 20. Ez a paraméter alapértelmezett értéke 0.|
| hajtsa végre a megfelelő| **Nem kötelező** a lekérni kívánt eredmények számát. Az egyes kérelmek maximális száma pedig a 100. Az alapértelmezett érték 50.|

> [!NOTE]
> A Kihagyás gombra, és végezze el a kérelem paramétereinek nagy számú rekordot a tördelés engedélyezése.

**Vrácená hodnota**

Az eredményhalmaz tömbjét tartalmazza a **UserTranslation**. Minden egyes UserTranslation a következő elemekből áll:

| Mező | Leírás |
|:---|:---|
| CreatedDateUtc| A létrehozást, a bejegyzés AddTranslation() használatával|
| Ettől:| A Forrásnyelv|
| OriginalText| A Forrásnyelv szöveget a kérelem elküldésekor használt|
|Minősítés |A minősítés az alkalmazott a küldőtől AddTranslation() metódushívás|
|Művelet|    A Célnyelv|
|TranslatedText|    A fordítás küldött AddTranslation() metódushívás|
|URI|   Az URI-t a AddTranslation() metódus meghívása a alkalmazni|
|Felhasználó   |A felhasználó neve|

**Kivételek**

| Kivétel | Üzenet | Feltételek |
|:---|:---|:---|
| ArgumentOutOfRangeException | A paraméter "**maxDateUtc**"kell lennie, nagyobb vagy egyenlő"**minDateUtc**".| A paraméter értékének **maxDateUtc** kisebb, mint a paraméter értékének **minDateUtc**.|
| TranslateApiException | IP-cím része fölé a kvótát.| <ul><li>Percenkénti kérések száma a határérték elérése.</li><li>A kérelem mérete legfeljebb 10000 karakter marad.</li><li>Óránként történik, és a egy napi kvóta korlátozza, amely elfogadja a Microsoft Translator API karakterek száma.</li></ul>|
| TranslateApiException | Identifikátor AppId kvóta felett van.| Az Alkalmazásazonosítót meghaladta az óránkénti vagy napi kvótáját.|

> [!NOTE]
> A kvóta növeléséhez, győződjön meg, hogy a szolgáltatás az összes felhasználó használ igazságos lehetőség.

**A GitHib hitelesítésikód-Példák megtekintése**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
