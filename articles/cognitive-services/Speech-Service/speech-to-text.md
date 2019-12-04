---
title: Beszéd – szöveg – beszéd szolgáltatás
titleSuffix: Azure Cognitive Services
description: A beszédfelismerési funkció lehetővé teszi a hangadatfolyamok valós idejű átírását olyan szöveggé, amelyet az alkalmazásai, eszközei vagy eszközei használhatnak, megjeleníthetnek, és műveleteket végezhetnek a parancs bemenetén. Ez a szolgáltatás zökkenőmentesen működik a szöveg-beszéd (Speech szintézis) és a beszédfelismerési funkciók használatával.
services: cognitive-services
author: erhopf
manager: nitinme
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/03/2019
ms.author: erhopf
ms.openlocfilehash: f04ad388922ad7f73bf4409f9a846291cbb08da3
ms.sourcegitcommit: 6bb98654e97d213c549b23ebb161bda4468a1997
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/03/2019
ms.locfileid: "74774008"
---
# <a name="what-is-speech-to-text"></a>Mi az a diktálás?

Az Azure Speech Services, más néven beszéd – szöveg szövegre való beszédfelismerés lehetővé teszi a hangadatfolyamok valós idejű átírását olyan szöveggé, amelyet az alkalmazások, az eszközök vagy az eszközök felhasználhatnak, megjeleníthetnek, és műveleteket végezhetnek a parancs bemenetének megfelelően. Ezt a szolgáltatást a Microsoft a Cortana és az Office-termékekhez használt felismerési technológiával működteti, és zökkenőmentesen együttműködik a fordítással és szöveg-beszédtel. Az elérhető beszéd – szöveg nyelvek teljes listáját a [támogatott nyelvek](https://docs.microsoft.com/azure/cognitive-services/speech-service/language-support#speech-to-text)című részben tekintheti meg.

Alapértelmezés szerint a beszédfelismerési szolgáltatás az univerzális nyelvi modellt használja. Ez a modell a Microsoft tulajdonában lévő adataival lett betanítva, és a felhőben van üzembe helyezve. Ez optimális a társalgási és a diktálási forgatókönyvek esetében. Ha egy egyedi környezetben beszéd-szövegeket használ az elismeréshez és az átíráshoz, létrehozhat és betaníthat egyéni akusztikai, nyelvi és kiejtési modelleket a környezeti zajok vagy az iparági specifikus szókincsek kezeléséhez.

A hanganyagot könnyedén rögzítheti egy mikrofonból, beolvashatja egy streamből, vagy a hangfájlokat a tárolóból is elérheti a Speech SDK és a REST API-k használatával. A Beszéd SDK 16 bites, 16 kHz/8 kHz frekvenciájú, egycsatornás WAV/PCM hangot támogat a beszédfelismeréshez. További hangformátumok a [beszéd – szöveg Rest-végpont](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis) vagy a [Batch transzkripciós szolgáltatás](https://docs.microsoft.com/azure/cognitive-services/speech-service/batch-transcription#supported-formats)használatával támogatottak.

## <a name="core-features"></a>Alapvető funkciók

A Speech SDK és a REST API-k segítségével elérhető funkciók:

| Használati eset | SDK | REST |
|--------- | --- | ---- |
| Rövid hosszúságú kimondott szöveg (< 15 másodperc) átirata. Csak egy végső átírási eredményt támogat. | Igen | Igen\* |
| Hosszú hosszúságú kimondott szöveg és folyamatos átviteli hang folyamatos átírása (> 15 másodperc). Támogatja az időközi és a végső átírási eredményeket. | Igen | Nem |
| A felismerés eredményeinek származtatása a [Luis](https://docs.microsoft.com/azure/cognitive-services/luis/what-is-luis)-mel. | Igen | Nincs\*\* |
| A hangfájlok kötegelt átírása aszinkron módon történik. | Nem  | Igen\*\*\* |
| Beszédfelismerési modellek létrehozása és kezelése. | Nem | Igen\*\*\* |
| Egyéni modell üzembe helyezésének létrehozása és kezelése. | Nem  | Igen\*\*\* |
| Pontossági tesztek létrehozásával mérhető az alapmodell és az egyéni modellek pontossága. | Nem  | Igen\*\*\* |
| Előfizetések kezelése. | Nem  | Igen\*\*\* |

\*_a REST-funkciók használatával akár 60 másodpercig is átviheti a hangot, és egy végső átírási eredményt fog kapni._

\*\*_Luis szándékait és entitásait külön Luis-előfizetés alapján lehet származtatni. Ezzel az előfizetéssel az SDK meghívja a LUIS-t az Ön számára, és biztosítja az entitások és a szándékok eredményét. A REST API a Luis saját maga hívja meg a LUIS-t, hogy kinyerje szándékait és entitásait a LUIS-előfizetésével._

\*\*\*_ezek a szolgáltatások a CRIS.ai végpont használatával érhetők el. Lásd a [hencegés referenciáját](https://westus.cris.ai/swagger/ui/index)._

## <a name="get-started-with-speech-to-text"></a>Ismerkedés a beszédfelismerési szöveggel

A legnépszerűbb programozási nyelveken gyors útmutatókat ajánlunk, amelyek mindegyike kevesebb, mint 10 perc alatt futtathatja a kódot. Ez a táblázat a Speech SDK gyors üzembe [helyezésének](https://aka.ms/csspeech#5-minute-quickstarts) teljes listáját tartalmazza platform és nyelv szerint rendezve. Az API-referenciák [itt](https://aka.ms/csspeech#reference)is megtalálhatók.

Ha inkább a beszéd-szöveg REST-szolgáltatást szeretné használni, lásd: [REST API](https://docs.microsoft.com/azure/cognitive-services/speech-service/rest-apis)-k.

## <a name="tutorials-and-sample-code"></a>Oktatóanyagok és mintakód

A Speech Services használatának lehetősége után próbálja ki az oktatóanyagot, amely bemutatja, hogyan ismerheti fel a beszédfelismerési szándékokat a Speech SDK és a LUIS használatával.

- [Oktatóanyag: beszédfelismerési szándékok felismerése a Speech SDK és a LUIS használatávalC#](how-to-recognize-intents-from-speech-csharp.md)

A Speech SDK mintakód a GitHubon érhető el. Ezek a minták olyan gyakori forgatókönyveket érintenek, mint például a hang fájlból vagy streamből való olvasása, a folyamatos és az egyszeri felvétel felismerése, valamint az egyéni modellek használata.

- [Beszéd – szöveg minták (SDK)](https://github.com/Azure-Samples/cognitive-services-speech-sdk)
- [Batch-átírási minták (REST)](https://github.com/Azure-Samples/cognitive-services-speech-sdk/tree/master/samples/batch)

## <a name="customization"></a>Testreszabás

A Speech Services által használt standard alapkonfiguráción kívül a modelleket a rendelkezésre álló adatokkal is testreszabhatja, így leküzdheti a beszédfelismerési korlátokat, például a beszéd stílusát, a szókincset és a háttérzajt, lásd: [Custom Speech](how-to-custom-speech.md)

> [!NOTE]
> A testreszabási lehetőségek nyelv/területi beállítás szerint változnak (lásd a [támogatott nyelveket](supported-languages.md)).

## <a name="migration-guides"></a>Migrálási útmutatók

> [!WARNING]
> A Bing Speech 2019. október 15-én lett leszerelve.

Ha alkalmazásai, eszközei vagy termékei a Bing Speech API-kat vagy Custom Speech használják, a rendszer útmutatók létrehozásával segít a Speech Services szolgáltatásba való Migrálás során.

- [Áttelepítés Bing Speechról a Speech Services szolgáltatásba](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-bing-speech)
- [Áttelepítés Custom Speechról a Speech Services szolgáltatásba](https://docs.microsoft.com/azure/cognitive-services/speech-service/how-to-migrate-from-custom-speech-service)

## <a name="reference-docs"></a>Segédanyagok

- [Beszéd SDK](https://aka.ms/csspeech)
- [Beszédfelismerési eszközök SDK](speech-devices-sdk.md)
- [REST API: beszéd – szöveg](rest-speech-to-text.md)
- [REST API: szövegről beszédre](rest-text-to-speech.md)
- [REST API: kötegelt átírás és testreszabás](https://westus.cris.ai/swagger/ui/index)

## <a name="next-steps"></a>Következő lépések

- [Ingyenes Speech Services-előfizetési kulcs beszerzése](get-started.md)
- [A Speech SDK beszerzése](speech-sdk.md)
