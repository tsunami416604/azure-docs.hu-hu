---
title: A Microsoft Translator együttműködési fordítási Reporting keretrendszer (CTF)
description: Hogyan együttműködési fordítási keretrendszer (CTF) jelentéseket.
services: cognitive-services
author: Jann-Skotdal
manager: chriswendt1
ms.service: cognitive-services
ms.component: translator-text
ms.topic: article
ms.date: 12/14/2017
ms.author: v-jansko
ms.openlocfilehash: cefc630a82a56703ba4942bcad18f6e0a38b1ee5
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347687"
---
# <a name="how-to-use-collaborative-translation-framework-ctf-reporting"></a>Közös fordítási keretrendszer (CTF) reporting használata

> [!NOTE]
> Ez a metódus elavult. Nem érhető el a fordító szöveg API 3.0-s verzió.

> Az együttműködési fordítások keretrendszer (CTF), a fordító szöveg API V2.0 korábban elérhető elavulttá vált 2018. február 1-től. A AddTranslation és AddTranslationArray funkciók tudassa a felhasználókkal, engedélyezze a közös fordítási keretrendszeren keresztül javításokat. 2018. január 31. után két függvényekhez nem fogadta el új mondat jelentések, és a felhasználók hibaüzenetet kap. Ezek a funkciók volt hatókörről, és nem fogja írni. 

