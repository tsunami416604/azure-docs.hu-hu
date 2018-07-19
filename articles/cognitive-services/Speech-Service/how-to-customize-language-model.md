---
title: Nyelvi modell létrehozása a Speech Service segítségével – Microsoft Cognitive Services | Microsoft Docs
description: Ismerje meg, hogyan hozhat létre nyelvi modellt a Microsoft Cognitive Services Speech Service szolgáltatásával.
services: cognitive-services
author: PanosPeriorellis
manager: onano
ms.service: cognitive-services
ms.component: speech-service
ms.topic: tutorial
ms.date: 06/25/2018
ms.author: panosper
ms.openlocfilehash: 85e67be406b3d9723476821adfb09fc4db8dc1d1
ms.sourcegitcommit: 0b05bdeb22a06c91823bd1933ac65b2e0c2d6553
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/17/2018
ms.locfileid: "39068573"
---
# <a name="tutorial-create-a-custom-language-model"></a>Oktatóanyag: Egyéni nyelvi modell létrehozása

Ebben a dokumentumban egy egyéni nyelvi modellt hozhat létre, amelyet a Microsoft meglévő korszerű beszédmodelljeivel együtt használva beszédinterakciót adhat az alkalmazáshoz.

A dokumentum a következőkről nyújt tájékoztatást:
> [!div class="checklist"]
> * Az adatok előkészítése
> * A nyelvi adatkészlet importálása
> * Az egyéni nyelvi modell létrehozása

Ha még nincs Cognitive Services-fiókja, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/try/cognitive-services/), mielőtt hozzákezd.

## <a name="prerequisites"></a>Előfeltételek

