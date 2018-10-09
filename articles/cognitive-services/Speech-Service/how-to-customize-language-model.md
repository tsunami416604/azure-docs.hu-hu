---
title: Nyelvi modell létrehozása a Speech Services segítségével – Microsoft Cognitive Services
description: Arra vonatkozó információk, hogyan hozhat létre nyelvi modellt a Microsoft Cognitive Services Speech Services szolgáltatásával.
services: cognitive-services
author: PanosPeriorellis
ms.service: cognitive-services
ms.component: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: 54bf38bf5a5858a2d7ac7237f58fc4db386dbac1
ms.sourcegitcommit: 42405ab963df3101ee2a9b26e54240ffa689f140
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/28/2018
ms.locfileid: "47423323"
---
# <a name="tutorial-create-a-custom-language-model"></a>Oktatóanyag: Egyéni nyelvi modell létrehozása

Ebben a dokumentumban egy egyéni nyelvi modellt fog létrehozni. Azután az egyéni nyelvi modell és a Microsoft meglévő korszerű beszédmodelljeinek együttes használatával beszédinterakciót adhat az alkalmazáshoz.

A dokumentum a következőkről nyújt tájékoztatást:
> [!div class="checklist"]
> * Az adatok előkészítése
> * A nyelvi adatkészlet importálása
> * Az egyéni nyelvi modell létrehozása

Ha még nincs Cognitive Services-fiókja, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/try/cognitive-services/), mielőtt hozzákezd.

## <a name="prerequisites"></a>Előfeltételek

