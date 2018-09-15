---
title: A LUIS a létrehozási és -végpont kulcsok kezelése
titleSuffix: Azure Cognitive Services
description: Miután létrehozott egy LUIS végponti kulcs az Azure Portalon, a kulcs hozzárendelése a LUIS-alkalmazás, és a megfelelő végpontra URL-Címének lekéréséhez. A végponti URL-cím használatával LUIS kaphatnak előjelzéseket.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 6d3f487fd64744fa390291d7e23d95cd9632cd23
ms.sourcegitcommit: ab9514485569ce511f2a93260ef71c56d7633343
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/15/2018
ms.locfileid: "45634937"
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

## <a name="assign-resource"></a>Erőforrás hozzárendelése

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

## <a name="unassign-resource"></a>Erőforrás hozzárendelésének megszüntetése
Ha a végpont kulcs szüntesse meg, nem törlődik az Azure-ból. Csak byl odpojen OD LUIS. 

Ha egy végpont kulcs hozzá nem rendelt, vagy nincs hozzárendelve az alkalmazáshoz, a kérelem a végponthoz való URL-cím hibát ad vissza: `401 This application cannot be accessed with the current subscription`. 

## <a name="include-all-predicted-intent-scores"></a>Tartalmazza az összes, szándék előrejelzett pontszámok
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

## <a name="enable-bing-spell-checker"></a>A Bing helyesírás-ellenőrzés engedélyezése 
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


## <a name="publishing-regions"></a>Közzétételi régiók

További tudnivalók a közzététel [régiók](luis-reference-regions.md) többek között a közzététel [Európa](luis-reference-regions.md#publishing-to-europe), és [Ausztrália](luis-reference-regions.md#publishing-to-australia). Közzétételi régiója nem ugyanaz a létrehozási régiók. A lekérdezés végpontot szeretne a közzétételi régióhoz tartozó, a szerzői műveletekhez részben régióban létrehozhat egy alkalmazást.

## <a name="next-steps"></a>További lépések

Az alkalmazás közzététele a kulcsa segítségével a **alkalmazás közzététele** lapot. Közzétételi útmutatásért lásd: [alkalmazás közzététele](luis-how-to-publish-app.md).

Lásd: [kulcsok a LUIS](luis-concept-keys.md) LUIS létrehozási és -végpont főbb fogalmak megértéséhez.