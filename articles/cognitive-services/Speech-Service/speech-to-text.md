---
title: Beszéd – szöveg – beszéd szolgáltatás
titleSuffix: Azure Cognitive Services
description: Beszédfelismerési szolgáltatás – más néven beszéd-szöveg típusú funkció – lehetővé teszi a hangadatfolyamok valós idejű átírását olyan szöveggé, amelyet az alkalmazások, eszközök vagy eszközök felhasználhatnak, megjeleníthetnek, és műveleteket végezhetnek a parancs bemenetének megfelelően. Ezt a szolgáltatást a Microsoft a Cortana és az Office-termékekhez használt felismerési technológiával működteti, és zökkenőmentesen együttműködik a fordítással és szöveg-beszédtel.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 07/05/2019
ms.author: erhopf
ms.openlocfilehash: 435de6e6516256d350ad93b121dd5a38d1512ddc
ms.sourcegitcommit: 7c4de3e22b8e9d71c579f31cbfcea9f22d43721a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/26/2019
ms.locfileid: "68558850"
---
# <a name="what-is-speech-to-text"></a>Mi a beszéd – szöveg?

Az Azure Speech Services, más néven beszéd – szöveg szövegre való beszédfelismerés lehetővé teszi a hangadatfolyamok valós idejű átírását olyan szöveggé, amelyet az alkalmazások, az eszközök vagy az eszközök felhasználhatnak, megjeleníthetnek, és műveleteket végezhetnek a parancs bemenetének megfelelően. Ezt a szolgáltatást a Microsoft a Cortana és az Office-termékekhez használt felismerési technológiával működteti, és zökkenőmentesen együttműködik a fordítással és szöveg-beszédtel.  Az elérhető beszéd – szöveg nyelvek teljes listáját a [támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text)című részben tekintheti meg.

Alapértelmezés szerint a beszédfelismerési szolgáltatás az univerzális nyelvi modellt használja. Ez a modell a Microsoft tulajdonában lévő adataival lett betanítva, és a felhőben van üzembe helyezve. Ez optimális a társalgási és a diktálási forgatókönyvek esetében. Ha egy egyedi környezetben beszéd-szövegeket használ az elismeréshez és az átíráshoz, létrehozhat és betaníthat egyéni akusztikai, nyelvi és kiejtési modelleket a környezeti zajok vagy az iparági specifikus szókincsek kezeléséhez.

A hanganyagot könnyedén rögzítheti egy mikrofonból, beolvashatja egy streamből, vagy a hangfájlokat a tárolóból is elérheti a Speech SDK és a REST API-k használatával. A Speech SDK támogatja a WAV/PCM 16 bites, 16 kHz/8 kHz, egycsatornás hang használatát a beszédfelismeréshez. További hangformátumok a [beszéd – szöveg Rest-végpont](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) vagy a [Batch transzkripciós szolgáltatás](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)használatával támogatottak.

## <a name="core-features"></a>Alapvető funkciók

A Speech SDK és a REST API-k segítségével az alábbi funkciók érhetők el:

| Használati eset | SDK | REST |
|----------|-----|------|
| Rövid hosszúságú kimondott szöveg (< 15 másodperc) átirata. Csak a végső átírási eredményt támogatja. | Igen | Igen |
| Hosszú hosszúságú kimondott szöveg és folyamatos átviteli hang folyamatos átírása (> 15 másodperc). Támogatja az időközi és a végső átírási eredményeket. | Igen | Nem |
| A felismerés eredményeinek származtatása a [Luis](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)-mel. | Igen | nem\* |
| A hangfájlok kötegelt átírása aszinkron módon történik. | Nem | igen\** |
| Beszédfelismerési modellek létrehozása és kezelése. | Nem | igen\** |
| Egyéni modell üzembe helyezésének létrehozása és kezelése. | Nem | igen\** |
| Pontossági tesztek létrehozásával mérhető az alapmodell és az egyéni modellek pontossága. | Nem | igen\** |
| Előfizetések kezelése. | Nem | igen\** |

\* *A LUIS szándékok és entitások egy külön LUIS-előfizetése lehet nyerni. Ezzel az előfizetéssel az SDK meghívhatja a LUIS-t az Ön számára, és megadhatja az entitások és a szándékok eredményeit. A LUIS hívása a REST API-val saját magának, hogy szándékokat és entitásokat a LUIS-előfizetéshez.*

