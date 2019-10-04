---
title: Kollaboratív fordítási keretrendszer (CTF) jelentése – Translator Text API
titleSuffix: Azure Cognitive Services
description: Az együttműködési célú fordítási keretrendszer (CTF) jelentéskészítésének használata.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 6a197095d97e67f7548e60375148cff57e47b797
ms.sourcegitcommit: fe6b91c5f287078e4b4c7356e0fa597e78361abe
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/29/2019
ms.locfileid: "68595936"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>A Collaborative Translation Framework (CTF) jelentéskészítés használata

> [!NOTE]
> Ez a metódus elavult. A Translator Text API 3.0-s verziójában nem érhető el.
> 
> A korábban a Translator Text API V 2.0-s verziójában már elérhető együttműködési fordítási keretrendszer (csoportmunka) a 2018. február 1-től elavult. A AddTranslation és a AddTranslationArray függvények lehetővé teszik a felhasználók számára, hogy az együttműködési fordítási keretrendszeren keresztül engedélyezzék a javítást. 2018. január 31-ig ez a két függvény nem fogadta el az új mondatok beküldését, és a felhasználók hibaüzenetet kapnak. Ezek a függvények ki lettek vonva, és nem lesznek lecserélve.

Az együttműködési célú fordítási keretrendszer (CTF) jelentéskészítési API visszaadja a statisztikát és a tényleges tartalmat a CTF-tárolóban. Ez az API különbözik a GetTranslations () metódustól, mert:
* A lefordított tartalmat és a teljes darabszámot adja vissza kizárólag a fiókjából (appId vagy Azure Marketplace-fiók).
* A lefordított tartalmat és a teljes darabszámot adja vissza anélkül, hogy meg kellene egyeznie a forrás mondatával.
* A nem ad vissza automatikus fordítást (gépi fordítás).

## <a name="endpoint"></a>Végpont
A CTF jelentési API végpontja https://api.microsofttranslator.com/v2/beta/ctfreporting.svc


## <a name="methods"></a>Metódusok
| Name (Név) |    Leírás|
|:---|:---|
| GetUserTranslationCounts metódus | A felhasználó által létrehozott fordítások számának beolvasása. |
| GetUserTranslations metódus | A felhasználó által létrehozott fordítások beolvasása. |

Ezek a módszerek lehetővé teszik a következőket:
* Kérje le a felhasználói fordítások és javítások teljes készletét a fiókja AZONOSÍTÓjának letöltéséhez.
* Szerezze be a gyakori közreműködők listáját. Győződjön meg arról, hogy a megfelelő Felhasználónév van megadva a AddTranslation () alkalmazásban.
* Hozzon létre egy felhasználói felületet (UI), amely lehetővé teszi, hogy a megbízható felhasználók az összes rendelkezésre álló pályázót lássák, ha szükséges, a hely egy részéhez, az URI-előtag alapján.

> [!NOTE]
> Mindkét módszer viszonylag lassú és költséges. Ajánlott ezeket takarékosan használni.

## <a name="getusertranslationcounts-method"></a>GetUserTranslationCounts metódus

Ez a metódus lekéri a felhasználó által létrehozott fordítások számát. Megjeleníti a fordítások listáját a uriPrefix, a, a, a felhasználó, a minRating és a maxRating kérelmek paramétereinek csoportosításával.

