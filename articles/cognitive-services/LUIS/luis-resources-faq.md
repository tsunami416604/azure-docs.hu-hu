---
title: Gyakori kérdések a nyelvi ismertetése (LUIS) az Azure-ban |} Microsoft Docs
description: Nyelvi ismertetése (LUIS) kapcsolatos gyakori kérdésekre adott válaszok
author: v-geberr
manager: kaiqb
services: cognitive-services
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 05/07/2018
ms.author: v-geberr
ms.openlocfilehash: b6b333937e7c88f566fc401967b26cbd31ca158b
ms.sourcegitcommit: ea5193f0729e85e2ddb11bb6d4516958510fd14c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/21/2018
ms.locfileid: "36301502"
---
# <a name="language-understanding-faq"></a>Nyelvi ismertetése – gyakori kérdések

Ez a cikk nyelvi ismertetése (LUIS) kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

## <a name="luis-authoring"></a>Szerzői LUIS

### <a name="what-are-the-luis-best-practices"></a>Mik a LUIS gyakorlati tanácsok? 
Indítsa el a [szerzői ciklus](luis-concept-app-iteration.md), olvassa el a [ajánlott eljárások](luis-concept-best-practices.md). 

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Mi az a legjobb módszer készítéséhez LUIS az alkalmazásom?

A legjobb módszer az alkalmazás elkészítésére keresztül történik egy [növekményes folyamat](luis-concept-app-iteration.md). 

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Mi az a leképezések az alkalmazás modell célszerű? Érdemes létrehozni pontosabb vagy több általános leképezések?

Válassza ki, amelyek nincsenek átfedésben lévő, de nem így adott, hogy teszi, hogy a hasonló leképezések megkülönböztetésére LUIS legyen ezért általános leképezések. Discriminative adott leképezések létrehozása az egyik modellezési LUIS ajánlott eljárásai.

### <a name="is-it-important-to-train-the-none-intent"></a>Fontos a nincs leképezési betanítása?

Igen, az jó kell még betanítani a **nincs** leképezési a további utterances, hozzáadhat további címkék más leképezések. Egy jó arány hozzá 1 vagy 2 címkék **nincs** minden 10 feliratainak megjelölésű hozzáadni. Ez az arány boosts LUIS discriminative hatványa.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Hogyan javíthatja helyesen adta-e hibák a utterances?

