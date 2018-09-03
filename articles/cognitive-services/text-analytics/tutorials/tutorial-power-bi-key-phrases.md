---
title: Szövegelemzés a Power BI-jal – Azure Cognitive Services | Microsoft Docs
description: Megismerheti, hogyan nyerhet ki kulcskifejezéseket a Power BI-ban tárolt szövegekből.
services: cognitive-services
author: luiscabrer
manager: cgronlun
ms.service: cognitive-services
ms.component: text-analytics
ms.topic: tutorial
ms.date: 3/07/2018
ms.author: luisca
ms.openlocfilehash: 2cdb93d44218627efdcb0360d8cf4a4eeeca177a
ms.sourcegitcommit: 7b845d3b9a5a4487d5df89906cc5d5bbdb0507c8
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/14/2018
ms.locfileid: "42889314"
---
# <a name="text-analytics-with-power-bi"></a>Szövegelemzés a Power BI-jal

A Microsoft Power BI a szervezeti adatokat gyönyörű jelentésekbe tömöríti, amelyeket a gyorsabb és részletesebb tájékoztatás érdekében közzétehet a szervezeten belül. A Text Analytics szolgáltatás a Microsoft Azure Cognitive Services szolgáltatásainak részét képezi, és a Key Phrases API használatával képes kinyerni a legfontosabb kifejezéseket a szövegekből. Ezekkel az eszközökkel gyors bepillantást nyerhet abba, hogy az ügyfelei miről beszélnek, és milyen érzéseket váltottak ki belőlük.

Az oktatóanyagból megtudhatja, hogy a Power BI Desktop és a Key Phrases API integrálásával hogyan nyerheti ki a fontosabb kifejezéseket az ügyfelek visszajelzéseiből egy egyéni Power Query-függvény használatával. Ezekből a kifejezésekből egy szófelhőt is készítünk.

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elvégzéséhez a következőkre lesz szüksége:

> [!div class="checklist"]
> * Microsoft Power BI Desktop. [Töltse le ingyenesen](https://powerbi.microsoft.com/get-started/).
> * Egy Microsoft Azure-fiók. [Kezdjen egy ingyenes próbaidőszakot](https://azure.microsoft.com/free/), vagy [jelentkezzen be](https://portal.azure.com/).
> * Egy Text Analytics-hozzáférési kulcs. [Regisztráljon](../../cognitive-services-apis-create-account.md), majd [szerezze be a kulcsot](../how-tos/text-analytics-how-to-access-key.md).
> * Ügyfelek megjegyzései. [Töltse le a mintaadatokat](https://aka.ms/cogsvc/ta), vagy használjon saját adatokat.

## <a name="loading-customer-data"></a>Ügyféladatok betöltése

Első lépésként nyissa meg a Power BI Desktopot, és töltse be a **FabrikamComments.csv** vesszővel tagolt (CSV-) fájlt. Ez a fájl egy fiktív kisvállalkozás támogatási fórumának egy napi feltételezett tevékenységeit tartalmazza.

> [!NOTE]
> A Power BI források széles választékából, például a Facebookról vagy egy SQL-adatbázisból származó adatokat is képes használni. További információt [a Facebook](https://powerbi.microsoft.com/integrations/facebook/) és [az SQL Server a Power BI-jal való integrációját](https://powerbi.microsoft.com/integrations/sql-server/) ismertető cikkekben olvashat.

A Power BI Desktop fő ablakának Kezdőlap szalagján keresse meg a Külső adatok csoportot. A csoport **Adatok lekérése** legördülő menüjében válassza a **Szöveg/CSV** lehetőséget.

![[Az Adatok lekérése gomb]](../media/tutorials/power-bi/get-data-button.png)

Megjelenik a Megnyitás párbeszédpanel. Navigáljon a Letöltések mappához, vagy ahhoz a mappához, amelyben a mintaadatokat tartalmazó fájl található. Kattintson a `FabrikamComments.csv` elemre, majd a **Megnyitás** gombra. Megjelenik a CSV-importálási párbeszédpanel.

![[A CSV-importálási párbeszédpanel]](../media/tutorials/power-bi/csv-import.png)

A CSV-importálási párbeszédpanelen ellenőrizheti, hogy a Power BI Desktop megfelelően észlelte-e a karakterkészletet, a tagoló karaktert, a fejlécsorokat és az oszloptípusokat. Ezek az információk most rendben vannak, ezért kattintson a **Betöltés** parancsra.

A betöltött adatok megtekintéséhez a Power BI-munkaterület bal szélén az Adatnézet gombra kattintva váltson az Adatnézetre. Az adatok egy táblázatban jelennek meg, a Microsoft Excelhez hasonlóan.

![[Az importált adatok kezdeti nézete]](../media/tutorials/power-bi/initial-data-view.png)

## <a name="preparing-the-data"></a>Az adatok előkészítése

A Power BI Desktopban esetleg át kell alakítania az adatokat, mielőtt azokat a Key Phrases szolgáltatás feldolgozhatná.

Például esetünkben az adatok egy `subject` és egy `comment` mezőt egyaránt tartalmaznak. A kulcskifejezések kivonatolásakor mindkét mezőben vizsgálnunk kell az adatokat, nem csak a `comment` mezőben. A Power BI Desktop Oszlopok egyesítése függvényével ez egyszerűen megoldható.

Nyissa meg a Lekérdezésszerkesztőt a Power BI Desktop ablak Kezdőlap menüszalagjának Külső adatok csoportjában a **Lekérdezések szerkesztése** parancsra kattintva. 

![[A Külső adatok csoport a Kezdőlap menüszalagon]](../media/tutorials/power-bi/edit-queries.png)

Ha még nem lenne bejelölve, jelölje be a „FabrikamComments” elemet a Lekérdezések listában az ablak bal oldalán.

Most jelölje be a `subject` és a `comment` oszlopot a táblázatban. Lehetséges, hogy vízszintesen görgetnie kell, hogy mindkét oszlopot elérje. Először kattintson a `subject` oszlop fejlécére, majd a Control billentyűt lenyomva tartva kattintson a `comment` oszlop fejlécére is.

![[Az egyesíteni kívánt mezők kijelölése]](../media/tutorials/power-bi/select-columns.png)

Az Átalakítás menüszalag Szöveges oszlopok csoportjában kattintson az **Oszlopok egyesítése** elemre. Megjelenik az Oszlopok egyesítése párbeszédpanel.

![[Oszlopok egyesítése az Oszlopok egyesítése párbeszédpanelen]](../media/tutorials/power-bi/merge-columns.png)

Az Oszlopok egyesítése párbeszédpanelen válassza a Tabulátort elválasztó karakterként, majd kattintson az **OK** gombra. Kész!

Ha szeretné, az Üres elemek eltávolítása szűrővel kiszűrheti az üres üzeneteket, illetve a Tisztítás átalakítással kiszűrheti vagy eltávolíthatja a nem nyomtatható karaktereket. Ha az adatkészlet tartalmaz egy `spamscore` vagy hasonló oszlopot, ahogy a mintaadatfájl is, a „spam” megjegyzéseket kiszűrheti egy Számszűrő használatával.

## <a name="understanding-the-api"></a>Az API ismertetése

A Key Phrases API HTTP-kérésenként ezer szöveges dokumentumot tud feldolgozni. A Power BI azonban jobban szeret egyszerre egy rekordot kezelni, ezért az API-hívásaink csak egyetlen dokumentumot fognak tartalmazni. [Az API](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6) a következő mezőket várja az egyes feldolgozandó dokumentumokhoz.

| | |
| - | - |
| `id`  | A dokumentum egy egyedi azonosítóját a kérésen belül. Ezt a mezőt a válasz is tartalmazza. Így ha több dokumentumot dolgoz fel, a kinyert kulcskifejezéseket könnyen társíthatja azzal a dokumentummal, amelyből származnak. Mi most kérésenként egy dokumentumot dolgozunk fel, ezért már eleve tudjuk, hogy a válasz melyik dokumentummal társítható, ezért az `id` mindegyik kérésben lehet ugyanaz.|
| `text`  | A feldolgozandó szöveg. Ezt az általunk létrehozott `Merged` oszlopból kapjuk meg, amely a tárgysor és az üzenettörzs kombinált szövegét tartalmazza. A Key Phrases API használatához ennek az adatnak a hossza nem haladhatja meg az 5000 karaktert.|
| `language` | A dokumentum nyelvét jelölő kód. Esetünkben mindegyik üzenet angol nyelven íródott, ezért szoftveresen rögzíthetjük az `en` nyelvet a lekérdezésben.|

Ezeket a mezőket kombinálnunk kell egy JSON- (JavaScript Object Notation-) dokumentumban, amelyet a Key Phrases API-ba küldünk. A kérésre kapott válasz szintén egy JSON-dokumentum lesz, amelynek az elemzésével kapjuk majd meg a kulcskifejezéseket.

## <a name="creating-a-custom-function"></a>Egyéni függvény létrehozása

Most már készen állunk az egyéni függvény létrehozására, amely integrálja majd a Power BI-t és a Text Analytics szolgáltatást. A Power BI Desktop a függvényt a táblázat minden egyes sorára meghívja, és létrehoz egy új oszlopot az eredményekkel.

A függvény a feldolgozandó szöveget paraméterként kapja meg. Az adatokat átalakítja a szükséges JavaScript Object Notation (JSON) formátumból és vissza, és elküldi a HTTP-kérést a Key Phrases API-végpontra. A válasz elemzését követően a függvény egy sztringet ad vissza, amely a kinyert kulcskifejezések vesszővel tagolt listáját tartalmazza.

> [!NOTE]
> A Power BI Desktop egyéni függvényei a [Power Query M képletnyelven](https://msdn.microsoft.com/library/mt211003.aspx) (röviden „M”) vannak megírva. Az M egy funkcionális programozási nyelv, amelynek az alapját az [F#](https://docs.microsoft.com/dotnet/fsharp/) képezi. Az oktatóanyag elvégzéséhez azonban nem kell programozónak lennie. A szükséges kód alább megtalálható.

Még mindig a Lekérdezésszerkesztő ablakában kell lennünk. A Kezdőlap menüszalagon kattintson az **Új forrás** elemre (az Új lekérdezés csoportban), és válassza az **Üres lekérdezés** lehetőséget a legördülő menüben. 

Egy új lekérdezés jelenik meg a Lekérdezések listában, kezdetben Query1 néven. Kattintson duplán erre az elemre, és nevezze át a `KeyPhrases` névre.

Most a Kezdőlap menüszalag Lekérdezés csoportjának **Speciális szerkesztő** elemére kattintva nyissa meg a Speciális szerkesztő ablakot. Törölje az eredetileg az ablakban lévő kódot, és illessze be a következőt. 

> [!NOTE]
> Az alábbi példában úgy vesszük, hogy a végpont a https://westus.api.cognitive.microsoft.com címen érhető el.  A Text Analytics-előfizetések létrehozása 13 régióban támogatott. Ha egy ezektől eltérő régióban regisztrált a szolgáltatásra, mindenképp a kiválasztott régió végpontját használja. Ezt az Azure Portal Áttekintés lapján találja meg a Text Analytics-előfizetésre kattintva.

```fsharp
// Returns key phrases from the text in a comma-separated list
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = Text.Lower(Text.Combine(jsonresp[documents]{0}[keyPhrases], ", "))
in  keyphrases
```

Illessze be továbbá a Text Analytics API-kulcsot, amelyet a Microsoft Azure irányítópultján talál az `apikey` kezdetű sorban. (A kulcs előtt és után mindenképp tartsa meg az idézőjeleket.) Ezután kattintson a **Kész** gombra.

## <a name="using-the-function"></a>A függvény használata

Most az egyéni függvény használatával kinyerhetjük az ügyfelek megjegyzéseiből a kulcskifejezéseket, és tárolhatjuk azokat a táblázat egy új oszlopában. 

Még mindig a Lekérdezésszerkesztőben váltson vissza a FabrikamComments lekérdezésre, váltson az Oszlop hozzáadása menüszalagra, és kattintson az Általános csoport **Egyéni függvény hívása** gombjára.

![[Egyéni függvény hívása gomb]](../media/tutorials/power-bi/invoke-custom-function-button.png)<br><br>

Az Egyéni függvény hívása párbeszédpanelen adja a `keyphrases` nevet az új oszlopnak. Függvénylekérdezésként válassza a `KeyPhrases` egyéni függvényt. 

Egy új mező jelenik meg a párbeszédpanelen, amely rákérdez, hogy melyik mezőt szeretné beadni a függvénynek `text` paraméterként. Válassza a `Merged` értéket (ez az az oszlop, amelyet korábban a tárgysor és az üzenettörzs egyesítésével létrehoztunk) a legördülő menüben.

![[Egyéni függvény hívása]](../media/tutorials/power-bi/invoke-custom-function.png)

Végezetül kattintson az **OK** gombra.

Ha mindennel elkészült, a Power BI a táblázat minden sorára meghívja a függvényt, végrehajtja a Key Phrases-lekérdezéseket, és hozzáadja az új oszlopot a táblázathoz. Mielőtt azonban ez megtörténne, lehetséges, hogy meg kell adnia a hitelesítési és adatvédelmi beállításokat.

## <a name="authentication-and-privacy"></a>Hitelesítés és adatvédelem

Miután bezárja az Egyéni függvény hívása párbeszédpanelt, egy értesítés jelenhet meg, amely megkéri, hogy adja meg, miként szeretne csatlakozni a Key Phrases-végponthoz.

![[hitelesítő adatok értesítése]](../media/tutorials/power-bi/credentials-banner.png)

Kattintson a **Hitelesítő adatok szerkesztése** gombra, és bizonyosodjon meg róla, hogy a párbeszédpanelen a Névtelen lehetőség van kiválasztva, majd kattintson a **Csatlakozás** parancsra. 

> [!NOTE]
> Miért névtelenül? A Text Analytics szolgáltatás az API-kulcs használatával hitelesíti Önt, ezért a Power BI-nak nem kell hitelesítő adatokat megadnia magához a HTTP-kéréshez.

![[a névtelen hitelesítés beállítása]](../media/tutorials/power-bi/access-web-content.png)

Ha a Hitelesítő adatok szerkesztése értesítés a névtelen hozzáférés beállítása után is látható, elképzelhető, hogy elfelejtette bemásolni az API-kulcsot. Ellenőrizze az egyéni `KeyPhrases` függvényben a Speciális szerkesztő alatt, hogy bemásolta-e.

Ezután egy értesítés jelenhet meg, amely arra kéri, hogy szolgáltasson információkat az adatforrás adatainak védelmével kapcsolatban. 

![[adatvédelmi értesítés]](../media/tutorials/power-bi/privacy-banner.png)

Kattintson a **Folytatás** gombra, és válassza a Nyilvános beállítást a párbeszédpanelen lévő minden adatforrásnál. Ezután kattintson a **Mentés** gombra.

![[adatforrás adatvédelmi beállításainak megadása]](../media/tutorials/power-bi/privacy-dialog.png)

## <a name="creating-the-word-cloud"></a>A szófelhő létrehozása

Miután a megjelenő értesítéseket elintézte, kattintson a Kezdőlap menüszalag **Bezárás és alkalmazás** elemére a Lekérdezésszerkesztő bezárásához.

A Power BI Desktop néhány pillanatig a szükséges HTTP-kérésekkel lesz elfoglalva. A táblázat mindegyik sorához az új `keyphrases` oszlopban találhatók a szövegben a Key Phrases API által talált kulcskifejezések. 

Ennek az oszlopnak a használatával hozzunk létre egy szövegfelhőt. Első lépésként kattintson a Jelentés gombra a Power BI Desktop főablakában, a munkaterület bal oldalán.

> [!NOTE]
> Miért a kivonatolt kulcskifejezéseket használjuk a szófelhő létrehozásához a megjegyzések teljes szövege helyett? A kulcskifejezések az ügyfelek megjegyzéseiből a *fontos* és nem egyszerűen csak a *leggyakoribb* szavakat tartalmazzák. Emellett a szavak méretezését így nem torzítja az, ha valamely szó csak szűk számú megjegyzésben fordul elő nagyon gyakran.

Ha a Word Cloud (Szófelhő) egyéni vizualizációt még nem telepítette, tegye meg. A munkaterület jobb oldalán lévő Vizualizációk panelen kattintson a három pontra (**...**), és válassza az **Importálás az áruházból** lehetőséget. Ezután keressen rá a „cloud” (felhő) kifejezésre, és kattintson a **Hozzáadás** gombra a Word Cloud (Szófelhő) vizualizáció mellett. A Power BI telepíti a vizualizációt, és tájékoztatja, amint ez sikeresen megtörtént.

![[egyéni vizualizáció hozzáadása]](../media/tutorials/power-bi/add-custom-visuals.png)<br><br>

És most végre hozzuk létre azt a szófelhőt!

![[A szófelhő ikonja a Vizualizációk panelen]](../media/tutorials/power-bi/visualizations-panel.png)

Először kattintson a szófelhő ikonjára a Vizualizációk panelen. Egy új jelentés jelenik meg a munkaterületen. Húzza a `keyphrases` mezőt a Mezők panelről a Vizualizációk panel Kategória mezőjébe. A szófelhő megjelenik a jelentésben.

Most váltson a Vizualizációk panel Formátum lapjára. A Stopszavak kategóriában kapcsolja be az **Alapértelmezett stopszavak** beállítást a rövid és gyakori szavak (például a névelők) kiszűréséhez a felhőből. 

![[az alapértelmezett stopszavak aktiválása]](../media/tutorials/power-bi/default-stop-words.png)

Egy kicsit lejjebb ugyanezen a panelen kapcsolja ki a **Szöveg elforgatása** és a **Cím** beállítást.

![[fókusz mód aktiválása]](../media/tutorials/power-bi/word-cloud-focus-mode.png)

A jelentésben a Fókusz mód eszközre kattintva közelebbről is megtekintheti a szófelhőt. Az eszköz kinagyítja a szófelhőt a teljes munkaterületre, amint az alább látható.

![[Egy szófelhő]](../media/tutorials/power-bi/word-cloud.png)

## <a name="more-text-analytics-services"></a>A Text Analytics további szolgáltatásai

A Text Analytics szolgáltatás a Microsoft Azure Cognitive Services szolgáltatásainak egyike, és hangulatelemzési és nyelvfelismerési funkciókat is kínál. A nyelvfelismerés különösen hasznos, ha az ügyfelek visszajelzései nem mind angol nyelven születnek.

Mindkét további API hasonlóan működik, mint a Key Phrases API. Ezért közel azonos egyéni függvények használhatók ezek a Power BI Desktopba való integrálásához is. Csak hozzon létre egy üres lekérdezést, és illessze be a megfelelő kódot az alábbiak közül a Speciális szerkesztőbe a korábbihoz hasonló módon. (Ne feledkezzen meg a hozzáférési kulcsról!) Ezután, ahogy korábban is, adjon hozzá egy új oszlopot a táblázathoz a függvény használatával.

Az alábbi hangulatelemzési függvény egy pontszámot ad vissza, amely a szövegben kifejezett hangulatot méri.

```fsharp
// Returns the sentiment score of the text, from 0.0 (least favorable) to 1.0 (most favorable)
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/sentiment",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    sentiment   = jsonresp[documents]{0}[score]
in  sentiment
```

Íme két változat egy nyelvfelismerési függvényre. Az első a nyelv ISO-kódját adja vissza (például az angol esetében: `en`), a második pedig a közkeletű nevét (például: `English`). Észreveheti, hogy a két változat között csak a törzs utolsó sorában van eltérés.

```fsharp
// Returns the two-letter language code (e.g. en for English) of the text
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[iso6391Name]
in  language
```
```fsharp
// Returns the name (e.g. English) of the language in which the text is written
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/languages",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    language    = jsonresp[documents]{0}[detectedLanguages]{0}[name]
in  language
```

Végezetül íme a korábban bemutatott Key Phrases-függvény egy olyan változata, amely a kifejezéseket nem egyetlen, vesszővel tagolt sztringként, hanem listaobjektumként adja vissza. 

> [!NOTE]
> Azzal, hogy egyetlen sztringet kaptunk vissza, a szófelhőt egyszerűbben lehetett létrehozni. A lista azonban egy olyan formátum, amellyel a visszaadott kifejezések sokkal rugalmasabban kezelhetők a Power BI-ban. A listaobjektumok a Power BI Desktopban a Lekérdezésszerkesztő Átalakítás menüszalagjának Strukturált oszlop csoportjában szerkeszthetők.

```fsharp
// Returns key phrases from the text as a list object
(text) => let
    apikey      = "YOUR_API_KEY_HERE",
    endpoint    = "https://westus.api.cognitive.microsoft.com/text/analytics/v2.0/keyPhrases",
    jsontext    = Text.FromBinary(Json.FromValue(Text.Start(Text.Trim(text), 5000))),
    jsonbody    = "{ documents: [ { language: ""en"", id: ""0"", text: " & jsontext & " } ] }",
    bytesbody   = Text.ToBinary(jsonbody),
    headers     = [#"Ocp-Apim-Subscription-Key" = apikey],
    bytesresp   = Web.Contents(endpoint, [Headers=headers, Content=bytesbody]),
    jsonresp    = Json.Document(bytesresp),
    keyphrases  = jsonresp[documents]{0}[keyPhrases]
in  keyphrases
```

## <a name="next-steps"></a>További lépések

További információk a Text Analytics szolgáltatásról, a Power Query M képletnyelvről és a Power BI-ról.

> [!div class="nextstepaction"]
> [Text Analytics API-referencia](//westus.dev.cognitive.microsoft.com/docs/services/TextAnalytics.V2.0/operations/56f30ceeeda5650db055a3c6)

> [!div class="nextstepaction"]
> [Power Query M-referencia](//msdn.microsoft.com/library/mt211003.aspx)

> [!div class="nextstepaction"]
> [Power BI-dokumentáció](//powerbi.microsoft.com/documentation/powerbi-landing-page/)