>Hasonló funkciókat a fordító Hub API, lehetővé téve olyan egyéni fordítási rendszer, amely a terminológia és stílusát, és hívhat meg a Kategóriaazonosító a fordító szöveg API használatával. A fordító Hub: [ https://hub.microsofttranslator.com ](https://hub.microsofttranslator.com). A fordító Hub API: [ https://hub.microsofttranslator.com/swagger ](https://hub.microsofttranslator.com/swagger).

A közös fordítási keretrendszer (CTF) Reporting API CTF tárolójában statisztikák és a tényleges tartalmat adja vissza. Ez az API eltér a GetTranslations() metódus mivel azt:
* A lefordított tartalom és a számuk csak fiókjából (appId vagy Azure piactér fiók) adja vissza.
* A lefordított tartalom és a teljes számának anélkül, hogy a forrás mondat egyezés adja vissza.
* Az automatikus fordítás (gépi fordítás) nem ad vissza.

## <a name="endpoint"></a>Végpont
Az a CTF Reporting API-végpont esetében http://api.microsofttranslator.com/v2/beta/ctfreporting.svc
                        

## <a name="methods"></a>Metódusok
| Name (Név) |    Leírás|
|:---|:---|
| GetUserTranslationCounts módszer | A fordítás a felhasználó által létrehozott számát beolvasása. |
| GetUserTranslations módszer | Lekéri a fordítások, a felhasználó által létrehozott. |

Ezek a módszerek lehetővé teszi:
* A felhasználó fordítások és javításokat a Fiókazonosító letölthető a teljes készletének beolvasása.
* Szerezze be a gyakori közreműködő szerepkörrel rendelkező személyek listáját. Győződjön meg arról, hogy a helyes felhasználónevet AddTranslation() megtalálható-e.
* Korlátozva van a helyhez, az URI-előtag alapján egy része, amely lehetővé teszi a megbízható felhasználóknak szükség esetén, tekintse meg az összes rendelkezésre álló jelöltek felhasználói felület (UI) létrehozása.

> [!NOTE]
> Mindkét a módszereket viszonylag lassú és drága. Javasoljuk, hogy takarékosan használhatja őket.

## <a name="getusertranslationcounts-method"></a>GetUserTranslationCounts módszer

Ez a módszer lekérdezi a felhasználó által létrehozott fordításainak száma. Azt a fordítási számát a uriPrefix szerint csoportosítva, a felhasználó, minRating és maxRating a kérelemben szereplő paraméterek listáját tartalmazza.

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
| appId | **Szükséges** hitelesítési fejlécéhez használata esetén hagyja üresen a appid mezőt ellenkező esetben adja meg egy karakterláncot, amely tartalmazza a "Tulajdonos" + "" + a hozzáférési jogkivonat.|
| uriPrefix | **Nem kötelező** a fordítás URI-előtag tartalmazó karakterláncot.|
| forrás: | **Nem kötelező** képviselő nyelvi kódját, a fordítás szöveges karakterláncot. |
| erre: | **Nem kötelező** karakterláncként lefordítani az a szöveg a nyelvi kódot.|
| minRating| **Nem kötelező** a minőségi besorolása a lefordított szöveg jelző egész értéket. Az érvényes értéke -10 és 10 között. Az alapértelmezett értéke 1.|
| maxRating| **Nem kötelező** a maximális minőségi besorolása a lefordított szöveg jelző egész értéket. Az érvényes értéke -10 és 10 között. Az alapértelmezett értéke 1.|
| Felhasználó | **Nem kötelező** karakterlánc, amely az eredmény szűrésére használatos a küldése a kezdeményezője alapján. |
| category| **Nem kötelező** a kategóriát vagy a tartomány a fordítás tartalmazó karakterláncot. Ez a paraméter csak az alapértelmezett beállítás általános támogatja.|
| minDateUtc| **Nem kötelező** Ha szeretné a fordítások beolvasni a dátumot. A dátum UTC formátumban kell lennie. |
| maxDateUtc| **Nem kötelező** : Ha szeretné beolvasni a fordítások dátumát. A dátum UTC formátumban kell lennie. |
| hagyja ki| **Nem kötelező** oldalon kihagyni kívánt eredmények száma. Például ha a Kihagyás eredmények és a 21 eredmény rekordból megtekintheti az első 20 sorait, adja meg az ehhez a paraméterhez 20. Ez a paraméter alapértelmezett értéke 0.|
| hajtsa végre a megfelelő | **Nem kötelező** a lekérni kívánt eredmények számát. Az egyes kérelmek maximális száma: 100. Az alapértelmezett érték 100.|

> [!NOTE]
> A skip, és hajtsa végre a megfelelő a kérelemben szereplő paraméterek engedélyezése a nagy számú rekordot tördelési.

**Visszatérési érték**

Az eredménykészlet tömbjét tartalmazza a **UserTranslationCount**. Minden egyes UserTranslationCount a következő elemeket tartalmazza:

| Mező | Leírás |
|:---|:---|
| Darabszám| A beolvasott eredmények számát|
| Ettől: | Az adatforrás nyelvi|
| Minősítés| A minősítés, amelyet a küldő AddTranslation() metódus hívásában alkalmaznak|
| Művelet| A megadott nyelv|
| URI| Az URI AddTranslation() metódus hívásában alkalmazása|
| Felhasználó| A felhasználó neve|

**Kivételek**

| Kivétel | Üzenet | Feltételek |
|:---|:---|:---|
| ArgumentOutOfRangeException | A paraméter "**maxDateUtc**"kell lennie, nagyobb vagy egyenlő"**minDateUtc**".| A paraméter értékének **maxDateUtc** korábbi, mint a paraméter értékének **minDateUtc**.|
| TranslateApiException | Az IP-cím, a kvóta keresztül.| <ul><li>A kérelmek percenkénti számát a határt.</li><li>A kérelem mérete legfeljebb 10000 karaktere marad.</li><li>Az óránkénti és naponkénti kvótájának korlátozza, amely elfogadja a Microsoft Translator API karakterek száma.</li></ul>|
| TranslateApiException | AppId kvóta felett van.| Alkalmazásazonosító az óránként vagy naponta kvóta túllépve.|

> [!NOTE]
> A kvóta fogja módosítani a szolgáltatás az összes felhasználó között fürtjében biztosításához.

**A GitHib nézet kódpéldák**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslationcounts-example-php.md)

## <a name="getusertranslations-method"></a>GetUserTranslations módszer

