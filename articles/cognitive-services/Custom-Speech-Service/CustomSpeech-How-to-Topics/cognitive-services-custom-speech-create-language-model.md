---
title: Oktatóanyag – Nyelvi modell létrehozása a Custom Speech Service segítségével – Microsoft Cognitive Services | Microsoft Docs
description: Ez az oktatóanyag bemutatja, hogyan hozhat létre nyelvi modellt a Microsoft Cognitive Services Custom Speech Service szolgáltatásával.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: custom-speech
ms.topic: tutorial
ms.date: 05/03/2017
ms.author: panosper
ROBOTS: NOINDEX
ms.openlocfilehash: 29f5c5efb78e85e265b56cba9ba20daa123d334e
ms.sourcegitcommit: 32d218f5bd74f1cd106f4248115985df631d0a8c
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/24/2018
ms.locfileid: "46961042"
---
# <a name="tutorial-create-a-custom-language-model"></a>Oktatóanyag: Egyéni nyelvi modell létrehozása

Ebben az oktatóanyagban létrehoz egy egyéni nyelvi modellt a felhasználók által az alkalmazásba várhatóan beadott szóbeli vagy beírt szöveges lekérdezésekhez vagy kimondott szövegekhez. Azután az egyéni nyelvi modell és a Microsoft meglévő korszerű beszédmodelljeinek együttes használatával beszédinterakciót adhat az alkalmazáshoz.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Az adatok előkészítése
> * A nyelvi adatkészlet importálása
> * Az egyéni nyelvi modell létrehozása

