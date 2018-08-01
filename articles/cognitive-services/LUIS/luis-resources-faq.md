---
title: Language Understanding (LUIS) az Azure-ban – gyakori kérdések |} A Microsoft Docs
description: Language Understanding (LUIS) kapcsolatos gyakori kérdésekre adott válaszok
author: diberry
manager: cjgronlund
services: cognitive-services
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 07/26/2018
ms.author: diberry
ms.openlocfilehash: 93ced2d0c79d80a631ad90aa3f5d2dc9f8c79c7e
ms.sourcegitcommit: e3d5de6d784eb6a8268bd6d51f10b265e0619e47
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2018
ms.locfileid: "39390194"
---
# <a name="language-understanding-faq"></a>Language Understanding – gyakori kérdések

Ez a cikk a Language Understanding (LUIS) kapcsolatos gyakori kérdésekre adott válaszokat tartalmazza.

## <a name="luis-authoring"></a>A LUIS készítése

### <a name="what-are-the-luis-best-practices"></a>Mik azok a LUIS-ajánlott eljárások? 
Kezdje a [szerzői ciklus](luis-concept-app-iteration.md), olvassa el a [ajánlott eljárások](luis-concept-best-practices.md). 

### <a name="what-is-the-best-way-to-start-building-my-app-in-luis"></a>Mi az a legjobb módja, és kezdje el létrehozni egy alkalmazást az intelligens HANGFELISMERÉSI?

A legjobb módszer az alkalmazás elkészítésére keresztül történik egy [növekményes folyamata](luis-concept-app-iteration.md). 

### <a name="what-is-a-good-practice-to-model-the-intents-of-my-app-should-i-create-more-specific-or-more-generic-intents"></a>Mi az jó megoldás a modell a leképezések az alkalmazásom? Kell-e létre pontosabb vagy több általános leképezések?

Válassza ki, amelyek nem átfedésben lévő, de nem így meghatározott, hogy lehetővé teszi, hogy nehéz megkülönböztetni a hasonló leképezések LUIS kell tehát általános leképezések. Discriminative adott szándékot létrehozása az ajánlott eljárást a LUIS modellezési egyik.

### <a name="is-it-important-to-train-the-none-intent"></a>Fontos a nincs szándék betanításához?

Igen, akkor jó taníthat be a **nincs** szándék a további utterances, hozzáadhat további címkék más leképezések. Egy jó arány hozzá 1 vagy 2 címke **nincs** megjelölésű hozzáadott minden 10 címkék. Ez az arány felgyorsíthatók a LUIS discriminative hatékonyságát.

### <a name="how-can-i-correct-spelling-mistakes-in-utterances"></a>Hogyan javíthatja a kimondott szöveg helyesírási hibákat?

Tekintse meg a [a Bing Spell ellenőrzés API 7-es](luis-tutorial-bing-spellcheck.md) oktatóanyag. A LUIS határoz meg a Bing Spell ellenőrzés API 7-es érvénybe lépteti. 

