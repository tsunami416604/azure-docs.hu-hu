---
title: 'Gyors útmutató: Kérdések és válaszok hozzáadása QnA Maker portálon'
titleSuffix: Azure Cognitive Services
description: ''
services: cognitive-services
author: diberry
manager: nitinme
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 08/27/2019
ms.author: diberry
ms.openlocfilehash: dc4548e0c07adc485d1bb5785179aeb7ea2f3fe1
ms.sourcegitcommit: 532335f703ac7f6e1d2cc1b155c69fc258816ede
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/30/2019
ms.locfileid: "70195693"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Gyors útmutató: Kérdések és válaszok hozzáadása QnA Maker portálon

Miután létrehozta a tudásbázist, adjon hozzá metaadatokat tartalmazó kérdéseket és válaszokat, hogy a felhasználók megtalálják a megfelelő választ a kérdésére.

A megfelelő válasz egyetlen válasz, de többféleképpen is megteheti, hogy az ügyfél felteheti az adott válaszhoz vezető kérdést.

Az alábbi táblázatban szereplő kérdések például az Azure szolgáltatási korlátaival kapcsolatosak, de mindegyiknek egy másik Azure-szolgáltatással kell rendelkezniük. 

<a name="qna-table"></a>


|Beállítás|Kérdések|Válasz|Metaadatok|
|--|--|--|--|
|#1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|#2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Miután hozzáadta a metaadatokat egy kérdés-válasz készlethez, az ügyfélalkalmazás a következőket teheti:

* Olyan válaszokat kér, amelyek csak bizonyos metaadatokat egyeznek meg.
* Fogadja az összes választ, de a válaszokat az egyes válaszok metaadataitól függően feldolgozhatja.

Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt. 

## <a name="prerequisites"></a>Előfeltételek

* Egy QnA Maker szolgáltatás
* A QnA Maker szolgáltatásban létrehozott Tudásbázis

Mindkettőt az [első](../how-to/create-knowledge-base.md)gyors útmutatóban hozták létre.

## <a name="sign-in-to-the-qna-maker-portal"></a>Bejelentkezés a QnA Maker portálra

