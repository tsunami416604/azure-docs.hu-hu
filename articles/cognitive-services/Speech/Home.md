---
title: Microsoft Bing Speech szolgáltatás | Microsoft Docs
titlesuffix: Azure Cognitive Services
description: A Microsoft Speech API használatával beszédes műveleteket adhat hozzá alkalmazásaihoz, beleértve a valós idejű interakciót a felhasználókkal.
services: cognitive-services
author: nitinme
manager: nitinme
ms.service: cognitive-services
ms.subservice: bing-speech
ms.topic: article
ms.date: 09/18/2018
ms.author: nitinme
ROBOTS: NOINDEX,NOFOLLOW
ms.openlocfilehash: ff9de2557583eecd5ddb2acd97b445a93abc5fb6
ms.sourcegitcommit: fbea2708aab06c19524583f7fbdf35e73274f657
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/13/2019
ms.locfileid: "70966643"
---
# <a name="what-is-bing-speech"></a>Mi az Bing Speech?

[!INCLUDE [Deprecation note](../../../includes/cognitive-services-bing-speech-api-deprecation-note.md)]

A felhőalapú Microsoft Bing Speech API lehetővé teszi a fejlesztők számára, hogy hatékony beszédfelismerési funkciókat hozzanak létre alkalmazásaikban, például hangvezérelt vezérlést, felhasználói párbeszédpanelt a természetes beszéd beszélgetés használatával, valamint a beszédfelismerést és a diktálást. A Microsoft Speech API támogatja a *szöveg* -és *text to speech* konverziót is.

- **Beszéd szövege** Az API átalakítja az emberi beszédet olyan szöveggé, amelyet bemenetként vagy parancsként használhat az alkalmazás vezérléséhez.
- **Text to speech** Az API átalakítja a szöveget az alkalmazás felhasználója számára visszatekinthető hangadatfolyamokra.

## <a name="speech-to-text-speech-recognition"></a>Beszéd – szöveg (beszédfelismerés)

A Microsoft Speech Recognition *API olyan* szöveggé alakítja át a hangadatfolyamokat, amelyet az alkalmazás megjelenít a felhasználó számára, vagy a parancs bemenetként is működhet. Két módszert biztosít a fejlesztők számára, hogy beszédfelismerést adjanak hozzá az alkalmazásaihoz: REST API-k **vagy** WebSocket-alapú ügyféloldali kódtárak.

- [REST API](GetStarted/GetStartedREST.md)-k: A fejlesztők a beszédfelismeréshez használhatnak HTTP-hívásokat az alkalmazásaikat a szolgáltatáshoz.
- [Ügyféloldali kódtárak](GetStarted/GetStartedClientLibraries.md): A fejlett funkciókhoz a fejlesztők letöltik a Microsoft Speech Client kódtárait, és az alkalmazásaikat is használhatják.  Az ügyféloldali kódtárak különböző platformokon (Windows, Android, iOS) érhetők el különböző nyelvekenC#(, Java, JavaScript, ObjectiveC). A REST API-kkal ellentétben az ügyféloldali kódtárak a WebSocket-alapú protokollt használják.

| Használati esetek | [REST API-k](GetStarted/GetStartedREST.md) | [Ügyféloldali kódtárak](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Rövid hangalapú hang konvertálása, például parancsok (hanghossz < 15 s) ideiglenes eredmények nélkül | Igen | Igen |
| Hosszú hang konvertálása (> 15 s) | Nem | Igen |
| Stream hang a kívánt ideiglenes eredményekkel | Nem | Igen |
| A hangról a LUIS használatával átalakított szöveg ismertetése | Nem | Igen |

Bármelyik megközelítés fejlesztő kiválasztja (REST API-k vagy ügyféloldali kódtárak), a Microsoft Speech Service a következőket támogatja:

- A Microsoft fejlett beszédfelismerési technológiáit, amelyeket a Cortana, az Office-diktálás, az Office Translator és más Microsoft-termékek használnak.
- Valós idejű folyamatos felismerése. A beszédfelismerési API lehetővé teszi, hogy a felhasználók valós időben másolják a hanganyagot a szövegbe, és a támogatja az eddig felismerhető szavak közbenső eredményeinek fogadását. A beszédfelismerési szolgáltatás emellett támogatja a beszédfelismerés befejezését. Emellett a felhasználók további formázási képességeket is választhatnak, például a nagybetűket és a központozást, a káromkodás maszkolását és a szöveg normalizálása.
- Támogatja az optimalizált beszédfelismerési eredményeket az *interaktív*, a *beszélgetési*és a *diktálási* forgatókönyvek esetében. A testreszabott nyelvi modelleket és akusztikus modelleket igénylő felhasználói forgatókönyvek esetében [Custom Speech Service](../custom-speech-service/cognitive-services-custom-speech-home.md) lehetővé teszi az alkalmazásra és a felhasználókra szabott beszédfelismerési modellek létrehozását.
- Számos különböző nyelven beszélő nyelv támogatása több dialektusban. Az egyes felismerési módok támogatott nyelveinek teljes listájáért lásd: [felismerési nyelvek](api-reference-rest/supportedlanguages.md).
- Integráció a nyelvi ismeretekkel. A szövegbeviteli hang szöveggé alakításán kívül a szövegre való *beszédfelismerés* további képességet biztosít az alkalmazások számára, hogy megértse a szöveg módját. A [Language Understanding Intelligent Service (Luis)](../LUIS/what-is-luis.md) használatával Kinyeri a leképezéseket és az entitásokat a felismert szövegből.

### <a name="next-steps"></a>További lépések

- Ismerkedés a Microsoft Speech Recognition szolgáltatás [REST API](GetStarted/GetStartedREST.md) -kkal vagy [ügyféloldali kódtárakkal](GetStarted/GetStartedClientLibraries.md)való használatával.
- Tekintse meg a [minta alkalmazásokat](samples.md) az előnyben részesített programozási nyelven.
- A [Microsoft Speech Protocol](API-Reference-REST/websocketprotocol.md) részleteinek és API-hivatkozásainak megkereséséhez lépjen a hivatkozás szakaszra.

## <a name="text-to-speech-speech-synthesis"></a>Szöveg-beszéd (Speech szintézis)

*Text to speech* Az API-k a REST használatával alakítják át a strukturált szöveget egy hangadatfolyamba. Az API-k gyors szöveget biztosítanak a beszéd átalakításához különböző hangokon és nyelveken. Emellett a felhasználók is módosíthatják az olyan hangjellemzőket, mint a kiejtés, a hangerő, a szurok stb. SSML-címkék használata.

### <a name="next-steps"></a>További lépések

- Ismerkedjen meg a Microsoft Text to speech szolgáltatás használatával: [Text to Speech API hivatkozás](api-reference-rest/bingvoiceoutput.md). A Text to Speech által támogatott nyelvek és hangok teljes listájáért lásd: [támogatott területi beállítások és Hangbetűkészletek](api-reference-rest/bingvoiceoutput.md#SupLocales).
