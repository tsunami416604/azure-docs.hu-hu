---
title: Hangtartalom létrehozása – beszédfelismerési szolgáltatás
titleSuffix: Azure Cognitive Services
description: A hangtartalom létrehozása egy online eszköz, amely lehetővé teszi a Microsoft szöveg-beszéd kimenetének testreszabását és finomhangolását az alkalmazásokhoz és termékekhez.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 11/04/2019
ms.author: erhopf
ms.openlocfilehash: 2fa4bcb9db6f836346167b76e609a1437df88b04
ms.sourcegitcommit: 428fded8754fa58f20908487a81e2f278f75b5d0
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2019
ms.locfileid: "74554990"
---
# <a name="audio-content-creation"></a>Hanganyag létrehozása

A [hangtartalom létrehozása](https://aka.ms/audiocontentcreation) egy online eszköz, amely lehetővé teszi a Microsoft szöveg-beszéd kimenetének testreszabását és finomhangolását az alkalmazásokhoz és termékekhez. Ezzel az eszközzel pontosabban hangolhatja a nyilvános és az egyéni hangokat, és kezelheti a felhőben a kimenetet.

A hangtartalom-létrehozási eszköz a [Speech szintézis Markup Language (SSML) nyelvén](speech-synthesis-markup.md)alapul. A Testreszabás és a finomhangolás egyszerűsítése érdekében a hangtartalom létrehozása lehetővé teszi, hogy valós időben vizuálisan vizsgálja meg a szöveg és a beszéd eredményét.

## <a name="how-does-it-work"></a>Hogyan működik?

Ez az ábra a testreszabott beszéd-szöveg kimenetek finomhangolásához és exportálásához szükséges lépéseket mutatja be. Az alábbi hivatkozásokat követve további információkat tudhat meg az egyes lépésekről.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. Első lépésként létre kell [hoznia egy Azure-fiókot, regisztrálnia kell egy beszédfelismerési erőforrást, és be kell szereznie egy előfizetési kulcsot](#create-a-speech-resource). Ha rendelkezik előfizetési kulccsal, használhatja a beszédfelismerési szolgáltatás meghívására, valamint a [hangtartalom létrehozásához](https://aka.ms/audiocontentcreation).
2. [Hozzon létre egy hang-hangolási fájlt](#create-an-audio-tuning-file) egyszerű SZÖVEGES vagy SSML használatával.
3. Válassza ki a felvenni kívánt hangot és nyelvet. A hangtartalom létrehozása magában foglalja az összes [Microsoft szöveg-beszéd hangokat](language-support.md#text-to-speech). Használhatja a standard, a neurális vagy a saját egyéni hangját is.
   >[!NOTE]
   > Az egyéni neurális hangok számára elérhető a vezérelt hozzáférés, amely lehetővé teszi, hogy a természetes hangú beszédhez hasonló, nagy felbontású hangokat hozzon létre. További részletekért lásd: [kapuzás folyamat](https://aka.ms/ignite2019/speech/ethics).

4. Tekintse át az alapértelmezett eredményt. Ezután a hangolás eszközzel állíthatja be a kiejtés, a szurok, a sebesség, a hanglejtés, a hangstílus és egyebek beállítását. A beállítások teljes listájáért lásd: [Speech szintézis Markup Language](speech-synthesis-markup.md).
5. [A beállított hang](#export-tuned-audio)mentése és exportálása. Ha menti a hangolási sávot a rendszeren, folytathatja a munkát, és megismételheti a kimenetet. Ha elégedett a kimenettel, létrehozhat egy hang-létrehozási feladatot az exportálási funkcióval. Megfigyelheti az exportálási feladat állapotát, és letöltheti a kimenetet az alkalmazásaival és termékeivel való használatra.
6. Az utolsó lépés az egyéni hangolt hang használata az alkalmazásokban és a termékekben.

## <a name="create-a-speech-resource"></a>Beszédfelismerési erőforrás létrehozása

Kövesse az alábbi lépéseket egy beszédfelismerési erőforrás létrehozásához és a Speech studióhoz való kapcsolódáshoz.

1. Kövesse ezeket az utasításokat [egy Azure-fiók regisztrálásához](get-started.md#try-the-speech-service-using-a-new-azure-account) és [egy beszédfelismerési erőforrás létrehozásához](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-a-speech-resource-in-azure). Győződjön meg arról, hogy a díjszabási szintje **S0**értékre van állítva. Ha az egyik neurális hangját használja, győződjön meg arról, hogy az erőforrást egy [támogatott régióban](regions.md#standard-and-neural-voices)hozza létre.
2. Jelentkezzen be a [hangtartalom létrehozásához](https://aka.ms/audiocontentcreation).
3. Válasszon ki egy meglévő projektet, vagy kattintson az **új létrehozása**lehetőségre.
4. Az előfizetést bármikor módosíthatja a **Beállítások** lehetőséggel, amely a felső NAV-ban található.

## <a name="create-an-audio-tuning-file"></a>Hang-hangolási fájl létrehozása

A tartalmat kétféleképpen lehet beolvasni a hangtartalom-létrehozási eszközbe.

**1. lehetőség:**

1. Miután bejelentkezett a [hangtartalom-létrehozásba](https://aka.ms/audiocontentcreation), kattintson a **hanghangolás** lehetőségre egy új hang-hangolási fájl létrehozásához.
2. Amikor megjelenik a szerkesztési ablak, akár 10 000 karaktert is megadhat.
3. Ne felejtse el menteni.

**2. lehetőség:**

1. Miután bejelentkezett a [hangtartalom-létrehozásba](https://aka.ms/audiocontentcreation), kattintson a **feltöltés** gombra egy vagy több szövegfájl importálásához. Az egyszerű szöveg-és SSML egyaránt támogatottak.
2. A szövegfájlok feltöltésekor ellenőrizze, hogy a tartalom megfelel-e a követelményeknek.

   | Tulajdonság | Érték/megjegyzések |
   |----------|---------------|
   | Fájl formátuma | Egyszerű szöveg (. txt)<br/> SSML szövege (. txt)<br/> A zip-fájlok nem támogatottak |
   | Kódolási formátum | UTF-8 |
   | Fájlnév | Minden fájlnak egyedi névvel kell rendelkeznie. Az ismétlődések nem támogatottak. |
   | Szöveg hossza | A szövegfájlok nem haladhatják meg a 10 000 karaktert. |
   | SSML-korlátozások | Minden SSML-fájl csak egyetlen SSML tartalmazhat. |

### <a name="plain-text-example"></a>Egyszerű szöveges példa

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>SSML – példa

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, JessaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Hangolt hang exportálása

Miután áttekintette a hangkimenetet, és elégedett a hangolással és a beállítással, exportálhatja a hangot.

1. A [hangtartalom-létrehozási](https://aka.ms/audiocontentcreation) eszközben kattintson az **Exportálás** elemre a hanglétrehozási feladat létrehozásához.
2. Válassza ki a beállított hang kimeneti formátumát. A támogatott formátumok és mintavételi díjak listája alább található.
3. A feladat állapotát az **Exportálás feladat** lapon tekintheti meg. Ha a feladat meghiúsul, tekintse meg a teljes jelentés részletes információit tartalmazó oldalt.
4. Ha a feladat befejeződött, a hang letölthető a **hangkönyvtár** lapon.
5. Kattintson a **Letöltés** gombra. Most már készen áll az egyéni hangolt hang használatára alkalmazásaiban vagy termékeiben.

### <a name="supported-audio-formats"></a>Támogatott hangformátumok

| Formátum | 16 kHz-es mintavételi arány | 24 kHz-es mintavételi arány |
|--------|--------------------|--------------------|
| WAV | riff-16khz-16bit-mono-PCM | riff-24khz-16bit-mono-PCM |
| MP3 | hang-16khz-128kbitrate-mono-MP3 | hang-24khz-160kbitrate-mono-MP3 |

## <a name="see-also"></a>Lásd még:

* [Hosszú hang API](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Speech Studio](https://speech.microsoft.com)
