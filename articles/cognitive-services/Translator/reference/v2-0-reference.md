---
title: Translator Text API 2.0-s verzió
titleSuffix: Azure Cognitive Services
description: A 2.0-s verziójú a Translator Text API dokumentációja.
services: cognitive-services
author: v-pawal
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: v-jansko
ms.openlocfilehash: b65182cac91f6ed3dc653d6d9e77f80e99346bb7
ms.sourcegitcommit: f093430589bfc47721b2dc21a0662f8513c77db1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/04/2019
ms.locfileid: "58918008"
---
# <a name="translator-text-api-v20"></a>Translator Text API v2.0

> [!IMPORTANT]
> A Translator Text API ezen verziója elavult. [A Translator Text API v3-as dokumentációban megtekintheti](v3-0-reference.md).

Translator Text API V2 zökkenőmentesen integrálható az alkalmazások, webhelyek, eszközöket vagy egyéb megoldásokat többnyelvű felhasználói élményt biztosít. Iparági szabványok kihasználva, használat bármely hardver platformon és bármely operációs rendszer nyelvi fordítást és egyéb nyelvi kapcsolatos műveletek, például szöveg nyelvfelismerés vagy szöveg-beszéd átalakítás végrehajtásához. A Microsoft Translator API-val kapcsolatos további részletekért kattintson ide.

