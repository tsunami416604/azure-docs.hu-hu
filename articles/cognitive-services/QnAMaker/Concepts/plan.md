---
title: Az alkalmazás megtervezése – QnA Maker
description: A QnA Maker alkalmazás megtervezéséhez meg kell ismernie, hogy a QnA Maker hogyan működik, és hogyan kommunikál más Azure-szolgáltatásokkal, valamint néhány Tudásbázis-koncepcióval.
ms.topic: conceptual
ms.date: 07/2/2020
ms.openlocfilehash: d19ec51aec7e71b6f040a03543f72af3aed09556
ms.sourcegitcommit: 9b5c20fb5e904684dc6dd9059d62429b52cb39bc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85875708"
---
# <a name="plan-your-qna-maker-app"></a>A QnA Maker alkalmazás megtervezése

A QnA Maker alkalmazás megtervezéséhez meg kell ismernie, hogy a QnA Maker hogyan működik, és hogyan kommunikál más Azure-szolgáltatásokkal, valamint néhány Tudásbázis-koncepcióval.

## <a name="azure-resources"></a>Azure-erőforrások

A QnA Makertel létrehozott összes [Azure-erőforrásnak](azure-resources.md#resource-purposes) konkrét célja van. Mivel minden erőforrás saját célra, korlátokra és [díjszabási](azure-resources.md#pricing-tier-considerations)csomagra vonatkozik, fontos tisztában lennie azzal, hogy ezeket az erőforrásokat hogyan végzi el a tervezési folyamat részeként.

|Erőforrás|Szerep|
|--|--|
| Erőforrás [QnA Maker](azure-resources.md#qna-maker-resource)|Szerzői műveletek és lekérdezések előrejelzése|
| Erőforrás [Cognitive Search](azure-resources.md#cognitive-search-resource)|Adattárolás és keresés|
| [Erőforrás-és alkalmazáscsomag-szolgáltatási erőforrás app Service](azure-resources.md#app-service-and-app-service-plan)|Előrejelzési végpont lekérdezése|
| Erőforrás [Application Insights](azure-resources.md#application-insights)|Lekérdezés-előrejelzési telemetria|

### <a name="resource-planning"></a>Erőforrás-tervezés

A szerzői műveletek és a lekérdezések előrejelzése – az ingyenes szinten – az `F0` egyes erőforrások esetében is működik, és a szerzői és a lekérdezési előrejelzési élményt is biztosítja. Ha éles környezetben helyez át, élő, forgatókönyv, újraértékeli az erőforrás-kijelölést.

#### <a name="qna-maker-resource"></a>Erőforrás QnA Maker

Egyetlen QnA Maker erőforrás több tudásbázist is képes tárolni. A tudásbázisok számát a Cognitive Search árképzési csomag által támogatott indexek mennyisége határozza meg. További információk az [Indexek és a tudásbázisok kapcsolatáról](azure-resources.md#index-usage).

#### <a name="knowledge-base-size-and-throughput"></a>Tudásbázis mérete és átviteli sebessége

Ha valós alkalmazást szeretne létrehozni, tervezze meg erőforrásait a Tudásbázis méretének megfelelően, és a lekérdezési előrejelzési kérelmeket.

A Tudásbázis méretét az alábbiak vezérlik:
* Az erőforrás-díjszabási szintek [Cognitive Search](../../../search/search-limits-quotas-capacity.md)
* [QnA Maker korlátok](../limits.md)

A Tudásbázis lekérdezés-előrejelzési kérelmét a webalkalmazás-csomag és a webalkalmazás vezérli. A díjszabási csomag megtervezéséhez tekintse meg az [ajánlott beállítások](azure-resources.md#recommended-settings) című témakört.

### <a name="resource-sharing"></a>Erőforrás-megosztás

Ha már rendelkezik néhány használatban lévő erőforrással, érdemes megfontolnia az erőforrások megosztását. Míg egyes erőforrások [megoszthatók](azure-resources.md#share-services-with-qna-maker), ez egy speciális forgatókönyv.

Az ugyanabban a QnA Maker erőforrásban létrehozott tudásbázisok ugyanazt a **tesztelési** lekérdezési előrejelzési végpontot használják.

### <a name="understanding-impact-of-resource-selection"></a>Az erőforrás-kijelölés hatásának megértése

A megfelelő erőforrás-kiválasztás azt jelenti, hogy az Ön tudásbázisa sikeresen válaszol a lekérdezések előrejelzésére.

Ha a Tudásbázis nem működik megfelelően, általában a probléma nem megfelelő erőforrás-kezelés.

Nem megfelelő erőforrás-kiválasztás esetén vizsgálat szükséges annak megállapításához, hogy melyik [erőforrást kell módosítani](azure-resources.md#when-to-change-a-pricing-tier).

## <a name="knowledge-bases"></a>Tudásbázisok

A Tudásbázis közvetlenül kapcsolódik a QnA Maker erőforráshoz, és tartalmazza a lekérdezés-előrejelzési kérelmek megválaszolásához használt kérdés-és QnA-párokat.

### <a name="language-considerations"></a>Nyelvi megfontolások

A QnA Maker erőforráson létrehozott első Tudásbázis az erőforrás nyelvét állítja be. Egy QnA Maker erőforráshoz csak egy nyelv tartozhat.

A lekérdezés a lekérdezés előrejelzési végpontjának való elküldése előtt strukturálja a QnA Maker erőforrásokat nyelv szerint, vagy használjon [fordítót](../../translator/translator-info-overview.md) a lekérdezés más nyelvről való módosítására a Tudásbázis nyelvén.

### <a name="ingesting-data-sources"></a>Adatforrások begyűjtése

A Tudásbázis létrehozásához felhasznált, betöltött [adatforrások](knowledge-base.md)a következők lehetnek:

* Nyilvános URL-cím
* Privát SharePoint URL-címe
* Fájl

A betöltési folyamat a [támogatott tartalomtípusokat](content-types.md) Markdown alakítja. A válasz további szerkesztése a Markdown- *mel* történik. A Tudásbázis létrehozása után szerkesztheti a [QnA párokat](question-answer-set.md) a QnA Maker-portálon, [Rich Text authoring](../how-to/edit-knowledge-base.md#rich-text-editing-for-answer)használatával.

### <a name="data-format-considerations"></a>Adatformátumra vonatkozó megfontolások

Mivel a QnA pár végső formátuma Markdown, a [Markdown-támogatás](../reference-markdown-format.md) ismertetése fontos.

A csatolt lemezképeknek elérhetőnek kell lenniük egy nyilvános URL-címről, hogy megjelenjenek a QnA Maker portál tesztelés ablaktábláján, valamint az ügyfélalkalmazások, mivel QnA Maker nem biztosít hitelesítést a tartalomhoz, beleértve a lemezképeket is.

### <a name="bot-personality"></a>Bot-személyiség

Adjon hozzá egy robot-személyiséget a tudásbázishoz a [Chit-Chat](../how-to/chit-chat-knowledge-base.md)használatával. Ez a személyiség egy bizonyos társalgási hangon, például a *Professional* és a *Friendly*szolgáltatásban elérhető válaszokat tartalmaz. Ez a Chit-Chat olyan társalgási készletként van megadva, amelynek teljes hozzáférése van a hozzáadáshoz, szerkesztéshez és eltávolításhoz.

A bot-személyiség akkor javasolt, ha a robot a tudásbázishoz csatlakozik. Ha több szolgáltatáshoz csatlakozik, amelyek közül az egyik az Ön tudásbázisa, dönthet úgy, hogy folytatja a chit-csevegés használatát a Tudásbázisban, de érdemes áttekintenie, hogy a bot szolgáltatás hogyan kommunikál, ha ez a megfelelő építészeti kialakítás a használathoz.

### <a name="conversation-flow-with-a-knowledge-base"></a>Beszélgetési folyamat tudásbázissal

A beszélgetési folyamat általában egy felhasználó megszólításával kezdődik, például `Hi` vagy `Hello` . A Tudásbázis választ kaphat az általános válaszokra, például a következő kérdésekre is, amelyek a `Hi, how can I help you` beszélgetés folytatására is lehetőséget nyújtanak.

Meg kell terveznie a beszélgetési folyamatát egy hurkoval, hogy a felhasználó tudja, hogyan használja a robotot, és ne hagyhatja el a beszélgetésben a robot. Az [utólagos utasítások](../how-to/multiturn-conversation.md) összekapcsolják a QnA párok közötti összekapcsolást, amelyek lehetővé teszik a beszélgetési folyamat használatát.

### <a name="authoring-with-collaborators"></a>Szerzői műveletek

A közreműködők más fejlesztők is lehetnek, akik megosztják a Tudásbázis-alkalmazás teljes fejlesztői veremét, vagy csak a tudásbázist használják.

A Tudásbázis authoring számos [szerepköralapú hozzáférési engedélyt](../how-to/collaborate-knowledge-base.md) támogat, amelyeket a Azure Portal alkalmaz a közreműködő képességei hatókörének korlátozására.

## <a name="integration-with-client-applications"></a>Integráció az ügyfélalkalmazások alkalmazásával

Az [ügyfélalkalmazások](integration-with-other-applications.md) közötti integráció egy lekérdezés küldését jelenti az előrejelzési futtatókörnyezet végpontjának. A rendszer egy lekérdezést küld az adott tudásbázisba egy SDK-val vagy REST-alapú kéréssel a QnA Maker webalkalmazás-végpontjának.

Az ügyfélalkalmazás helyes hitelesítéséhez az ügyfélalkalmazás a megfelelő hitelesítő adatokat és Tudásbázis-azonosítót kell küldenie. Ha Azure Bot Service használ, konfigurálja a beállítást a Azure Portal bot-konfigurációjának részeként.

### <a name="conversation-flow-in-a-client-application"></a>Beszélgetési folyamat egy ügyfélalkalmazás

A beszélgetési folyamat egy [ügyfélalkalmazás](integration-with-other-applications.md), például egy Azure bot esetében a tudásbázissal való interakció előtt és után is megkövetelheti a funkciót.

Ha az ügyfélalkalmazás támogatja a beszélgetési folyamatot, vagy alternatív módszert biztosít a követő kérések vagy a Chit-Chit kezelésére, tervezze meg ezeket a korai, és győződjön meg arról, hogy az ügyfélalkalmazás által használt lekérdezés megfelelően van kezelve, vagy egy másik szolgáltatás, vagy pedig a tudásbázisba.

### <a name="dispatching-between-qna-maker-and-language-understanding-luis"></a>QnA Maker és Language Understanding (LUIS) közötti elküldés

Az ügyfélalkalmazás több funkciót is biztosíthat, amelyek közül csak egy Tudásbázis válaszol. Más funkcióknak továbbra is ismerniük kell a társalgási szöveget, és ki kell bontani a jelentésből.

Az ügyfélalkalmazások általános architektúrája QnA Maker és [Language Understanding (Luis)](../../LUIS/what-is-luis.md) együttes használata. A LUIS megadja a szöveg besorolását és kinyerését bármely lekérdezéshez, beleértve az egyéb szolgáltatásokat is, míg a QnA Maker a tudásbázistól választ ad.

[Megosztott architektúrában](../choose-natural-language-processing-service.md)a két szolgáltatás közötti elküldés a bot Framework [Feladó](https://github.com/Microsoft/botbuilder-tools/tree/master/packages/Dispatch) eszközével történik.

### <a name="active-learning-from-a-client-application"></a>Az ügyfélalkalmazás aktív tanulása

A QnA Maker az _aktív tanulással_ fejleszti a tudásbázist, ha alternatív kérdéseket javasol a válaszra. Az ügyfélalkalmazás felelős az [aktív tanulás](active-learning-suggestions.md)egy részéért. Az ügyfélalkalmazás a társalgási kérések segítségével megállapíthatja, hogy a Tudásbázisból visszaadott válasz nem a felhasználó által keresett válasz volt, és a jobb válasz meghatározása. Az ügyfélalkalmazás [ezt az információt vissza kell küldenie a tudásbázisba](active-learning-suggestions.md#how-you-give-explicit-feedback-with-the-train-api) az előrejelzési minőség javítása érdekében.

### <a name="providing-a-default-answer"></a>Alapértelmezett válasz megadása

Ha a Tudásbázis nem talál választ, az _alapértelmezett választ_adja vissza. Ez a válasz konfigurálható a QnA Maker portálon, a **Beállítások** lapon vagy az [API](https://docs.microsoft.com/rest/api/cognitiveservices/qnamaker/knowledgebase/update#request-body)-kon.

Ez az alapértelmezett válasz eltér az Azure bot alapértelmezett válaszával. Az Azure bot alapértelmezett válasza a Azure Portalban van konfigurálva a robot esetében, a konfigurációs beállítások részeként, és a rendszer akkor adja vissza, ha a pontszám küszöbértéke nem teljesül.

## <a name="prediction"></a>Előrejelzés

Az előrejelzés a Tudásbázis válasza, és több információt tartalmaz, mint amennyit csak a válasz. A lekérdezés-előrejelzési válasz beszerzéséhez használja a [GENEATEANSWER API](query-knowledge-base.md)-t.

### <a name="prediction-score-fluctuations"></a>Előrejelzési pontszámok ingadozásai

A pontszám több tényező alapján is változhat:

* A tulajdonsággal [GenerateAnswer](query-knowledge-base.md) válaszként kért válaszok száma `top`
* Számos elérhető alternatív kérdés
* Metaadatok szűrése
* A lekérdezés elküldése `test` vagy `production` Tudásbázis

[Kétfázisú válasz rangsorolása](query-knowledge-base.md#how-qna-maker-processes-a-user-query-to-select-the-best-answer):
* Cognitive Search – az első rangsorban, hogy a válasz a Cognitive Searchtól való visszatéréshez, az _engedélyezett válaszok_ számának elég magasnak kell lennie ahhoz, hogy az Cognitive Search a legjobb válaszokat adja vissza, így azok beléphetnek a QnA Maker sorba.
* QnA Maker – második rang – alkalmazza a featurization és a gépi tanulást a legjobb válasz meghatározásához.

### <a name="service-updates"></a>Szolgáltatási hírek

A szolgáltatás frissítései automatikusan kezelhetők a [legújabb futtatókörnyezet-frissítések](../how-to/set-up-qnamaker-service-azure.md#get-the-latest-runtime-updates)alkalmazásával.

### <a name="scaling-throughput-and-resiliency"></a>Skálázás, átviteli sebesség és rugalmasság

A skálázást, az átviteli sebességet és a rugalmasságot az [Azure-erőforrások](../how-to/set-up-qnamaker-service-azure.md), az azok díjszabási szintjei, valamint a környező architektúrák, például a [Traffic Manager](../how-to/set-up-qnamaker-service-azure.md#business-continuity-with-traffic-manager)határozzák meg.

### <a name="analytics-with-application-insights"></a>Analitika Application Insights

A Tudásbázis összes lekérdezését Application Insights tárolja. A mérőszámok megismeréséhez használja a [legfontosabb lekérdezéseket](../how-to/get-analytics-knowledge-base.md) .

## <a name="development-lifecycle"></a>Fejlesztési életciklus

A Tudásbázis [fejlesztési életciklusa](development-lifecycle-knowledge-base.md) folyamatban van: a Tudásbázis szerkesztése, tesztelése és közzététele.

### <a name="knowledge-base-development-of-qna-maker-pairs"></a>QnA Maker pár tudásbázisának fejlesztése

Az [QnA-párokat](question-answer-set.md) az ügyfélalkalmazás használata alapján kell megtervezni és kifejleszteni.

Minden pár tartalmazhatja a következőket:
* Metaadatok – szűrhetők lekérdezéskor. Ez lehetővé teszi az QnA-párok címkézését az adatok forrásával, tartalmával, formátumával és céljával kapcsolatos további információkkal.
* Követési kérések – az elérési út meghatározása a Tudásbázisban, hogy a felhasználó a megfelelő válaszra jusson.
* Alternatív kérdések – a alternatív kérdések fontosak ahhoz, hogy a keresés a kérdés különböző formáiban is megfeleljen a válasznak. Az [aktív tanulási javaslatok](active-learning-suggestions.md) alternatív kérdéseket tesznek fel.

### <a name="devops-development"></a>DevOps-fejlesztés

Az DevOps-folyamatba beszúrandó Tudásbázis létrehozásához a tudásbázist a [Batch-tesztelés](../quickstarts/batch-testing.md)során el kell különíteni.

A Tudásbázis megosztja a Cognitive Search indexet az QnA Maker erőforrás minden más tudásbázisával. Amíg a Tudásbázis el van különítve a partícióval, az index megosztása a közzétett tudásbázishoz képest különbséget eredményezhet a pontszámban.

Ahhoz, hogy ugyanaz a _pontszám_ legyen a `test` és a `production` Tudásbázisban, el kell különíteni egy QnA Maker erőforrást egyetlen tudásbázisba. Ebben az architektúrában az erőforrásnak csak az elkülönített batch-teszt hosszával kell élnie.

## <a name="next-step"></a>Következő lépés

* [Azure-erőforrások](../how-to/set-up-qnamaker-service-azure.md)
* [Kérdés-válasz párok](question-answer-set.md)