1. Jelentkezzen be a [QnA Maker portálra](https://www.qnamaker.ai).

1. Válassza ki a meglévő tudásbázist. Ha nem rendelkezik tudásbázissal, térjen vissza az [előző](../how-to/create-knowledge-base.md) rövid útmutatóhoz, és fejezze be a Tudásbázis létrehozásához szükséges lépéseket.

## <a name="add-additional-alternatively-phrased-questions"></a>További megfogalmazott kérdések hozzáadása 

Az [előző](../how-to/create-knowledge-base.md)rövid útmutatóból származó aktuális Tudásbázis rendelkezik a QnA Maker hibaelhárítási kérdésekkel és válaszokkal. Ezek a készletek akkor jöttek létre, amikor az URL-címet hozzáadták a tudásbázishoz a létrehozási folyamat során. 

Az URL-cím importálása után csak egy kérdés lett létrehozva. 

Ebben az eljárásban további kérdéseket is megadhat.

1. A **Szerkesztés** lapon használja a kérdés-és Levelesláda fölötti keresési szövegmezőt a kérdés megtalálásához.`How large a knowledge base can I create?`

1. A **kérdés** oszlopban válassza az **+ alternatív megfogalmazás hozzáadása** lehetőséget, majd adja hozzá a következő táblázatban megadott összes új megfogalmazást.

    |Alternatív megfogalmazás|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`| 

1. A Tudásbázis újratanításához válassza a **Mentés és a tanítás** lehetőséget. 

1. Válassza a **teszt**lehetőséget, majd adjon meg egy olyan kérdést, amely az új alternatív megfogalmazások egyikéhez közeledik, de nem pontosan ugyanaz a megfogalmazás:

    `What GB size can a knowledge base be?`

    A megfelelő választ a rendszer Markdown formátumban adja vissza:`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Ha a visszaadott válasz alatt bejelöli a **vizsgálat** lehetőséget, további válaszok jelenhetnek meg a kérdésben, de nem ugyanazzal a magas szintű megbízhatósággal. 

    Ne adja meg az alternatív megfogalmazás minden lehetséges kombinációját. A QnA Maker [aktív tanulásának](../how-to/improve-knowledge-base.md)bekapcsolása megkeresi azokat az alternatív megfogalmazásokat, amelyek segítenek a Tudásbázisban a felhasználók igényeinek kielégítésében.

1. A teszt ablak bezárásához kattintson ismét a **test (teszt** ) elemre.

## <a name="add-metadata-to-filter-the-answers"></a>Metaadatok hozzáadása a válaszok szűréséhez

Ha metaadatokat ad hozzá egy kérdéshez és a válaszhoz, akkor az ügyfélalkalmazás szűrt válaszokat kér. Ezt a szűrőt az [első és a második rangsor](../concepts/knowledge-base.md#ranker-process) alkalmazása előtt alkalmazza a rendszer.

1. Adja hozzá a második kérdést és választ a metaadatok nélkül a rövid útmutató [első táblájából](#qna-table), majd folytassa a következő lépésekkel. 

1. Válassza a **megtekintési beállítások**, majd a **metaadatok megjelenítése**lehetőséget. 

1. Az imént hozzáadott kérdés és válasz beállításnál válassza a **metaadatok hozzáadása**, majd a `service` `service:search`és a `search`értékének hozzáadása elemet.

1. Adjon hozzá egy másik metaadat-címkét `link_in_answer` `false`, `link_in_answer:false`amelynek a neve és értéke.

1. Keresse meg az első választ a táblában `How large a knowledge base can I create?`. 
1. Metaadat-párok hozzáadása ugyanahhoz a két metaadat-címkéhez:

    `link_in_answer`:`true`<br>
    `server`: `qna_maker`

    Most már két kérdés van ugyanazzal a metaadatokkal, amelyek különböző értékeket tartalmaznak. 

1. A Tudásbázis újratanításához válassza a **Mentés és a tanítás** lehetőséget. 

1. Válassza a **Közzététel** lehetőséget a felső menüben a közzétételi lapra való ugráshoz. 
1. A **Közzététel** gombra kattintva közzéteheti az aktuális tudásbázist egy lekérdezhető végponton. 
1. Miután közzétette a tudásbázist, válassza a **curl (fürt** ) fület, és tekintse meg a Tudásbázisból a válasz létrehozásához használt cURL-parancsot.
1. Másolja a parancsot egy jegyzetfüzetbe vagy más szerkeszthető környezetbe, hogy a parancs szerkeszthető legyen. Szerkessze a saját erőforrás nevét, a Tudásbázis AZONOSÍTÓját és a végpont kulcsát:

    |Csere|
    |--|
    |`your-resource-name`|
    |`your-knowledge-base-id`|
    |`your-endpoint-key`|

    ```curl
    curl -X POST https://your-resource-name.azurewebsites.net/qnamaker/knowledgebases/your-knowledge-base-id/generateAnswer -H "Authorization: EndpointKey your-endpoint-key" -H "Content-type: application/json" -d "{'top':30, 'question':'size','strictFilters': [{'name':'service','value':'qna_maker'}]}"
    ```

    Figyelje meg, hogy a kérdés csak egyetlen szó `size`, amely a kérdést és a válaszadási készletet is visszaadja. A `strictFilters` tömb arra utasítja a választ, hogy csak a `qna_maker` válaszokat csökkentse. 

    [!INCLUDE [Tip for debug property to JSON request](../includes/tip-debug-json.md)]

1. A válasz csak azt a választ tartalmazza, amely megfelel a szűrési feltételeknek. 

    A következő cURL-válasz formázva lett az olvashatóság érdekében:

    ```JSON
    {
        "answers": [
            {
                "questions": [
                    "How large a knowledge base can I create?",
                    "What is the max size of a knowledge base?",
                    "How many GB of data can a knowledge base hold?"
                ],
                "answer": "The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment)for more details.",
                "score": 68.76,
                "id": 3,
                "source": "https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting",
                "metadata": [
                    {
                        "name": "link_in_answer",
                        "value": "true"
                    },
                    {
                        "name": "service",
                        "value": "qna_maker"
                    }
                ],
                "context": {
                    "isContextOnly": false,
                    "prompts": []
                }
            }
        ],
        "debugInfo": null
    }
    ```

    Ha van olyan kérdés-és Levelesláda, amely nem felelt meg a keresési kifejezésnek, de megfelel a szűrőnek, akkor a rendszer nem adja vissza. Ehelyett a rendszer az általános `No good match found in KB.` választ adja vissza.

    Ügyeljen rá, hogy a metaadatok neve és értéke párokat a szükséges korlátok között tartsa. 

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha Cognitive Services-előfizetést szeretne törölni, törölheti az erőforrást vagy az erőforráscsoportot. Az erőforráscsoport törlésével a hozzá társított egyéb erőforrások is törlődnek.

* [Portál](../../cognitive-services-apis-create-account.md#clean-up-resources)
* [Azure CLI](../../cognitive-services-apis-create-account-cli.md#clean-up-resources)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Get-válasz a Poster-vel](get-answer-from-kb-using-postman.md)