## <a name="getting-started"></a>Első lépések
A Translator Text API kell eléréséhez [Regisztráljon a Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authorization"></a>Engedélyezés
Összes hívás a Translator Text API egy előfizetési kulcsot hitelesítéshez szükséges. Az API-hitelesítés két módot támogat:

* Hozzáférési token használatával. Használja az előfizetési kulcsra is hivatkoznak **lépés** 9 számára, hogy létrehozzon egy hozzáférési jogkivonatot a hitelesítési szolgáltatás, így egy POST-kérelmet. Részletek a jogkivonat-szolgáltatás dokumentációjában talál. Adja meg a hozzáférési jogkivonatot a fordító szolgáltatás, az engedélyezési fejléc vagy a access_token lekérdezési paraméter használatával. A hozzáférési jogkivonatot a 10 percig érvényes. Új hozzáférési jogkivonat beszerzése 10 percenként, és így továbbra is használhatja ugyanazt a hozzáférést token ismétlődő kérelmek ezen 10 percen belül.

* Használatával közvetlenül egy előfizetési kulcsot. Adja át az előfizetési kulcs értékként `Ocp-Apim-Subscription-Key` fejléc a kérelemmel együtt a Translator API-hoz. Ebben a módban nem kell meghívni a hitelesítési jogkivonat-szolgáltatás a hozzáférési jogkivonat létrehozásához.

Fontolja meg az előfizetési kulcs és a hozzáférési jogkivonatot, amely a nézetből el kell rejteni titkos kódként.

## <a name="profanity-handling"></a>Káromkodás kezelése
Általában a Translator szolgáltatás megőrzi a vulgáris, amely a forrásból a fordítás található. Kulturális környezetek közötti különbségeit cenzúrázása mértékét, és a környezetet, amely a szavakat profán lehetővé teszi, és ennek eredményeképpen a párhuzamossági cenzúrázása a célként megadott nyelven lehet, hogy bővíteni vagy csökkenteni.

Ha el szeretné kerülni, a fordítás, függetlenül a forrás szövegben cenzúrázása jelenléte cenzúrázása első a vulgáris szűrés a módszereket, amelyek támogatják ezt a lehetőséget is használhatja. A beállítás lehetővé teszi, hogy adja meg, hogy meg szeretné tekinteni a vulgáris törölték, vagy a megfelelő címkék megjelölve vagy hajt végre semmilyen műveletet. Az elfogadott értékek a `ProfanityAction` vannak `NoAction` (alapértelmezett), Marked és `Deleted`.


|ProfanityAction    |Műveletek |Példa forrás (japán)  |Példa fordítási (angol nyelven)  |
|:--|:--|:--|:--|
|NoAction   |Default (Alapértelmezett): Ugyanaz, mint a beállítás nem található. Káromkodás cél forrásból fogja továbbítani.        |彼はジャッカスです。     |Egy Pápaszemes áll.   |
|Megjelölve     |Profán szavak XML-címkéket kell lennie.%n <profanity> és </profanity>.     |彼はジャッカスです。 |Ő egy <profanity>Pápaszemes</profanity>.    |
|Törölve    |Profán szavakat a kimenet nélküli helyettesítő törlődni fog.     |彼はジャッカスです。 |Ő egy.   |

    
## <a name="excluding-content-from-translation"></a>Tartalom kizárása a fordítás
Amikor a címkéket például HTML-tartalom fordításával (`contentType=text/html`), egyes esetekben hasznos lehet fordítási kizárandó konkrét tartalmakat. Az attribútumot használhatja `class=notranslate` , adja meg a tartalom maradjon az eredeti nyelven. A következő példában az első belüli tartalom `div` elem nem fordítja, miközben a tartalmat, a második `div` elem lesznek fordítva.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Egy szöveges karakterlánc egyik nyelvről a másikra fordítja le.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**A visszatérési érték:** Egy karakterlánc, amely a fordítást.

Ha korábban már használt `AddTranslation` vagy `AddTranslationArray` , adja meg az 5-ös vagy újabb forrás egy mondatban, egy minősítéssel rendelkező fordítását `Translate` csak az első számú választása, amely érhető el a rendszer adja vissza. "Egy forrás mondatban" azt jelenti, hogy pontosan megegyezik (100 %-os egyeztetéséhez), kis-és nagybetűk, a térközt, a címkeértékeket és a egy mondatnál végén absztrakt. Ha nincs minősítés tárolja és 5 vagy újabb minősítést a visszaadott eredmény az automatikus fordítás a Microsoft Translator által lesz.

### <a name="response-class-status-200"></a>Válasz osztály (állapota 200)

sztring

A válasz tartalomtípusa: application/xml 

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás    |Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|alkalmazásazonosító  |(üres)    |Kötelező. Az engedélyezési vagy Ocp-Apim-Subscription-Key fejléc használata esetén hagyja üresen a appid mezőt más tartalmaznak egy karakterlánc, amely tartalmazza a "Tulajdonos" + "" + "access_token".|lekérdezés|sztring|
|szöveg|(üres)   |Kötelező. Egy karakterlánc, amely a fordítandó szöveg. A szöveg mérete nem haladhatja meg a 10000 karakternél.|lekérdezés|sztring|
|forrás:|(üres)   |Választható. Egy karakterlánc, amely a fordítandó szöveg nyelvkódja. Ha például az angol nyelvű tájékoztatáshoz en.|lekérdezés|sztring|
|erre:|(üres) |Kötelező. Egy karakterlánc, amely lefordítja a szöveget a nyelv kódja.|lekérdezés|sztring|
|contentType|(üres)    |Választható. Folyamatban van a lefordított szöveg formátuma. A támogatott formátumok a következők: text/plain (alapértelmezett) és a text/html. HTML kell lennie egy megfelelően formázott, teljes elemhez.|lekérdezés|sztring|
|category|(üres)   |Választható. A kategória (tartomány), a fordítás tartalmazó karakterlánc. Az alapértelmezett "általános".|lekérdezés|sztring|
|Engedélyezés|(üres)  |Szükséges, ha az alkalmazásazonosító mezőben, vagy Ocp-Apim-Subscription-Key fejléc nincs megadva. Engedélyezési jogkivonat:  "Tulajdonos" + "" + "access_token".|header|sztring|
|OCP-Apim-Subscription-Key|(üres)  |Szükséges, ha az alkalmazásazonosító mezőben vagy engedélyeztetési fejléc nincs megadva.|header|sztring|


### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg a hozzávetőleges dátum és idő, a kérelem és a kérés azonosítója, az X-MS-Trans-Info fejlécet tartalmazza.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Használja a `TranslateArray` metódusának segítéségével lekérheti a fordítások több forrás szövegekhez.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

A kérelem törzsének formátuma a következő legyen:

```
<TranslateArrayRequest>
  <AppId />
  <From>language-code</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" />
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" >string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</TranslateArrayRequest>
```

Elemek a `TranslateArrayRequest` vannak:


* `appid`: Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más adjon meg egy karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.
* `from`: Választható. Egy karakterlánc, amely a lefordítandó szöveget nyelvi kódot. Ha üresen a válasz tartalmazza a nyelvi automatikus észlelés eredménye.
* `options`: Választható. Egy `Options` az alább felsorolt értékeket tartalmazó objektum. Minden nem kötelező, és alapértelmezés szerint a leggyakrabban használt beállításait. A megadott elemek betűrendbe szerepelnie kell.
    - `Category`: A kategória (tartomány), a fordítás tartalmazó karakterlánc. Alapértelmezés szerint a `general`.
    - `ContentType`: Folyamatban van a lefordított szöveg formátuma. A támogatott formátumok a következők `text/plain` (alapértelmezett), `text/xml` és `text/html`. HTML kell lennie egy megfelelően formázott, teljes elemhez.
    - `ProfanityAction`: Itt adhatja meg, hogyan kezelje profanities amint azt fent kifejtettük. Engedélyezett értékei `ProfanityAction` vannak `NoAction` (alapértelmezett), `Marked` és `Deleted`.
    - `State`: Felhasználói állapot összevetését kérések és válaszok érdekében. Ugyanaz a tartalma visszatér a válaszban.
    - `Uri`: Ez az URI által eredmények szűréséhez. Alapértelmezett: `all`.
    - `User`: Ez a felhasználó eredmények szűréséhez. Alapértelmezett: `all`.
* `texts`: Kötelező. A fordítási szövegeket tartalmazó tömb. Az összes karakterláncok ugyanannak a nyelvnek kell lennie. Az összes mind a fordítandó szöveg kell legfeljebb 10000 karakterből állhat. A tömb elemek maximális számát: 2000.
* `to`: Kötelező. Egy karakterlánc, amely lefordítja a szöveget a nyelv kódja.

Nem kötelező elemek elhagyható. Elemek, amelyek közvetlen gyermekeinek TranslateArrayRequest betűrendben szerepelnie kell.

TranslateArray metódus fogad el paraméterként `application/xml` vagy `text/xml` a `Content-Type`.

**A visszatérési érték:** A `TranslateArrayResponse` tömb. Minden egyes `TranslateArrayResponse` a következő elemekből áll:

* `Error`: Jelzi a hiba, ha az egyik történt. Nem lehet null értékű.
* `OriginalSentenceLengths`: Minden egyes mondatról az eredeti forrás szöveg hosszát jelző egész számok tömbje. A hossz a tömb mondatokat számát jelzi.
* `TranslatedText`: A lefordított szöveg.
* `TranslatedSentenceLengths`: Minden egyes mondatról a lefordított szöveg hosszát jelző egész számok tömbje. A hossz a tömb mondatokat számát jelzi.
* `State`: Felhasználói állapot összevetését kérések és válaszok érdekében. Ugyanazon a tartalmon, mint a kérelem adja vissza.

A válasz törzse formátuma a következő.

```
<ArrayOfTranslateArrayResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <TranslateArrayResponse>
    <From>language-code</From>
    <OriginalTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </OriginalTextSentenceLengths>
    <State/>
    <TranslatedText>string-value</TranslatedText>
    <TranslatedTextSentenceLengths xmlns:a="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
      <a:int>int-value</a:int>
    </TranslatedTextSentenceLengths>
  </TranslateArrayResponse>
</ArrayOfTranslateArrayResponse>
```

### <a name="response-class-status-200"></a>Válasz osztály (állapota 200)
A sikeres válasz tartalmazza a tömbjét `TranslateArrayResponse` a fent ismertetett formátumban.

sztring

A válasz tartalomtípusa: application/xml

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Engedélyezés|(üres)) |Szükséges, ha az alkalmazásazonosító mezőben, vagy Ocp-Apim-Subscription-Key fejléc nincs megadva. Engedélyezési jogkivonat:  "Tulajdonos" + "" + "access_token".|header|sztring|
|OCP-Apim-Subscription-Key|(üres)|Szükséges, ha az alkalmazásazonosító mezőben vagy engedélyeztetési fejléc nincs megadva.|header|sztring|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód   |Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet. Gyakori hibák a következők: <ul><li>Nem lehet üres tömb elem</li><li>Érvénytelen kategória</li><li>A nyelvi érvénytelen</li><li>A nyelvi érvénytelen</li><li>A kérelem túl sok elemet tartalmaz</li><li>A From nyelv nem támogatott.</li><li>A címzett nyelv nem támogatott.</li><li>Fordítása kérelem túl sok adatot tartalmaz.</li><li>HTML není ve správném formátu</li><li>Túl sok karakterlánc lett átadva a kérelmek fordítása</li></ul>|
|401    |Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg a hozzávetőleges dátum és idő, a kérelem és a kérés azonosítója, az X-MS-Trans-Info fejlécet tartalmazza.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
A nyelvek rövid nevének beolvasása az átadott paraméterként `languageCodes`, és az adott régiónak a átadott területi beállítás nyelv használatával.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

