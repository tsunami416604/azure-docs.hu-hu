---
title: Előfizetői azonosítók
titleSuffix: Language Understanding - Azure Cognitive Services
description: Nem kell használni az ingyenes első-1000 végpont lekérdezések előfizetői azonosítók létrehozása. Ha megjelenik egy _kvótájából_ hiba történt egy HTTP 403-as és 429-es formájában, kell hozzon létre egy kulcsot, és rendelje hozzá az alkalmazáshoz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 06/18/2019
ms.author: diberry
ms.openlocfilehash: 7f82bf5a40df0554d4f98b2d835fcbd69279be43
ms.sourcegitcommit: b7a44709a0f82974578126f25abee27399f0887f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/18/2019
ms.locfileid: "67204161"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>Előfizetési kulcsok használata a LUIS-appal

Language Understanding (LUIS) első használatakor nem kell előfizetői azonosítók létrehozása. 1000 végpont lekérdezések először kapnak. 

Teszteléshez és prototípus csak az ingyenes (F0) csomagot használja. Éles rendszerek esetén használhatja a [fizetős](https://aka.ms/luis-price-tier) szint. Ne használja a [kulcs létrehozási](luis-concept-keys.md#authoring-key) végpont lekérdezések éles környezetben.


<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"/>

## <a name="create-prediction-endpoint-runtime-resource-in-the-azure-portal"></a>Előrejelzési végpont futásidejű erőforrás létrehozása az Azure Portalon

Hoz létre a [előrejelzési végponti erőforrás](get-started-portal-deploy-app.md#create-the-endpoint-resource) az Azure Portalon. Ehhez az erőforráshoz csak használandó végpont előrejelzési lekérdezések. Ne használja ezt az erőforrást módosítások az alkalmazás szerzői műveletekhez részben.

<a name="programmatic-key" ></a>
<a name="authoring-key" ></a>
<a name="endpoint-key" ></a>
<a name="use-endpoint-key-in-query" ></a>
<a name="api-usage-of-ocp-apim-subscription-key" ></a>
<a name="key-limits" ></a>
<a name="key-limit-errors" ></a>
<a name="key-concepts"></a>
<a name="authoring-key"></a>
<a name="create-and-use-an-endpoint-key"></a>
<a name="assign-endpoint-key"></a>
<a name="assign-resource"></a>


## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>Az erőforráskulcs hozzárendelése a LUIS-portál a LUIS-alkalmazás

Minden alkalommal, amikor egy új erőforrást hoz létre a LUIS, kell [az erőforrás hozzárendelése a LUIS-alkalmazásokon](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal). Miután van hozzárendelve, nem kell újra el ezt a lépést, ha nem hoz létre egy új erőforrást. Létrehozhat egy új erőforrást, bontsa ki a régiók, az alkalmazás vagy egy előrejelzési lekérdezések nagyobb számának támogatásához.

<!-- content moved to luis-reference-regions.md, need replacement links-->
<a name="regions-and-keys"></a>
<a name="publishing-to-europe"></a>
<a name="publishing-to-australia"></a>

### <a name="unassign-resource"></a>Erőforrás hozzárendelésének megszüntetése
Ha a végpont kulcs szüntesse meg, nem törlődik az Azure-ból. Csak byl odpojen OD LUIS. 

Ha egy végpont kulcs hozzá nem rendelt, vagy nincs hozzárendelve az alkalmazáshoz, a kérelem a végponthoz való URL-cím hibát ad vissza: `401 This application cannot be accessed with the current subscription`. 

### <a name="include-all-predicted-intent-scores"></a>Tartalmazza az összes, szándék előrejelzett pontszámok
A **minden beágyazott előre jelzett szándék pontszámok** jelölőnégyzet lehetővé teszi, hogy a végpont lekérdezésekre adott válaszok tartalmazzák az előrejelzési pontszámának egyes szándékot. 

Ez a beállítás lehetővé teszi, hogy a csevegőrobot, vagy a LUIS-hívása alkalmazás a programozott döntéshozáshoz, a visszaadott leképezések a pontszámok alapján. A felső két szándék általában a legjobban érdekli. Ha a felső pontszám nem a nincs szándék, a csevegőrobot, amely lehetővé teszi a nincs szándékának és a nagy pontozási célt végleges választhat követő kérdést feltenni választhat. 

A leképezések és azok pontszámok is vannak-e a végpont naplók tartalmazza. Is [exportálása](luis-how-to-start-new-app.md#export-app) ezeket a naplókat és elemezheti a pontszámokat. 

```JSON
{
  "query": "book a flight to Cairo",
  "topScoringIntent": {
    "intent": "None",
    "score": 0.5223427
  },
  "intents": [
    {
      "intent": "None",
      "score": 0.5223427
    },
    {
      "intent": "BookFlight",
      "score": 0.372391433
    }
  ],
  "entities": []
}
```

### <a name="enable-bing-spell-checker"></a>A Bing helyesírás-ellenőrzés engedélyezése 
Az a **végpont URL-beállítások**, a **Bing helyesírás-ellenőrzővel** váltógomb lehetővé teszi, hogy a LUIS, mielőtt előrejelzési kijavítsa a hibásan leírt szavakat. Hozzon létre egy  **[kulcs a Bing Spell Check](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)** . 

Adja hozzá a **helyesírás-ellenőrzés = true** sztringet és a **bing – helyesírás-ellenőrzés-subscription-key = {YOUR_BING_KEY_HERE}** . Cserélje le a `{YOUR_BING_KEY_HERE}` és a Bing helyesírás-ellenőrző kulcs.

```JSON
{
  "query": "Book a flite to London?",
  "alteredQuery": "Book a flight to London?",
  "topScoringIntent": {
    "intent": "BookFlight",
    "score": 0.780123
  },
  "entities": []
}
```

### <a name="publishing-regions"></a>Közzétételi régiók

További tudnivalók a közzététel [régiók](luis-reference-regions.md) többek között a közzététel [Európa](luis-reference-regions.md#publishing-to-europe), és [Ausztrália](luis-reference-regions.md#publishing-to-australia). Közzétételi régiója nem ugyanaz a létrehozási régiók. A lekérdezés végpontot szeretne a közzétételi régióhoz tartozó, a szerzői műveletekhez részben régióban létrehozhat egy alkalmazást.

## <a name="assign-resource-without-luis-portal"></a>Rendelje hozzá a LUIS-portál nélküli erőforráshoz

Például egy CI/CD-folyamat automation célból érdemes automatizálni a LUIS-alkalmazásokon LUIS erőforrás hozzárendelését. Ehhez hajtsa végre a következő lépéseket kell:

1. Egy Azure Resource Manager a token beszerzése [webhely](https://resources.azure.com/api/token?plaintext=true). Ez a token hamarosan lejár, azonnal használható. A kérelem egy Azure Resource Manager-jogkivonatát adja vissza.

    ![Azure Resource Manager-token és az Azure Resource Manager-tokent kap](./media/luis-manage-keys/get-arm-token.png)

1. A jogkivonat használatával kérhet a LUIS-erőforrások, előfizetések között a [LUIS lekérése az azure-fiókok API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be313cec181ae720aa2b26c), a felhasználói fiók rendelkezik hozzáféréssel. 

    A bejegyzés API megköveteli a következő beállításokat:

    |Fejléc|Érték|
    |--|--|
    |`Authorization`|Az érték `Authorization` van `Bearer {token}`. Figyelje meg, hogy a token értékét meg kell előznie a word `Bearer` és egy szóközt.| 
    |`Ocp-Apim-Subscription-Key`|A [kulcs létrehozási](luis-how-to-account-settings.md).|

    Az API-t a LUIS előfizetések, beleértve az előfizetés-azonosító, az erőforráscsoportot és az erőforrás nevét adja vissza a fiók neve a JSON-objektumok tömbjét adja vissza. Egy elem a tömbben, amely a LUIS alkalmazás hozzárendelése a LUIS-erőforrás megkeresése. 

1. A jogkivonat hozzárendelése a LUIS erőforrás a [alkalmazáshoz az azure-fiókok a LUIS hozzárendelése](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5be32228e8473de116325515) API-t. 

    A bejegyzés API megköveteli a következő beállításokat:

    |Típus|Beállítás|Érték|
    |--|--|--|
    |Fejléc|`Authorization`|Az érték `Authorization` van `Bearer {token}`. Figyelje meg, hogy a token értékét meg kell előznie a word `Bearer` és egy szóközt.|
    |Fejléc|`Ocp-Apim-Subscription-Key`|A [kulcs létrehozási](luis-how-to-account-settings.md).|
    |Fejléc|`Content-type`|`application/json`|
    |A lekérdezési karakterlánc|`appid`|A LUIS-app azonosítója. 
    |Törzs||{"AzureSubscriptionId": "ddda2925-af7f-4b05-9ba1-2155c5fe8a8e",<br>"ResourceGroup": "erőforráscsoport-2"<br>"AccountName": "a luis-uswest-S0-2"}|

    Ha ez az API sikeres volt, a 201 - létrehozott állapotát adja vissza. 

## <a name="change-pricing-tier"></a>A tarifacsomag módosítása

1.  A [Azure](https://portal.azure.com), keresse meg a LUIS-előfizetés. Válassza ki a LUIS-előfizetést.
    ![Keresse meg a LUIS-előfizetés](./media/luis-usage-tiers/find.png)
1.  Válassza ki **tarifacsomag** az elérhető tarifacsomagok láthatók. 
    ![Árképzési szintek megtekintése](./media/luis-usage-tiers/subscription.png)
1.  Válassza ki a árképzési szint, és válasszon **kiválasztása** menteni a módosítást. 
    ![A LUIS fizetési szint módosítása](./media/luis-usage-tiers/plans.png)
1.  Ha a díjszabás módosítása befejeződött, egy előugró ablak ellenőrzi az új tarifacsomag. 
    ![A LUIS támogatási csomag ellenőrzése](./media/luis-usage-tiers/updated.png)
1. Ne felejtse el [rendelje hozzá a végpont kulcs](#assign-endpoint-key) a a **közzététel** lapon, és használhatja az összes endpoint lekérdezés. 

## <a name="fix-http-status-code-403-and-429"></a>Hárítsa el a HTTP-állapotkód: 403-as és 429-es

Hibaüzenetet 403-as és 429 állapotkódok Ha túllépi a tranzakció / másodperc vagy tarifacsomag havi tranzakciók.

### <a name="when-you-receive-an-http-403-error-status-code"></a>Amikor megjelenik egy HTTP 403-as hibakód állapota:

Amikor az összes ingyenes 1000 végpont lekérdezések használhatja, vagy a tarifacsomag havi tranzakciók kvóta, kap egy HTTP 403-as hibakód állapota. 

Ez a hiba javításához egyaránt szüksége [tarifacsomagra](luis-how-to-azure-subscription.md#change-pricing-tier) magasabb szintre vagy [hozzon létre egy új erőforrást](get-started-portal-deploy-app.md#create-the-endpoint-resource) és [rendelje hozzá az alkalmazás](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

Ez a hiba-megoldások a következők:

* Az a [az Azure portal](https://portal.azure.com), az ismertetése, erőforrás, a nyelv a **erőforrás-kezelés -> tarifacsomag**, módosítsa a tarifacsomagot TPS magasabb szintre. Nem kell az erőforrás már hozzá van rendelve a Language Understanding app, nincs semmi, a Language Understanding portálon.
*  Ha a felhasználás meghaladja a legmagasabb tarifacsomagot, további Language Understanding erőforrások hozzáadása egy előtti terheléselosztó tartománynévcímkéje őket a. A [Language Understanding tároló](luis-container-howto.md) a Kubernetes vagy a Docker Compose segítségével ezt.

### <a name="when-you-receive-an-http-429-error-status-code"></a>Amikor megjelenik egy HTTP 429-es hibakód állapota:

Ezzel az állapotkóddal van adott vissza, ha a tranzakciók másodpercenkénti meghaladja a tarifacsomagot.  

Megoldások a következők:

* Is [tarifacsomag növelése](#change-pricing-tier), ha nem, a legmagasabb szintű.
* Ha a felhasználás meghaladja a legmagasabb tarifacsomagot, további Language Understanding erőforrások hozzáadása egy előtti terheléselosztó tartománynévcímkéje őket a. A [Language Understanding tároló](luis-container-howto.md) a Kubernetes vagy a Docker Compose segítségével ezt.
* Akkor is gate az ügyfélkérelmek alkalmazás az egy [újrapróbálkozási szabályzat](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) megvalósítása saját kezűleg Ha ezzel az állapotkóddal. 

## <a name="viewing-summary-usage"></a>Összefoglaló használat megtekintése
A LUIS-használati adatokat megtekintheti az Azure-ban. A **áttekintése** lapon többek között a hívások és hibák legutóbbi összegző információit jeleníti meg. Ha Ön kérést egy LUIS végpontot, majd azonnal tekintse meg a **áttekintőlapján**, akár öt perc alatt jelenik meg a használat engedélyezése.

![Összefoglaló használat megtekintése](./media/luis-usage-tiers/overview.png)

## <a name="customizing-usage-charts"></a>Használati diagramok testreszabása
Metrikák az adatok részletesebb betekintést biztosít.

![Alapértelmezett mérőszámok](./media/luis-usage-tiers/metrics-default.png)

Konfigurálhatja a mérőszámdiagramok időszak és a metrika típusa. 

![Egyéni metrikák](./media/luis-usage-tiers/metrics-custom.png)

## <a name="total-transactions-threshold-alert"></a>Összes tranzakciós küszöbértékének riasztási
Ha szeretné tudni, hogy ha egy bizonyos tranzakció küszöbértéket, például 10 000 tranzakció, elérte a riasztás is létrehozhat. 

![Alapértelmezett riasztások](./media/luis-usage-tiers/alert-default.png)

Metrikariasztás hozzáadása a **összes hívás** metrika egy bizonyos ideig. Adja hozzá, amelyek megkapják a riasztás minden személyek e-mail címét. Adja hozzá a webhookok az összes rendszer, amelyre a riasztást kapni. A riasztás akkor aktiválódik, ha egy logikai alkalmazást is futtathatja. 

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan használható [verziók](luis-how-to-manage-versions.md) a LUIS-alkalmazás kezeléséhez.