Nyissa meg a [Cognitive Services-előfizetések](https://customspeech.ai/Subscriptions) oldalt, és ellenőrizze, hogy Cognitive Services-fiókja egy előfizetés alá tartozik-e.

A **Connect existing subscription** (Meglévő előfizetés csatlakoztatása) gombra kattintva csatlakozhat egy, az Azure Portalon létrehozott Speech Service-előfizetéshez.

A Speech Service-előfizetések az Azure Portalon való létrehozásával kapcsolatos útmutatásért lásd az [első lépések](get-started.md) oldalt.

## <a name="prepare-the-data"></a>Az adatok előkészítése

Ha egyéni nyelvi modellt szeretne létrehozni az alkalmazás számára, meg kell adnia a rendszernek egy sor példát a kimondott szövegekre, például:

*   „A betegnek a múlt héten csalánkiütése volt.”
*   „A betegnek szépen begyógyult sérvműtéthege van.”

A mondatoknak nem kell teljes vagy nyelvtanilag helyes mondatoknak lenniük, azonban pontosan tükrözniük kell a működés során várható beszédbemeneteket. A példáknak tükrözniük kell a felhasználók által az alkalmazással végrehajtott feladatok stílusát és tartalmát.

A nyelvi modell adatait UTF-8 BOM kódolásban kell megadni. A szövegfájlnak soronként egy példát (mondatot, kimondott szöveget vagy lekérdezést) kell tartalmaznia.

Ha egyes kifejezésekhez nagyobb súlyt (fontosságot) szeretne rendelni, vegyen fel több olyan kimondott szöveget az adatok közé, amely tartalmazza az adott kifejezést. 

A nyelvi adatokra vonatkozó főbb követelményeket az alábbi táblázat foglalja össze.

| Tulajdonság | Érték |
|----------|-------|
| Szövegkódolás | UTF-8 BOM|
| Kimondott szövegek száma soronként | 1 |
| Maximális fájlméret | 1,5 GB |
| Megjegyzések | az egyes karaktereket 4-nél többször ne ismételje (például „aaaaa”)|
| Megjegyzések | ne alkalmazzon különleges karaktereket, például „\t” vagy bármely egyéb UTF-8-karaktert U+00A1 felett a [Unicode karaktertáblán](http://www.utf8-chartable.de/)|
| Megjegyzések | A rendszer az URI-azonosítókat sem fogadja be, mivel ezeknek nem létezik egységes kiejtése|

A szövegek importálásakor a rendszer normalizálja őket, hogy fel tudja dolgozni. Azonban bizonyos fontosabb normalizálási tevékenységeket a felhasználónak kell végrehajtania az adatok feltöltése _előtt_. A nyelvi adatok előkészítése során használandó megfelelő nyelv meghatározásához lásd az [átírási irányelveket](prepare-transcription.md).

## <a name="language-support"></a>Nyelvi támogatás

Az alábbi nyelvek támogatottak az egyéni **diktálási** nyelvi modellek esetében.

A [támogatott nyelvek](supported-languages.md) teljes listájának megtekintéséhez kattintson ide.

## <a name="import-the-language-data-set"></a>A nyelvi adatkészlet importálása

Kattintson az „Language Datasets” (Nyelvi adatkészletek) sorban az „Import” (Importálás) gombra, és a webhely megjeleníti az új adatkészlet feltöltésére szolgáló oldalt.

Amikor készen áll importálni a nyelvi adatkészletet, jelentkezzen be a [Speech Service portálra](https://customspeech.ai).  Ezután kattintson a felső sávon a „Custom Speech” legördülő menüre, és válassza az „Adaptation Data” (Adaptációs adatok) lehetőséget. Amikor először próbál adatokat feltölteni a Speech Service szolgáltatásba, egy üres tábla jelenik meg „Datasets” (Adatkészletek) néven.

Új adatkészletek importálásához kattintson a „Language Datasets” (Nyelv adatkészletek) sorban az „Import” (Importálás) gombra, és a webhely megjeleníti az új adatkészlet feltöltésére szolgáló oldalt. Adja meg a nevet és egy leírást, amelyek alapján később azonosíthatja az adatkészletet, majd válassza ki a területi beállítást. Ezután a „Choose File” (Fájl kiválasztása) gombra kattintva keresse meg a nyelvi adatokat tartalmazó szövegfájlt. Ezután kattintson az „Importálás” gombra, és a rendszer feltölti az adatkészletet. Az adatkészlet méretétől függően az importálás eltarthat néhány percig.

![kipróbálás](media/stt/speech-language-datasets-import.png)

Ha az importálás befejeződött, a rendszer visszalép a nyelvi adattáblára, és megjelenik egy, a nyelvi adatkészletre vonatkozó bejegyzés. A rendszer ehhez egy egyedi azonosítót (GUID) rendelt hozzá. Az adatok aktuális állapota is látható a táblában. A feldolgozásra várva az állapot „Waiting” (Várakozik), az érvényesítés során „Processing” (Feldolgozás), majd „Complete” (Kész), ha az adatok használatra készek. Az adatok érvényesítéséhez a rendszer egy sor ellenőrzést végez a fájlban lévő szövegen, valamint bizonyos mértékű szövegnormalizálást végez az adatokon.

Ha az állapot „Complete” (Kész), a „View Report” (Jelentés megtekintése) gombra kattintva megtekintheti a nyelvi adatok ellenőrzésére vonatkozó jelentést. Ebben látható az ellenőrzésen sikeresen és sikertelenül átment kimondott szövegek száma, valamint a sikertelen kimondott szövegek részletei. Az alábbi példában két minta bizonyult sikertelennek az ellenőrzés során helytelen karakterek miatt (az adatkészletben az első sor két TAB karaktert, a második több, a nyomtatható ASCII karakterkészleten kívül eső karaktert tartalmaz, míg a harmadik sor üres).

![kipróbálás](media/stt/speech-language-datasets-report.png)

Ha a nyelvi adatkészlet „Complete” (Kész) állapotra vált, a használatával létrehozható egy egyéni nyelvi modell.

![kipróbálás](media/stt/speech-language-datasets.png)

## <a name="create-a-custom-language-model"></a>Egyéni nyelvi modell létrehozása

Ha a nyelvi adatok készen vannak, kattintson a „Menü” legördülő menü „Language Models” (Nyelvi modellek) elemére az egyéni nyelvi modell létrehozásának indításához. Ezen az oldalon a „Language Models” (Nyelvi modellek) tábla látható, amely az aktuális egyéni nyelvi modelleket tartalmazza. Ha még nem hozott létre egyéni nyelvi modellt, a táblázat üres. A aktuális területi beállítás a táblázatban a megfelelő adatbejegyzés mellett jelenik meg.

Bármely művelet elvégzése előtt ki kell választani a megfelelő területi beállítást. Az aktuális területi beállítás minden adat-, modell- és üzembehelyezési oldalon megjelenik a táblázat címében. A területi beállítás módosításához kattintson a táblázat címe alatt található „Change Locale” (Területi beállítás módosítása) gombra, amely a területi beállítás jóváhagyási oldalára irányítja át. A táblázathoz való visszatéréshez kattintson az „OK” gombra.

A „Create Language Model” (Nyelvi modell létrehozása) lapon a „Name” (Név) és a „Description” (Leírás) mezők kitöltésével adhat meg vonatkozó információkat a modellel kapcsolatban, például a használt adatkészletre vonatkozóan. Ezután válassza ki a legördülő menü „Base Language Model” (Alap nyelvi modell) elemét. Ez a modell szolgál a testreszabás kiindulópontjaként. Két alap nyelvi modell közül választhat. A Search and Dictation (Keresési és diktálási) modell az alkalmazásoknak szóló beszéd, például parancsok, keresőlekérdezések vagy diktálás feldolgozásához alkalmas. A Conversational (Beszélgetési) modell a beszélgetési stílusban elhangzott beszélgetések feldolgozására alkalmas. Az ilyen típusú beszéd általában másik személynek szól, és telefonos ügyfélszolgálatokon vagy értekezleteken hangzik el. Egy új, „Universal” (Univerzális) nevű modell is nyilvánosan elérhető. A Universal (Univerzális) modell célja, hogy a használatával minden beszédhelyzet kezelhető legyen, és a jövőben felváltsa a Search and Dictation (Keresési és diktálási) és Conversational (Beszélgetési) modelleket.

5.  Miután az alábbi példában megadta az alap nyelvi modellt, válassza ki a testreszabáshoz használni kívánt nyelvi adatkészletet a „Language Data” (Nyelvi adatok) legördülő menüben.

![kipróbálás](media/stt/speech-language-models-create2.png)

Az akusztikai modell létrehozásához hasonlóan itt is tesztelheti offline az új modellt a feldolgozás befejezése után. A modellelemzésekhez akusztikai adatkészlet szükséges.

A nyelvi modell offline teszteléséhez jelölje be az „Offline Testing” (Offline tesztelés) jelölőnégyzetet. Ezután válassza ki az akusztikai modellt a legördülő menüből. Ha nem hozott létre egyéni akusztikai modelleket, a menü csak a Microsoft alap akusztikai modelljeit tartalmazza. Amennyiben beszélgetési nyelvi alapmodellt választott, most egy beszélgetési akusztikai modellt kell használnia. Amennyiben egy keresési és diktálási nyelvi modellt használ, egy keresési és diktálási akusztikai modellt kell választania.

Végül válassza ki az értékeléshez használni kívánt akusztikai adatkészletet.

Amikor készen áll a folyamat indítására, kattintson a „Create” (Létrehozás) gombra, amely a nyelvi modellek táblázatára irányítja át. A tábla egy, a modellnek megfelelő új bejegyzést tartalmaz. A bejegyzés állapota a modell állapotát mutatja, és sorban a következő értékeket veheti fel: „Waiting” (Várakozik), „Processing” (Feldolgozás) és „Complete” (Kész).

Ha a modell eléri a „Complete” (Kész) állapotot, üzembe helyezhető a végponton. A végrehajtott offline tesztelés eredményei a „View Result” (Eredmény megtekintése) gombra kattintva tekinthetők meg.

Ha a modell „Name” (Név) vagy „Description” (Leírás) értékét bármikor módosítani szeretné, a nyelvi modellek táblájának megfelelő sorában kattintson az „Edit” (Szerkesztés) hivatkozásra.

## <a name="next-steps"></a>További lépések

- [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [Beszéd felismerése C# nyelven](quickstart-csharp-dotnet-windows.md)
- [Git-mintaadatok](https://github.com/Microsoft/Cognitive-Custom-Speech-Service)
