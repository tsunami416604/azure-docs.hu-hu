---
title: Tudnivalók a beszédfelismerés
description: A Speech to Text API-funkcióinak áttekintése.
titleSuffix: Microsoft Cognitive Services
services: cognitive-services
author: erhopf
ms.service: cognitive-services
ms.component: speech-service
ms.topic: article
ms.date: 05/07/2018
ms.author: erhopf
ms.openlocfilehash: 3d03cd6a5cceb805ed8ee34548bd1a3973e9d78b
ms.sourcegitcommit: c282021dbc3815aac9f46b6b89c7131659461e49
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/12/2018
ms.locfileid: "49167086"
---
# <a name="about-the-speech-to-text-api"></a>Tudnivalók a Speech to Text API

A **Speech to Text** API *transcribes* audiostreamek lejátszásával, szöveg, amely az alkalmazás a felhasználót, vagy mint való reagáláshoz bemeneti parancsot. Az API-k is használható, az SDK ügyféloldali kódtár (a támogatott platformok és nyelvek) vagy a REST API-t.

A **Speech to Text** API a következő szolgáltatásokat nyújtja:

- A Microsoft beszédfelismerési technológiáját speciális – ugyanaz a Cortana, az Office és más Microsoft-termékek használják.

- Valós idejű folyamatos felismerése. **Beszédfelismerés** lehetővé teszi a felhasználóknak lefényképezze hangot szöveggé valós időben. Ezenkívül támogatja fogadása eddig felismerhető szavak köztes eredményeket. A szolgáltatás automatikusan észleli a speech végén. Felhasználók is beállíthatja a további formázási beállításokat, beleértve a kis-és nagybetűk és írásjelek, vulgáris maszkolás és más néven Inverz szöveg normalizálási.

- Lexical és a megjelenített képernyő eredményeket adja vissza (lexikai eredményt, lásd: DetailedSpeechRecognitionResult példák vagy API-t).

- Számos használja a beszélt nyelv és Dialektusok támogatása. Minden egyes felismerés módban támogatott nyelvek teljes listáját lásd: [támogatott nyelvek](language-support.md#speech-to-text).

- Testreszabott nyelvi és akusztikai modell, így testre szabhatja a felhasználók speciális tartomány szöveg szóhasználati, hogy az alkalmazás, és beszéljen környezetben és különféle módja.

- Beszédfelismerés. Integráció révén [Language Understanding](https://docs.microsoft.com/azure/cognitive-services/luis/) (LUIS), akkor származtathatók szándékok és entitások speech. A felhasználók nem szükséges ismerni az alkalmazás szöveg szóhasználati, de tud adni, amit szeretnének a saját szavaival.

- Magabiztossági pontszámot ad vissza a szolgáltatásból vissza, ha megad egy részletes kimenet a speech konfigurációs objektum (SpeechConfig.OutputFormat tulajdonság). Majd használja az eredményt vagy Best() metódust, vagy (alábbihoz hasonló eredményt a szolgáltatás által visszaadott JSON-ból közvetlenül a pontszám letöltés. Properties.GetProperty(PropertyId.SpeechServiceResponse_JsonResult)).

## <a name="api-capabilities"></a>API-funkciók

Az a funkciók némelyike a **Speech to Text** API - különös tekintettel testreszabása – REST-en keresztül érhetők el. Az alábbi táblázat foglalja össze az egyes módszerek az API eléréséhez képességeit. 

| Használati eset | REST | SDK-k |
|-----|-----|-----|----|
| Lefényképezze az utterance (kifejezés egy rövid), például a parancs (hossz < 15 s). Nincs köztes eredményeket | Igen | Igen |
| A hosszabb utterance (> 15 mp) alapuló átírás | Nem | Igen |
| Streamelési audio-választható köztes eredményeket alapuló átírás | Nem | Igen |
| Hangszóró leképezések a LUIS-n keresztül ismertetése | nem\* | Igen |
| Pontossági tesztek létrehozása | Igen | Nem |
| A modellbetanítás adatkészletek feltöltése | Igen | Nem |
| Hozzon létre & beszédfelismerési modellek kezelése | Igen | Nem |
| Modell üzembe helyezett megoldások kezelése & létrehozása | Igen | Nem |
| Előfizetések kezelése | Igen | Nem |
| Modell üzembe helyezett megoldások kezelése & létrehozása | Igen | Nem |
| Modell üzembe helyezett megoldások kezelése & létrehozása | Igen | Nem |

> [!NOTE]
> A REST API-t valósít meg, amely korlátozza az API-kérelmek 25, 5 másodpercenként szabályozás. Üzenet hearders tájékoztatni fogja korlátját

\* *A LUIS szándékok és entitások egy külön LUIS-előfizetése lehet nyerni. Ennek az előfizetésnek az SDK a LUIS hívja meg, és adja meg az entitás- és leképezés eredményeket, valamint a speech beszédátírás. A LUIS hívása a REST API-val saját magának, hogy szándékokat és entitásokat a LUIS-előfizetéshez.*

## <a name="next-steps"></a>További lépések

* [Próbaverziós Speech-előfizetés beszerzése](https://azure.microsoft.com/try/cognitive-services/)
* [Gyors útmutató: beszédfelismerést a C#-ban](quickstart-csharp-dotnet-windows.md)
* [Tekintse meg, hogyan ismerhetik fel a szándék fog vonatkozni, a beszéd, a C#-ban](how-to-recognize-intents-from-speech-csharp.md)