A kérés törzse tartalmazza az ISO 639-1 nyelvkódokról rövid nevének lekéréséhez jelölő karakterlánc-tömbben. Példa:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**A visszatérési érték:** A Translator szolgáltatásban, a kért nyelvre honosított által támogatott nyelvek nevét tartalmazó karakterlánc-tömbben.

### <a name="response-class-status-200"></a>Válasz osztály (állapota 200)
A Translator szolgáltatásban, a kért nyelvre honosított által támogatott nyelvek nevét tartalmazó karakterlánc-tömbben.

sztring

A válasz tartalomtípusa: application/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|alkalmazásazonosító|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más adjon meg egy karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.|lekérdezés|sztring|
|területi beállítás|(üres) |Kötelező. Egy karakterlánc, amely egy ISO 639 kétbetűs kisbetűs kulturális környezet társított nyelvet és a egy ISO 3166 kétbetűs nagybetűssé szubkultúrákhoz kód megkeresni a nyelv nevét vagy a egy ISO 639 kisbetűs kulturális környezet kombinációja önmagában.|lekérdezés|sztring|
|Engedélyezés|(üres)  |Kötelező, ha az alkalmazásazonosító mezőben, vagy `Ocp-Apim-Subscription-Key` fejléc nincs megadva. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|OCP-Apim-Subscription-Key|(üres)  |Kötelező, ha az alkalmazásazonosító mezőben, vagy `Authorization` fejléc nincs megadva.|header|sztring|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg a hozzávetőleges dátum és idő, a kérelem és a kérés azonosítója, az X-MS-Trans-Info fejlécet tartalmazza.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Szerezze be a nyelvkódokról jelölő a fordítási szolgáltatás által támogatott nyelvek listáját.  `Translate` és `TranslateArray` lefordítja bármely két nyelvek között.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**A visszatérési érték:** A Translator szolgáltatás által támogatott nyelvkódok tartalmazó karakterlánc-tömbben.

### <a name="response-class-status-200"></a>Válasz osztály (állapota 200)
A Translator szolgáltatás által támogatott nyelvkódok tartalmazó karakterlánc-tömbben.

sztring

A válasz tartalomtípusa: application/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|alkalmazásazonosító|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más adjon meg egy karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.|lekérdezés|sztring|
|Engedélyezés|(üres)  |Kötelező, ha a `appid` mező vagy `Ocp-Apim-Subscription-Key` fejléc nincs megadva. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|OCP-Apim-Subscription-Key|(üres)|Kötelező, ha a `appid` mező vagy `Authorization` fejléc nincs megadva.|header|sztring|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg a hozzávetőleges dátum és idő, a kérelem és a kérés azonosítója, az X-MS-Trans-Info fejlécet tartalmazza.|
|503|A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
A beszédszintézishez elérhető nyelvek beolvasása.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**A visszatérési érték:** A beszédszintézishez a Translator szolgáltatás által támogatott nyelvkódok tartalmazó karakterlánc-tömbben.

### <a name="response-class-status-200"></a>Válasz osztály (állapota 200)
A beszédszintézishez a Translator szolgáltatás által támogatott nyelvkódok tartalmazó karakterlánc-tömbben.

sztring

A válasz tartalomtípusa: application/xml

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|alkalmazásazonosító|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más adjon meg egy karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.|lekérdezés|sztring|
|Engedélyezés|(üres)|Kötelező, ha a `appid` mező vagy `Ocp-Apim-Subscription-Key` fejléc nincs megadva. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|OCP-Apim-Subscription-Key|(üres)|Kötelező, ha a `appid` mező vagy `Authorization` fejléc nincs megadva.|header|sztring|
 
### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Ok|
|:--|:--|
|400|Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401|Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérés és a kérés azonosítója szerepel a válaszfejléc `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
A beszélt folyamatban van a kívánt nyelvet az átadott a szöveg wave vagy az mp3 adatfolyamot adja vissza.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**A visszatérési érték:** A beszélt folyamatban van a kívánt nyelvet az átadott a szöveg wave vagy az mp3 adatfolyam.

### <a name="response-class-status-200"></a>Válasz osztály (állapota 200)

Bináris

A válasz tartalomtípusa: application/xml 

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|alkalmazásazonosító|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más adjon meg egy karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.|lekérdezés|sztring|
|szöveg|(üres)   |Kötelező. Egy mondatban vagy a megadott lehet a wave Stream beszélt nyelv mondatokat tartalmazó karakterlánc. A mérnökeinkkel szöveg mérete nem haladhatja meg a 2000 karakternél hosszabb.|lekérdezés|sztring|
|language|(üres)   |Kötelező. Egy karakterlánc, amely az a szöveg felolvasása a támogatott nyelvi kódot. A kód szerepelnie kell a metódus visszatérési kódok listája `GetLanguagesForSpeak`.|lekérdezés|sztring|
|Formátum|(üres)|Választható. Egy olyan karakterláncot kell a tartalomtípus-azonosítót. Jelenleg `audio/wav` és `audio/mp3` érhetők el. Az alapértelmezett érték `audio/wav`.|lekérdezés|sztring|
|beállítások|(üres)    |<ul><li>Választható. A karakterlánc a szintetizált tulajdonságainak megadása:<li>`MaxQuality` és `MinSize` érhetők el a hang jelekkel erősségének megadása. A `MaxQuality`, beszerezheti a legjobb minőségű, valamint a beszédhangot `MinSize`, beszerezheti a beszédhangot a legkisebb méretű. Alapértelmezett érték a `MinSize`.</li><li>`female` és `male` érhetők el, adja meg a hangalapú kívánt tartalmazzák. Az alapértelmezett szint a `female`. Használja a függőleges vonal <code>\|</code> több beállításokat tartalmazza. Például `MaxQuality|Male`.</li></li></ul> |lekérdezés|sztring|
|Engedélyezés|(üres)|Kötelező, ha a `appid` mező vagy `Ocp-Apim-Subscription-Key` fejléc nincs megadva. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|OCP-Apim-Subscription-Key|(üres)  |Kötelező, ha a `appid` mező vagy `Authorization` fejléc nincs megadva.|header|sztring|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérés és a kérés azonosítója szerepel a válaszfejléc `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Használja a `Detect` azonosítására a kiválasztott adott szöveg nyelvét.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**A visszatérési érték:** A megadott szöveg két karakter nyelvi kódot tartalmazó karakterlánc. .

### <a name="response-class-status-200"></a>Válasz osztály (állapota 200)

sztring

