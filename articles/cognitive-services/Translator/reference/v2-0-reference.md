---
title: Translator Text API v2.0
titleSuffix: Azure Cognitive Services
description: A Translator Text API 2.0-s verziójú dokumentációja.
services: cognitive-services
author: swmachan
manager: nitinme
ms.service: cognitive-services
ms.subservice: translator-text
ms.topic: reference
ms.date: 05/15/2018
ms.author: swmachan
ms.openlocfilehash: a29e123c44ca198ce19db451ee4c624b6f993538
ms.sourcegitcommit: c105ccb7cfae6ee87f50f099a1c035623a2e239b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/09/2019
ms.locfileid: "67705428"
---
# <a name="translator-text-api-v20"></a>Translator Text API v2.0

> [!IMPORTANT]
> A Translator Text API ezen verziója elavult. [A Translator Text API-verzió 3 dokumentáció megtekintése](v3-0-reference.md).

A Translator Text API 2-es verziója is zökkenőmentesen integrálható az alkalmazások, webhelyek, eszközöket vagy más megoldásokkal, valamint a többnyelvű felhasználói élményt biztosít. Használhatja azt bármely hardver platformon és bármely operációs rendszer nyelvi fordítást és egyéb nyelvi megfelelőséggel kapcsolatos feladatok, például SMS-nyelv észlelése és a szöveg-beszéd átalakítás, iparági szabványoknak megfelelően. További információkért lásd: [a Translator Text API](../translator-info-overview.md).

## <a name="getting-started"></a>Első lépések
A Translator Text API eléréséhez kell [Regisztráljon a Microsoft Azure](../translator-text-how-to-signup.md).

## <a name="authentication"></a>Hitelesítés 
Összes hívás a Translator Text API egy előfizetési kulcsot hitelesítéshez szükséges. Az API-hitelesítés három módszereket támogatja:

- Hozzáférési jogkivonat. Az előfizetési kulcsot a 9. lépés hivatkozott használatával hozzon létre egy hozzáférési jogkivonatot a hitelesítési szolgáltatás, így egy POST kérést. Részletek a jogkivonat-szolgáltatás dokumentációjában talál. A hozzáférési jogkivonat át a Translator szolgáltatás használatával a `Authorization` fejléc vagy a `access_token` lekérdezési paraméter. A hozzáférési jogkivonatot a 10 percig érvényes. Új hozzáférési jogkivonat beszerzése 10 percenként, és így továbbra is használhatja ugyanazt a hozzáférést az ismételt kérések jogkivonat a 10 perc alatt.
- Egy előfizetési kulcsot közvetlenül használni. Adja át az előfizetési kulcs értékként a `Ocp-Apim-Subscription-Key` foglalt kérelmét a Translator Text API-fejléc. Ha közvetlenül az előfizetési kulcsot használ, nincs, hozzon létre egy hozzáférési jogkivonatot a jogkivonat-hitelesítés szolgáltatás hívásához.
- Egy [több szolgáltatást az Azure Cognitive Services-előfizetés](https://azure.microsoft.com/pricing/details/cognitive-services/). Ez a módszer lehetővé teszi, hogy egyetlen titkos kulcs több szolgáltatás-kérelmek hitelesítéséhez.
Több szolgáltatást a titkos kulcs használata, ha például két hitelesítési fejléceket a kérelmét kell. Az első fejléc adja meg a titkos kulcsot. A második fejléc megadja az előfizetéshez társított régió:
   - `Ocp-Apim-Subscription-Key`
   - `Ocp-Apim-Subscription-Region`

A régió szükség a több szolgáltatásos Text API-előfizetésre. A választott régió az egyedüli olyan régió, több szolgáltatásos előfizetési kulcsát használatakor szövegfordítás használható. Kell lennie az előfizetéséhez több szolgáltatást az Azure Portal webhelyen való regisztráció során kiválasztott ugyanabban a régióban.

A választható régiók a következők `australiaeast`, `brazilsouth`, `canadacentral`, `centralindia`, `centraluseuap`, `eastasia`, `eastus`, `eastus2`, `japaneast`, `northeurope`, `southcentralus`, `southeastasia`, `uksouth`, `westcentralus`, `westeurope`, `westus`, és `westus2`.

Az előfizetési kulcs és a hozzáférési token titkos kódok, amelyek nézetből el kell rejteni vannak.

## <a name="profanity-handling"></a>Káromkodás kezelése
A Translator szolgáltatás normális esetben megőrzi az cenzúrázása a forrásban megtalálható. A fokú cenzúrázása és a környezetet, amely a szavakat profán lehetővé teszi a kulturális környezet eltérőek. Ezért a cél nyelven cenzúrázása fokú sikerült kell növelhető vagy csökkenthető.

Ha azt szeretné, hogy a fordítás vulgáris, akkor is, ha a forrás szöveg van, használhatja a vulgáris szűrés a módszereket, amelyek támogatják ezt a beállítást. A beállítás lehetővé teszi, hogy válassza ki, hogy törölték, vagy a megfelelő címkék megjelölve cenzúrázása megtekintéséhez, vagy hogy szeretné-e a cél a vulgáris való használatának engedélyezése. Az elfogadott értékek a `ProfanityAction` vannak `NoAction` (alapértelmezett), `Marked`, és `Deleted`.


|ProfanityAction    |Műveletek |Példa forrás (japán)  |Példa fordítási (angol nyelven)  |
|:--|:--|:--|:--|
|NoAction   |Default (Alapértelmezett): Ugyanaz, mint a beállítás nem található. Káromkodás cél forrásból fogja továbbítani.        |彼はジャッカスです。     |Egy Pápaszemes áll.   |
|Megjelölve     |Profán szavak XML-címkéket kell lennie.%n \<cenzúrázása > és \</profanity >.       |彼はジャッカスです。 |Ő egy \<cenzúrázása > Pápaszemes\</profanity >.  |
|Törölve    |Profán szavakat a kimenet nélküli helyettesítő törlődni fog.     |彼はジャッカスです。 |Ő egy.   |

    
## <a name="excluding-content-from-translation"></a>Tartalom kizárása a fordítás
Ha Ön fordítása címkéket, például HTML-tartalom (`contentType=text/html`), egyes esetekben hasznos lehet a fordítás kizárandó konkrét tartalmakat. Az attribútum is használhat `class=notranslate` , adja meg a tartalom maradjon az eredeti nyelven. A következő példában a tartalmat az első `div` elem nem fordítható le, de a tartalom a második `div` elem lesznek fordítva.

```HTML
<div class="notranslate">This will not be translated.</div>
<div>This will be translated. </div>
```

## <a name="get-translate"></a>GET /Translate

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Egy szöveges karakterlánc egyik nyelvről a másikra fordítja le.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/Translate`.

**A visszatérési érték:** A lefordított szöveg jelölő karakterláncot.

Ha korábban már használt `AddTranslation` vagy `AddTranslationArray` , adja meg az 5-ös vagy újabb forrás egy mondatban, egy minősítéssel rendelkező fordítását `Translate` csak az első számú választása, amely érhető el a rendszer adja vissza. "Egy forrás mondatban" azt jelenti, hogy pontosan az azonos (100 %-os egyeztetéséhez), kis-és nagybetűk, a térközt, a címkeértékeket és a egy mondatnál végén absztrakt. Ha nincs minősítés 5 vagy újabb minősítéssel rendelkező, a visszaadott eredmény lesz az automatikus fordítás a Microsoft Translator által.

### <a name="response-class-status-200"></a>Válasz osztály (200-as állapot)

string

A válasz tartalomtípusa: application/xml

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás    |Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|appid  |(üres)    |Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc használata esetén hagyja meg az `appid` mezője üres. Ellenkező esetben a tartalmazó karakterláncot tartalmaznak `"Bearer" + " " + "access_token"`.|query|string|
|szöveg|(üres)   |Kötelező. A fordítandó szöveg jelölő karakterláncot. A szöveg nem tartalmazhat több mint 10 000 karakternél.|query|string|
|from|(üres)   |Választható. Folyamatban van a lefordított szöveg nyelvkódja jelölő karakterláncot. Ha például az angol nyelvű tájékoztatáshoz en.|query|string|
|erre:|(üres) |Kötelező. A kód fordítása a szöveget, a nyelv jelölő karakterláncot.|query|string|
|contentType|(üres)    |Választható. Folyamatban van a lefordított szöveg formátuma. Támogatott formátumok a következők `text/plain` (alapértelmezett), és `text/html`. Helytelen formátumú, teljes körű elemek bármely HTML-elemek kell.|query|string|
|category|(üres)   |Választható. A kategória (tartomány), a fordítás tartalmazó karakterlánc. A mező alapértelmezett értéke: `general`.|query|string|
|Engedélyezés|(üres)  |Kötelező, ha mindkét a `appid` mezőt és a `Ocp-Apim-Subscription-Key` fejléc üres marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|string|
|OCP-Apim-Subscription-Key|(üres)  |Kötelező, ha mindkét a `appid` mezőt és a `Authorization` fejléc üres marad.|header|string|


### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Reason|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérés és a kérés azonosítója szerepel a válaszfejléc `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="post-translatearray"></a>POST /TranslateArray

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Fordítások több forrás szövegekhez kérdezi le.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/TranslateArray`.

A kérelem törzsének formátuma a következő:

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

Ezek az elemek szerepelnek `TranslateArrayRequest`:


* `AppId`: Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc használata esetén hagyja meg az `AppId` mezője üres. Ellenkező esetben a tartalmazó karakterláncot tartalmaznak `"Bearer" + " " + "access_token"`.
* `From`: Választható. Folyamatban van a lefordított szöveg nyelvkódja jelölő karakterláncot. Ez a mező üres, ha a válasz tartalmazza a eredményét az automatikus nyelvfelismerést.
* `Options`: Választható. Egy `Options` objektum, amely a következő értékeket tartalmazza. Azok az összes opcionális és a leggyakrabban használt beállításai az alapértelmezett. A megadott elemek betűrendbe szerepelnie kell.
    - `Category`: A kategória (tartomány), a fordítás tartalmazó karakterlánc. A mező alapértelmezett értéke: `general`.
    - `ContentType`: Folyamatban van a lefordított szöveg formátuma. A támogatott formátumok a következők `text/plain` (alapértelmezett), `text/xml`, és `text/html`. Helytelen formátumú, teljes körű elemek bármely HTML-elemek kell.
    - `ProfanityAction`: Itt adható meg, hogyan kezelje profanities, korábban leírtak. Elfogadott értékek a következők `NoAction` (alapértelmezett), `Marked`, és `Deleted`.
    - `State`: Felhasználói állapot korrelációját, ha a kérés-válasz érdekében. A válasz ugyanahhoz a tartalomhoz lesznek visszaadva.
    - `Uri`: Ez az URI által eredmények szűréséhez. Alapértelmezett: `all`.
    - `User`: Ez a felhasználó eredmények szűréséhez. Alapértelmezett: `all`.
* `Texts`: Kötelező. A fordítás szöveget tartalmazó tömb. Az összes karakterláncok ugyanazt a nyelvet kell lennie. Minden szöveget lehet az összes nem lehet hosszabb 10 000 karakternél. A tömb elemeinek maximális száma 2000.
* `To`: Kötelező. A kód fordítása a szöveget, a nyelv jelölő karakterláncot.

Nem kötelező elemek, akkor kihagyhatja. Elemek közvetlen gyermekeinek `TranslateArrayRequest` betűrendben szerepelnie kell.

A `TranslateArray` metódus fogad el paraméterként `application/xml` vagy `text/xml` a `Content-Type`.

**A visszatérési érték:** A `TranslateArrayResponse` tömb. Minden egyes `TranslateArrayResponse` ezeket az elemeket tartalmaz:

* `Error`: Hiba azt jelzi, ha történik egy. Nem lehet null értékű.
* `OriginalSentenceLengths`: Egész számok tömbje, amely azt jelzi, hogy minden mondat a forrás szöveg hosszát. A hossz a tömb mondatokat számát jelzi.
* `TranslatedText`: A lefordított szöveg.
* `TranslatedSentenceLengths`: Egész számok tömbje, amely azt jelzi, hogy minden egyes mondatról a lefordított szöveg hosszát. A hossz a tömb mondatokat számát jelzi.
* `State`: Felhasználói állapot korrelációját, ha a kérés-válasz érdekében. A kérelem ugyanahhoz a tartalomhoz adja vissza.

A válasz törzse formátuma a következő:

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

### <a name="response-class-status-200"></a>Válasz osztály (200-as állapot)
A sikeres válasz tartalmazza a tömbjét `TranslateArrayResponse` tömbök korábban ismertetett formátumban.

string

A válasz tartalomtípusa: application/xml

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Engedélyezés|(üres)  |Kötelező, ha mindkét a `appid` mezőt és a `Ocp-Apim-Subscription-Key` fejléc üres marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|string|
|OCP-Apim-Subscription-Key|(üres)|Kötelező, ha mindkét a `appid` mezőt és a `Authorization` fejléc üres marad.|header|string|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód   |Reason|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet. Gyakori hibák a következők: <ul><li>Tömb elem nem lehet üres.</li><li>Érvénytelen kategória.</li><li>A nyelv nem érvényes.</li><li>A nyelv nem érvényes.</li><li>A kérelem túl sok elemet tartalmaz.</li><li>A From nyelv nem támogatott.</li><li>A címzett nyelv nem támogatott.</li><li>Fordítása kérelem túl sok adatot tartalmaz.</li><li>HTML nem a megfelelő formátumban van.</li><li>Túl sok karakterlánc lett átadva a lefordítja a kérelmet.</li></ul>|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérés és a kérés azonosítója szerepel a válaszfejléc `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="post-getlanguagenames"></a>POST /GetLanguageNames

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Lekéri a nyelvek rövid nevének átadott paraméterként `languageCodes`, az átadott be honosított `locale` nyelv.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/GetLanguageNames`.

A kérés törzse tartalmazza az ISO 639-1 nyelvkódokról, amelynek meg szeretné beolvasni a felhasználóbarát nevek jelölő karakterlánc-tömbben. Például:

```
<ArrayOfstring xmlns:i="https://www.w3.org/2001/XMLSchema-instance"  xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>zh</string>
    <string>en</string>
</ArrayOfstring>
```

**A visszatérési érték:** A Translator szolgáltatásban, a kért nyelvre honosított által támogatott nyelvek nevét tartalmazó karakterlánc-tömbben.

### <a name="response-class-status-200"></a>Válasz osztály (200-as állapot)
A Translator szolgáltatásban, a kért nyelvre honosított által támogatott nyelvek nevét tartalmazó karakterlánc-tömbben.

string

A válasz tartalomtípusa: application/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|appid|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc használata esetén hagyja meg az `appid` mezője üres. Ellenkező esetben a tartalmazó karakterláncot tartalmaznak `"Bearer" + " " + "access_token"`.|query|string|
|Területi beállítás|(üres) |Kötelező. A nyelv nevének honosításához használt jelölő a következők egyikét: <ul><li>Egy nyelv társított ISO 639-kétbetűs kisbetűs kulturális környezet kód és a egy ISO 3166 kétbetűs nagybetűssé szubkultúrákhoz kódot kombinációja. <li>Egy önálló ISO 639 kisbetűs kulturális környezet kódot.|query|string|
|Engedélyezés|(üres)  |Kötelező, ha mindkét a `appid` mezőt és a `Ocp-Apim-Subscription-Key` fejléc üres marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|string|
|OCP-Apim-Subscription-Key|(üres)  |Kötelező, ha mindkét a `appid` mezőt és a `Authorization` fejléc üres marad.|header|string|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Reason|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérés és a kérés azonosítója szerepel a válaszfejléc `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="get-getlanguagesfortranslate"></a>GET /GetLanguagesForTranslate

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Nyelvi kódok, amelyek a fordítási szolgáltatás által támogatott nyelvek listáját kéri le.  `Translate` és `TranslateArray` lefordítja bármely két nyelvek között.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForTranslate`.

**A visszatérési érték:** A Translator szolgáltatás által támogatott nyelvkódok tartalmazó karakterlánc-tömbben.

### <a name="response-class-status-200"></a>Válasz osztály (200-as állapot)
A Translator szolgáltatás által támogatott nyelvkódok tartalmazó karakterlánc-tömbben.

string

A válasz tartalomtípusa: application/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|appid|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc használata esetén hagyja meg az `appid` mezője üres. Ellenkező esetben a tartalmazó karakterláncot tartalmaznak `"Bearer" + " " + "access_token"`.|query|string|
|Engedélyezés|(üres)  |Kötelező, ha mindkét a `appid` mezőt és a `Ocp-Apim-Subscription-Key` fejléc üres marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|string|
|OCP-Apim-Subscription-Key|(üres)|Kötelező, ha mindkét a `appid` mezőt és a `Authorization` fejléc üres marad.|header|string|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Reason|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérés és a kérés azonosítója szerepel a válaszfejléc `X-MS-Trans-Info`.|
|503|A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="get-getlanguagesforspeak"></a>GET /GetLanguagesForSpeak

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
A beszédszintézishez elérhető nyelvek beolvasása.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/GetLanguagesForSpeak`.

**A visszatérési érték:** A beszédszintézishez a Translator szolgáltatás által támogatott nyelvkódok tartalmazó karakterlánc-tömbben.

### <a name="response-class-status-200"></a>Válasz osztály (200-as állapot)
A beszédszintézishez a Translator szolgáltatás által támogatott nyelvkódok tartalmazó karakterlánc-tömbben.

string

A válasz tartalomtípusa: application/xml

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|appid|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc használata esetén hagyja meg az `appid` mezője üres. Ellenkező esetben a tartalmazó karakterláncot tartalmaznak `"Bearer" + " " + "access_token"`.|query|string|
|Engedélyezés|(üres)|Kötelező, ha mindkét a `appid` mezőt és a `Ocp-Apim-Subscription-Key` fejléc üres marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|string|
|OCP-Apim-Subscription-Key|(üres)|Kötelező, ha mindkét a `appid` mezőt és a `Authorization` fejléc üres marad.|header|string|
 
### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Reason|
|:--|:--|
|400|Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401|Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérés és a kérés azonosítója szerepel a válaszfejléc `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="get-speak"></a>GET /Speak

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Az átadott a szöveg, a kívánt nyelvet beszélt WAV vagy az MP3 adatfolyamot adja vissza.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/Speak`.

**A visszatérési érték:** Az átadott a szöveget, a kívánt nyelvet beszélt WAV vagy az MP3 adatfolyam.

### <a name="response-class-status-200"></a>Válasz osztály (200-as állapot)

binary

A válasz tartalomtípusa: application/xml

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|appid|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc használata esetén hagyja meg az `appid` mezője üres. Ellenkező esetben a tartalmazó karakterláncot tartalmaznak `"Bearer" + " " + "access_token"`.|query|string|
|szöveg|(üres)   |Kötelező. A Stream, a megadott nyelven lehet beszélt, egy vagy több mondatokat tartalmazó karakterlánc. A szöveg nem haladhatja meg a 2000 karakter lehet.|query|string|
|language|(üres)   |Kötelező. Egy karakterlánc, amely a nyelv, amelyen a szöveg felolvasása támogatott nyelvi kódját jelöli. A kódot kell lennie a metódus által visszaadott a kódok `GetLanguagesForSpeak`.|query|string|
|format|(üres)|Választható. Egy karakterlánc, amely meghatározza a tartalomtípus-azonosítót. Jelenleg `audio/wav` és `audio/mp3` érhetők el. Az alapértelmezett érték `audio/wav`.|query|string|
|options|(üres)    |Választható. Egy karakterlánc, amely meghatározza a szintetizált tulajdonságait:<ul><li>`MaxQuality` és `MinSize` adja meg a hang jel minőségét. `MaxQuality` a legjobb minőségű biztosít. `MinSize` a legkisebb fájlméret itt. Az alapértelmezett érték `MinSize`.</li><li>`female` és `male` adja meg a hangalapú kívánt tartalmazzák. A mező alapértelmezett értéke: `female`. Használja a függőleges vonal (<code>\|</code>) több beállításokat tartalmazza. Például:  `MaxQuality|Male`.</li></li></ul>  |query|string|
|Engedélyezés|(üres)|Kötelező, ha mindkét a `appid` mezőt és a `Ocp-Apim-Subscription-Key` fejléc üres marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|string|
|OCP-Apim-Subscription-Key|(üres)  |Kötelező, ha mindkét a `appid` mezőt és a `Authorization` fejléc üres marad.|header|string|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Reason|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérés és a kérés azonosítója szerepel a válaszfejléc `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="get-detect"></a>GET /Detect

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
A szakasz a szöveg nyelvét adja meg.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/Detect`.

**A visszatérési érték:** Egy-két karakter nyelvi kód a szöveget tartalmazó karakterlánc.

### <a name="response-class-status-200"></a>Válasz osztály (200-as állapot)

string

A válasz tartalomtípusa: application/xml

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|appid|(üres)  |Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc használata esetén hagyja meg az `appid` mezője üres. Ellenkező esetben a tartalmazó karakterláncot tartalmaznak `"Bearer" + " " + "access_token"`.|query|string|
|szöveg|(üres)|Kötelező. Szöveg, amelynek a nyelve, hogy azonosítható tartalmazó karakterlánc. A szöveg nem lehet hosszabb 10 000 karakternél.|query|  string|
|Engedélyezés|(üres)|Kötelező, ha mindkét a `appid` mezőt és a `Ocp-Apim-Subscription-Key` fejléc üres marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|string|
|OCP-Apim-Subscription-Key  |(üres)    |Kötelező, ha mindkét a `appid` mezőt és a `Authorization` fejléc üres marad.|header|string|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Reason|
|:--|:--|
|400|Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérés és a kérés azonosítója szerepel a válaszfejléc `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|


## <a name="post-detectarray"></a>/DetectArray közzététele

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések

Azonosítja a nyelveket a karakterláncok tömbje. Egymástól függetlenül minden egyes tömbelemeken nyelvének észleli, és a tömb minden egyes sorára eredményt adja vissza.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/DetectArray`.

A kérelem törzsének formátuma a következő:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays">
    <string>string-value-1</string>
    <string>string-value-2</string>
</ArrayOfstring>
```

A szöveg nem lehet hosszabb 10 000 karakternél.

**A visszatérési érték:** A bemeneti tömb minden egyes sorára két karakter nyelvkódot tartalmazó karakterlánc-tömbben.

A válasz törzse formátuma a következő:

```
<ArrayOfstring xmlns="http://schemas.microsoft.com/2003/10/Serialization/Arrays" xmlns:i="https://www.w3.org/2001/XMLSchema-instance">
  <string>language-code-1</string>
  <string>language-code-2</string>
</ArrayOfstring>
```

### <a name="response-class-status-200"></a>Válasz osztály (200-as állapot)
`DetectArray` sikeres volt. Adja vissza, amely tartalmazza a bemeneti tömb minden egyes sorára két karakter nyelvkódot karakterlánc-tömbben.

string

A válasz tartalomtípusa: application/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|appid|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc használata esetén hagyja meg az `appid` mezője üres. Ellenkező esetben a tartalmazó karakterláncot tartalmaznak `"Bearer" + " " + "access_token"`.|query|string|
|Engedélyezés|(üres)|Kötelező, ha mindkét a `appid` mezőt és a `Ocp-Apim-Subscription-Key` fejléc üres marad.  Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|string|
|OCP-Apim-Subscription-Key|(üres)|Kötelező, ha mindkét a `appid` mezőt és a `Authorization` fejléc üres marad.|header|string|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Reason|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérés és a kérés azonosítója szerepel a válaszfejléc `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="get-addtranslation"></a>GET /AddTranslation

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések

> [!IMPORTANT]
> **Elavulással kapcsolatos megjegyzés:** 2018. január 31. után ez a módszer nem fogad el új mondat jelentkezés. Hibaüzenetet kap. Tekintse át a közlemény az együttműködésen alapuló Translation Framework (CTF), a változásokat.

A fordítási memória ad hozzá a fordítást.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/AddTranslation`.

### <a name="response-class-status-200"></a>Válasz osztály (200-as állapot)

string

A válasz tartalomtípusa: alkalmazás: xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus   |
|:--|:--|:--|:--|:--|
|appid|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc használata esetén hagyja meg az `appid` mezője üres. Ellenkező esetben a tartalmazó karakterláncot tartalmaznak `"Bearer" + " " + "access_token"`.|query|string|
|originalText|(üres)|Kötelező. A lefordítandó szöveget tartalmazó karakterlánc. A karakterlánc hossza legfeljebb 1000 karakter lehet.|query|string|
|translatedText|(üres) |Kötelező. Egy szöveget tartalmazó karakterláncot a Célnyelv lefordítva. A karakterlánc hossza legfeljebb 2000 karakter lehet.|query|string|
|from|(üres)   |Kötelező. Egy karakterlánc, amely a szöveg eredeti nyelvű nyelvi kódját jelöli. Ha például a angol és német de en.|query|string|
|erre:|(üres)|Kötelező. Egy karakterlánc, amely lefordítja a szöveget, a nyelv a nyelvi kódját jelöli.|query|string|
|rating|(üres) |Választható. A karakterlánc a minőségi minősítés jelölő egész szám. Az érték -10- és 10 között. Az alapértelmezett érték 1.|query|integer|
|contentType|(üres)    |Választható. Folyamatban van a lefordított szöveg formátuma. A támogatott formátumok a következők `text/plain` és `text/html`. Helytelen formátumú, teljes körű elemek bármely HTML-elemek kell.    |query|string|
|category|(üres)|Választható. A kategória (tartomány), a fordítás tartalmazó karakterlánc. A mező alapértelmezett értéke: `general`.|query|string|
|Felhasználó|(üres)|Kötelező. Egy karakterlánc, amely a Küldés létrehozója nyomon követésére szolgál.|query|string|
|URI-t|(üres)|Választható. A tartalom helyét a fordítás tartalmazó karakterlánc.|query|string|
|Engedélyezés|(üres)|Kötelező, ha mindkét a `appid` mezőt és a `Ocp-Apim-Subscription-Key` fejléc üres marad.  Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.  |header|string|
|OCP-Apim-Subscription-Key|(üres)|Kötelező, ha mindkét a `appid` mezőt és a `Authorization` fejléc üres marad.|header|string|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Reason|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok.|
|410|`AddTranslation` már nem támogatott.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérés és a kérés azonosítója szerepel a válaszfejléc `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="post-addtranslationarray"></a>POST /AddTranslationArray

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések

> [!IMPORTANT]
> **Elavulással kapcsolatos megjegyzés:** 2018. január 31. után ez a módszer nem fogad el új mondat jelentkezés. Hibaüzenetet kap. Tekintse át a közlemény az együttműködésen alapuló Translation Framework (CTF), a változásokat.

Fordítások tömböt ad fordítási memória. Ez a módszer egy tömb verzióját `AddTranslation`.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/AddTranslationArray`.

A kérelem törzsének formátuma a következő:

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

Ezek az elemek szerepelnek `AddtranslationsRequest`:

* `AppId`: Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc használata esetén hagyja meg az `AppId` mezője üres. Ellenkező esetben a tartalmazó karakterláncot tartalmaznak `"Bearer" + " " + "access_token"`.
* `From`: Kötelező. A Forrásnyelv nyelvkódja tartalmazó karakterlánc. A nyelv által visszaadott egyikének kell lennie a `GetLanguagesForTranslate` metódust.
* `To`: Kötelező. A Célnyelv nyelvkódja tartalmazó karakterlánc. A nyelv által visszaadott egyikének kell lennie a `GetLanguagesForTranslate` metódust.
* `Translations`: Kötelező. Fordítások fordítási memória hozzáadása tömbje. Tartalmaznia kell minden egyes fordítási `OriginalText`, `TranslatedText`, és `Rating`. A maximális mérete az egyes `OriginalText` és `TranslatedText` 1000 karakter hosszú lehet. Az összes összes `OriginalText` és `TranslatedText` elemeket nem lehet hosszabb 10 000 karakternél. A tömb elemeinek maximális száma pedig a 100.
* `Options`: Kötelező. Olyan beállítási lehetőségekkel, beleértve a `Category`, `ContentType`, `Uri`, és `User`. `User` kötelező megadni. `Category`, `ContentType`, és `Uri` megadása nem kötelező. A megadott elemek betűrendbe szerepelnie kell.

### <a name="response-class-status-200"></a>Válasz osztály (200-as állapot)
`AddTranslationArray` metoda sikeres volt. 

2018. január 31. után mondat jelentkezés nem fogadható el. A szolgáltatás válaszol, és 410-es hibakód.

string

A válasz tartalomtípusa: application/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Engedélyezés|(üres)|Kötelező, ha mindkét a `appid` mezőt és a `Ocp-Apim-Subscription-Key` fejléc üres marad.  Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|string|
|OCP-Apim-Subscription-Key|(üres)|Kötelező, ha mindkét a `appid` mezőt és a `Authorization` fejléc üres marad.|header|string|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Reason|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok.|
|410    |`AddTranslation` már nem támogatott.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérés és a kérés azonosítója szerepel a válaszfejléc `X-MS-Trans-Info`.|
|503|A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="get-breaksentences"></a>GET /BreakSentences

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Szöveg egy szakasza bontja a mondatokat, és minden mondat hosszának tartalmazó tömböt ad vissza.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/BreakSentences`.

**A visszatérési érték:** Egész számok tömbje, amely a mondatok hosszának jelöli. A hossz a tömb mondatok számát jelöli. Az egyes mondat hossza jelölik.

### <a name="response-class-status-200"></a>Válasz osztály (200-as állapot)
Egész számok tömbje, amely a mondatok hosszának jelöli. A hossz a tömb mondatok számát jelöli. Az egyes mondat hossza jelölik.

integer

A válasz tartalomtípusa: application/xml

### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|appid|(üres)  |Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc használata esetén hagyja meg az `appid` mezője üres. Ellenkező esetben a tartalmazó karakterláncot tartalmaznak `"Bearer" + " " + "access_token"`.|query| string|
|szöveg|(üres)   |Kötelező. Szöveg felosztása a mondatok jelölő karakterláncot. A szöveg maximális mérete 10 000 karakternél.|query|string|
|language   |(üres)    |Kötelező. Egy karakterlánc, amely a bemeneti szöveg nyelvkódja jelöli.|query|string|
|Engedélyezés|(üres)|Kötelező, ha mindkét a `appid` mezőt és a `Ocp-Apim-Subscription-Key` fejléc üres marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.   |header|string|
|OCP-Apim-Subscription-Key|(üres)|Kötelező, ha mindkét a `appid` mezőt és a `Authorization` fejléc üres marad.|header|string|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Reason|
|:--|:--|
|400|Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401|Érvénytelen hitelesítő adatok.|
|500|Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérés és a kérés azonosítója szerepel a válaszfejléc `X-MS-Trans-Info`.|
|503|A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="post-gettranslations"></a>POST /GetTranslations

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Egy adott nyelven párhoz fordítások tömbjét lekéri a tároló és a fő Célkiszolgáló motor. `GetTranslations` eltér a `Translate` abban, hogy az összes rendelkezésre álló fordítások adja vissza.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/GetTranslations`.

A kérelem törzse tartalmazza a választható `TranslationOptions` objektum, amely a következő formátumban:

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

A `TranslateOptions` objektum tartalmazza a következő listában található értékek. Azok az összes opcionális és a leggyakrabban használt beállításai az alapértelmezett. A megadott elemek betűrendbe szerepelnie kell.

* `Category`: A kategória (tartomány), a fordítás tartalmazó karakterlánc. A mező alapértelmezett értéke: `general`.
* `ContentType`: Az egyetlen támogatott beállítás, és az alapértelmezett `text/plain`.
* `IncludeMultipleMTAlternatives`: Adja meg, hogy egynél több alternatív az MT motortól vissza kell a logikai jelzőt. Érvényes értékek a következők `true` és `false` (kis-és nagybetűket). Az alapértelmezett érték `false`, csak egy alternatív ad vissza. A jelző `true` lehetővé teszi a mesterséges alternatívák, teljes körűen integrált az együttműködésen alapuló Translation Framework (CTF) rendelkező létrehozását. A szolgáltatás lehetővé teszi a mondatokat, amelyek a mesterséges alternatív megoldások hozzáadásával lefordított szerepel CTF vracející alternatívák a *n*– a dekóder ajánlott listája.
    - Minősítések. A minősítések az ehhez hasonló érvényesek: 
         - A legjobb automatikus fordítás, 5 besorolású.
       - Az alternatív megoldások a CTF tükrözik a felülvizsgáló felügyelete. Ezek között a -10 + 10.
       - Az automatikusan generált (*n*-legjobb) fordítási lehetőségeket rendelkezik egy 0 és 100 egyezés fokú minősítése.
    - Számos lehetőség közül. A visszaadott alternatív megoldások száma lehet magas, mint a megadott érték `maxTranslations`, de alacsonyabb lehet.
    - Nyelvi párokat. Ez a funkció nem érhető el a fordítások egyszerűsített és hagyományos kínai, között mindkét irányban. Minden, más Microsoft Translator által támogatott nyelvi pár érhető el.
* `State`: Felhasználói állapot korrelációját, ha a kérés-válasz érdekében. A válasz ugyanahhoz a tartalomhoz lesznek visszaadva.
* `Uri`: Ez az URI által eredmények szűréséhez. Ha nincs érték van beállítva, az alapértelmezett érték `all`.
* `User`: Ez a felhasználó eredmények szűréséhez. Ha nincs érték van beállítva, az alapértelmezett érték `all`.

Kérelem `Content-Type` lehet `text/xml`.

**A visszatérési érték:** A formátumot a válasz a következő:

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

Ez a válasz tartalmaz egy `GetTranslationsResponse` elem, amely tartalmazza a következő értékeket:

* `Translations`: Az egyezések tömbjét található, a tárolt `TranslationMatch` objektumok (lásd a következő szakaszban). A fordítások enyhe változatának az eredeti szöveg (intelligens egyező) tartalmazhatnak. A fordítások rendezése: 100 %-os illeszkedik először intelligens egyezések mellett.
* `From`: Ha a metódus nem ad meg egy `From` nyelv, ez az érték fog érkezni az automatikus nyelvfelismerést. Ellenkező esetben lesz a megadott `From` nyelv.
* `State`: Felhasználói állapot korrelációját, ha a kérés-válasz érdekében. A megadott értéket tartalmaz a `TranslateOptions` paraméter.

A `TranslationMatch` ezeket az értékeket tartalmazza:

* `Error`: A hiba kódot, ha hiba történik egy adott bemeneti karakterlánchoz. Ellenkező esetben ez a mező értéke üres.
* `MatchDegree`: azt jelzi, különös figyelmet fordítanak a bemeneti szöveg megfelel-e az eredeti szöveg található a tárban. A rendszer megfelel a tárolóban, többek között a pontatlan egyezések bemeneti mondatokat. A visszaadott érték címtartományok 0 és 100, ahol 0 nincs hasonlóság, 100 pedig egy pontos, a kis-és nagybetűket egyezést.
* `MatchedOriginalText`: Eredeti szöveg, amely ezt az eredményt a megfelelést. Ez az érték csak akkor, ha az egyeztetett eredeti szöveg a bemeneti szövegből származó különböző volt adja vissza. Segítségével az intelligens egyeztetésű forrás szöveget adja vissza. Ez az érték nem adják vissza a Microsoft Translator eredmények elérése érdekében.
* `Rating`: Azt jelzi, hogy a szolgáltató minőségi megugró személy. Gépi fordítás eredmények rendelkezik minősítéssel az 5-ből. Névtelenül megadott fordítások általában 1 és 4 közötti minősítéssel rendelkezik. Mérvadó megadott fordítások általában fog rendelkezni a 6 – 10 minősítést.
* `Count`: Hányszor az ilyen minősítést kapott fordítását lett kiválasztva. A lefordított automatikusan választ a 0 érték.
* `TranslatedText`: A lefordított szöveg.

### <a name="response-class-status-200"></a>Válasz osztály (200-as állapot)
A `GetTranslationsResponse` objektum korábban ismertetett formátumban.

string

A válasz tartalomtípusa: application/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|appid|(üres)|Kötelező. Ha a `Authorization` vagy `Ocp-Apim-Subscription-Key` fejléc használata esetén hagyja meg az `appid` mezője üres. Ellenkező esetben a tartalmazó karakterláncot tartalmaznak `"Bearer" + " " + "access_token"`.|query|string|
|szöveg|(üres)|Kötelező. A fordítandó szöveg jelölő karakterláncot. A szöveg maximális mérete 10 000 karakternél.|query|string|
|from|(üres)|Kötelező. Folyamatban van a lefordított szöveg nyelvkódja jelölő karakterláncot.|query|string|
|erre: |(üres)    |Kötelező. Egy karakterlánc, amely lefordítja a szöveget, a nyelv a nyelvi kódját jelöli.|query|string|
|maxTranslations|(üres)|Kötelező. A visszaadandó fordítások maximális számát jelölő egész szám.|query|integer|
|Engedélyezés| (üres)|Kötelező, ha mindkét a `appid` mezőt és a `Ocp-Apim-Subscription-Key` fejléc üres marad. Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|string|  header|
|OCP-Apim-Subscription-Key|(üres)  |Kötelező, ha mindkét a `appid` mezőt és a `Authorization` fejléc üres marad.|header|string|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Reason|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérés és a kérés azonosítója szerepel a válaszfejléc `X-MS-Trans-Info`.|
|503|A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="post-gettranslationsarray"></a>POST /GetTranslationsArray

### <a name="implementation-notes"></a>Megvalósításhoz fűzött megjegyzések
Több fordítási jelöltek a több forrás szövegek kérdezi le.

A kérelem URI azonosítója `https://api.microsofttranslator.com/V2/Http.svc/GetTranslationsArray`.

A kérelem törzsének formátuma a következő:

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

`GetTranslationsArrayRequest` Ezeket az elemeket tartalmazza:

* `AppId`: Kötelező. Ha a `Authorization` fejléc használata esetén hagyja meg az `AppId` mezője üres. Ellenkező esetben a tartalmazó karakterláncot tartalmaznak `"Bearer" + " " + "access_token"`.
* `From`: Kötelező. Folyamatban van a lefordított szöveg nyelvkódja jelölő karakterláncot.
* `MaxTranslations`: Kötelező. A visszaadandó fordítások maximális számát jelölő egész szám.
* `Options`: Választható. Egy `Options` objektum, amely a következő értékeket tartalmazza. Azok az összes opcionális és a leggyakrabban használt beállításai az alapértelmezett. A megadott elemek betűrendbe szerepelnie kell.
    - `Category`: A kategória (tartomány), a fordítás tartalmazó karakterlánc. A mező alapértelmezett értéke: `general`.
    - `ContentType`: Az egyetlen támogatott beállítás, és az alapértelmezett `text/plain`.
    - `IncludeMultipleMTAlternatives`: Adja meg, hogy egynél több alternatív az MT motortól vissza kell a logikai jelzőt. Érvényes értékek a következők `true` és `false` (kis-és nagybetűket). Az alapértelmezett érték `false`, csak egy alternatív ad vissza. A jelző `true` lehetővé teszi, hogy a fordítás, teljes körűen integrált az együttműködésen alapuló fordítások Framework (CTF) rendelkező mesterséges alternatívák generációja. A szolgáltatás lehetővé teszi a mondatokat, amelyek nem alternatívák CTF rendelkezik a mesterséges alternatív megoldások hozzáadásával vracející alternatívák a *n*– a dekóder ajánlott listája.
        - A minősítések minősítése alkalmaznak ehhez hasonló:
          - A legjobb automatikus fordítás, 5 besorolású.
          - Az alternatív megoldások a CTF tükrözik a felülvizsgáló felügyelete. Ezek között a -10 + 10.
          - Az automatikusan generált (*n*-legjobb) fordítási lehetőségeket rendelkezik egy 0 és 100 egyezés fokú minősítése.
        - Számos lehetőség közül. A visszaadott alternatív megoldások száma lehet magas, mint a megadott érték `maxTranslations`, de alacsonyabb lehet.
        - Nyelvi párokat. Ez a funkció nem érhető el a fordítások egyszerűsített és hagyományos kínai, között mindkét irányban. Minden, más Microsoft Translator által támogatott nyelvi pár érhető el.
* `State`: Felhasználói állapot korrelációját, ha a kérés-válasz érdekében. A válasz ugyanahhoz a tartalomhoz lesznek visszaadva.
* `Uri`: Ez az URI által eredmények szűréséhez. Ha nincs érték van beállítva, az alapértelmezett érték `all`.
* `User`: Ez a felhasználó eredmények szűréséhez. Ha nincs érték van beállítva, az alapértelmezett érték `all`.
* `Texts`: Kötelező. A fordítás szöveget tartalmazó tömb. Az összes karakterláncok ugyanazt a nyelvet kell lennie. Minden szöveget lehet az összes nem lehet hosszabb 10 000 karakternél. A tömb elemeinek maximális száma 10.
* `To`: Kötelező. Egy karakterlánc, amely lefordítja a szöveget, a nyelv a nyelvi kódját jelöli.

Nem kötelező elemek, akkor kihagyhatja. Elemek közvetlen gyermekeinek `GetTranslationsArrayRequest` betűrendben szerepelnie kell.

Kérelem `Content-Type` lehet `text/xml`.

**A visszatérési érték:** A formátumot a válasz a következő:

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

Minden egyes `GetTranslationsResponse` elem tartalmazza ezeket az értékeket:

* `Translations`: Az egyezések tömbjét található, a tárolt `TranslationMatch` objektumok (lásd a következő szakaszban). A fordítások enyhe változatának az eredeti szöveg (intelligens egyező) tartalmazhatnak. A fordítások rendezése: 100 %-os illeszkedik először intelligens egyezések mellett.
* `From`: Ha a metódus nem ad meg egy `From` nyelv, ez az érték fog érkezni az automatikus nyelvfelismerést. Ellenkező esetben lesz a megadott `From` nyelv.
* `State`: Felhasználói állapot korrelációját, ha a kérés-válasz érdekében. A megadott értéket tartalmaz a `TranslateOptions` paraméter.

A `TranslationMatch` objektum tartalmazza a következő értékeket:
* `Error`: A hiba kódot, ha hiba történik egy adott bemeneti karakterlánchoz. Ellenkező esetben ez a mező értéke üres.
* `MatchDegree`: azt jelzi, különös figyelmet fordítanak a bemeneti szöveg megfelel-e az eredeti szöveg található a tárban. A rendszer megfelel a tárolóban, többek között a pontatlan egyezések bemeneti mondatokat. A visszaadott érték címtartományok 0 és 100, ahol 0 nincs hasonlóság, 100 pedig egy pontos, a kis-és nagybetűket egyezést.
* `MatchedOriginalText`: Eredeti szöveg, amely ezt az eredményt a megfelelést. Ez az érték csak akkor, ha az egyeztetett eredeti szöveg a bemeneti szövegből származó különböző volt adja vissza. Segítségével az intelligens egyeztetésű forrás szöveget adja vissza. Ez az érték nem adják vissza a Microsoft Translator eredmények elérése érdekében.
* `Rating`: Azt jelzi, hogy a szolgáltató minőségi megugró személy. Gépi fordítás eredmények rendelkezik minősítéssel az 5-ből. Névtelenül megadott fordítások általában 1 és 4 közötti minősítéssel rendelkezik. Mérvadó megadott fordítások általában rendelkezik minősítéssel 6 – 10.
* `Count`: Hányszor az ilyen minősítést kapott fordítását lett kiválasztva. A lefordított automatikusan választ a 0 érték.
* `TranslatedText`: A lefordított szöveg.


### <a name="response-class-status-200"></a>Válasz osztály (200-as állapot)

string

A válasz tartalomtípusa: application/xml
 
### <a name="parameters"></a>Paraméterek

|Paraméter|Érték|Leírás|Paraméter típusa|Adattípus|
|:--|:--|:--|:--|:--|
|Engedélyezés  |(üres)    |Kötelező, ha mindkét a `appid` mezőt és a `Ocp-Apim-Subscription-Key` fejléc üres marad.  Engedélyezési jogkivonat: `"Bearer" + " " + "access_token"`.|header|string|
|OCP-Apim-Subscription-Key|(üres)  |Kötelező, ha mindkét a `appid` mezőt és a `Authorization` fejléc üres marad.|header|string|

### <a name="response-messages"></a>Parancsválasz-üzeneteket

|HTTP-állapotkód|Reason|
|:--|:--|
|400    |Hibás kérés. Ellenőrizze a bemeneti paraméterek, a részletes hibaüzenetet.|
|401    |Érvénytelen hitelesítő adatok.|
|500    |Kiszolgálóhiba. Ha a hiba továbbra is fennáll, ossza meg velünk. Adja meg az hozzávetőleges dátumot és időt a kérés és a kérés azonosítója szerepel a válaszfejléc `X-MS-Trans-Info`.|
|503    |A szolgáltatás átmenetileg nem érhető el. Próbálkozzon újra, és tudassa velünk, ha a probléma továbbra is fennáll-e.|

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Át a Translator Text API v3 ](../migrate-to-v3.md)


