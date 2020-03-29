---
title: Együttműködésen alapuló fordítási keretrendszer (CTF) jelentése – Fordítószöveg API
titleSuffix: Azure Cognitive Services
description: Az együttműködésen alapuló fordítási keretrendszer (CTF) jelentéskészítési útmutatója.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: conceptual
ms.date: 12/14/2017
ms.author: swmachan
ms.openlocfilehash: 6a197095d97e67f7548e60375148cff57e47b797
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "68595936"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>A Collaborative Translation Framework (CTF) jelentéskészítés használata

> [!NOTE]
> Ez a módszer elavult. Nem érhető el a Translator Text API 3.0-s verziójában.
> 
> 2018. február 1-jén elavult az együttműködésen alapuló fordítási keretrendszer (CTF), amely korábban a Translator Text API 2.0-s verzióihoz volt elérhető. Az AddTranslation és az AddTranslationArray függvények lehetővé teszik a felhasználók számára a korrekciók engedélyezését az együttműködésen alapuló fordítási keretrendszeren keresztül. 2018. január 31-e után ez a két funkció nem fogadta el az új mondatbenyújtásokat, és a felhasználók hibaüzenetet kaptak. Ezek a funkciók kivannak vonva, és nem lesznek lecserélve.

Az együttműködésen alapuló fordítási keretrendszer (CTF) jelentéskészítő API a CTF-tároló statisztikáit és tényleges tartalmát adja vissza. Ez az API különbözik a GetTranslations() metódustól, mert:
* A lefordított tartalmat és annak teljes számát csak a fiókjából (appId vagy Azure Marketplace-fiók) adja vissza.
* A lefordított tartalmat és annak teljes számát adja eredményül anélkül, hogy a forrásmondategyezést igényelne.
* Nem adja vissza az automatikus fordítást (gépi fordítás).

## <a name="endpoint"></a>Végpont
A CTF jelentéskészítő API végpontjahttps://api.microsofttranslator.com/v2/beta/ctfreporting.svc


## <a name="methods"></a>Metódusok
| Név |    Leírás|
|:---|:---|
| GetUserTranslationCounts metódus | A felhasználó által létrehozott fordítások számának leszámítása. |
| GetUserTranslations metódus | A felhasználó által létrehozott fordítások beolvasása. |

Ezek a módszerek lehetővé teszik, hogy:
* Töltse le a fiókazonosító japalatt a felhasználói fordítások és javítások teljes készletét.
* Szerezze be a gyakori közreműködők listáját. Győződjön meg arról, hogy a megfelelő felhasználónév van megadva az AddTranslation() alkalmazásban.
* Hozzon létre egy felhasználói felületet, amely lehetővé teszi a megbízható felhasználók számára, hogy az URI-előtag alapján az összes rendelkezésre álló jelöltet lássák, szükség esetén a webhely egy részére korlátozva.

> [!NOTE]
> Mindkét módszer viszonylag lassú és drága. Javasoljuk, hogy használja őket takarékosan.

## <a name="getusertranslationcounts-method"></a>GetUserTranslationCounts metódus

Ez a módszer a felhasználó által létrehozott fordítások számát kapja. Az uriPrefix, a felhasználó, a minRating és a maxRating kérelem paraméterek szerint csoportosított fordítási számlálók listáját tartalmazza.

