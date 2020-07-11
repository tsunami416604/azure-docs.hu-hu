---
title: Hangtartalom létrehozása – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A hangtartalom létrehozása egy online eszköz, amely lehetővé teszi a Microsoft szöveg-beszéd kimenetének testreszabását és finomhangolását az alkalmazásokhoz és termékekhez.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: 72fecbdc173a6174e54a28e48f983965f397ba6a
ms.sourcegitcommit: f844603f2f7900a64291c2253f79b6d65fcbbb0c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86224587"
---
# <a name="improve-synthesis-with-the-audio-content-creation-tool"></a>A hanganyag-létrehozási eszközzel fejlesztheti a szintézist

A [hangtartalom létrehozása](https://aka.ms/audiocontentcreation) egy online eszköz, amely lehetővé teszi a Microsoft szöveg-beszéd kimenetének testreszabását és finomhangolását az alkalmazásokhoz és termékekhez. Ezzel az eszközzel pontosabban hangolhatja a nyilvános és az egyéni hangokat, és kezelheti a felhőben a kimenetet.

A hangtartalom-létrehozási eszköz a [Speech szintézis Markup Language (SSML) nyelvén](speech-synthesis-markup.md)alapul. A Testreszabás és a finomhangolás egyszerűsítése érdekében a hangtartalom létrehozása lehetővé teszi, hogy valós időben vizuálisan vizsgálja meg a szöveg és a beszéd eredményét.

Tekintse meg a hangtartalom létrehozásának [videós oktatóanyagát](https://www.youtube.com/watch?v=O1wIJ7mts_w) .

## <a name="how-does-it-work"></a>Hogyan működik?

Ez az ábra azokat a lépéseket mutatja be, amelyek a szöveg-beszéd kimenetek finomhangolásához szükségesek. Az alábbi hivatkozásokat követve további információkat tudhat meg az egyes lépésekről.

![A szöveg és a beszéd közötti kimenet finomhangolásához szükséges lépések diagramja.](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. [Állítsa be az Azure-fiókját és a beszédfelismerési erőforrást](#set-up-your-azure-account-and-speech-resource) a kezdéshez.
2. [Hozzon létre egy hang-hangolási fájlt](#create-an-audio-tuning-file) egyszerű SZÖVEGES vagy SSML szkriptek használatával.
3. Válassza ki a parancsfájl tartalmának hangját és nyelvét. A hangtartalom létrehozása magában foglalja az összes [Microsoft szöveg-beszéd hangokat](language-support.md#text-to-speech). Használhatja a standard, a neurális vagy a saját egyéni hangját is.
   >[!NOTE]
   > Az egyéni neurális hangok számára elérhető a vezérelt hozzáférés, amely lehetővé teszi, hogy a természetes hangú beszédhez hasonló, nagy felbontású hangokat hozzon létre. További részletekért lásd: [kapuzás folyamat](https://aka.ms/ignite2019/speech/ethics).

4. Tekintse át az alapértelmezett szintézisi kimenetet. Ezután javítsa a kimenetet a kiejtés, a törés, a szurok, a sebesség, a hanglejtés, a hangstílus és más beállítások módosításával. A beállítások teljes listájáért lásd: [Speech szintézis Markup Language](speech-synthesis-markup.md). Itt látható egy [videó](https://youtu.be/mUvf2NbfuYU) , amely bemutatja, hogyan finomíthatja a beszédfelismerési kimenetet hanggal a tartalom létrehozásával. 
5. [A beállított hang](#export-tuned-audio)mentése és exportálása. Ha menti a hangolási sávot a rendszeren, folytathatja a munkát, és megismételheti a kimenetet. Ha elégedett a kimenettel, létrehozhat egy hang-létrehozási feladatot az exportálási funkcióval. Megfigyelheti az exportálási feladat állapotát, és letöltheti a kimenetet az alkalmazásaival és termékeivel való használatra.

## <a name="set-up-your-azure-account-and-speech-resource"></a>Az Azure-fiók és a beszédfelismerési erőforrás beállítása

1. A hangtartalom létrehozásához Azure-fiókkal kell rendelkeznie. A Microsoft-fiók használatával létrehozhat egy Azure-fiókot. [Egy Azure-fiók beállításához](get-started.md#new-resource)kövesse az alábbi utasításokat. 
2. [Hozzon létre egy beszédfelismerési erőforrást](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource) az Azure-fiókjával. Győződjön meg arról, hogy a díjszabási szintje **S0**értékre van állítva. Ha az egyik neurális hangját használja, győződjön meg arról, hogy az erőforrást egy [támogatott régióban](regions.md#standard-and-neural-voices)hozza létre.
2. Az Azure-fiók és a beszédfelismerési erőforrás beszerzése után használhatja a Speech Servicest, és elérheti a [hangtartalom létrehozását](https://aka.ms/audiocontentcreation).
3. Válassza ki azt a beszédfelismerési erőforrást, amelyre dolgozni szeretne. Itt létrehozhat egy új beszédfelismerési erőforrást is. 
4. A beszédfelismerési erőforrást bármikor módosíthatja a **Beállítások** lehetőséggel, amely a felső NAV-ban található.

## <a name="create-an-audio-tuning-file"></a>Hang-hangolási fájl létrehozása

A tartalmat kétféleképpen lehet beolvasni a hangtartalom-létrehozási eszközbe.

**1. lehetőség:**

1. Új hang-hangolási fájl létrehozásához kattintson az **új fájl** elemre.
2. Írja be vagy illessze be a tartalmat a szerkesztési ablakba. Az egyes fájlok karaktereinek száma 20 000. Ha a parancsfájl 20 000 karakternél hosszabb, a 2. lehetőség használatával a tartalmat automatikusan több fájlba is kioszthatja. 
3. Ne felejtse el menteni.

**2. lehetőség:**

1. Egy vagy több szövegfájl importálásához kattintson a **feltöltés** gombra. Az egyszerű szöveg-és SSML egyaránt támogatottak.
2. Ha a parancsfájl több mint 20 000 karakterből áll, Ossza szét a fájlt bekezdések, karakter vagy reguláris kifejezések alapján. 
3. A szövegfájlok feltöltésekor ellenőrizze, hogy a fájl megfelel-e a követelményeknek.

   | Tulajdonság | Érték/megjegyzések |
   |----------|---------------|
   | Fájlformátum | Egyszerű szöveg (. txt)<br/> SSML szövege (. txt)<br/> A zip-fájlok nem támogatottak |
   | Kódolási formátum | UTF-8 |
   | Fájlnév | Minden fájlnak egyedi névvel kell rendelkeznie. Az ismétlődések nem támogatottak. |
   | Szöveg hossza | A szövegfájlok nem haladhatják meg a 20 000 karaktert. |
   | SSML-korlátozások | Minden SSML-fájl csak egyetlen SSML tartalmazhat. |

### <a name="plain-text-example"></a>Egyszerű szöveges példa

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>SSML – példa

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Hangolt hang exportálása

Miután áttekintette a hangkimenetet, és elégedett a hangolással és a beállítással, exportálhatja a hangot.

1. Az **Exportálás** gombra kattintva hozzon létre egy hang-létrehozási feladatot. A **Hangkönyvtárba való exportálás** ajánlott, mivel a hosszú hangkimenetet és a teljes hangkimeneti élményt támogatja. Közvetlenül is letöltheti a hangot a helyi lemezre, de csak az első 10 perc érhető el. 
2. Válassza ki a beállított hang kimeneti formátumát. A támogatott formátumok és mintavételi díjak listája alább található.
3. A feladat állapotát az **Exportálás feladat** lapon tekintheti meg. Ha a feladat meghiúsul, tekintse meg a teljes jelentés részletes információit tartalmazó oldalt.
4. Ha a feladat befejeződött, a hang letölthető a **hangkönyvtár** lapon.
5. Kattintson a **Letöltés**gombra. Most már készen áll az egyéni hangolt hang használatára alkalmazásaiban vagy termékeiben.

### <a name="supported-audio-formats"></a>Támogatott hangformátumok

| Formátum | 16 kHz-es mintavételi arány | 24 kHz-es mintavételi arány |
|--------|--------------------|--------------------|
| WAV | riff-16khz-16bit-mono-PCM | riff-24khz-16bit-mono-PCM |
| MP3 | hang-16khz-128kbitrate-mono-MP3 | hang-24khz-160kbitrate-mono-MP3 |

## <a name="see-also"></a>Lásd még

* [Hosszú hang API](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
