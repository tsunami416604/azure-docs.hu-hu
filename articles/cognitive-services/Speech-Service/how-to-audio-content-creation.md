---
title: Audiotartalom-létrehozás – beszédszolgáltatás
titleSuffix: Azure Cognitive Services
description: Az audiotartalom-létrehozás egy online eszköz, amely lehetővé teszi a Microsoft szövegfelolvasó kimenetének testreszabását és finomhangolására az alkalmazások és termékek számára.
services: cognitive-services
author: trevorbye
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 01/31/2020
ms.author: trbye
ms.openlocfilehash: a263e7e17cda64a8519bab215f97fdf26e88d9d2
ms.sourcegitcommit: b80aafd2c71d7366838811e92bd234ddbab507b6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81402245"
---
# <a name="improve-synthesis-with-audio-content-creation"></a>A szintézis javítása a hangtartalom létrehozásával

[Az audiotartalom-létrehozás](https://aka.ms/audiocontentcreation) egy online eszköz, amely lehetővé teszi a Microsoft szövegfelolvasó kimenetének testreszabását és finomhangolására az alkalmazások és termékek számára. Ezzel az eszközzel finomhangolhatja a nyilvános és egyéni hangokat a pontosabb természetes kifejezések érdekében, és kezelheti a kimenetet a felhőben.

A Hangtartalom-létrehozás eszköz a [beszédszintetizáló nyelvi (SSML)](speech-synthesis-markup.md)nyelven alapul. A testreszabás és a hangolás egyszerűsítése érdekében az audiotartalom-létrehozás lehetővé teszi, hogy vizuálisan, valós időben ellenőrizze a szövegfelolvasó kimeneteket.

## <a name="how-does-it-work"></a>Hogyan működik?

Ez az ábra a testreszabott beszéd-szöveg kimenetek finomhangolásához és exportálásához szükséges lépéseket mutatja be. Az alábbi hivatkozásokra kattintva többet is megtudhat az egyes lépésekről.

![](media/audio-content-creation/audio-content-creation-diagram.jpg)

1. Az első lépés [egy Azure-fiók létrehozása, egy beszédfelismerési erőforrás regisztrálása és előfizetési kulcs beolvasása.](#create-a-speech-resource) Miután rendelkezik előfizetési kulccsal, a beszédfelismerési szolgáltatás hívására és a [Hangtartalom-létrehozás](https://aka.ms/audiocontentcreation)elérésére is használható.
2. [Hanghangolási fájl létrehozása](#create-an-audio-tuning-file) egyszerű szöveg gel vagy SSML-el.
3. Válassza ki a hang és a nyelvet, hogy szeretné behangolni. Az audiotartalom-létrehozás magában foglalja a [Microsoft összes szövegfelolvasó hangját](language-support.md#text-to-speech). Használhatja a standard, neurális, vagy a saját egyéni hang.
   >[!NOTE]
   > A custom neural voices számára zárt hozzáférés érhető el, amely lehetővé teszi a természetes hangzású beszédhez hasonló nagy felbontású hangok létrehozását. További részleteket a [Gating-folyamat ban](https://aka.ms/ignite2019/speech/ethics)talál.

4. Tekintse át az alapértelmezett eredményt. Ezután a hangoló eszközzel módosíthatja a kiejtést, a hangmagasságot, a sebességet, az intonációt, a hangstílust és egyebeket. A beállítások teljes listáját a [Beszédszintetizáló jelölőnyelv ben található.](speech-synthesis-markup.md)
5. Mentse és [exportálja a hangolt hangot.](#export-tuned-audio) Amikor menti a hangolási sávot a rendszerben, folytathatja a munkát, és itefedheti a kimenetet. Ha elégedett a kimenettel, az exportálási funkcióval hanglétrehozási feladatot hozhat létre. Megfigyelheti az exportálási feladat állapotát, és letöltheti a kimenetet az alkalmazásokkal és termékekkel való használatra.
6. Az utolsó lépés az, hogy az egyéni hangolt hang az alkalmazások és termékek.

## <a name="create-a-speech-resource"></a>Beszédfelismerési erőforrás létrehozása

Kövesse az alábbi lépéseket egy beszédfelismerési erőforrás létrehozásához és a Beszédstúdióhoz való csatlakoztatásához.

1. Kövesse ezeket az utasításokat az [Azure-fiókregisztráláshoz](get-started.md#new-resource) és [hozzon létre egy beszédfelismerési erőforrást.](https://docs.microsoft.com/azure/cognitive-services/speech-service/get-started#create-the-resource) Győződjön meg arról, hogy a tarifacsomag **S0-re**van állítva. Ha az egyik neurális hangok, győződjön meg arról, hogy hozza létre az erőforrást egy [támogatott régióban.](regions.md#standard-and-neural-voices)
2. Jelentkezzen be [a hangtartalom-létrehozásba.](https://aka.ms/audiocontentcreation)
3. Jelöljön ki egy meglévő projektet, vagy kattintson **az Új létrehozása gombra.**
4. Előfizetését bármikor módosíthatja a felső navigációs rendszerben található **Beállítások** lehetőséggel.

## <a name="create-an-audio-tuning-file"></a>Hanghangolási fájl létrehozása

A tartalom kétféleképpen juthat be a Hangtartalom-készítő eszközbe.

**1. lehetőség:**

1. Miután bejelentkezett [a hangtartalom-létrehozásba,](https://aka.ms/audiocontentcreation)kattintson **a Hangolás gombra** egy új hanghangolási fájl létrehozásához.
2. Amikor megjelenik a szerkesztőablak, legfeljebb 10 000 karaktert adhat meg.
3. Ne felejts el spórolni.

**2. lehetőség:**

1. Miután bejelentkezett a [Hangtartalom-létrehozás](https://aka.ms/audiocontentcreation)ba, kattintson a **Feltöltés gombra** egy vagy több szövegfájl importálásához. Az egyszerű szöveg és az SSML egyaránt támogatott.
2. A szövegfájlok feltöltésekén ellenőrizze, hogy a tartalom megfelel-e ezeknek a követelményeknek.

   | Tulajdonság | Érték / Megjegyzések |
   |----------|---------------|
   | Fájlformátum | Egyszerű szöveg (.txt)<br/> SSML-szöveg (.txt)<br/> A zip-fájlok nem támogatottak |
   | Kódolási formátum | UTF-8 |
   | Fájlnév | Minden fájlnak egyedi névvel kell rendelkeznie. Az ismétlődések nem támogatottak. |
   | Szöveg hossza | A szövegfájlok nem haladhatják meg a 10 000 karaktert. |
   | SSML-korlátozások | Minden SSML-fájl csak egyetlen Darab SSML-t tartalmazhat. |

### <a name="plain-text-example"></a>Példa egyszerű szövegre

```txt
Welcome to use Audio Content Creation to customize audio output for your products.
```

### <a name="ssml-text-example"></a>Példa SSML-szövegre

```xml
<speak xmlns="http://www.w3.org/2001/10/synthesis" xmlns:mstts="http://www.w3.org/2001/mstts" version="1.0" xml:lang="en-US">
    <voice name="Microsoft Server Speech Text to Speech Voice (en-US, AriaNeural)">
    Welcome to use Audio Content Creation <break time="10ms" />to customize audio output for your products.
    </voice>
</speak>
```

## <a name="export-tuned-audio"></a>Hangolt hang exportálása

Miután áttekintette a hangkimenetet, és elégedett a hangolással és a beállítással, exportálhatja a hangot.

1. A [Hangtartalom-létrehozás](https://aka.ms/audiocontentcreation) **eszközben** kattintson az Exportálás gombra egy hangkészítő feladat létrehozásához.
2. Válassza ki a hanganyag kimeneti formátumát. A támogatott formátumok és a mintavételi arányok listája az alábbiakban érhető el.
3. A feladat állapotát a Tevékenység **exportálása** lapon tekintheti meg. Ha a feladat sikertelen, tekintse meg a részletes információs oldalt a teljes jelentésért.
4. Ha a feladat befejeződött, a hang letölthető a **Hangkönyvtár** lapon.
5. Kattintson **a Letöltés gombra.** Most már használhatja az egyénileg hangolt hangot az alkalmazásokban vagy a termékekben.

### <a name="supported-audio-formats"></a>Támogatott hangformátumok

| Formátum | 16 kHz-es mintavételi sebesség | 24 kHz-es mintavételi sebesség |
|--------|--------------------|--------------------|
| Wav | riff-16khz-16bit-mono-pcm | riff-24khz-16bit-mono-pcm |
| Mp3 | audio-16khz-128kbitrate-mono-mp3 | audio-24khz-160kbitrate-mono-mp3 |

## <a name="see-also"></a>Lásd még

* [Hosszú audio API](https://aka.ms/long-audio-api)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Beszéd Stúdió](https://speech.microsoft.com)