Ez a módszer lekéri a fordítások, a felhasználó által létrehozott. Elérhetővé teszi a fordítások szerint csoportosítva a uriPrefix származó, a, felhasználó, és minRating és maxRating a kérelemben szereplő paraméterek.

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
| appId | **Szükséges** hitelesítési fejlécéhez használata esetén hagyja üresen a appid mezőt ellenkező esetben adja meg egy karakterláncot, amely tartalmazza a "Tulajdonos" + "" + a hozzáférési jogkivonat.|
| uriPrefix| **Nem kötelező** a fordítás URI-előtag tartalmazó karakterláncot.|
| forrás:| **Nem kötelező** képviselő nyelvi kódját, a fordítás szöveges karakterláncot.|
| erre:| **Nem kötelező** karakterláncként lefordítani az a szöveg a nyelvi kódot.|
| minRating| **Nem kötelező** a minőségi besorolása a lefordított szöveg jelző egész értéket. Az érvényes értéke -10 és 10 között. Az alapértelmezett értéke 1.|
| maxRating| **Nem kötelező** a maximális minőségi besorolása a lefordított szöveg jelző egész értéket. Az érvényes értéke -10 és 10 között. Az alapértelmezett értéke 1.|
| Felhasználó| **Nem kötelező. Karakterlánc, amely alapján a küldése a kezdeményezője eredménye szűrésére használatos**|
| category| **Nem kötelező** a kategóriát vagy a tartomány a fordítás tartalmazó karakterláncot. Ez a paraméter csak az alapértelmezett beállítás általános támogatja.| 
| minDateUtc| **Nem kötelező** Ha szeretné a fordítások beolvasni a dátumot. A dátum UTC formátumban kell lennie.| 
| maxDateUtc| **Nem kötelező** : Ha szeretné beolvasni a fordítások dátumát. A dátum UTC formátumban kell lennie.|
| hagyja ki| **Nem kötelező** oldalon kihagyni kívánt eredmények száma. Például ha a Kihagyás eredmények és a 21 eredmény rekordból megtekintheti az első 20 sorait, adja meg az ehhez a paraméterhez 20. Ez a paraméter alapértelmezett értéke 0.|
| hajtsa végre a megfelelő| **Nem kötelező** a lekérni kívánt eredmények számát. Az egyes kérelmek maximális száma: 100. Az alapértelmezett érték 50.|

> [!NOTE]
> A skip, és hajtsa végre a megfelelő a kérelemben szereplő paraméterek engedélyezése a nagy számú rekordot tördelési.

**Visszatérési érték**

Az eredménykészlet tömbjét tartalmazza a **UserTranslation**. Minden egyes UserTranslation a következő elemeket tartalmazza:

| Mező | Leírás |
|:---|:---|
| CreatedDateUtc| A létrehozás dátuma AddTranslation() használja|
| Ettől:| Az adatforrás nyelvi|
| OriginalText| Az adatforrás nyelvi szöveg, amely az a kérelem elküldésekor|
|Minősítés |A minősítés, amelyet a küldő AddTranslation() metódus hívásában alkalmaznak|
|Művelet|    A megadott nyelv|
|TranslatedText|    A fordítás AddTranslation() metódus hívásában küldött|
|URI|   Az URI AddTranslation() metódus hívásában alkalmazása|
|Felhasználó   |A felhasználó neve|

**Kivételek**

| Kivétel | Üzenet | Feltételek |
|:---|:---|:---|
| ArgumentOutOfRangeException | A paraméter "**maxDateUtc**"kell lennie, nagyobb vagy egyenlő"**minDateUtc**".| A paraméter értékének **maxDateUtc** korábbi, mint a paraméter értékének **minDateUtc**.|
| TranslateApiException | Az IP-cím, a kvóta keresztül.| <ul><li>A kérelmek percenkénti számát a határt.</li><li>A kérelem mérete legfeljebb 10000 karaktere marad.</li><li>Az óránkénti és naponkénti kvótájának korlátozza, amely elfogadja a Microsoft Translator API karakterek száma.</li></ul>|
| TranslateApiException | AppId kvóta felett van.| Alkalmazásazonosító az óránként vagy naponta kvóta túllépve.|

> [!NOTE]
> A kvóta fogja módosítani a szolgáltatás az összes felhasználó között fürtjében biztosításához.

**A GitHib nézet kódpéldák**
* [C#](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-csharp.md)
* [PHP](https://github.com/MicrosoftTranslator/Documentation-Code-TextAPI/blob/master/ctf/ctf-getusertranslations-example-php.md)


















