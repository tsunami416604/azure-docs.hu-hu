---
title: Előfizetés kulcsa – LUIS
titleSuffix: Azure Cognitive Services
description: Az ingyenes első-1000 Endpoint-lekérdezések használatához nem kell előfizetési kulcsokat létrehoznia. Ha a hiba a http 403 vagy 429 formátumban érkezik, létre kell hoznia egy kulcsot, és hozzá kell rendelnie az alkalmazáshoz.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 07/10/2019
ms.author: diberry
ms.openlocfilehash: 1f8b84722c881cee1fe196e5a614b58cf3c19031
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932861"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>Előfizetési kulcsok használata a LUIS-appal

Ha először használja a Language Understanding (LUIS), nem kell előfizetési kulcsokat létrehoznia. Az 1000-es végponti lekérdezésekkel kezdődik. 

Teszteléshez és prototípus csak az ingyenes (F0) csomagot használja. Éles rendszerek esetén használhatja a [fizetős](https://aka.ms/luis-price-tier) szint. Ne használja a [kulcs létrehozási](luis-concept-keys.md#authoring-key) végpont lekérdezések éles környezetben.


<a name="create-luis-service"></a>
<a name="create-language-understanding-endpoint-key-in-the-azure-portal"/>

## <a name="create-prediction-endpoint-runtime-resource-in-the-azure-portal"></a>Előrejelzési végpont futásidejű erőforrásának létrehozása a Azure Portal

Az előrejelzési [végpont erőforrását](get-started-portal-deploy-app.md#create-the-endpoint-resource) a Azure Portal hozza létre. Ez az erőforrás csak végpont-előrejelző lekérdezésekhez használható. Ne használja ezt az erőforrást az alkalmazás módosításainak létrehozásához.

Language Understanding erőforrást vagy Cognitive Services erőforrást is létrehozhat. Ha Language Understanding-erőforrást hoz létre, akkor célszerű postpend az erőforrás nevét. 

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

### <a name="using-resource-from-luis-portal"></a>A LUIS-portál erőforrásának használata

Ha a LUIS-portálon keresztül használja az erőforrást, nem kell tudnia a kulcsot és a helyet. Ehelyett ismernie kell az erőforrás-bérlőt, az előfizetést és az erőforrás nevét.

Miután a [](#assign-resource-key-to-luis-app-in-luis-portal) Luis-portálon hozzárendelte az erőforrást a Luis-alkalmazáshoz, a kulcs és a hely a lekérdezés-előrejelzési végpont URL-címének részeként van megadva a kezelés szakasz **kulcsok és végpont beállításai** lapján.
 
### <a name="using-resource-from-rest-api-or-sdk"></a>Erőforrás használata REST API vagy SDK-ból

Ha az erőforrást a REST API (ok) ból vagy az SDK-ból használja, ismernie kell a kulcsot és a helyet. Ezek az információk a lekérdezés-előrejelzési végpont URL-címének részeként jelennek meg a kezelés szakasz **kulcsok és végpont beállításai** lapján, valamint a Azure Portal az erőforrás áttekintése és kulcsok oldalain.

## <a name="assign-resource-key-to-luis-app-in-luis-portal"></a>Erőforrás-kulcs kiosztása LUIS-alkalmazáshoz a LUIS Portalon

Minden alkalommal, amikor új-erőforrást hoz létre a LUIS számára, [hozzá kell rendelnie az erőforrást a Luis alkalmazáshoz](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal). A hozzárendelés után ezt a lépést csak akkor kell végrehajtania, ha új erőforrást hoz létre. Létrehozhat egy új erőforrást az alkalmazás régiói kibontásához, vagy nagyobb számú előrejelzési lekérdezés támogatásához.

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

Adja hozzá a **helyesírás = True** querystring paramétert és a **Bing-Spell-Check-előfizetés-Key = {YOUR_BING_KEY_HERE}** értéket. Cserélje le a `{YOUR_BING_KEY_HERE}` és a Bing helyesírás-ellenőrző kulcs.

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

Például egy CI/CD-folyamat automation célból érdemes automatizálni a LUIS-alkalmazásokon LUIS erőforrás hozzárendelését. Ehhez a következő lépéseket kell elvégeznie:

1. Egy Azure Resource Manager a token beszerzése [webhely](https://resources.azure.com/api/token?plaintext=true). Ez a token hamarosan lejár, azonnal használható. A kérelem egy Azure Resource Manager-jogkivonatát adja vissza.

    ![Azure Resource Manager jogkivonat igénylése és Azure Resource Manager token fogadása](./media/luis-manage-keys/get-arm-token.png)

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

## <a name="change-pricing-tier"></a>Tarifacsomag-váltás

1.  A [Azure](https://portal.azure.com), keresse meg a LUIS-előfizetés. Válassza ki a LUIS-előfizetést.
    ![Keresse meg a LUIS-előfizetés](./media/luis-usage-tiers/find.png)
1.  A rendelkezésre álló díjszabási szintek megtekintéséhez válassza az **árképzési szintet** . 
    ![Árképzési szintek megtekintése](./media/luis-usage-tiers/subscription.png)
1.  Válassza ki a díjszabási szintet, és válassza a **kiválasztás** lehetőséget a módosítás mentéséhez. 
    ![A LUIS fizetési szint módosítása](./media/luis-usage-tiers/plans.png)
1.  Ha a díjszabás módosítása befejeződött, egy előugró ablak ellenőrzi az új tarifacsomag. 
    ![A LUIS támogatási csomag ellenőrzése](./media/luis-usage-tiers/updated.png)
1. Ne felejtse el [rendelje hozzá a végpont kulcs](#assign-endpoint-key) a a **közzététel** lapon, és használhatja az összes endpoint lekérdezés. 

## <a name="fix-http-status-code-403-and-429"></a>A 403-es és a 429-es HTTP-állapotkód javítása

A 403-es és a 429-es hibakód akkor jelenik meg, ha az árképzési szinten a másodpercenkénti tranzakciók száma vagy a havi tranzakció.

### <a name="when-you-receive-an-http-403-error-status-code"></a>HTTP 403-es hiba állapotának kódja esetén

Ha az összes ingyenes 1000-végpontot lekérdezi, vagy túllépi a díjszabási csomag havi tranzakciós kvótáját, a rendszer HTTP 403 hibakódot kap. 

Ennek a hibának a kijavításához módosítania kell [az árképzési szintet](luis-how-to-azure-subscription.md#change-pricing-tier) egy magasabb szintű csomagra, vagy [létre kell hoznia egy új erőforrást](get-started-portal-deploy-app.md#create-the-endpoint-resource) , és hozzá kell rendelnie az [alkalmazáshoz](get-started-portal-deploy-app.md#assign-the-resource-key-to-the-luis-app-in-the-luis-portal).

A hiba megoldásai a következők:

* A [Azure Portal](https://portal.azure.com)a Language Understanding erőforráson az **Erőforrás-kezelés – > díjszabási**szinten módosítsa az árképzési szintet magasabb TPS szintjére. Ha az erőforrás már hozzá van rendelve a Language Understanding alkalmazáshoz, semmit nem kell tennie a Language Understanding portálon.
*  Ha a használat meghaladja a legmagasabb szintű díjszabást, vegyen fel további Language Understanding erőforrásokat egy terheléselosztó elé. A Kubernetes vagy Docker-összeállítással rendelkező [Language Understanding-tároló](luis-container-howto.md) segíthet ennek elvégzésében.

### <a name="when-you-receive-an-http-429-error-status-code"></a>HTTP 429-es hiba állapotának kódja esetén

Ezt az állapotkódot akkor adja vissza a rendszer, ha a másodpercenkénti tranzakciók száma meghaladja a díjszabási szintet.  

A megoldások a következők:

* Megnövelheti [az árképzési szintet](#change-pricing-tier), ha nem a legmagasabb szintű szinten van.
* Ha a használat meghaladja a legmagasabb szintű díjszabást, vegyen fel további Language Understanding erőforrásokat egy terheléselosztó elé. A Kubernetes vagy Docker-összeállítással rendelkező [Language Understanding-tároló](luis-container-howto.md) segíthet ennek elvégzésében.
* Az ügyfélalkalmazás kérelmeit megadhatja az újrapróbálkozási [szabályzattal](https://docs.microsoft.com/azure/architecture/best-practices/transient-faults#general-guidelines) , amelyet Ön saját maga is végrehajthat, amikor megkapja ezt az állapotkódot. 

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
