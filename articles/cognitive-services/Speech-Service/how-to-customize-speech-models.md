---
title: Beszéd szöveg modellekhez testreszabása |} Microsoft Docs
description: Beszédfelismerés javítása szöveg modellekhez beszéd testreszabásával.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: v-jerkin
manager: noellelacharite
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: v-jerkin
ms.openlocfilehash: 380c585f57737c0aa4ec99303c52d4567500b5f4
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348719"
---
# <a name="customize-speech-to-text-models-using-speech-service"></a>Beszéd szolgáltatással "Beszéd szöveg" modellek testreszabása

Ön jobb beszéd felismerési eredmények elérése érdekében a beszédfelismerés szolgáltatás lehetővé teszi által használt három modellek testreszabását a **szöveg beszédfelismerés** API. A modellek automatikusan képzett a megadott minta adatok.

| Modell | Mintaadatok | Cél |
|-------|---------------|---------|
| Akusztikus modell      | Beszéd-szöveg | Testre szabhatja az adott szavakra társított hangok (Fonémák). A vonat egy új hangsúlyos vagy dialektus, és beszéljen környezet stb. |
| Nyelvi modell      | Szöveg | Testre szabhatja a szöveget, amelyről ismert, hogy a szolgáltatás, és megismerkedhet használatukkal utterances. Adja hozzá a műszaki feltételeket, helyi helyneveket, stb. |
| Kiejtés | Szöveg | Javíthatja a felismerését a problémás szavak, vegyületek és rövidítéseket. Például a "talál threepio" leképezése ismeri fel, "C3PO" |

Miután létrehozta az új modell, hozzon létre egy egyéni végpontot, amely egy vagy több fenti célt a modellt használ. Dönthet úgy is egy alap modell által biztosított a beszédfelismerés szolgáltatást szeretné használni, például ha egy egyéni akusztikus modell és a nem szabványos nyelvi modell. Ezt követően használhatja a szabványos végponthoz helyett egyéni végpont REST-kérelmek. Minden egyes végpont tartozik egy *üzembehelyezés-azonosító* , hogy a beszédfelismerés SDK-val használható.