**Szintaxis**

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
| appId | **Kötelező** Ha az engedélyezésfejlécet használja, hagyja üresen az appid mezőt, adjon meg egy "Tulajdonos" + " " + hozzáférési jogkivonatot tartalmazó karakterláncot.|
| uriElőtag | **Nem kötelező** A fordítás URI-előtagát tartalmazó karakterlánc.|
| honnan | **Nem kötelező** A fordítási szöveg nyelvkódját jelölő karakterlánc. |
| erre: | **Nem kötelező** A szöveg lefordításához használt nyelvkódot jelölő karakterlánc.|
| minRating (minRating)| **Nem kötelező** Egész szám, amely a lefordított szöveg minimális minőségi besorolását jelöli. Az érvényes érték -10 és 10 között van. Az alapértelmezett érték az 1.|
| maxRating (maxRating)| **Nem kötelező** Egész szám, amely a lefordított szöveg maximális minőségi besorolását jelöli. Az érvényes érték -10 és 10 között van. Az alapértelmezett érték az 1.|
| felhasználó! | **Nem kötelező** Az eredmény szűrésére használt karakterlánc a beküldött anyag létrehozója alapján. |
| category| **Nem kötelező** A fordítás kategóriáját vagy tartományát tartalmazó karakterlánc. Ez a paraméter csak az alapértelmezett általános beállítást támogatja.|
| minDateUtc| **Nem kötelező** Az a dátum, amelytől kezdve a fordításokat be szeretné olvasni. A dátumnak UTC formátumúnak kell lennie. |
| maxDateUtc| **Nem kötelező** Az a dátum, amíg vissza szeretné szerezni a fordításokat. A dátumnak UTC formátumúnak kell lennie. |
| Ugrál| **Nem kötelező** Az oldalon kihagyni kívánt eredmények száma. Ha például az eredmények első 20 sorának kihagyását és a 21. A paraméter alapértelmezett értéke 0.|
| venni | **Nem kötelező** A beolvasni kívánt eredmények száma. Az egyes kérelmek maximális száma 100. Az alapértelmezett érték 100.|

> [!NOTE]
> A kihagyási és átvehetővé tételes kérelem paraméterei nagyszámú eredményrekord esetében engedélyezik a tördelést.

**Visszatérési érték**

Az eredményhalmaz a **UserTranslationCount**tömböt tartalmazza. Minden UserTranslationCount a következő elemekkel rendelkezik:

| Mező | Leírás |
|:---|:---|
| Darabszám| A beolvasott eredmények száma|
| Feladó | A forrásnyelv|
| Minősítés| Az AddTranslation() metódus hívásában a beküldő által alkalmazott minősítés|
| Művelet| A célnyelv|
| Uri| Az AddTranslation() metódushívásában alkalmazott URI|
| Felhasználó| A felhasználónév|

**Kivételek**

| Kivétel | Üzenet | Feltételek |
|:---|:---|:---|
| ArgumentOutOfRangeException | A "**maxDateUtc**" paraméternek nagyobbnak vagy egyenlőnek kell lennie a '**minDateUtc**'-nak.| A **maxDateUtc** paraméter értéke kisebb, mint a **minDateUtc**paraméter értéke.|
| TranslateApiException | Az IP túllépi a kvótát.| <ul><li>A kérelmek percenkénti száma eléri a korlátot.</li><li>A kérelem mérete továbbra is korlátozott, 10000 karakter.</li><li>Az óránkénti és a napi kvóta korlátozza a Microsoft Translator API által elfogadott karakterek számát.</li></ul>|
| TranslateApiException | Az AppId túllépi a kvótát.| Az alkalmazásazonosító túllépte az óránkénti vagy napi kvótát.|

> [!NOTE]
> A kvóta igazodni fog, hogy biztosítsa a szolgáltatás valamennyi felhasználója közötti méltányosságot.

