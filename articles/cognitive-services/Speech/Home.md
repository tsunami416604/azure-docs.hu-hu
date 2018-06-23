---
title: Microsoft beszéd szolgáltatás |} Microsoft Docs
description: Beszéd adatvezérelt műveletek hozzá az alkalmazásokat, beleértve a valós idejű kölcsönhatások a felhasználók a Microsoft Diktálásfelismerési API.
services: cognitive-services
author: zhouwangzw
manager: wolfma
ms.service: cognitive-services
ms.component: bing-speech
ms.topic: article
ms.date: 09/15/2017
ms.author: zhouwang
ms.openlocfilehash: c041132e992f07e94e4b6669ec7ce174f7c2d0dd
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35347422"
---
# <a name="microsoft-speech-api-overview"></a>Microsoft-hang transzformációs API – áttekintés

A felhőalapú Microsoft-hang transzformációs API egyszerűen az alkalmazásokban, például a hang parancsvezérlő, felhasználói párbeszédpanelen természetes beszéd beszélgetés és beszéd valamint a Diktálás használatával hozzon létre beszédfelismerő szolgáltatásokat nyújt a fejlesztők számára. A Microsoft-hang transzformációs API egyaránt támogat *szöveg beszédfelismerés* és *szöveg-beszéd átalakítás* átalakítás.

- **Beszédfelismerés szöveg** API emberi beszéd konvertálja az alkalmazás vezérlésére használható beviteli vagy parancsok szöveg.
- **Szöveg-beszéd átalakítás** API szöveget is futtathatók a felhasználónak az alkalmazás hang adatfolyamokkal alakítja át.

## <a name="speech-to-text-speech-recognition"></a>Beszédfelismerés szöveg (beszédfelismerés)

A Microsoft a beszédfelismerés API *transcribes* szöveggé, amely az alkalmazás képes megjeleníteni a felhasználó vagy hajthat végre műveletet illetve hang-adatfolyamok bemeneti parancsot. A fejlesztők számára a beszédfelismerés hozzáadása alkalmazások két módon biztosít: REST API-k **vagy** Websocket alapuló ügyféloldali kódtáraknál.

- [REST API-k](GetStarted/GetStartedREST.md): a fejlesztők a beszédfelismerési a szolgáltatásnak HTTP alkalmazások hívást.
- [Ügyféloldali kódtáraknál](GetStarted/GetStartedClientLibraries.md): olyan speciális funkciókhoz, a fejlesztők letöltheti Microsoft Speech klienskódtárak segítségével, és az alkalmazásokba való hivatkozás.  A klienskódtárak segítségével a különböző platformokon (Android, iOS Windows,) használatával (C#, Java, JavaScript, ObjectiveC) különböző nyelveken érhetők el. A REST API-k, ellentétben a klienskódtárak segítségével hasznosítani szűrődémon Websocket-alapú.

| Használati esetek | [REST API-k](GetStarted/GetStartedREST.md) | [Ügyfél-függvénytárak](GetStarted/GetStartedClientLibraries.md) |
|-----|-----|-----|
| Egy rövid szóbeli hang-, a konvertálás például parancsok köztes eredmények nélkül (hang hossza < 15 s) | Igen | Igen |
| Alakítsa át a hosszú hang (> 15 s) | Nem | Igen |
| Az adatfolyam hang szükséges ideiglenes eredményekkel | Nem | Igen |
| A szöveg LUIS használatával hang konvertálásához ismertetése | Nem | Igen |

Bármelyik módszert a fejlesztők válasszon (REST API vagy klienskódtárak), és a Microsoft beszéd szolgáltatás támogatja a következő:

- Speciális Beszéd felismerés technológiák Cortana, Office Diktálás, Office fordító és más Microsoft-termékek által használt Microsoft.
- Valós idejű folyamatos használatát. A beszédfelismerés API lehetővé teszi, hogy a felhasználók audio átírni szöveggé valós idejű, és a köztes eredményeket, amennyiben felismerhető szó támogatott. A beszédfelismerés szolgáltatás vége a beszédfelismerés észlelését is támogatja. Emellett a felhasználók maguk dönthetik további formázási lehetőségeket, például a kis-és nagybetűk és írásjelek, maszkolási Profanitás és szöveges normalizálási.
- Támogatja a beszédfelismerés felismerési eredmények optimalizált *interaktív*, *beszélgetés*, és *Diktálás* forgatókönyvek. Testre szabott nyelvi modellek és akusztikus modellek igénylő felhasználó forgatókönyvek [egyéni beszéd szolgáltatás](../custom-speech-service/cognitive-services-custom-speech-home.md) lehetővé teszi, hogy az alkalmazás és a felhasználók igazított beszéd modellek létrehozásához.
- Sok szóbeli nyelveket támogatja több nyelvjárást. A támogatott nyelveket mindegyik mód teljes listájáért lásd: [nyelveket](api-reference-rest/supportedlanguages.md).
- Integráció a nyelvi ismertetése. Szöveg, amelyet a bemeneti hang konvertálásakor mellett a *szöveg beszédfelismerés* lehetővé teszi a megértése, hogy mi az szöveg azt jelenti, hogy egy további lehetőség. Használja a [nyelvi ismertetése intelligens Service(LUIS)](../LUIS/Home.md) leképezések és entitások kibontani a felismert szöveg.

### <a name="next-steps"></a>További lépések

- Ismerkedés a Microsoft beszéd felismerési szolgáltatást, amely használandó [REST API-k](GetStarted/GetStartedREST.md) vagy [klienskódtárak](GetStarted/GetStartedClientLibraries.md).
- Tekintse meg [mintaalkalmazást](samples.md) az előnyben részesített a programozási nyelv.
- Lépjen az útmutató szakaszban található [Microsoft beszéd protokoll](API-Reference-REST/websocketprotocol.md) részleteit és API-hivatkozás.

## <a name="text-to-speech-speech-synthesis"></a>Szöveg-beszéd átalakítás (beszéd összefoglaló)

*Szöveg-beszéd átalakítás* API-k segítségével REST strukturált szöveg egy hangadatfolyam. Az API-k olyan különböző hangok és nyelvek gyors szöveg-beszéd átalakítás. Ezenkívül felhasználók is megváltoztathatják hang jellemzőkkel, ilyenek például a kiejtés, kötet, betűközű stb. címkék SSML használatával.

### <a name="next-steps"></a>További lépések

- Ismerkedés a Microsoft szöveg-beszéd átalakítás szolgáltatás használatára: [Diktálásfelismerési API-referencia a szöveg](api-reference-rest/bingvoiceoutput.md). Nyelv és a szöveg-beszéd átalakítás által támogatott hangok teljes listáját lásd: [támogatott területi beállításokat, és hang betűtípusok](api-reference-rest/bingvoiceoutput.md#SupLocales).