A válasz tartalomtípusa: application/xml

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|alkalmazásazonosító|(üres)  |Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más adjon meg egy karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.|lekérdezés|sztring|
|szöveg|(üres)|Kötelező. Néhány szöveg, amelynek a nyelve, hogy azonosítható tartalmazó karakterlánc. A szöveg mérete nem haladhatja meg a 10000 karakternél.|lekérdezés| sztring|
|Engedélyezés|(üres)|Kötelező, ha a `appid` mező vagy `Ocp-Apim-Subscription-Key` fejléc nincs megadva. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|OCP-Apim-Subscription-Key  |(üres)    |Kötelező, ha a `appid` mező vagy `Authorization` fejléc nincs megadva.|header|sztring|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Ok|
|:--|:--|
|400|Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérés és a kérés azonosítója szerepel a válaszfejléc `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|


## <a name="post-detectarray"></a>/DetectArray közzététele

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Használja a `DetectArray` egyszerre azonosítására a nyelv, a karakterlánc tömbje. Minden egyes tömbelemeken független felismerése végzi, és a tömb minden egyes sorára eredményt adja vissza.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

A kérelem törzsének formátuma a következőképpen kell lennie.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

A szöveg mérete nem haladhatja meg a 10000 karakternél.

**A visszatérési érték:** A bemeneti tömb minden egyes sorához egy két karakter nyelvkódokról tartalmazó karakterlánc-tömbben.

A válasz törzse formátuma a következő.

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Válasz osztály (állapota 200)
DetectArray sikeres volt. Adja vissza a bemeneti tömb minden egyes sorához egy két karakter nyelvkódokról tartalmazó karakterlánc-tömbben.

sztring

A válasz tartalomtípusa: application/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|alkalmazásazonosító|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más adjon meg egy karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.|lekérdezés|sztring|
|Engedélyezés|(üres)|Kötelező, ha a `appid` mező vagy `Ocp-Apim-Subscription-Key` fejléc nincs megadva. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|OCP-Apim-Subscription-Key|(üres)|Kötelező, ha a `appid` nincs megadva mező vagy engedélyeztetési fejléc.|header|sztring|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg a hozzávetőleges dátum és idő, a kérelem és a kérés azonosítója, az X-MS-Trans-Info fejlécet tartalmazza.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések

> [!IMPORTANT]
> **ELAVULÁSSAL KAPCSOLATOS MEGJEGYZÉS:** 2018. január 31. után ez a módszer nem fogadja el az új mondat jelentkezés, és a egy hibaüzenetet fog kapni. Tekintse meg ezt a hirdetményt, az együttműködésen alapuló fordítási funkciók érintő változásokról.

A fordítási memória ad hozzá a fordítást.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Válasz osztály (állapota 200)

sztring

A válasz tartalomtípusa: alkalmazás: xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus   |
|:--|:--|:--|:--|:--|
|alkalmazásazonosító|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más adjon meg egy karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.|lekérdezés|sztring|
|originalText|(üres)|Kötelező. A lefordítandó szöveget tartalmazó karakterlánc. A karakterlánc maximális hossza legfeljebb 1000 karakter lehet.|lekérdezés|sztring|
|translatedText|(üres) |Kötelező. Karakterlánc, amely tartalmazza a lefordított szöveg a célként megadott nyelven. A karakterlánc maximális hossza a 2000 karakternél hosszabb.|lekérdezés|sztring|
|forrás:|(üres)   |Kötelező. Egy karakterlánc, amely a fordítandó szöveg nyelvkódja. en = angol nyelven, de = német stb...|lekérdezés|sztring|
|erre:|(üres)|Kötelező. Egy karakterlánc, amely lefordítja a szöveget a nyelv kódja.|lekérdezés|sztring|
|rating|(üres) |Választható. Ez a karakterlánc a minőségi besorolását jelző egész szám. -10- és 10 közötti értéket. Alapértelmezett értéke 1.|lekérdezés|egész szám|
|contentType|(üres)    |Választható. Folyamatban van a lefordított szöveg formátuma. A támogatott formátumok a következők: "text/plain" és "text/html". HTML kell lennie egy megfelelően formázott, teljes elemhez.   |lekérdezés|sztring|
|category|(üres)|Választható. A kategória (tartomány), a fordítás tartalmazó karakterlánc. Az alapértelmezett "általános".|lekérdezés|sztring|
|Felhasználó|(üres)|Kötelező. A Küldés létrehozója nyomon követésére használt karakterlánc.|lekérdezés|sztring|
|uri azonosító|(üres)|Választható. A tartalom helyét a fordítási tartalmazó karakterlánc.|lekérdezés|sztring|
|Engedélyezés|(üres)|Kötelező, ha az alkalmazásazonosító mezőben, vagy `Ocp-Apim-Subscription-Key` fejléc nincs megadva. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.    |header|sztring|
|OCP-Apim-Subscription-Key|(üres)|Kötelező, ha a `appid` mező vagy `Authorization` fejléc nincs megadva.|header|sztring|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|410|AddTranslation már nem támogatott.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg a hozzávetőleges dátum és idő, a kérelem és a kérés azonosítója, az X-MS-Trans-Info fejlécet tartalmazza.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések

> [!IMPORTANT]
> **ELAVULÁSSAL KAPCSOLATOS MEGJEGYZÉS:** 2018. január 31. után ez a módszer nem fogadja el az új mondat jelentkezés, és a egy hibaüzenetet fog kapni. Tekintse meg ezt a hirdetményt, az együttműködésen alapuló fordítási funkciók érintő változásokról.

Hozzáadja a fordítások fordítási memóriáját tömbjét. Egy tömb verziója `AddTranslation`.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

A kérelem törzsének formátuma a következő.

```
<AddtranslationsRequest>
  <AppId></AppId>
  <From>A string containing the language code of the source language</From>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
  </Options>
  <To>A string containing the language code of the target language</To>
  <Translations>
    <Translation xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
      <OriginalText>string-value</OriginalText>
      <Rating>int-value</Rating>
      <Sequence>int-value</Sequence>
      <TranslatedText>string-value</TranslatedText>
    </Translation>
  </Translations>