**Syntax**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslationCount[]GetUserTranslationCounts(
>            string appId,
>            string uriPrefix,
>            string from,
>            string to,
>            int? minRating,
>            int? maxRating,
>            string user,
>            string category
>            DateTime? minDateUtc,
>            DateTime? maxDateUtc,
>            int? skip,
>            int? take);
> ```

**Paraméterek**

| Paraméter | Leírás |
|:---|:---|
| appId | **Kötelező megadni** Ha az engedélyezési fejlécet használja, hagyja üresen a AppID mezőt, és írjon be egy olyan karakterláncot, amely tartalmazza a "tulajdonos" + "+" hozzáférési tokent.|
| uriPrefix | Nem **kötelező** A fordítás URI-azonosítóját tartalmazó karakterlánc.|
| from | Nem **kötelező** A fordítási szöveg nyelvi kódját jelölő sztring. |
| to | Nem **kötelező** A szöveg fordítására szolgáló nyelvkód jelölő sztring.|
| minRating| Nem **kötelező** Egy egész szám, amely a lefordított szöveg minimális minőségi minősítését jelképezi. Az érvényes érték:-10 és 10. Az alapértelmezett érték az 1.|
| maxRating| Nem **kötelező** Egy egész szám, amely a lefordított szöveg maximális minőségi minősítését jelképezi. Az érvényes érték:-10 és 10. Az alapértelmezett érték az 1.|
| Felhasználó | Nem **kötelező** Egy karakterlánc, amely az eredménynek a Küldés kezdeményezője alapján történő szűrésére szolgál. |
| category| Nem **kötelező** A fordítás kategóriáját vagy tartományát tartalmazó karakterlánc. Ez a paraméter csak az alapértelmezett általános beállítást támogatja.|
| minDateUtc| Nem **kötelező** Az a dátum, amikor a fordításokat le szeretné kérni. A dátumnak UTC formátumúnak kell lennie. |
| maxDateUtc| Nem **kötelező** Az a dátum, ameddig a fordításokat le szeretné kérni. A dátumnak UTC formátumúnak kell lennie. |
| kihagyás| Nem **kötelező** Az oldalon kihagyni kívánt eredmények száma. Ha például azt szeretné, hogy az eredmények és a nézet első 20 sora a huszonegyedik eredmény rekordban legyen, a 20 érték megadása ehhez a paraméterhez. A paraméter alapértelmezett értéke 0.|
| take | Nem **kötelező** A lekérdezni kívánt eredmények száma. Az egyes kérések maximális száma 100. Az alapértelmezett érték a 100.|

> [!NOTE]
> A skip és a Take kérelem paraméterek nagy számú eredményhalmaz esetében lehetővé teszik a tördelést.

**Visszatérési érték**

Az eredményhalmaz a **UserTranslationCount**tömbjét tartalmazza. Minden UserTranslationCount a következő elemekből áll:

| Mező | Leírás |
|:---|:---|
| Count| A beolvasott eredmények száma|
| Forrás | A forrás nyelve|
| Minősítés| A küldő által a AddTranslation () metódus hívásakor alkalmazott minősítés|
| Cél| A cél nyelve|
| URI| A AddTranslation () metódus hívásakor alkalmazott URI|
| Felhasználó| A Felhasználónév|

**Kivételek**

| Kivétel | Message | Feltételek |
|:---|:---|:---|
| ArgumentOutOfRangeException | A "**maxDateUtc**" paraméternek nagyobbnak vagy egyenlőnek kell lennie, mint "**minDateUtc**".| A **maxDateUtc** paraméter értéke kisebb, mint a **minDateUtc**paraméter értéke.|
| TranslateApiException | Az IP-cím meghaladja a kvótát.| <ul><li>A kérések száma percenként elérte a korlátot.</li><li>A kérelem mérete 10000 karakternél továbbra is korlátozott.</li><li>Az óránkénti és a napi kvóta korlátozza a Microsoft Translator API által elfogadott karakterek számát.</li></ul>|
| TranslateApiException | A AppId a kvóta felett van.| Az alkalmazás azonosítója túllépte az óránkénti vagy a napi kvótát.|

> [!NOTE]
> A kvóta úgy módosul, hogy a szolgáltatás minden felhasználója számára igazságos legyen.

**Példák a GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>GetUserTranslations metódus

Ezzel a módszerrel a felhasználó által létrehozott fordítások olvashatók be. A fordítást a uriPrefix, a, a, a felhasználó és a minRating, valamint a maxRating-kérelmek paramétereinek csoportosításával biztosítja.

**Syntax**

> [!div class="tabbedCodeSnippets"]
> ```cs
> UserTranslation[] GetUserTranslations (
>             string appId,
>             string uriPrefix,
>             string from,
>             string to,
>             int? minRating,
>             int? maxRating,
>             string user,
>             string category
>             DateTime? minDateUtc,
>             DateTime? maxDateUtc,
>             int? skip,
>             int? take);
> ```

**Paraméterek**

| Paraméter | Leírás |
|:---|:---|
| appId | **Kötelező megadni** Ha az engedélyezési fejlécet használja, hagyja üresen a AppID mezőt, és írjon be egy olyan karakterláncot, amely tartalmazza a "tulajdonos" + "+" hozzáférési tokent.|
| uriPrefix| Nem **kötelező** A fordítás URI-azonosítóját tartalmazó karakterlánc.|
| from| Nem **kötelező** A fordítási szöveg nyelvi kódját jelölő sztring.|
| to| Nem **kötelező** A szöveg fordítására szolgáló nyelvkód jelölő sztring.|
| minRating| Nem **kötelező** Egy egész szám, amely a lefordított szöveg minimális minőségi minősítését jelképezi. Az érvényes érték:-10 és 10. Az alapértelmezett érték az 1.|
| maxRating| Nem **kötelező** Egy egész szám, amely a lefordított szöveg maximális minőségi minősítését jelképezi. Az érvényes érték:-10 és 10. Az alapértelmezett érték az 1.|
| Felhasználó| **Választható. Egy karakterlánc, amely az eredmény szűrésére szolgál a Küldés kezdeményezője alapján**|
| category| Nem **kötelező** A fordítás kategóriáját vagy tartományát tartalmazó karakterlánc. Ez a paraméter csak az alapértelmezett általános beállítást támogatja.|
| minDateUtc| Nem **kötelező** Az a dátum, amikor a fordításokat le szeretné kérni. A dátumnak UTC formátumúnak kell lennie.|
| maxDateUtc| Nem **kötelező** Az a dátum, ameddig a fordításokat le szeretné kérni. A dátumnak UTC formátumúnak kell lennie.|
| kihagyás| Nem **kötelező** Az oldalon kihagyni kívánt eredmények száma. Ha például azt szeretné, hogy az eredmények és a nézet első 20 sora a huszonegyedik eredmény rekordban legyen, a 20 érték megadása ehhez a paraméterhez. A paraméter alapértelmezett értéke 0.|
| take| Nem **kötelező** A lekérdezni kívánt eredmények száma. Az egyes kérések maximális száma 100. Az alapértelmezett érték a 50.|

> [!NOTE]
> A skip és a Take kérelem paraméterek nagy számú eredményhalmaz esetében lehetővé teszik a tördelést.

**Visszatérési érték**

Az eredményhalmaz a **UserTranslation**tömbjét tartalmazza. Minden UserTranslation a következő elemekből áll:

| Mező | Leírás |
|:---|:---|
| CreatedDateUtc| A bejegyzés létrehozásának dátuma a AddTranslation () használatával|
| Forrás| A forrás nyelve|
| OriginalText| A kérelem elküldésekor használt forrás nyelvi szövege|
|Minősítés |A küldő által a AddTranslation () metódus hívásakor alkalmazott minősítés|
|Cél|    A cél nyelve|
|TranslatedText|    A AddTranslation () metódus hívása által beküldött fordítás|
|URI|   A AddTranslation () metódus hívásakor alkalmazott URI|
|Felhasználó   |A Felhasználónév|

**Kivételek**

| Kivétel | Message | Feltételek |
|:---|:---|:---|
| ArgumentOutOfRangeException | A "**maxDateUtc**" paraméternek nagyobbnak vagy egyenlőnek kell lennie, mint "**minDateUtc**".| A **maxDateUtc** paraméter értéke kisebb, mint a **minDateUtc**paraméter értéke.|
| TranslateApiException | Az IP-cím meghaladja a kvótát.| <ul><li>A kérések száma percenként elérte a korlátot.</li><li>A kérelem mérete 10000 karakternél továbbra is korlátozott.</li><li>Az óránkénti és a napi kvóta korlátozza a Microsoft Translator API által elfogadott karakterek számát.</li></ul>|
| TranslateApiException | A AppId a kvóta felett van.| Az alkalmazás azonosítója túllépte az óránkénti vagy a napi kvótát.|

> [!NOTE]
> A kvóta úgy módosul, hogy a szolgáltatás minden felhasználója számára igazságos legyen.

**Példák a GitHib**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
