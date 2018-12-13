---
title: Kulcsok kezelése
titleSuffix: Language Understanding - Azure Cognitive Services
description: Miután létrehozott egy LUIS végponti kulcs az Azure Portalon, a kulcs hozzárendelése a LUIS-alkalmazás, és a megfelelő végpontra URL-Címének lekéréséhez. A végponti URL-cím használatával LUIS kaphatnak előjelzéseket.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 0a278ded7ce290347645f345e4eee0b15972787f
ms.sourcegitcommit: 9fb6f44dbdaf9002ac4f411781bf1bd25c191e26
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/08/2018
ms.locfileid: "53088102"
---
# <a name="add-an-azure-luis-resource-to-app"></a>Az Azure LUIS-erőforrás hozzáadása alkalmazáshoz

Miután létrehozott egy LUIS-erőforrást az Azure Portalon, az erőforrás hozzárendelése a LUIS-alkalmazás, és a megfelelő végpontra URL-Címének lekéréséhez. A végponti URL-cím használatával LUIS kaphatnak előjelzéseket.

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


## <a name="assign-resource-in-luis-portal"></a>A LUIS-portál erőforrás hozzárendelése

1. A LUIS kulcs létrehozása a [az Azure portal](https://portal.azure.com). További utasításokért lásd: [egy az Azure-végpont kulcsának létrehozása](luis-how-to-azure-subscription.md).
 
2. Válassza ki **kezelés** a jobb felső menüben, majd válassza ki **kulcsokat és a végpontok**.

    [ ![Kulcsok és a végpontok lapot.](./media/luis-manage-keys/keys-and-endpoints.png) ](./media/luis-manage-keys/keys-and-endpoints.png#lightbox)

3. A LUIS hozzáadásához válassza ki a **erőforrás hozzárendelése +**.

    ![Rendelje hozzá egy erőforrást az alkalmazáshoz](./media/luis-manage-keys/assign-key.png)

4. Válassza ki a bérlők a párbeszédpanelen, a LUIS webhely bejelentkezési e-mail-címmel társított.  

5. Válassza ki a **Előfizetésnevet** társított hozzáadni kívánt Azure-erőforrás.

6. Válassza ki a **LUIS erőforrásnév**. 

7. Válassza ki **erőforrás hozzárendelése**. 

8. Keresse meg az új sort a táblázatban, és másolja a végpont URL-címe. Helyesen kialakítani, egy HTTP GET kérés legyen előrejelzési LUIS végpontját. 

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

## <a name="next-steps"></a>További lépések

Az alkalmazás közzététele a kulcsa segítségével a **alkalmazás közzététele** lapot. Közzétételi útmutatásért lásd: [alkalmazás közzététele](luis-how-to-publish-app.md).

Lásd: [kulcsok a LUIS](luis-concept-keys.md) LUIS létrehozási és -végpont főbb fogalmak megértéséhez.