</AddtranslationsRequest>
```

Az AddtranslationsRequest elemben található elemek a következők:

* `AppId`: Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más adjon meg egy karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.
* `From`: Kötelező. A Forrásnyelv nyelvkódja tartalmazó karakterlánc. A nyelv által visszaadott egyikének kell lennie a `GetLanguagesForTranslate` metódust.
* `To`: Kötelező. A Célnyelv nyelvkódja tartalmazó karakterlánc. A nyelv által visszaadott egyikének kell lennie a `GetLanguagesForTranslate` metódust.
* `Translations`: Kötelező. Fordítások fordítási memória hozzáadása tömbje. Minden egyes fordítási tartalmaznia kell: originalText, translatedText és minősítés. Minden egyes originalText és translatedText mérete legfeljebb 1000 karakter. Az összes a originalText(s) és translatedText(s) kell legfeljebb 10000 karakterből állhat. A tömb elemeinek maximális száma pedig a 100.
* `Options`: Kötelező. Lehetőségeket, ideértve a kategória, ContentType, Uri és felhasználói csoportja. Felhasználó megadása kötelező. Kategória, ContentType és Uri megadása nem kötelező. A megadott elemek betűrendbe szerepelnie kell.

### <a name="response-class-status-200"></a>Válasz osztály (állapota 200)
AddTranslationArray metódus sikeres volt. 2018. január 31. után mondat módosítások nem lesznek elfogadva. A szolgáltatás válaszol, és 410-es hibakód.

sztring

A válasz tartalomtípusa: application/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Engedélyezés|(üres)|Szükséges, ha az alkalmazásazonosító mezőben, vagy Ocp-Apim-Subscription-Key fejléc nincs megadva. Engedélyezési jogkivonat:  "Tulajdonos" + "" + "access_token".|header|sztring|
|OCP-Apim-Subscription-Key|(üres)|Szükséges, ha az alkalmazásazonosító mezőben vagy engedélyeztetési fejléc nincs megadva.|header|sztring|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|410    |AddTranslation már nem támogatott.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérés és a kérés azonosítója szerepel a válaszfejléc `X-MS-Trans-Info`.|
|503|A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Egy adott szöveg bontja a mondatok és a hossza az egyes mondatban tartalmazó tömböt ad vissza.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**A visszatérési érték:** A mondatok hosszának jelölő egész számok tömbje. A hossz a tömb mondatok számát, és az értékek a következők minden mondat hosszát.

### <a name="response-class-status-200"></a>Válasz osztály (állapota 200)
A mondatok hosszának jelölő egész számok tömbje. A hossz a tömb mondatok számát, és az értékek a következők minden mondat hosszát.

egész szám

A válasz tartalomtípusa: application/xml 

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|alkalmazásazonosító|(üres)  |Kötelező. Az engedélyezési vagy Ocp-Apim-Subscription-Key fejléc használata esetén hagyja üresen a appid mezőt más tartalmaznak egy karakterlánc, amely tartalmazza a "Tulajdonos" + "" + "access_token".|lekérdezés| sztring|
|szöveg|(üres)   |Kötelező. Szöveg felosztása a mondatok képviselő karakterláncot. A szöveg mérete nem haladhatja meg a 10000 karakternél.|lekérdezés|sztring|
|language   |(üres)    |Kötelező. Egy karakterlánc, amely a bemeneti szöveg nyelvkódja.|lekérdezés|sztring|
|Engedélyezés|(üres)|Szükséges, ha az alkalmazásazonosító mezőben, vagy Ocp-Apim-Subscription-Key fejléc nincs megadva. Engedélyezési jogkivonat:  "Tulajdonos" + "" + "access_token".    |header|sztring|
|OCP-Apim-Subscription-Key|(üres)|Szükséges, ha az alkalmazásazonosító mezőben vagy engedélyeztetési fejléc nincs megadva.|header|sztring|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Ok|
|:--|:--|
|400|Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401|Érvénytelen hitelesítő adatok|
|500|Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg a hozzávetőleges dátum és idő, a kérelem és a kérés azonosítója, az X-MS-Trans-Info fejlécet tartalmazza.|
|503|A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Egy adott nyelven párhoz fordítások tömbjét lekéri a tároló és a fő Célkiszolgáló motor. GetTranslations eltér a fordítás, az összes rendelkezésre álló fordítások adja vissza.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

A kérelem törzse tartalmazza a választható TranslationOptions objektum, amely a következő formátumot.

```
<TranslateOptions xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">
  <Category>string-value</Category>
  <ContentType>text/plain</ContentType>
  <ReservedFlags></ReservedFlags>
  <State>int-value</State>
  <Uri>string-value</Uri>
  <User>string-value</User>
</TranslateOptions>
```

A `TranslateOptions` objektum tartalmazza az alább felsorolt értékeket. Minden nem kötelező, és alapértelmezés szerint a leggyakrabban használt beállításait. A megadott elemek betűrendbe szerepelnie kell.

* `Category`: A kategória (tartomány), a fordítás tartalmazó karakterlánc. Az alapértelmezett "általános".
* `ContentType`: Az egyetlen támogatott, és az alapértelmezett beállítás "text/plain".
* `IncludeMultipleMTAlternatives`: határozza meg, hogy egynél több alternatívák vissza kell a fő Célkiszolgáló motor logikai kapcsolóval. Érvényes értékek: true és false (kis-és nagybetűket). Alapértelmezés a False (hamis), és csak 1 alternatív tartalmazza. A jelző IGAZ lehetővé teszi a mesterséges alternatív megoldások létrehozása a fordítás, teljes mértékben integráltuk az együttműködésen alapuló fordítások framework (CTF). A funkció lehetővé teszi, hogy visszaadó alternatíva a mondatokat, amelyek nem alternatívák CTF, a mesterséges alternatív megoldások hozzáadásával a dekóder legjobb n listájából.
    - A besorolások a minősítések alkalmazza az alábbiak szerint: 1.) a legjobb automatikus fordítás, 5 besorolású. (2) a alternatíva a CTF tükrözik a felülvizsgáló, a -10 + 10 felügyelete. (3) az automatikusan generált (n-a legjobb) fordítási lehetőségeket 0 minősítést, és egy 100 egyezés mértékét rendelkeznek.
    - Alternatívák a visszaadott alternatív megoldások száma legfeljebb maxTranslations, de kevesebb is lehet száma.
    - Nyelvi párok Ez a funkció nem érhető el a fordítások közötti egyszerűsített és hagyományos kínai, mindkét irányban. Minden más támogatott Microsoft Translator nyelvi párok érhető el.
* `State`: Felhasználói állapot összevetését kérések és válaszok érdekében. Ugyanaz a tartalma visszatér a válaszban.
* `Uri`: Ez az URI által eredmények szűréséhez. Ha nincs érték van beállítva, az alapértelmezett érték az összes.
* `User`: Ez a felhasználó eredmények szűréséhez. Ha nincs érték van beállítva, az alapértelmezett érték az összes.

Kérelem `Content-Type` lehet `text/xml`.

**A visszatérési érték:** A válasz formátuma a következő.

```
<GetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"
  xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <From>Two character language code</From>
  <State/>
  <Translations>
    <TranslationMatch>
      <Count>int-value</Count>
      <MatchDegree>int-value</MatchDegree>
      <MatchedOriginalText/>
      <Rating>int value</Rating>
      <TranslatedText>string-value</TranslatedText>
    </TranslationMatch>
  </Translations>