Ellenőrizze, hogy a Cognitive Services-fiókja össze van-e kapcsolva az előfizetésével. Ehhez nyissa meg a [Cognitive Services előfizetési](https://customspeech.ai/Subscriptions) lapját.

Az Azure Portalon létrehozott Speech Services-előfizetéshez való kapcsolódáshoz kattintson a **meglévő előfizetés csatlakoztatására** szolgáló gombra.

A Speech Services-előfizetések Azure Portalon való létrehozásával kapcsolatos útmutatásért lásd az [első lépések](get-started.md) oldalát.

## <a name="prepare-the-data"></a>Az adatok előkészítése

Ha egyéni nyelvi modellt szeretne létrehozni az alkalmazás számára, meg kell adnia a rendszernek egy sor például szolgáló kimondott szöveget, például:

*   „A betegnek a múlt héten csalánkiütése volt.”
*   „A betegnek szépen begyógyult sérvműtéthege van.”

A mondatoknak nem kell teljesnek vagy nyelvtanilag helyesnek lenniük, és pontosan tükrözniük kell a működés során várható beszédbemeneteket. A példáknak tükrözniük kell a felhasználók által az alkalmazással végrehajtott feladatok stílusát és tartalmát.

A nyelvi modell adatait UTF-8 BOM kódolásban kell megadni. A szövegfájlnak soronként egy példát (mondatot, kimondott szöveget vagy lekérdezést) kell tartalmaznia.

Ha egyes kifejezésekhez nagyobb súlyt (fontosságot) szeretne rendelni, vegyen fel több olyan kimondott szöveget az adatok közé, amely tartalmazza az adott kifejezést. 

A nyelvi adatokra vonatkozó főbb követelményeket az alábbi táblázat foglalja össze.

| Tulajdonság | Érték |
|----------|-------|
| Szövegkódolás | UTF-8 BOM|
| Kimondott szövegek száma soronként | 1 |
| Maximális fájlméret | 1,5 GB |
| Megjegyzések | Az egyes karaktereket négynél többször ne ismételje (például „aaaaa”)|
| Megjegyzések | Ne alkalmazzon különleges karaktereket, például „\t” vagy bármely egyéb UTF-8-karaktert U+00A1 felett a [Unicode karaktertáblán](http://www.utf8-chartable.de/)|
| Megjegyzések | A rendszer az URI-azonosítókat sem fogadja be, mivel ezeknek nem létezik egységes kiejtése|

A szövegek importálásakor a rendszer normalizálja őket, hogy fel tudja dolgozni. Azonban bizonyos fontosabb normalizálási tevékenységeket a felhasználónak kell végrehajtania az adatok feltöltése _előtt_. A nyelvi adatok előkészítése során használandó megfelelő nyelv meghatározásához lásd az [átírási irányelveket](prepare-transcription.md).

## <a name="language-support"></a>Nyelvi támogatás

Tekintse meg az egyéni **diktálási** nyelvi modellek [támogatott](supported-languages.md) nyelveinek listáját.



## <a name="import-the-language-data-set"></a>A nyelvi adatkészlet importálása

Kattintson a **Language Datasets** (Nyelvi adatkészletek) sorban az **Import** (Importálás) gombra, és a webhely megjeleníti az új adatkészlet feltöltésére szolgáló oldalt.

Amikor készen áll importálni a nyelvi adatkészletet, jelentkezzen be a [Speech Services portálra](https://customspeech.ai). Először is kattintson a felső sávon a **Custom Speech** legördülő menüre. Ezután válassza az **Adaptation Data** (Adaptációs adatok) lehetőséget. Amikor először próbál adatokat feltölteni a Speech Servicesbe, egy üres tábla jelenik meg **Datasets** (Adatkészletek) néven.

Új adatkészlet importálásához kattintson a **Language Datasets** (Nyelvi adatkészletek) sorban az **Import** (Importálás) gombra. A webhely megjeleníti az új adatkészlet feltöltésére szolgáló oldalt. Adja meg a **nevet** és egy **leírást**, amelyek alapján később azonosíthatja az adatkészletet, majd válassza ki a területi beállítást. 

Ezután a **Choose File** (Fájl kiválasztása) gombra kattintva keresse meg a nyelvi adatokat tartalmazó szövegfájlt. Kattintson az **Import** (Importálás) gombra, és a rendszer feltölti az adatkészletet. Az adatkészlet méretétől függően az importálás eltarthat néhány percig.

![Kipróbálás](media/stt/speech-language-datasets-import.png)

Ha az importálás befejeződött, a nyelvi adatok között megjelenik egy, a nyelvi adatkészletre vonatkozó bejegyzés. A rendszer ehhez egy egyedi azonosítót (GUID) rendelt hozzá. Az adatok aktuális állapota is látható a táblában. A feldolgozásra várva az állapot **Waiting** (Várakozik), az érvényesítés során **Processing** (Feldolgozás), majd **Complete** (Kész), ha az adatok használatra készek. Az adatok érvényesítéséhez a rendszer egy sor ellenőrzést végez a fájlban lévő szövegen, és bizonyos mértékű szövegnormalizálást is végez az adatokon.

Ha az állapot **Complete** (Kész), a **View Report** (Jelentés megtekintése) gombra kattintva megtekintheti a nyelvi adatok ellenőrzésére vonatkozó jelentést. Ebben látható az ellenőrzésen sikeresen és sikertelenül átment kimondott szövegek száma, valamint a sikertelen kimondott szövegek részletei. Az alábbi példában két minta bukta el az ellenőrzést, helytelen karakterek miatt. (Az adatkészletben az első sor két tab karaktert, a második több olyan karaktert tartalmaz, amely nem része a nyomtatható ASCII karakterkészletnek, míg a harmadik sor üres.)

![Kipróbálás](media/stt/speech-language-datasets-report.png)

Ha a nyelvi adatkészlet **Complete** (Kész) állapotra vált, a használatával létrehozható egy egyéni nyelvi modell.

![Kipróbálás](media/stt/speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Egyéni nyelvi modell létrehozása

Ha a nyelvi adatok készen vannak, válassza a **Menu** legördülő menü **Language Models** (Nyelvi modellek) elemét az egyéni nyelvi modell létrehozásának indításához. Ezen az oldalon a **Language Models** (Nyelvi modellek) táblázat látható, amely az aktuális egyéni nyelvi modelleket tartalmazza. Ha még nem hozott létre egyéni nyelvi modellt, a táblázat üres. A aktuális területi beállítás a táblázatban a megfelelő adatbejegyzés mellett jelenik meg.

Bármely művelet elvégzése előtt ki kell választani a megfelelő területi beállítást. Az aktuális területi beállítás minden adat-, modell- és üzembehelyezési oldalon megjelenik a táblázat címében. A területi beállítás módosításához kattintson a táblázat címe alatt található **Change Locale** (Területi beállítás módosítása) gombra.  Ez a területi beállítás jóváhagyási oldalára irányítja át. A táblázathoz való visszatéréshez kattintson az **OK** gombra.

A Create Language Model (Nyelvi modell létrehozása) lapon a **Name** (Név) és a **Description** (Leírás) mezők kitöltésével követheti nyomon a modellel kapcsolatos információkat, például azt, hogy milyen adatkészletet használ. Ezután válassza ki a legördülő menü **Base Language Model** (Alap nyelvi modell) elemét. Ez a modell szolgál a testreszabás kiindulópontjaként. 

Két alap nyelvi modell közül választhat. A Search and Dictation (Keresési és diktálási) modell az alkalmazásokhoz intézett beszéd, például parancsok, keresőlekérdezések vagy diktálás feldolgozásához alkalmas. A Conversational (Beszélgetési) modell a beszélgetési stílusban elhangzott beszélgetések feldolgozására való. Az ilyen típusú beszéd általában másik személynek szól, és telefonos ügyfélszolgálatokon vagy értekezleteken hangzik el. 

A „Create Language Model” (Nyelvi modell létrehozása) lapon a **Name** (Név) és a **Description** (Leírás) mezők kitöltésével követheti nyomon a modellel kapcsolatos információkat, például azt, hogy milyen adatkészletet használt. Ezután válassza ki a legördülő menü **Base Language Model** (Alap nyelvi modell) elemét. Ez a modell szolgál a testreszabás kiindulópontjaként. Két alap nyelvi modell közül választhat. 

A Search and Dictation (Keresési és diktálási) modell az alkalmazásokhoz intézett beszéd, például parancsok, keresőlekérdezések vagy diktálás feldolgozásához alkalmas. A Conversational (Beszélgetési) modell a beszélgetési stílusban elhangzott beszélgetések feldolgozására való. Az ilyen típusú beszéd általában másik személynek szól, és telefonos ügyfélszolgálatokon vagy értekezleteken hangzik el. Egy új, „Universal” (Univerzális) nevű modell is általánosan elérhetővé vált. A Universal (Univerzális) modell célja, hogy a használatával minden beszédhelyzet kezelhető legyen, és a jövőben felváltsa a Search and Dictation (Keresési és diktálási) és Conversational (Beszélgetési) modelleket.

A következő példában látható módon, az alap nyelvi modell kiválasztása után a **Language Data** (Nyelvi adatok) legördülő menüből válassza ki azt a nyelvi adatkészletet, amelyet a testreszabáshoz szeretne használni.

![Kipróbálás](media/stt/speech-language-models-create2.png)

Az akusztikai modell létrehozásához hasonlóan itt is tesztelheti offline az új modellt a feldolgozás befejezése után. A modellelemzésekhez akusztikai adatkészlet szükséges.

A nyelvi modell offline teszteléséhez jelölje be az **Offline Testing** (Offline tesztelés) jelölőnégyzetet. Ezután válassza ki az akusztikai modellt a legördülő menüből. Ha nem hozott létre egyéni akusztikai modelleket, a menü csak a Microsoft alap akusztikai modelljeit tartalmazza. Amennyiben beszélgetési nyelvi alapmodellt választott, most egy beszélgetési akusztikai modellt kell használnia. Amennyiben egy keresési és diktálási nyelvi modellt használ, egy keresési és diktálási akusztikai modellt kell választania.

Végül válassza ki az értékeléshez használni kívánt akusztikai adatkészletet.

A feldolgozás elindításához válassza a **Create** (Létrehozás) lehetőséget. Ezután megjelenik a nyelvi modelleket tartalmazó tábla. A tábla egy, a modellnek megfelelő új bejegyzést tartalmaz. A bejegyzés állapota a modell állapotát mutatja, és egymás után a következő értékeket veheti fel: **Waiting** (Várakozik), **Processing** (Feldolgozás) és **Complete** (Kész).

Ha a modell eléri a **Complete** (Kész) állapotot, üzembe helyezhető a végponton. A **View Result** (Eredmény megtekintése) gombra kattintva az offline tesztelés eredményei tekinthetők meg, amennyiben végzett ilyen tesztet.

Ha a modell **nevét** vagy **leírását** bármikor módosítani szeretné, a nyelvi modellek táblájának megfelelő sorában kattintson az **Edit** (Szerkesztés) hivatkozásra.

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech Services-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [Beszéd felismerése C# nyelven](quickstart-csharp-dotnet-windows.md)
- [Git-mintaadatok](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
