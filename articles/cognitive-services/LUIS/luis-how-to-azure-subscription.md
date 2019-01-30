---
title: Előfizetői azonosítók
titleSuffix: Language Understanding - Azure Cognitive Services
description: Nem kell használni az ingyenes első-1000 végpont lekérdezések előfizetői azonosítók létrehozása. Ha megjelenik egy _kvótájából_ hiba történt egy HTTP 403-as és 429-es formájában, kell hozzon létre egy kulcsot, és rendelje hozzá az alkalmazáshoz.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/18/2019
ms.author: diberry
ms.openlocfilehash: 2e5ecf32782c86b236c4947d5d2793be9c3883d8
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55223680"
---
# <a name="using-subscription-keys-with-your-luis-app"></a>Előfizetési kulcsok használata a LUIS-appal

Nem kell használni az ingyenes első-1000 végpont lekérdezések előfizetői azonosítók létrehozása. Miután ezen végpont-lekérdezést használ, hozzon létre egy Azure-erőforrást a [az Azure portal](http://portal.azure.com), hozzárendelheti a LUIS-alkalmazásokon ennek az erőforrásnak a [LUIS portál](https://www.luis.ai).

Ha megjelenik egy _kvótájából_ hiba történt egy HTTP 403-as és 429-es formájában, kell hozzon létre egy kulcsot, és rendelje hozzá az alkalmazáshoz. 

Teszteléshez és prototípus csak az ingyenes (F0) csomagot használja. Éles rendszerek esetén használhatja a [fizetős](https://aka.ms/luis-price-tier) szint. Ne használja a [kulcs létrehozási](luis-concept-keys.md#authoring-key) végpont lekérdezések éles környezetben.

<a name="create-luis-service"></a>

## <a name="create-language-understanding-endpoint-key-in-the-azure-portal"></a>Language Understanding végponti kulcs létrehozása az Azure Portalon

Ez az eljárás létrehoz egy **Language Understanding** erőforrás. Ha azt szeretné, hogy egy erőforrás, amely a Cognitive Services is használható, teljes körű kulcs létre **[Cognitive Service](../cognitive-services-apis-create-account.md)** a Language Understanding resource helyett. 

Ezt a kulcsot csak használandó végpont előrejelzési lekérdezések. Ezt a kulcsot a módosításokat a modell vagy az alkalmazás nem tudja ezt. 

1. Jelentkezzen be a  **[az Azure portal](https://ms.portal.azure.com/)**. 
1. Válassza ki a zöld **+** jelentkezzen be a felső bal oldali panelen, és keresse meg `Language Understanding` a Marketplace-en, majd válassza ki a **Language Understanding** , és kövesse a  **Hozzon létre felhasználói élményt** LUIS előfizetés fiók létrehozásához. 

    ![Azure Search](./media/luis-azure-subscription/azure-search.png) 

1. Konfigurálja az előfizetés fiók nevét, például beállításokkal árképzési szint stb. 

    ![Az Azure API kiválasztása](./media/luis-azure-subscription/azure-api-choice.png) 

1. Miután létrehozta a Language Understanding resource, megtekintheti a létrehozott hozzáférési kulcsok **erőforrás-kezelés -> kulcsok**. A kulcsok nem ne. Ez a szakasz bemutatja, hogyan az új erőforrás csatlakozni a LUIS-alkalmazások, a LUIS-portálon. 3. lépésében a LUIS-erőforrás neve van szüksége.

    ![Az Azure-kulcsok](./media/luis-azure-subscription/azure-keys.png)

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

1. Jelentkezzen be a LUIS-portálra, válassza ki egy alkalmazást, majd válassza ki az új kulcs hozzáadása **kezelés** a jobb felső menüben, majd válassza ki **kulcsokat és a végpontok**.

    [ ![Kulcsok és a végpontok lapot.](./media/luis-manage-keys/keys-and-endpoints.png) ](./media/luis-manage-keys/keys-and-endpoints.png#lightbox)

1. A LUIS hozzáadásához válassza ki a **erőforrás hozzárendelése +**.

    ![Rendelje hozzá egy erőforrást az alkalmazáshoz](./media/luis-manage-keys/assign-key.png)

1. Az e-mail-cím a bejelentkezés a LUIS webhelyre társított válassza a párbeszédpanel a bérlő.  

1. Válassza ki a **Előfizetésnevet** társított hozzáadni kívánt Azure-erőforrás.

1. Válassza ki a **LUIS erőforrásnév**. 

1. Válassza ki **erőforrás hozzárendelése**. 

1. Keresse meg az új sort a táblázatban, és másolja a végpont URL-címe. Helyesen kialakítani, egy HTTP GET kérés legyen előrejelzési LUIS végpontját. 

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
Az a **végpont URL-beállítások**, a **Bing helyesírás-ellenőrzővel** váltógomb lehetővé teszi, hogy a LUIS, mielőtt előrejelzési kijavítsa a hibásan leírt szavakat. Hozzon létre egy  **[kulcs a Bing Spell Check](https://azure.microsoft.com/try/cognitive-services/?api=spellcheck-api)**. 

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

Például egy CI/CD-folyamat automation célból érdemes automatizálni a LUIS-alkalmazásokon LUIS erőforrás hozzárendelését. Adott sorrendben kell hajtsa végre az alábbi lépéseket:

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

## <a name="how-to-fix-out-of-quota-errors-when-the-key-exceeds-pricing-tier-usage"></a>Hogyan out-az-kvóta hibák javítása, ha a kulcs meghaladja az árképzési szint használatához
Minden egyes csomaggal meghatározott arányban végpont kéréseket a LUIS-fiókjába. Ha a kérelmek száma nagyobb, mint a megengedett sebesség a díjköteles fiók percenként vagy havonta, a kérelmek, HTTP hibaüzenetet kap "429-es: Túl sok kérelem."

Minden egyes csomaggal halmozódnak kérés havonta. Ha az összes kérelmet magasabb, mint az engedélyezett sebességét, a kérelmek, HTTP hibaüzenetet kap "403-as: tiltott".  

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