\** *Ezek a szolgáltatások az cris.ai-végpont használatával érhetők el. Lásd [](https://westus.cris.ai/swagger/ui/index)a hencegés referenciáját.*

## <a name="get-started-with-speech-to-text"></a>Ismerkedés a beszédfelismerési szöveggel

A legnépszerűbb programozási nyelveken gyors útmutatókat ajánlunk, amelyek mindegyike kevesebb, mint 10 perc alatt futtathatja a kódot. [Ez a táblázat](https://aka.ms/csspeech#5-minute-quickstarts) a platfrom és a Language által rendezett Speech SDK-gyors útmutatók teljes listáját tartalmazza.  Az API-referenciák [itt](https://aka.ms/csspeech#reference)is megtalálhatók.

Ha inkább a beszéd-szöveg REST-szolgáltatást szeretné használni, lásd: [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)-k.

## <a name="tutorials-and-sample-code"></a>Oktatóanyagok és mintakód

A Speech Services használatának lehetősége után próbálja ki az oktatóanyagot, amely bemutatja, hogyan ismerheti fel a beszédfelismerési szándékokat a Speech SDK és a LUIS használatával.

* [Oktatóanyag: Beszédfelismerési szándék felismerése a Speech SDK és a LUIS használatávalC#](how-to-recognize-intents-from-speech-csharp.md)

A Speech SDK mintakód a GitHubon érhető el. Ezek a minták olyan gyakori forgatókönyveket érintenek, mint például a hang fájlból vagy streamből való olvasása, a folyamatos és az egyszeri felvétel felismerése, valamint az egyéni modellek használata.

* [Beszéd – szöveg minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
* [Batch-átírási minták (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Testreszabás

A Speech Services által használt univerzális modellen kívül egyéni akusztikai, nyelvi és kiejtési modelleket is létrehozhat a felhasználói élményhez. A testreszabási lehetőségek listája:

| Modell | Leírás |
|-------|-------------|
| [Akusztikai modell](how-to-customize-acoustic-models.md) | Az egyéni akusztikai modell létrehozása akkor hasznos, ha az alkalmazás, az eszközök vagy az eszközök egy adott környezetben, például egy autóban vagy a gyárban, adott rögzítési feltételekkel vannak használatban. A példák közé tartozik az akcentussal való beszéd, a meghatározott háttérzajok vagy egy adott mikrofon használata a felvételhez. |
| [Nyelvi modell](how-to-customize-language-model.md) | Hozzon létre egy egyéni nyelvi modellt, amellyel javítható az iparágra jellemző szókincs és nyelvtan, például az orvosi szakkifejezések vagy az informatikai szakzsargonk átírása. |
| [Kiejtési modell](how-to-customize-pronunciation.md) | Egyéni kiejtési modellel megadhatja egy szó vagy kifejezés fonetikus formáját és megjelenítését. Ez hasznos testre szabott feltételek, például termékneveket vagy betűszavakat kezelése. A kezdéshez szüksége egy írásmódja fájlnevével – egy egyszerű .txt fájlt. |

> [!NOTE]
> A testreszabási lehetőségek nyelv/területi beállítás szerint változnak (lásd a [támogatott nyelveket](supported-languages.md)).

## <a name="migration-guides"></a>Áttelepítési útmutatók

> [!WARNING]
> A Bing Speech 2019. október 15-én lesznek leszerelve.

Ha alkalmazásai, eszközei vagy termékei a Bing Speech API-kat vagy Custom Speech használják, a rendszer útmutatók létrehozásával segít a Speech Services szolgáltatásba való Migrálás során.

* [Áttelepítés Bing Speechról a Speech Services szolgáltatásba](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
* [Áttelepítés Custom Speechról a Speech Services szolgáltatásba](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Segédanyagok

* [Beszéd SDK](https://aka.ms/csspeech)
* [Speech Devices SDK](speech-devices-sdk.md)
* [REST API: Speech-to-text](rest-speech-to-text.md)
* [REST API: Szöveg – beszéd](rest-text-to-speech.md)
* [REST API: Kötegelt átírás és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>További lépések

* [Ingyenes Speech Services-előfizetési kulcs beszerzése](get-started.md)
* [A Speech SDK beszerzése](speech-sdk.md)