</GetTranslationsResponse>
```

Ez magában foglalja egy `GetTranslationsResponse` elem, amely tartalmazza a következő értékeket:

* `Translations`: Az egyezések tömb található, TranslationMatch (lásd alább) objektumok tárolja. A fordítások enyhe változatának az eredeti szöveg (intelligens egyező) tartalmazhat. A fordítások rendezése: 100 %-os intelligens megfelel az alábbi először illeszkedik.
* `From`: Ha a metódus nem adta meg egy From nyelvet, ez lesz az automatikus nyelvfelismerést eredményét. Ellenkező esetben lesz az adott nyelv.
* `State`: Felhasználói állapot összevetését kérések és válaszok érdekében. A TranslateOptions paraméterben megadott ugyanazt az értéket tartalmazza.

TranslationMatch objektum a következőkből áll:

* `Error`: Hiba történt egy adott a bemeneti karakterláncot keres, ha a hiba kódja tárolódik. Ellenkező esetben a mező üres lesz.
* `MatchDegree`: A rendszer megfelel a tárolóban, többek között a pontatlan egyezések bemeneti mondatokat.  MatchDegree azt jelzi, különös figyelmet fordítanak a bemeneti szöveg megfelel-e az eredeti szöveg található a tárban. 100 által visszaadott érték címtartományok 0 és 100, ahol 0 a nincs hasonlóság és kis-és nagybetűket pontosan egyezik.
MatchedOriginalText: Eredeti szöveg, amely ezt az eredményt a megfelelést. Csak adott vissza, ha az egyeztetett eredeti szöveg volt eltér a bemeneti szöveg. Használja az intelligens egyeztetésű forrás szöveget adja vissza. A Microsoft Translator eredmény nem adott vissza.
* `Rating`: Azt jelzi, hogy a szolgáltató minőségi megugró személy. Gépi fordítás eredmények 5 minősítést kap. Névtelenül megadott fordítások általában a minősítés 1-4 fog rendelkezni, miközben mérvadó megadott fordítások általában fog rendelkezni a 6 – 10 minősítést.
* `Count`: Hányszor az ilyen minősítést kapott fordítását lett kiválasztva. Az érték 0 a automatikusan lefordított válasz lesz.
* `TranslatedText`: A lefordított szöveg.

### <a name="response-class-status-200"></a>Válasz osztály (állapota 200)
A `GetTranslationsResponse` objektum a fent ismertetett formátumban.

sztring

A válasz tartalomtípusa: application/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|alkalmazásazonosító|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc, hagyja üresen a appid mezőt más adjon meg egy karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.|lekérdezés|sztring|
|szöveg|(üres)|Kötelező. Egy karakterlánc, amely a fordítandó szöveg. A szöveg mérete nem haladhatja meg a 10000 karakternél.|lekérdezés|sztring|
|forrás:|(üres)|Kötelező. Egy karakterlánc, amely a fordítandó szöveg nyelvkódja.|lekérdezés|sztring|
|erre: |(üres)    |Kötelező. Egy karakterlánc, amely lefordítja a szöveget a nyelv kódja.|lekérdezés|sztring|
|maxTranslations|(üres)|Kötelező. Egy egész számot a fordítások maximális számát jelölő való visszatéréshez.|lekérdezés|egész szám|
|Engedélyezés| (üres)|Kötelező, ha a `appid` mező vagy `Ocp-Apim-Subscription-Key` fejléc nincs megadva. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|sztring| header|
|OCP-Apim-Subscription-Key|(üres)  |Kötelező, ha a `appid` mező vagy `Authorization` fejléc nincs megadva.|header|sztring|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérés és a kérés azonosítója szerepel a válaszfejléc `X-MS-Trans-Info`.|
|503|A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Használja a `GetTranslationsArray` metódussal lehet bekérni több forrás szövegek több fordítási a deduplikációra.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

A kérelem törzsének formátuma a következő.

```
<GetTranslationsArrayRequest>
  <AppId></AppId>
  <From>language-code</From>
  <MaxTranslations>int-value</MaxTranslations>
  <Options>
    <Category xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Category>
    <ContentType xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">text/plain</ContentType>
    <ReservedFlags xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2"/>
    <State xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">int-value</State>
    <Uri xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</Uri>
    <User xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2">string-value</User>
  </Options>
  <Texts>
    <string xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">string-value</string>
  </Texts>
  <To>language-code</To>