Testreszabási összes modell segítségével történik a [egyéni beszéd portal](https://www.cris.ai/).

## <a name="language-support"></a>Nyelvi támogatás

A következő nyelveket támogatja az egyéni **szöveg beszédfelismerés** nyelvi modellek.

| Kód | Nyelv |
|-|-|
| hu-HU | angol (Egyesült Államok) 
| zh-CN | kínai 
| SP-SP | spanyol (spanyolországi) 
| FR-FR | francia (Franciaország) 
| it-IT | olasz 
| de-DE | német
| pt-BR | portugál (brazíliai)
| ru-RU | orosz
| JP-JP | japán
| ar EG | arab (Egyiptom)

Egyéni akusztikus támogatnak csak angol (en-US). Kínai akusztikus adatkészletek importálni lehessen kínai nyelvi modellek teszteléshez.

Egyéni kiejtés jelenleg csak angol (en-US) és német (de-DE) támogatja.

## <a name="prepare-data-sets"></a>Adatok előkészítése

Minden egyes modell típusú némileg eltérő adatait és formázását, van szükség, itt leírt módon.

| Modell | A kínált szolgáltatás      |
|-------|-----------------------|
| Akusztikus | A teljes utterances hangfájlok tartalmazó ZIP-fájl, és a fájlok transcriptions tartalmazó szövegfájl. A fájl minden egyes sorban kell állnia a fájlt, egy lap (ASCII 9) és a szöveges nevét.|
| Nyelv | Soronként egy utterance tartalmazó szövegfájl. |
| Kiejtés | Soronként egy kiejtés mutató tartalmazó szövegfájl. Minden olyan megjelenítési (word vagy rövidítése), egy lap (ASCII 9) követ és a szóbeli képernyőn (a kívánt kiejtés) megadása.  |

Szövegfájlok kell követnie a [szöveg írjanak elő irányelvek](prepare-transcription.md) a modell nyelvhez.

## <a name="prepare-audio-files"></a>Hangfájlok előkészítése

Hangfájlok akusztikus modellek kell rögzítheti egy reprezentatív helyen reprezentatív felhasználók számos (kivéve, ha a cél egy hangalapú felismerést optimalizálása érdekében), a felhasználók rendelkeznek hasonló mikrofon segítségével. Ebben a táblázatban ismertetett összes hang minta által megkívánt formátumban.

| Tulajdonság | Szükséges érték |
|----------|------|
Fájlformátum | RIFF (WAV)
Mintavételi gyakoriság | 8000 Hz vagy 16 000 Hz
Csatornák | 1 (monó)
A minta formátumban | PCM, 16 bites egész szám
Fájl időtartama | 0,1 és 60 másodperc között
Értéke a csend gyűrű | 0,1 másodpercenként
Archiválási formátum | zip
Archívum maximális mérete | 2 GB

Ha képzési egy modell zajos háttér, gyári vagy egy autó, például a munkahelyi tipikus háttérzaj elején vagy végén néhány minták néhány másodperc közé tartozik. Csak zaj minták nem tartalmaznak.

## <a name="upload-data-sets"></a>Adatok feltöltése

Hozzon létre egy egyéni modell, először töltse fel az adatokat, majd a képzés megkezdéséhez.

1.  Jelentkezzen be a [egyéni beszéd portal](https://www.cris.ai/).

1.  Válassza ki a létrehozni kívánt adatkészlet a **egyéni beszéd** menü - akusztikus modell, nyelvi modell nyelvi adatait, vagy kiejtés kiigazítása adatait. Az adott típusú (ha van ilyen) meglévő adatkészletek listája jelenik meg.

1. Válassza ki a nyelvet kattintva **módosítás területi**.

1.  Kattintson a **importálási új** , és adja meg a nevét és leírását, új adatkészlet.

1. Válassza ki az adatfájlokat, készítse elő.

1. Kattintson a **importálási** tölti fel az adatokat, és az érvényesítés megkezdéséhez. Az érvényesítés biztosítja, hogy minden fájl a megfelelő formátumban vannak-e. Érvényesítési eltarthat néhány másodpercig.

## <a name="create-a-model"></a>Modell létrehozása

 Az adatkészlet ellenőrzését követően az alábbiak szerint is betanítani a modellt.

> [!NOTE]
> Kiejtés adatokat nem szükséges szükség.

1. Válassza ki a modell alapján hoz létre a **egyéni beszéd** menüben, majd kattintson a **új létrehozása.**

1. Válassza ki a modell területi beállításokat.

1. Válassza ki az új modell egy alap modelljét. A kiválasztott alap modell határozza meg, hogy a felismerési módok, amelynek a modell is használható, valamint az adatok készletében nem szereplő adatokról a fallback szolgál.

1.  Válassza ki, amelyből a modell létrehozni az adatkészlet. Adatkészlet modellek tetszőleges számú felhasználhatja.

1. Kattintson a **létrehozása** az új modell betanítása megkezdéséhez.

## <a name="test-a-model"></a>A modell teszteléséhez

A modell létrehozási folyamat részeként a modell tanítását egy akusztikus adatkészlet tesztelheti. Az új modell segítségével az adatkészlet hangfájlok és az eredményeket a megfelelő szöveget tesztelése beszéd felismerésére. A legjobb eredmények elérése érdekében használja a modell létrehozásához használt egy másik akusztikus adatkészletet.

## <a name="custom-endpoint"></a>Egyéni végpont

Egyéni akusztikus modellek vagy nyelvi modellek létrehozása után is telepítheti őket az egyéni **szöveg beszédfelismerés** végpont. A létrehozott fiók engedélyezett hívások rá.

A végpont létrehozásához válassza a **központi telepítések** a a **egyéni beszéd** menü az oldal tetején. A **központi telepítések** lapon található a jelenlegi egyéni végpontokat, ha bármelyik hozott létre. Kattintson a **hozzon létre új** új végpont létrehozásához.

Válassza ki a használni kívánt modellek a **akusztikus modell** és **nyelvi modell** sorolja fel. A választható mindig adja meg az alap Microsoft modellek. Nem lehet, hogy conversational modellek keverje keresési, és modellek előírják, így olyan akusztikus modell kiválasztásakor az elérhető nyelvi modellek, és ez fordítva is igaz. Az azonos modellek végpontok tetszőleges számú lehet használni.

Kattintson a **létrehozása** a modellek kiválasztása után. Az új végpont létesítése akár 30 percet is tarthat.

Amikor készen áll a végponthoz, válassza ki azt a **központi telepítések** tábla az URI és a telepítési azonosítót. Az egyéni végpontokat is használhatja a [Rest API](rest-apis.md#speech-to-text) és a [beszéd SDK](speech-sdk.md). A [Kódminták](samples.md) szöveg végpont egy egyéni beszédfelismerés használatának példája tartalmazza.

## <a name="next-steps"></a>További lépések

- [Beszéd próbaverziós előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
- [A C# beszéd felismerése](quickstart-csharp-windows.md)