### <a name="how-do-i-edit-my-luis-app-programmatically"></a>Hogyan szerkeszthetem a LUIS-alkalmazásokon programozott módon?
A LUIS-alkalmazás programozott módon szerkesztéséhez használja a [szerzői API](https://aka.ms/luis-authoring-apis). Lásd: [szerzői API hívása LUIS](./luis-quickstart-node-add-utterance.md) és [programozott módon a Node.js használatával a LUIS-alkalmazások felépítése](./luis-tutorial-node-import-utterances-csv.md) példákat arra, hogyan hívhat meg a jelentéskészítési API-t. A jelentéskészítési API használatát igényli egy [kulcs létrehozási](luis-concept-keys.md#authoring-key) egy végponti kulcs helyett. Programozott szerzői lehetővé teszi, hogy legfeljebb 1 000 000 hívást, havi és öt tranzakció / másodperc. További információ a kulcsokról, az intelligens hangfelismerési szolgáltatással használja, lásd: [kulcsok kezelése](./luis-concept-keys.md).

### <a name="where-is-the-pattern-feature-that-provided-regular-expression-matching"></a>Hol van a minta-szolgáltatás, amely a megadott reguláris kifejezéssel egyező?
Az előző **minta funkció** jelenleg elavult, lecserélve  **[minták](luis-concept-patterns.md)**. 

### <a name="how-do-i-use-an-entity-to-pull-out-the-correct-data"></a>Hogyan használhatom egy entitás is, a helyes adatokat? 
Lásd: [entitások](luis-concept-entity-types.md) és [adatkinyerés](luis-concept-data-extraction.md).

### <a name="should-variations-of-an-example-utterance-include-punctuation"></a>Tartalmaznia kell egy példa utterance (kifejezés) változata írásjelek? 
A példában szándéka kimondott szöveg szerint adja hozzá, a másik változata, vagy adja hozzá a példa utterance (kifejezés) minta a [figyelmen kívül hagyása szintaxis](luis-concept-patterns.md#pattern-syntax) az absztrakt. 

### <a name="does-luis-currently-support-cortana"></a>A LUIS jelenleg támogatja a Cortana?

A Cortana előre elkészített alkalmazásokat is elavult 2017-ben. Már nem támogatottak. 

## <a name="luis-endpoint"></a>A LUIS-végpont

### <a name="why-does-luis-add-spaces-to-the-query-around-or-in-the-middle-of-words"></a>Miért nem LUIS szóközöket a lekérdezésbe felvenni kívánt körül vagy közepén szavak?
A LUIS [tokenizes](luis-glossary.md#token) az utterance (kifejezés) alapján a [kulturális környezet](luis-supported-languages.md#tokenization). Az eredeti és tokenekre értéke is érhetők el [adatkinyerés](luis-concept-data-extraction.md#tokenized-entity-returned).

### <a name="how-do-i-create-and-assign-a-luis-endpoint-key"></a>Hogyan létrehozása és hozzárendelése egy LUIS végponti kulcs?
[A végpont kulcs létrehozása](luis-how-to-azure-subscription.md#create-luis-endpoint-key) az Azure-ban a [szolgáltatás](https://azure.microsoft.com/pricing/details/cognitive-services/language-understanding-intelligent-services/) szintjét. [A kulcs hozzárendelése](luis-how-to-manage-keys.md#assign-endpoint-key) a a **[közzététel](luis-how-to-publish-app.md)** lapot. Nincs a művelet nem megfelelő API-t. Ezután a végpontot kell módosítania a HTTP-kérelem [használja az új végpont kulcs](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-interpret-luis-scores"></a>Mi a LUIS pontszámok? 
A rendszer függetlenül annak értéke a legmagasabb pontozási leképezést kell használnia. Ha például 0.5-ös (kevesebb mint 50 %) alatti nem feltétlenül jelenti, hogy a LUIS alacsony megbízhatósági rendelkezik. További betanítási adatok biztosítása révén növelheti a pontszám a legvalószínűbb leképezés.

### <a name="why-dont-i-see-my-endpoint-hits-in-my-apps-dashboard"></a>Miért nem látom, hogy a végpont a találatok saját alkalmazás-irányítópult?
Az alkalmazás-irányítópult a teljes végpont a találatok rendszeres időközönként frissülnek, de a metrikák az Azure Portalon, a LUIS végponti kulcs társított gyakran frissülnek. 

Ha nem látja az irányítópult a frissített végpont találatok, jelentkezzen be az Azure Portalon, és keresse meg az erőforrást, a LUIS végpontkulcsának társított, és nyissa meg a **metrikák** válassza ki a **összes hívás** metrikát. A végpont kulcs egynél több LUIS alkalmazás használata esetén az Azure Portalon a metrika azt használó összes LUIS-alkalmazások hívásait összesített számát jeleníti meg.

### <a name="my-luis-app-was-working-yesterday-but-today-im-getting-403-errors-i-didnt-change-the-app-how-do-i-fix-it"></a>A LUIS-alkalmazásokon tegnap dolgozott, de még ma érkeznek meg hozzám a 403-as hibák. Az alkalmazás nem módosítható. Hogyan javíthatom? 
A következő a [utasításokat](#how-do-i-create-and-assign-a-luis-endpoint-key) a következő gyakori kérdések a LUIS végponti kulcs létrehozásához, és rendelje hozzá az alkalmazást. Ezután a végpontot kell módosítania a HTTP-kérelem [használja az új végpont kulcs](luis-concept-keys.md#use-endpoint-key-in-query).

### <a name="how-do-i-secure-my-luis-endpoint"></a>Hogyan védhetem meg a LUIS végpontomhoz? 
Lásd: [biztonságossá tétele az endpoint](luis-concept-security.md#securing-the-endpoint).

## <a name="working-within-luis-limits"></a>A LUIS korlátokon belül működik

### <a name="what-is-the-maximum-number-of-intents-and-entities-that-a-luis-app-can-support"></a>Mi az a szándékok és entitások, LUIS-alkalmazások által támogatott maximális számát?
Tekintse meg a [határok](luis-boundaries.md) hivatkozást.

### <a name="i-want-to-build-a-luis-app-with-more-than-the-maximum-number-of-intents-what-should-i-do"></a>Szeretnék a LUIS alkalmazás felépítése a több mint leképezések maximális számát. Mit tegyek?

Lásd: [ajánlott eljárásai leképezések](luis-concept-intent.md#if-you-need-more-than-the-maximum-number-of-intents).

### <a name="i-want-to-build-an-app-in-luis-with-more-than-the-maximum-number-of-entities-what-should-i-do"></a>Szeretnék az entitások maximális száma meghaladja a LUIS-alkalmazás létrehozása. Mit tegyek?

Lásd: [ajánlott eljárások az entitások](luis-concept-entity-types.md#if-you-need-more-than-the-maximum-number-of-entities)

### <a name="what-are-the-limits-on-the-number-and-size-of-phrase-lists"></a>Mik azok a számát és méretét, a kifejezés korlátait felsorolja?
A maximális hosszához egy [kifejezéslista](./luis-concept-feature.md), tekintse meg a [határok](luis-boundaries.md) hivatkozást.

### <a name="what-are-the-limits-on-example-utterances"></a>Mik azok a példa utterances korlátait?
Tekintse meg a [határok](luis-boundaries.md) hivatkozást.

## <a name="testing-and-training"></a>Tesztelés és képzés

### <a name="i-see-some-errors-in-the-batch-testing-pane-for-some-of-the-models-in-my-app-how-can-i-address-this-problem"></a>A köteg bizonyos, a modellek az alkalmazásom panel tesztelése hibák láthatók. Hogyan kezelheti a probléma?

A hibákat jelzik, hogy van néhány eltérés van, a címkék és a modellek által létrehozott javaslatok között. A probléma megoldása érdekében hajthatja végre a következő feladatok közül:
* A LUIS közötti leképezések megkülönböztetés javítása érdekében adjon hozzá további címkéket.
* Ismerje meg, gyorsabb LUIS érdekében adja hozzá a kifejezéslista funkciók, amelyek bemutatják a tartomány-specifikus szókincsből eredőket.

Tekintse meg a [Batch tesztelés](luis-tutorial-batch-testing.md) oktatóanyag.

### <a name="when-an-app-is-exported-then-reimported-into-a-new-app-with-a-new-app-id-the-luis-prediction-scores-are-different-why-does-this-happen"></a>Amikor az alkalmazás van exportálva, majd újra be egy új alkalmazást (egy új alkalmazás azonosítója), a LUIS-előrejelzési eredmények eltérőek. Miért jelentkezik? 

Lásd: [ugyanazon alkalmazás példánya előrejelzési különbségeit](luis-concept-prediction-score.md#differences-with-predictions).

## <a name="app-publishing"></a>Alkalmazások közzététele

### <a name="what-is-the-tenant-id-in-the-add-a-key-to-your-app-window"></a>Mi az a bérlő Azonosítóját, a "Hozzáadás egy billentyűt az alkalmazás a" ablakban?
Az Azure-ban a bérlő az ügyfél vagy a szervezet, amely a társított szolgáltatás jelöl. Az Azure Portalon keresse meg a bérlő Azonosítóját a **címtár-azonosító** mező kiválasztásával **Azure Active Directory** > **kezelés**  >  **Tulajdonságok**.

![Az Azure Portalon a bérlő azonosítója](./media/luis-manage-keys/luis-assign-key-tenant-id.png)

<a name="why-are-there-more-subscription-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>
### <a name="why-are-there-more-endpoint-keys-on-my-apps-publish-page-than-i-assigned-to-the-app"></a>Miért vannak további végpont kulcsok saját alkalmazás közzétételi oldalon, mint az alkalmazáshoz hozzárendel? 
Minden egyes LUIS-alkalmazás a szerzői/alapszintű kulccsal rendelkezik. A LUIS végpont a végleges verzió időtartam alatt létrehozott kulcsai a közzétételi oldalon látható, függetlenül attól, ha az alkalmazás hozzá őket. Ez megtörtént, a végleges verzió áttelepítés egyszerűbbé. Minden olyan új LUIS végpont kulcsok nem jelennek meg a közzétételi oldalon. 

## <a name="app-management"></a>Alkalmazáskezelés

### <a name="how-do-i-transfer-ownership-of-a-luis-app"></a>Hogyan ruházhatom át tulajdonjogát a LUIS-alkalmazások?
LUIS-alkalmazásokon át egy másik Azure-előfizetést, a LUIS alkalmazás exportálása, és importálja egy új fiók használatával. Frissítse a LUIS alkalmazás azonosítója, amely meghívja ezt az ügyfélalkalmazásban. Az új alkalmazás adhat vissza eltérő LUIS pontszámokat az eredeti alkalmazásból. 

### <a name="how-do-i-download-a-log-of-user-utterances"></a>Hogyan töltse le a felhasználó utterances naplózása?
Alapértelmezés szerint a LUIS-alkalmazás a felhasználók naplózza a kimondott szöveg. Töltse le, hogy a felhasználók küldeni a LUIS-alkalmazás megcímkézzen naplóját, lépjen a **saját alkalmazások**, és kattintson a három (***...*** ) az alkalmazások listáján. Kattintson a **végpont naplók exportálása**. A napló formátuma vesszővel tagolt (CSV) fájlként.

### <a name="how-can-i-disable-the-logging-of-utterances"></a>Hogyan tilthatom az utterances naplózását?
Felhasználók bármikor kikapcsolhatják a naplózást a felhasználói kimondott szöveg beállításával `log=false` az a végpont URL-címe, amely az ügyfélalkalmazásban használ a LUIS-lekérdezéshez. Azonban letiltja a LUIS-alkalmazás lehetőségét utterances javaslat, amely azon alapul, a teljesítmény javítása naplózási kikapcsolásával [aktív tanulás](luis-concept-review-endpoint-utterances.md#what-is-active-learning). Ha `log=false` miatt az adatvédelem problémákat, nem töltheti le az adott felhasználó utterances rekord LUIS és használja ezeket a kimondott szöveg tökéletesítheti alkalmazását.

A naplózás nem utterances csak tárolására. 

### <a name="why-dont-i-want-all-my-endpoint-utterances-logged"></a>Miért nem szeretné az összes naplózott saját végpont utterances?
Ha a napló előrejelzési elemzésre használ, ne rögzítsen rajta teszt utterances a naplóban tárolt.

## <a name="data-management"></a>Adatkezelés

### <a name="can-i-delete-data-from-luis"></a>Törölhetem-e adatokat a LUIS? 

* A LUIS oktatási használt példa kimondott szöveg mindig törölheti. Ha töröl egy példa utterance (kifejezés) a LUIS-alkalmazás, törlődik a LUIS webszolgáltatás és az exportálás nem érhető el.
* Beszédmódok törölheti, hogy a LUIS javasol a felhasználó megcímkézzen listájából a **tekintse át a végpont utterances** lapot. Beszédmódok törlése a listáról a továbbiakban nem javasolt, de nem törli azokat a naplókat.
* Ha töröl egy fiókot, az összes alkalmazás törlődnek, azokhoz példa kimondott szöveg és a naplókat. Az adatok végleges törlés előtt 60 napig őrződnek a kiszolgálókon.

### <a name="does-microsoft-access-my-luis-app-data-for-its-own-purposes-for-example-to-enhance-luis-or-microsoft-in-general"></a>Nem a Microsoft hozzáférést a LUIS alkalmazás adatainak a saját célra, például általában javíthatja a LUIS vagy a Microsoft? 

Nem. A LUIS-alkalmazásokon adatmodell nem javíthatja a LUIS-platformként a LUIS segítségével vagy a Microsoft semmilyen módon használja. Minden alkalmazás adatait külön, és csak a felhasználó és a közreműködők által saját tulajdonú. 

Tudjon meg többet [felhasználói adatvédelem](luis-reference-gdpr.md), [további biztonsági megfelelőségi](luis-concept-security.md#security-compliance), és [adattárolás](luis-concept-data-storage.md).

## <a name="language-and-translation-support"></a>Nyelvi és fordítási támogatás 

### <a name="i-have-an-app-in-one-language-and-want-to-create-a-parallel-app-in-another-language-what-is-the-easiest-way-to-do-so"></a>Szükségem van egy alkalmazása egy nyelven, és hozzon létre egy párhuzamos alkalmazást egy másik nyelven szeretné. Mi az a legegyszerűbb módja, ehhez?
1. Az alkalmazás exportálása.
2. Az exportált alkalmazásnak, hogy a célként megadott nyelv a JSON-fájlban címkézett megcímkézzen fordítja le.
3. Szüksége lehet a nevet a szándékok és entitások, vagy hagyja őket, mivel ezek.
4. Végül importálja a LUIS-alkalmazásokon rendelkezik a célként megadott nyelven az alkalmazást.

## <a name="app-notification"></a>Alkalmazásban megjelenő értesítésre

### <a name="why-did-i-get-an-email-saying-im-almost-out-of-quota"></a>Miért jelenik meg egy e-mail arról tájékoztatja, szinte kvótájából vagyok?
A szerzői műveletek/alapszintű kulcs csak akkor engedélyezett, 1000 végpont havonta kérdezi le. Hozzon létre egy LUIS végponti kulcs (ingyenes vagy fizetős) és a kulcs végpont lekérdezések létrehozásakor. Ha egy robot vagy egy másik ügyfélalkalmazás végpont lekérdezések végez, a LUIS végponti kulcs van módosítani szeretné. 

## <a name="integrating-luis"></a>A LUIS integrálása

### <a name="where-is-my-luis-app-created-during-the-azure-web-app-bot-subscription-process"></a>Ahol létrejött a LUIS-alkalmazásom az Azure web app bot előfizetés során?
Ha, válasszon ki egy LUIS-sablont, és válassza a **kiválasztása** gombra a sablon ablaktáblán, a bal oldali ablaktáblán tartalmazza a sablon típusát módosítja, és kéri a régióját, a LUIS-sablon létrehozásához. A web app bot folyamat LUIS előfizetés azonban nem létrehozása.

![A LUIS sablon web app bot régió](./media/luis-faq/web-app-bot-location.png)

### <a name="what-luis-regions-support-bot-framework-speech-priming"></a>Milyen a LUIS-régiók támogatják a Bot Framework speech betanítási művelet?
[Beszéd betanítási művelet](https://docs.microsoft.com/bot-framework/bot-service-manage-speech-priming) csak a központi (amerikai) példány LUIS-alkalmazások esetén támogatott. 

## <a name="luis-service"></a>Intelligens HANGFELISMERÉSI szolgáltatás 

### <a name="is-luis-available-on-premises-or-in-private-cloud"></a>A LUIS elérhető helyszíni vagy a privát felhőben?
Nem. 


### <a name="at-the-build-2018-conference-i-heard-about-a-language-understanding-feature-or-demo-but-i-dont-remember-what-it-was-called"></a>A Build 2018 konferencián hallottam Language Understanding funkció vagy bemutató, de nem emlékszem, hogy milyen hívták? 

A következő szolgáltatásai jelentek meg a Build 2018 konferencián:

|Name (Név)|Tartalom|
|--|--|
|Fejlesztések|[Reguláris kifejezés](luis-concept-data-extraction.md##regular-expression-entity-data) entitás és [Key kifejezés](luis-concept-data-extraction.md#key-phrase-extraction-entity-data) entitás
|Minták|Minták [fogalom](luis-concept-patterns.md), [oktatóanyag](luis-tutorial-pattern.md), [útmutató](luis-how-to-model-intent-pattern.md)<br>[Patterns.Any](luis-concept-entity-types.md) entitás fogalom beleértve [Explicit lista](luis-concept-patterns.md#explicit-lists) kivételekhez<br>[Szerepkörök](luis-concept-roles.md) fogalma|
|Integráció|[Szövegelemzés](https://docs.microsoft.com/azure/cognitive-services/text-analytics/) integrációjának [hangulatelemzés](luis-how-to-publish-app.md#enable-sentiment-analysis)<br>[Beszéd](https://docs.microsoft.com/azure/cognitive-services/speech) speech betanítási művelet együtt integrációjának [beszéd SDK](https://aka.ms/SpeechSDK)|
|Küldő eszközt|Része [BotBuilder-eszközök](https://github.com/Microsoft/botbuilder-tools), feladó parancssori [eszköz](luis-concept-enterprise.md#when-you-need-to-combine-several-luis-and-qna-maker-apps) LUIS és a QnA Maker alkalmazások egyesítendő jobb szándékfelismerés a robot a LUIS Egyalkalmazásos

További szerzői [API útvonalak](https://github.com/Microsoft/LUIS-Samples/blob/master/authoring-routes.md) szerepeltek. 

Videók: 
* [Azure Friday Build 2018: Cognitive Services – nyelvi (LUIS)](https://channel9.msdn.com/Shows/Azure-Friday/At-Build-2018-Cognitive-Services-Language-LUIS/player)
* [Build 2018 AI Show - a hangfelismerési szolgáltatás újdonságai](https://channel9.msdn.com/Shows/AI-Show/Whats-New-with-Language-Understanding-Service-LUIS/player)
* [Build 2018-bemutató – Mesterséges intelligencia, beszédképességek és NLU – ajánlott eljárások](https://channel9.msdn.com/events/Build/2018/BRK3208)
* [Build 2018 - LUIS-frissítések](https://channel9.msdn.com/events/Build/2018/THR3118/player)

Projektek: 
* [Contoso Cafe bot](https://github.com/botbuilderbuild2018/build2018demo) demo - forráskód a Githubon

## <a name="next-steps"></a>További lépések

A LUIS kapcsolatos további információkért lásd a következőket:
* [Stack Overflow kérdések címkézett az intelligens hangfelismerési szolgáltatással](https://stackoverflow.com/questions/tagged/luis)
* [MSDN Language Understanding Intelligent Services (LUIS) fórum](https://social.msdn.microsoft.com/forums/azure/home?forum=LUIS) 