</GetTranslationsArrayRequest>
```

`GetTranslationsArrayRequest` az alábbi elemeket tartalmazza:

* `AppId`: Kötelező. Engedélyeztetési fejléc használata esetén a appid mezőt üresen hagyja más tartalmazza a karakterláncot tartalmazó `"Bearer" + " " + "access_token"`.
* `From`: Kötelező. Egy karakterlánc, amely a fordítandó szöveg nyelvkódja.
* `MaxTranslations`: Kötelező. Egy egész számot a fordítások maximális számát jelölő való visszatéréshez.
* `Options`: Választható. Az alább felsorolt értékeket tartalmazó beállítások objektum. Minden nem kötelező, és alapértelmezés szerint a leggyakrabban használt beállításait. A megadott elemek betűrendbe szerepelnie kell.
    - Kategória ": A kategória (tartomány), a fordítás tartalmazó karakterlánc. Az alapértelmezett általános.
    - `ContentType`: Az egyetlen támogatott, és az alapértelmezett beállítás egyszerű szöveg.
    - `IncludeMultipleMTAlternatives`: határozza meg, hogy egynél több alternatívák vissza kell a fő Célkiszolgáló motor logikai kapcsolóval. Érvényes értékek: true és false (kis-és nagybetűket). Alapértelmezés a False (hamis), és csak 1 alternatív tartalmazza. A jelző IGAZ lehetővé teszi a mesterséges alternatív megoldások létrehozása a fordítás, teljes mértékben integráltuk az együttműködésen alapuló fordítások framework (CTF). A funkció lehetővé teszi, hogy visszaadó alternatíva a mondatokat, amelyek nem alternatívák CTF, a mesterséges alternatív megoldások hozzáadásával a dekóder legjobb n listájából.
        - A besorolások a minősítések alkalmazza az alábbiak szerint: 1.) a legjobb automatikus fordítás, 5 besorolású. (2) a alternatíva a CTF tükrözik a felülvizsgáló, a -10 + 10 felügyelete. (3) az automatikusan generált (n-a legjobb) fordítási lehetőségeket 0 minősítést, és egy 100 egyezés mértékét rendelkeznek.
        - Alternatívák a visszaadott alternatív megoldások száma legfeljebb maxTranslations, de kevesebb is lehet száma.
        - Nyelvi párok Ez a funkció nem érhető el a fordítások közötti egyszerűsített és hagyományos kínai, mindkét irányban. Minden más támogatott Microsoft Translator nyelvi párok érhető el.
* `State`: Felhasználói állapot összevetését kérések és válaszok érdekében. Ugyanaz a tartalma visszatér a válaszban.
* `Uri`: Ez az URI által eredmények szűréséhez. Ha nincs érték van beállítva, az alapértelmezett érték az összes.
* `User`: Ez a felhasználó eredmények szűréséhez. Ha nincs érték van beállítva, az alapértelmezett érték az összes.
* `Texts`: Kötelező. A fordítási szövegeket tartalmazó tömb. Az összes karakterláncok ugyanannak a nyelvnek kell lennie. Az összes mind a fordítandó szöveg kell legfeljebb 10000 karakterből állhat. A tömb elemeinek maximális száma 10.
* `To`: Kötelező. Egy karakterlánc, amely lefordítja a szöveget a nyelv kódja.

Nem kötelező elemek elhagyható. Elemek, amelyek közvetlen gyermekeinek `GetTranslationsArrayRequest` betűrendben szerepelnie kell.

Kérelem `Content-Type` lehet `text/xml`.

**A visszatérési érték:** A válasz formátuma a következő.

```
<ArrayOfGetTranslationsResponse xmlns="http://schemas.datacontract.org/2004/07/Microsoft.MT.Web.Service.V2" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <GetTranslationsResponse>
    <From>language-code</From>
    <State/>
    <Translations>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText>string-value</MatchedOriginalText>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
      <TranslationMatch>
        <Count>int-value</Count>
        <MatchDegree>int-value</MatchDegree>
        <MatchedOriginalText/>
        <Rating>int-value</Rating>
        <TranslatedText>string-value</TranslatedText>
      </TranslationMatch>
    </Translations>
  </GetTranslationsResponse>
</ArrayOfGetTranslationsResponse>
```

Minden egyes `GetTranslationsResponse` elem tartalmazza a következő értékeket:

* `Translations`: Egyezések tömbjét található, a tárolt `TranslationMatch` objektumok (lásd alább). A fordítások enyhe változatának az eredeti szöveg (intelligens egyező) tartalmazhat. A fordítások rendezése: 100 %-os intelligens megfelel az alábbi először illeszkedik.
* `From`: Ha nem adta meg a metódus egy `From` nyelv, ez lesz az automatikus nyelvfelismerést eredményét. Ellenkező esetben lesz az adott nyelv.
* `State`: Felhasználói állapot összevetését kérések és válaszok érdekében. A ugyanazt az értéket tartalmazza a `TranslateOptions` paraméter.

`TranslationMatch` objektum a következőkből áll:
* `Error`: Hiba történt egy adott a bemeneti karakterláncot keres, ha a hiba kódja tárolódik. Ellenkező esetben a mező üres lesz.
* `MatchDegree`: A rendszer megfelel a tárolóban, többek között a pontatlan egyezések bemeneti mondatokat.  `MatchDegree` azt jelzi, különös figyelmet fordítanak a bemeneti szöveg megfelel-e az eredeti szöveg található a tárban. 100 által visszaadott érték címtartományok 0 és 100, ahol 0 a nincs hasonlóság és kis-és nagybetűket pontosan egyezik.
* `MatchedOriginalText`: Eredeti szöveg, amely ezt az eredményt a megfelelést. Csak adott vissza, ha az egyeztetett eredeti szöveg volt eltér a bemeneti szöveg. Használja az intelligens egyeztetésű forrás szöveget adja vissza. A Microsoft Translator eredmény nem adott vissza.
* `Rating`: Azt jelzi, hogy a szolgáltató minőségi megugró személy. Gépi fordítás eredmények 5 minősítést kap. Névtelenül megadott fordítások általában a minősítés 1-4 fog rendelkezni, miközben mérvadó megadott fordítások általában fog rendelkezni a 6 – 10 minősítést.
* `Count`: Hányszor az ilyen minősítést kapott fordítását lett kiválasztva. Az érték 0 a automatikusan lefordított válasz lesz.
* `TranslatedText`: A lefordított szöveg.


### <a name="response-class-status-200"></a>Válasz osztály (állapota 200)

sztring

A válasz tartalomtípusa: application/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Engedélyezés  |(üres)    |Kötelező, ha a `appid` mező vagy `Ocp-Apim-Subscription-Key` fejléc nincs megadva. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|sztring|
|OCP-Apim-Subscription-Key|(üres)  |Kötelező, ha a `appid` mező vagy `Authorization` fejléc nincs megadva.|header|sztring|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Ok|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérés és a kérés azonosítója szerepel a válaszfejléc `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [V3 Translator Text API áttelepítése](../migrate-to-v3.md)