Tekintse meg a [Bing helyesírás ellenőrizze API V7](luis-tutorial-bing-spellcheck.md) oktatóanyag. LUIS határoz meg küszöbértéket Bing helyesírás ellenőrizze API V7 érvénybe lépteti. 

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Hogyan szerkeszthetők LUIS alkalmazásom programozott módon?
A LUIS app programozott módon szerkesztéséhez használja a [szerzői API](https://aka.ms/luis-authoring-apis). Lásd: [szerzői API hívása LUIS](./luis-quickstart-node-add-utterance.md) és [hozza létre a programozott módon, Node.js LUIS alkalmazását](./luis-tutorial-node-import-utterances-csv.md) példákat a szerzői API hívása. A szerzői API számára kell használnia egy [kulcs szerzői](luis-concept-keys.md#authoring-key) ahelyett, hogy egy végpont-kulcsot. Programozott szerzői lehetővé teszi, hogy legfeljebb 1 000 000 hívások száma havonta és öt tranzakciók száma másodpercenként. A kulcsok LUIS használ további információk: [kulcsok kezelése](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Ha megfelelő a reguláris kifejezés a minta szolgáltatásának?
Az előző **mintát szolgáltatás** jelenleg elavult, lecserélve  **[minták](luis-concept-patterns.md)**. 

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Miként használható az entitás húzzon ki a megfelelő adatokat? 
Lásd: [entitások](luis-concept-entity-types.md) és [adatok kinyerése](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Tartalmaznia kell egy példa utterance változatait írásjelek? 
Adja hozzá a különböző változatát példa utterances a szándéka az, vagy adja hozzá a példa utterance a minta a [szintaxisát, és figyelmen kívül hagyása](luis-concept-patterns.md#pattern-syntax) a absztrakt. 

## <a name="luis-endpoint"></a>LUIS végpont

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Miért nem LUIS szóközöket a lekérdezésbe felvenni körül vagy közepén szavak?
LUIS [tokenizes](luis-glossary.md#token) a utterance alapján a [kulturális környezet](luis-supported-languages.md#tokenization). Az eredeti és tokenekre értéke egyaránt elérhetők a [adatok kinyerése](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Hogyan létrehozása és hozzárendelése egy LUIS végpontkulcs?
[A végpont-kulcs létrehozása](luis-how-to-azure-subscription.md#create-luis-endpoint-key) az Azure-ban a [szolgáltatás](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) szintjét. [A kulcs hozzárendelése](Manage-keys.md#assign-endpoint-key) a a **[közzététel](publishapp.md)** lap. Nincs a művelet nem megfelelő API-t. Ezután a HTTP-kérelem módosítania kell a végponthoz való [az új végpont billentyűvel](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-interpret-luis-scores"></a>Mi a LUIS pontszámok? 
A rendszer függetlenül annak értéke a legmagasabb pontozási leképezést kell használni. Például 0,5 (kevesebb mint 50 %) alá pontszámot nem feltétlenül jelenti azt, hogy LUIS alacsony abban, hogy rendelkezik-e. További tanítási adatokat szolgáltató segít az a legvalószínűbb leképezés pontszám növelni.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Miért nem látom, hogy a végpont a találatok saját alkalmazás irányítópult?
Az alkalmazás irányítópult a teljes végpont a találatok rendszeresen frissülnek, de az Azure-portálon a LUIS előfizetés kulcshoz tartozó metrikákat gyakrabban frissülnek. 

Ha nem látja az irányítópulton a frissített végpont találatok, jelentkezzen be az Azure-portálon, és a LUIS előfizetés kulcshoz tartozó erőforrás található, és nyissa meg **metrikák** jelölje be a **az összes hívás** metrikát. Az Előfizetés kulcs egynél több LUIS alkalmazás használata esetén a metrika az Azure portálon, az azt használó összes LUIS alkalmazásokból hívások összesített számát jeleníti meg.

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>Saját LUIS alkalmazás tegnap dolgozott, de még ma kapok a 403-as hibákat. Az alkalmazás nem módosítható. Hogyan tegye megjavítani? 
A következő a [utasításokat](#how-do-i-create-and-assign-a-luis-endpoint-key) a LUIS végpont kulcs létrehozása, és rendelje hozzá az alkalmazás a következő gyakori kérdések. Ezután a HTTP-kérelem módosítania kell a végponthoz való [az új végpont billentyűvel](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-secure-my-luis-endpoint"></a>Hogyan biztonságos a saját LUIS végpont? 
Lásd: [biztonságossá tétele az endpoint](luis-concept-security.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>LUIS határértékek használata

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Mi az a leképezések és entitások egy LUIS alkalmazás által támogatott maximális száma?
Tekintse meg a [határok](luis-boundaries.md) hivatkozás.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Szeretnék leképezések maximális száma csak egy LUIS alkalmazás elkészítésére. Mit tegyek?

Lásd: [ajánlott eljárások a leképezések](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Szeretném hozhat létre egy alkalmazást a LUIS nagyobb, mint az entitások maximális száma. Mit tegyek?

Lásd: [ajánlott eljárások az entitások](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Felsorolja a számának és méretének kifejezés korlátai?
A maximális hosszához egy [kifejezéslista](./luis-concept-feature.md), tekintse meg a [határok](luis-boundaries.md) hivatkozás.

### <a name="what-are-the-limits-on-example-utterances"></a>A példa utterances korlátai
Tekintse meg a [határok](luis-boundaries.md) hivatkozás.

## <a name="testing-and-training"></a>Tesztelés és képzési

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>A tesztelési ablaktábla alkalmazásom szereplő modellek némelyike kötegben hibák láthatók. Hogyan lehet oldja meg a problémát?

A hibák, az azt jelzi, hogy néhány a címkéket és az előrejelzés a modelleket között eltérés van. A probléma megoldása érdekében hajthatja végre a következő feladatokat:
* LUIS közötti leképezések megkülönböztetés javítása érdekében adjon hozzá további címkék.
* Ismerje meg, gyorsabban LUIS érdekében kifejezéslista funkciókat, amelyek tartományspecifikus szóhasználatának hozzáadása.

Tekintse meg a [kötegelt tesztelés](luis-tutorial-batch-testing.md) oktatóanyag.

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Ha egy alkalmazás exportált, akkor újra be egy új alkalmazást (egy új app ID), a LUIS előrejelzés pontszámok különböző. Miért történik ez? 

Lásd: [ugyanazon alkalmazás példánya előrejelzés különbségei](luis-concept-prediction-score.md#differences-with-predictions).

## <a name="app-publishing"></a>Alkalmazások közzététele

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Mi az a bérlő azonosítója "Egy kulccsal, hogy az alkalmazás hozzáadása" ablakban?
Az Azure a bérlő jelenti. az ügyfél vagy a szervezet, amely rendelkezik a szolgáltatáshoz társított. A bérlő azonosítója az Azure portálon található a **könyvtár-azonosítója** mező kiválasztásával **Azure Active Directory** > **kezelése**  >  **Tulajdonságok**.

![Bérlő azonosítója az Azure-portálon](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

### <a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>Miért van több előfizetés kulcsok saját alkalmazás közzétételi lap, mint az alkalmazás rendelt? 
Minden LUIS alkalmazás szerzői/alapszintű kulccsal rendelkezik. A GA időtartományban létrehozott LUIS előfizetés kulcsok jelennek meg a közzétételi lap attól függetlenül történik, ha az alkalmazás fel őket. Ezt az tettük GA áttelepítési megkönnyítése. Új LUIS előfizetés kulcs közzétételi lapon nem jelennek meg. 

## <a name="app-management"></a>Alkalmazáskezelés

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Hogyan hajtsa végre a LUIS alkalmazások tulajdonjogának átruházása?
Egy LUIS alkalmazást át egy másik Azure-előfizetéssel, exportálja a LUIS alkalmazást, és importálja azt egy új fiók használatával. Frissítse a LUIS Alkalmazásazonosító meghívja az ügyfél-alkalmazásban. Az új alkalmazás adhatnak vissza némileg eltérő LUIS pontszámok az eredeti alkalmazásból. 

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Hogyan töltse le a felhasználó utterances naplót?
Alapértelmezés szerint a LUIS app utterances naplózza a felhasználóktól. A naplófájlt, amely a felhasználók küldése az LUIS alkalmazásnak utterances letöltéséhez keresse fel **saját alkalmazások**, és kattintson a három pont a (***...*** ) a az átjáróra a listában az alkalmazást. Kattintson a **exportálása végpont Logs**. A napló egy vesszővel tagolt (CSV) fájl formátuma.

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Hogyan letilthatja az utterances naplózását?
A felhasználó utterances naplózását úgy, hogy kikapcsolható `log=false` lekérdezéshez LUIS használ az ügyfélalkalmazás végpont URL-cím. Naplózási kikapcsolása azonban letiltja az LUIS alkalmazás képes utterances javaslat vagy alapuló felhasználói lekérdezések teljesítményének javítása. Ha `log=false` adatok-adatvédelmi megfontolások, mert nem feljegyzés az adott felhasználó utterances letöltését LUIS és ezek utterances segítségével fejleszteni az alkalmazást.

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Miért nem? az összes, a naplózott végpont utterances
Ha a napló használ előrejelzési elemzésre, nem rögzíthető lemezkép a teszt utterances a naplóban.

## <a name="data-management"></a>Adatkezelés

### <a name="can-i-delete-data-from-luis"></a>Képes vagyok adatok törléséhez a LUIS? 

* Példa utterances LUIS betanítása használt mindig törölheti. Ha törli egy példa utterance a LUIS alkalmazás, a LUIS webszolgáltatás törlődik, és nem érhető el az exportált.
* A listája, amely LUIS javasol, a felhasználó utterances utterances törölheti a **tekintse át a végpont utterances** lap. Utterances törlése a listáról megakadályozza, hogy a javasolt, de nem törli a naplókat.
* Ha töröl egy fiókot, akkor minden alkalmazás törlődnek, együtt a példa utterances és a naplókat. Az adatok vissza a rendszer a kiszolgálókon 60 nap elteltével az véglegesen törlődni fog.

## <a name="language-and-translation-support"></a>Nyelv és címfordítás támogatása 

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Rendelkezem egy alkalmazást az egyik nyelven, és hozzon létre egy párhuzamos alkalmazást más nyelven szeretné. Mi az a legegyszerűbb módja?
1. Az alkalmazás exportálása.
2. Az exportált alkalmazásnak, hogy a megadott nyelv a JSON-fájlban címkézett utterances lefordítani.
3. Szükség lehet módosítani a leképezések és entitások nevét, vagy hagyja őket, mert azok.
4. Végül importálja az alkalmazásnak egy LUIS alkalmazást a célként megadott nyelven.

## <a name="app-notification"></a>Alkalmazásban megjelenő értesítésre

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Miért jelenik meg arról, hogy szinte nem vagyok kvóta e-mailt?
A szerzői/alapszintű kulcs csak engedélyezett 1000 végpont lekérdezi egy hónap. Hozzon létre egy LUIS előfizetés kulcs (ingyenes és fizetős) és kulcs végpont lekérdezések létrehozásakor. Ha végpont lekérdezések bot vagy egy másik ügyfélalkalmazást, ott a LUIS végpontkulcs módosítani szeretné. 

## <a name="integrating-luis"></a>LUIS integrálása

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Ahol létrehozzák az Azure web app botot előfizetés során LUIS alkalmazásom?
Ha egy LUIS sablont, és válasszon a **válasszon** gombra a sablon ablaktáblán, a bal oldali panelen módosításai közé tartoznak a sablon típusát, és kéri a LUIS sablon milyen régióban. A webes alkalmazás botot folyamat LUIS előfizetés azonban nem létrehozása.

![LUIS sablon web app botot régió](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Milyen LUIS régiók Botot keretrendszer beszéd elsődleges támogatja?
[Beszéd elsődleges](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) csak LUIS alkalmazások központi (amerikai) példány esetében támogatott. 

## <a name="luis-service"></a>LUIS szolgáltatás 

### <a name="is-luis-available-on-premise-or-in-private-cloud"></a>LUIS elérhető helyszíni vagy magánfelhőben?
Nem. 

## <a name="changes-to-the-docs"></a>A dokumentumok módosításai

### <a name="where-did-the-tutorials-go"></a>Eltűnt az oktatóprogramok 
A cikkeket, amelyek korábban már az útmutató szakaszban a jelenleg a dokumentumok az útmutatóban. 

|Oktatóanyag|
|--|
|LUIS integrálása a bot [C#](luis-csharp-tutorial-build-bot-framework-sample.md) és [Node.js](luis-nodejs-tutorial-build-bot-framework-sample.md)|
|Application Insights hozzáadása a Bot [C#](luis-tutorial-bot-csharp-appinsights.md) és [Node.js](luis-tutorial-function-appinsights.md)|
|Hozza létre a programozott módon LUIS alkalmazását [Node.js](luis-tutorial-node-import-utterances-csv.md)|
|Használjon [összetett entitást](luis-tutorial-composite-entity.md) csoportosított kibontása|
|Adja hozzá [entitás listában](luis-tutorial-list-entity.md) használata Node.js fokozott entitás észleléséhez|
|Az előrejelzési pontosság növeléséhez egy [kifejezéslista](luis-tutorial-interchangeable-phrase-list.md), [minták](luis-tutorial-pattern.md), és [kötegelt tesztelése](luis-tutorial-batch-testing.md)|
|[A helyesírás](luis-tutorial-batch-testing.md) a Bing helyesírás ellenőrizze API v7

### <a name="at-the-build-2018-conference-i-heard-about-a-language-understanding-feature-or-demo-but-i-dont-remember-what-it-was-called"></a>A Build 2018 konferencián vonatkozó nyelvi ismertetése bemutató hallottam, de mi hívták nem emlékszem? 

A következő funkciókat a Build 2018 konferencián kiadott:

|Name (Név)|Tartalom|
|--|--|
|Fejlesztések|[Reguláris kifejezés](luis-concept-data-extraction.md##regular-expression-entity-data) entitás és [kulcs kifejezés](luis-concept-data-extraction.md#key-phrase-extraction-entity-data) entitás
|Minták|Minták [koncepció](luis-concept-patterns.md), [oktatóanyag](luis-tutorial-pattern.md), [– útmutató](luis-how-to-model-intent-pattern.md)<br>[Patterns.Any](luis-concept-entity-types.md) entitás koncepció beleértve [Explicit lista](luis-concept-patterns.md#explicit-lists) kivételekhez<br>[Szerepkörök](luis-concept-roles.md) koncepció|
|Integráció|[Szövegelemzések](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) integrációja [véleményeket elemzés](publishapp.md#enable-sentiment-analysis)<br>[Beszéd](https://docs.microsoft.com/azure/cognitive-services/speech) integrációja [beszéd elsődleges](publishapp.md#enable-speech-priming) együtt [beszéd SDK](https://aka.ms/SpeechSDK)|
|Küldő eszköz|Része [BotBuilder-eszközök](https://github.com/Microsoft/botbuilder-tools), feladó parancssori [eszköz](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps) több LUIS és kérdések és válaszok készítő alkalmazások egyesítése egyetlen LUIS alkalmazásba az Bot jobb leképezési felismerés

További szerzői [API útvonalak](https://github.com/Microsoft/LUIS-Samples/blob/master/authoring-routes.md) szerepeltek. 

Videók: 
* [Azure Friday: Build 2018: Kognitív szolgáltatások – nyelvi (LUIS)](https://channel9.msdn.com/Shows/Azure-Friday/At-Build-2018-Cognitive-Services-Language-LUIS/player)
* [Build 2018 AI megjelenítése - nyelvi ismertetése szolgáltatás újdonságai](https://channel9.msdn.com/Shows/AI-Show/Whats-New-with-Language-Understanding-Service-LUIS/player)
* [Munkamenet - Botot az eszközintelligencia, a beszédfelismerés képességek és a NLU 2018 ajánlott eljárások](https://channel9.msdn.com/events/Build/2018/BRK3208)
* [Build 2018 - LUIS frissítések](https://channel9.msdn.com/events/Build/2018/THR3118/player)

Projektek: 
* [Contoso Cafe botot](https://github.com/botbuilderbuild2018/build2018demo) bemutató - forrás kódja a Githubon

## <a name="next-steps"></a>További lépések

LUIS kapcsolatos további tudnivalókért lásd a következőket:
* [A verem LUIS címkéjű túlcsordulás kérdések](https://stackoverflow.com/questions/tagged/luis)
* [MSDN nyelvi ismertetése intelligens szolgáltatásokkal (LUIS) foglalkozó fóruma](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS) 

[LUIS]: https://docs.microsoft.com/azure/cognitive-services/luis/luis-reference-regions#luis-website