Ha még nincs Cognitive Services-fiókja, hozzon létre egy [ingyenes fiókot](https://cris.ai), mielőtt hozzákezd.

## <a name="prerequisites"></a>Előfeltételek

Nyissa meg a [Cognitive Services-előfizetések](https://cris.ai/Subscriptions) oldalt, és ellenőrizze, hogy Cognitive Services-fiókja egy előfizetés alá tartozik-e.

Ha nincsenek előfizetések listázva, a **Get free subscription** (Ingyenes előfizetés igénylése) gombra kattintva létrehozhat egy fiókot a Cognitive Services szolgáltatásban. Vagy a **Connect existing subscription** (Meglévő előfizetés csatlakoztatása) gombra kattintva csatlakozhat egy, az Azure Portalon létrehozott Custom Search Service-előfizetéshez.

A Custom Search Service-előfizetések az Azure Portalon való létrehozásával kapcsolatos útmutatásért lásd a [Cognitive Services API-fiókok az Azure Portalon való létrehozását](../../cognitive-services-apis-create-account.md) ismertető cikket.

## <a name="prepare-the-data"></a>Az adatok előkészítése

Ha egyéni nyelvi modellt szeretne létrehozni az alkalmazás számára, meg kell adnia a rendszernek egy sor példát a kimondott szövegekre, például:

*   „A múlt héten csalánkiütése volt.”
*   „A betegnek szépen begyógyult sérvműtéthege van.”

A mondatoknak nem kell teljes vagy nyelvtanilag helyes mondatoknak lenniük, azonban pontosan tükrözniük kell a működés során várható beszédbemeneteket. A példáknak tükrözniük kell a felhasználók által az alkalmazással végrehajtott feladatok stílusát és tartalmát.

A nyelvi modell adatait egyszerű szöveges fájlként kell megadni, a területi beállításoktól függően US-ASCII vagy UTF-8 kódolásban. Az en-US beállítás esetén mindkét kódolás támogatott. A zh-CN beállítás esetén csak az UTF-8 támogatott (a BOM választható). A szövegfájlnak soronként egy példát (mondatot, kimondott szöveget vagy lekérdezést) kell tartalmaznia.

Ha egyes mondatokhoz nagyobb súlyt (fontosságot) szeretne rendelni, vegye fel többször az adatok közé. Jó, ha az ismétlések száma 10 és 100 közé esik. Ha 100-as normál skálát alkalmaz, ehhez képest egyszerűen súlyozhatja a mondatokat.

A nyelvi adatokra vonatkozó főbb követelményeket az alábbi táblázat foglalja össze.

| Tulajdonság | Érték |
|----------|-------|
| Szövegkódolás | en-US: US-ACSII vagy UTF-8, illetve zh-CN: UTF-8|
| Kimondott szövegek száma soronként | 1 |
| Maximális fájlméret | 200 MB |
| Megjegyzések | az egyes karaktereket 4-nél többször ne ismételje (például „aaaaa”)|
| Megjegyzések | ne alkalmazzon különleges karaktereket, például „\t” vagy bármely egyéb UTF-8-karaktert U+00A1 felett a [Unicode karaktertáblán](http://www.utf8-chartable.de/)|
| Megjegyzések | A rendszer az URI-azonosítókat sem fogadja be, mivel ezeknek nem létezik egységes kiejtése|

A szövegek importálásakor a rendszer normalizálja őket, hogy fel tudja dolgozni. Azonban bizonyos fontosabb normalizálási tevékenységeket a felhasználónak kell végrehajtania az adatok feltöltése _előtt_. A nyelvi adatok előkészítése során használandó megfelelő nyelv meghatározásához lásd az [átírási irányelveket](cognitive-services-custom-speech-transcription-guidelines.md).

## <a name="import-the-language-data-set"></a>A nyelvi adatkészlet importálása

Kattintson az „Acoustic Datasets” (Akusztikai adatkészletek) sorban az „Import” (Importálás) gombra, és a webhely megjeleníti az új adatkészlet feltöltésére szolgáló oldalt.

Amikor készen áll importálni a nyelvi adatkészletet, jelentkezzen be a [Custom Speech Service portálra](https://cris.ai).  Ezután kattintson a felső sávon a „Custom Speech” legördülő menüre, és válassza az „Adaptation Data” (Adaptációs adatok) lehetőséget. Ha most tölt fel először adatokat a Custom Speech Service szolgáltatásba, egy üres tábla jelenik meg „Datasets” (Adatkészletek) néven.

Új adatkészletek importálásához kattintson a „Language Datasets” (Nyelv adatkészletek) sorban az „Import” (Importálás) gombra, és a webhely megjeleníti az új adatkészlet feltöltésére szolgáló oldalt. Adja meg a nevet és egy leírást, amelyek alapján később azonosíthatja az adatkészletet. Ezután a „Choose File” (Fájl kiválasztása) gombra kattintva keresse meg a nyelvi adatokat tartalmazó szövegfájlt. Ezután kattintson az „Importálás” gombra, és a rendszer feltölti az adatkészletet. Az adatkészlet méretétől függően ez eltarthat néhány percig.

![kipróbálás](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets-import.png)

Ha az importálás befejeződött, a rendszer visszalép a nyelvi adattáblára, és megjelenik egy, a nyelvi adatkészletre vonatkozó bejegyzés. A rendszer ehhez egy egyedi azonosítót (GUID) rendelt hozzá. Az adatok aktuális állapota is látható a táblában. A feldolgozásra várva az állapot „Waiting” (Várakozik), az érvényesítés során „Processing” (Feldolgozás), majd „Complete” (Kész), ha az adatok használatra készek. Az adatok érvényesítéséhez a rendszer egy sor ellenőrzést végez a fájlban lévő szövegen, valamint bizonyos mértékű szövegnormalizálást végez az adatokon.

Ha az állapot „Complete” (Kész), a „View Report” (Jelentés megtekintése) gombra kattintva megtekintheti a nyelvi adatok ellenőrzésére vonatkozó jelentést. Ebben látható az ellenőrzésen sikeresen és sikertelenül átment kimondott szövegek száma, valamint a sikertelen kimondott szövegek részletei. Az alábbi példában két minta bizonyult sikertelennek az ellenőrzés során helytelen karakterek miatt (az adatkészletben az első két hangulatjelet, a második több, a nyomtatható ASCII karakterkészleten kívül eső karaktert tartalmaz).

![kipróbálás](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets-report.png)

Ha a nyelvi adatkészlet „Complete” (Kész) állapotra vált, a használatával létrehozható egy egyéni nyelvi modell.

![kipróbálás](../../../media/cognitive-services/custom-speech-service/custom-speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Egyéni nyelvi modell létrehozása

Ha a nyelvi adatok készen vannak, kattintson a „Menü” legördülő menü „Language Models” (Nyelvi modellek) elemére az egyéni nyelvi modell létrehozásának indításához. Ezen az oldalon a „Language Models” (Nyelvi modellek) tábla látható, amely az aktuális egyéni nyelvi modelleket tartalmazza. Ha még nem hozott létre egyéni nyelvi modellt, a táblázat üres. A tábla címe az aktuális területi beállítást mutatja. Ha egy másik nyelvhez szeretne egyéni nyelvi modellt létrehozni, kattintson a „Change Locale” (Területi beállítások módosítása) gombra. A támogatott nyelvekkel kapcsolatban további információt a [területi beállítások módosításával foglalkozó](cognitive-services-custom-speech-change-locale.md) szakaszban talál. Új modell létrehozásához kattintson a „Create New” (Új létrehozása) gombra a tábla címe alatt.

A „Create Language Model” (Nyelvi modell létrehozása) lapon a „Name” (Név) és a „Description” (Leírás) mezők kitöltésével adhat meg vonatkozó információkat a modellel kapcsolatban, például a használt adatkészletre vonatkozóan. Ezután válassza ki a legördülő menü „Base Language Model” (Alap nyelvi modell) elemét. Ez a modell szolgál a testreszabás kiindulópontjaként. Két alap nyelvi modell közül választhat. A _Microsoft Search and Dictation LM_ (Microsoft keresési és diktálási nyelvi modell) az alkalmazásoknak szóló beszéd, például parancsok, keresőlekérdezések vagy diktálás feldolgozásához alkalmas. A _Microsoft Conversational LM_ (Microsoft beszélgetési nyelvi modell) a beszélgetési stílusban elhangzott beszélgetések feldolgozásához alkalmas. Az ilyen típusú beszéd általában másik személynek szól, és telefonos ügyfélszolgálatokon vagy értekezleteken hangzik el.

Miután megadta az alap nyelvi modellt, válassza ki a testreszabáshoz használni kívánt nyelvi adatkészletet a „Language Data” (Nyelvi adatok) legördülő menüben.

![kipróbálás](../../../media/cognitive-services/custom-speech-service/custom-speech-language-models-create2.png)

Az akusztikai modell létrehozásához hasonlóan itt is tesztelheti offline az új modellt a feldolgozás befejezése után. Mivel ez a beszéd–szöveg konverzió teljesítményét értékeli, az offline vizsgálathoz egy akusztikai adatkészlet szükséges.

A nyelvi modell offline teszteléséhez jelölje be az „Offline Testing” (Offline tesztelés) jelölőnégyzetet. Ezután válassza ki az akusztikai modellt a legördülő menüből. Ha nem hozott létre egyéni akusztikai modelleket, a menü csak a Microsoft alap akusztikai modelljeit tartalmazza. Amennyiben beszélgetési nyelvi alapmodellt választott, most egy beszélgetési akusztikai modellt kell használnia. Amennyiben egy keresési és diktálási nyelvi modellt használ, egy keresési és diktálási akusztikai modellt kell választania.

Végül válassza ki az értékeléshez használni kívánt akusztikai adatkészletet.

Amikor készen áll a folyamat indítására, kattintson a „Create” (Létrehozás) gombra. Ez visszalépteti a nyelvi modelleket tartalmazó táblára. A tábla egy, a modellnek megfelelő új bejegyzést tartalmaz. A bejegyzés állapota a modell állapotát mutatja, és sorban a következő értékeket veheti fel: „Waiting” (Várakozik), „Processing” (Feldolgozás) és „Complete” (Kész).

Ha a modell eléri a „Complete” (Kész) állapotot, üzembe helyezhető a végponton. A végrehajtott offline tesztelés eredményei a „View Result” (Eredmény megtekintése) gombra kattintva tekinthetők meg.

Ha a modell „Name” (Név) vagy „Description” (Leírás) értékét bármikor módosítani szeretné, a nyelvi modellek táblájának megfelelő sorában kattintson az „Edit” (Szerkesztés) hivatkozásra.

## <a name="next-steps"></a>További lépések

Ebben az oktatóanyagban egy szövegekhez használható egyéni nyelvi modellt hoztunk létre. Hangfájlokkal és átiratokkal használható egyéni akusztikai modell létrehozásához lépjen tovább az akusztikai modellek létrehozásával foglalkozó oktatóanyagra.

> [!div class="nextstepaction"]
> [Egyéni akusztikai modell létrehozása](cognitive-services-custom-speech-create-acoustic-model.md)