**Kódpéldák megtekintése a GitHib-on**
* [C #](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [Php](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>GetUserTranslations metódus

Ez a módszer lekéri a felhasználó által létrehozott fordításokat. Az uriPrefix, a felhasználó, valamint a minRating és a maxRating kérelem paraméterei szerint csoportosított fordításokat biztosítja.

**Szintaxis**

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
| appId | **Kötelező** Ha az engedélyezésfejlécet használja, hagyja üresen az appid mezőt, adjon meg egy "Tulajdonos" + " " + hozzáférési jogkivonatot tartalmazó karakterláncot.|
| uriElőtag| **Nem kötelező** A fordítás URI-előtagát tartalmazó karakterlánc.|
| honnan| **Nem kötelező** A fordítási szöveg nyelvkódját jelölő karakterlánc.|
| erre:| **Nem kötelező** A szöveg lefordításához használt nyelvkódot jelölő karakterlánc.|
| minRating (minRating)| **Nem kötelező** Egész szám, amely a lefordított szöveg minimális minőségi besorolását jelöli. Az érvényes érték -10 és 10 között van. Az alapértelmezett érték az 1.|
| maxRating (maxRating)| **Nem kötelező** Egész szám, amely a lefordított szöveg maximális minőségi besorolását jelöli. Az érvényes érték -10 és 10 között van. Az alapértelmezett érték az 1.|
| felhasználó!| **Választható. Az eredmény szűrésére használt karakterlánc a beküldött anyag létrehozója alapján**|
| category| **Nem kötelező** A fordítás kategóriáját vagy tartományát tartalmazó karakterlánc. Ez a paraméter csak az alapértelmezett általános beállítást támogatja.|
| minDateUtc| **Nem kötelező** Az a dátum, amelytől kezdve a fordításokat be szeretné olvasni. A dátumnak UTC formátumúnak kell lennie.|
| maxDateUtc| **Nem kötelező** Az a dátum, amíg vissza szeretné szerezni a fordításokat. A dátumnak UTC formátumúnak kell lennie.|
| Ugrál| **Nem kötelező** Az oldalon kihagyni kívánt eredmények száma. Ha például az eredmények első 20 sorának kihagyását és a 21. A paraméter alapértelmezett értéke 0.|
| venni| **Nem kötelező** A beolvasni kívánt eredmények száma. Az egyes kérelmek maximális száma 100. Az alapértelmezett érték 50.|

> [!NOTE]
> A kihagyási és átvehetővé tételes kérelem paraméterei nagyszámú eredményrekord esetében engedélyezik a tördelést.

**Visszatérési érték**

Az eredményhalmaz a **UserTranslation**tömböt tartalmazza. Minden UserTranslation a következő elemekkel rendelkezik:

| Mező | Leírás |
|:---|:---|
| CreatedDateUtc| A bejegyzés létrehozási dátuma az AddTranslation() használatával|
| Feladó| A forrásnyelv|
| Eredeti szöveg| A kérelem benyújtásakor használt forrásnyelvi szöveg|
|Minősítés |Az AddTranslation() metódus hívásában a beküldő által alkalmazott minősítés|
|Művelet|    A célnyelv|
|Lefordított szöveg|    Az AddTranslation() metódushívásában elküldött fordítás|
|Uri|   Az AddTranslation() metódushívásában alkalmazott URI|
|Felhasználó   |A felhasználónév|

**Kivételek**

| Kivétel | Üzenet | Feltételek |
|:---|:---|:---|
| ArgumentOutOfRangeException | A "**maxDateUtc**" paraméternek nagyobbnak vagy egyenlőnek kell lennie a '**minDateUtc**'-nak.| A **maxDateUtc** paraméter értéke kisebb, mint a **minDateUtc**paraméter értéke.|
| TranslateApiException | Az IP túllépi a kvótát.| <ul><li>A kérelmek percenkénti száma eléri a korlátot.</li><li>A kérelem mérete továbbra is korlátozott, 10000 karakter.</li><li>Az óránkénti és a napi kvóta korlátozza a Microsoft Translator API által elfogadott karakterek számát.</li></ul>|
| TranslateApiException | Az AppId túllépi a kvótát.| Az alkalmazásazonosító túllépte az óránkénti vagy napi kvótát.|

> [!NOTE]
> A kvóta igazodni fog, hogy biztosítsa a szolgáltatás valamennyi felhasználója közötti méltányosságot.

**Kódpéldák megtekintése a GitHib-on**
* [C #](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [Php](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)
