---
title: 'Gyors útmutató: kérdések és válaszok hozzáadása QnA Maker portálon'
description: Ez a rövid útmutató bemutatja, hogyan adhat hozzá a kérdésekhez és a válaszokhoz a metaadatokat, hogy a felhasználók megtalálják a megfelelő választ a kérdésére.
ms.topic: quickstart
ms.date: 05/26/2020
ms.openlocfilehash: a832d9f421d3a1851401c286f129e29d0196ec99
ms.sourcegitcommit: eb6bef1274b9e6390c7a77ff69bf6a3b94e827fc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/05/2020
ms.locfileid: "84331389"
---
# <a name="quickstart-add-questions-and-answer-with-qna-maker-portal"></a>Gyors útmutató: kérdések és válaszok felvétele QnA Maker portálra

Miután létrehozta a tudásbázist, adja hozzá a kérdés-és válasz (QnA) párokat metaadatokkal a válasz szűréséhez. Az alábbi táblázatban szereplő kérdések az Azure szolgáltatási korlátaival kapcsolatosak, de mindegyiknek egy másik Azure Search szolgáltatással kell foglalkoznia.

<a name="qna-table"></a>

|Pár|Kérdések|Válasz|Metaadatok|
|--|--|--|--|
|#1|`How large a knowledge base can I create?`<br><br>`What is the max size of a knowledge base?`<br><br>`How many GB of data can a knowledge base hold?` |`The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`|`service=qna_maker`<br>`link_in_answer=true`|
|#2|`How many knowledge bases can I have for my QnA Maker service?`<br><br>`I selected a Azure Cognitive Search tier that holds 15 knowledge bases, but I can only create 14 - what is going on?`<br><br>`What is the connection between the number of knowledge bases in my QnA Maker service and the Azure Cognitive Search service size?` |`Each knowledge base uses 1 index, and all the knowledge bases share a test index. You can have N-1 knowledge bases where N is the number of indexes your Azure Cognitive Search tier supports.`|`service=search`<br>`link_in_answer=false`|

Miután hozzáadta a metaadatokat egy QnA-párosításhoz, az ügyfélalkalmazás a következőket teheti:

* Olyan válaszokat kér, amelyek csak bizonyos metaadatokat egyeznek meg.
* Fogadja az összes választ, de a válaszokat az egyes válaszok metaadataitól függően feldolgozhatja.


## <a name="prerequisites"></a>Előfeltételek

* Az [előző](./create-publish-knowledge-base.md) rövid útmutató befejezése

## <a name="sign-in-to-the-qna-maker-portal"></a>Bejelentkezés a QnA Maker portálra

1. Jelentkezzen be a [QnA Maker portálra](https://www.qnamaker.ai).

1. Válassza ki a meglévő tudásbázist az [előző](../how-to/create-knowledge-base.md)rövid útmutatóból.

## <a name="add-additional-alternatively-phrased-questions"></a>További megfogalmazott kérdések hozzáadása

Az aktuális Tudásbázis rendelkezik a QnA Maker hibaelhárítási QnA párokkal. Ezek a párok akkor jöttek létre, amikor az URL-címet hozzáadták a tudásbázishoz a létrehozási folyamat során.

Az URL-cím importálása után csak egy kérdés lett létrehozva. Ebben az eljárásban további kérdéseket is megadhat.

1. A **Szerkesztés** lapon használja a kérdés és válasz párok fölötti keresési szövegmezőt a kérdés megtalálásához. `How large a knowledge base can I create?`

1. A **kérdés** oszlopban válassza az **+ alternatív megfogalmazás hozzáadása** lehetőséget, majd adja hozzá a következő táblázatban megadott összes új megfogalmazást.

    |Alternatív megfogalmazás|
    |--|
    |`What is the max size of a knowledge base?`|
    |`How many GB of data can a knowledge base hold?`|

1. A Tudásbázis újratanításához válassza a **Mentés és a tanítás** lehetőséget.

1. Válassza a **teszt**lehetőséget, majd adjon meg egy olyan kérdést, amely az új alternatív megfogalmazások egyikéhez közeledik, de nem pontosan ugyanaz a megfogalmazás:

    `What GB size can a knowledge base be?`

    A megfelelő választ a rendszer Markdown formátumban adja vissza:

    `The size of the knowledge base depends on the SKU of Azure search you choose when creating the QnA Maker service. Read [here](https://docs.microsoft.com/azure/cognitive-services/qnamaker/tutorials/choosing-capacity-qnamaker-deployment) for more details.`

    Ha a visszaadott válasz alatt bejelöli a **vizsgálat** lehetőséget, további válaszok jelenhetnek meg a kérdésben, de nem ugyanazzal a magas szintű megbízhatósággal.

    Ne adja meg az alternatív megfogalmazás minden lehetséges kombinációját. Ha bekapcsolja QnA Maker [aktív tanulását](../how-to/improve-knowledge-base.md), ez megkeresi azokat az alternatív megfogalmazásokat, amelyek segítenek a Tudásbázisban a felhasználók igényeinek kielégítésében.

1. A teszt ablak bezárásához kattintson ismét a **test (teszt** ) elemre.

## <a name="add-metadata-to-filter-the-answers"></a>Metaadatok hozzáadása a válaszok szűréséhez

A metaadatok kérdés-válasz párokhoz való hozzáadásával az ügyfélalkalmazás szűrt válaszokat kérhet. Ezt a szűrőt az [első és a második rangsor](../concepts/query-knowledge-base.md#ranker-process) alkalmazása előtt alkalmazza a rendszer.

1. Adja hozzá a második kérdést és választ a metaadatok nélkül a rövid útmutató [első táblájából](#qna-table), majd folytassa a következő lépésekkel.

1. Válassza a **megtekintési beállítások**, majd a **metaadatok megjelenítése**lehetőséget.

1. Az imént hozzáadott QnA pár esetében válassza a **metaadatok hozzáadása**elemet, majd adja hozzá a nevét `service` és a értékét `search` . Így néz ki: `service:search` .

1. Adjon hozzá egy másik metaadat-címkét, amelynek a neve `link_in_answer` és értéke `false` . Így néz ki: `link_in_answer:false` .

1. Keresse meg az első választ a táblában `How large a knowledge base can I create?` .

1. Metaadat-párok hozzáadása ugyanahhoz a két metaadat-címkéhez:

    `link_in_answer` : `true`<br>
    `service`: `qna_maker`

    Most már két kérdés van ugyanazzal a metaadatokkal, amelyek különböző értékeket tartalmaznak.

1. A Tudásbázis újratanításához válassza a **Mentés és a tanítás** lehetőséget.

1. Válassza a **Közzététel** lehetőséget a felső menüben a közzétételi lapra való ugráshoz.
1. A **Közzététel** gombra kattintva közzéteheti az aktuális tudásbázist a végponton.
1. A Tudásbázis közzétételét követően folytassa a következő rövid útmutatóval, amelyből megtudhatja, hogyan hozhat ki választ a Tudásbázisból.

## <a name="what-did-you-accomplish"></a>Mit ért el?

A tudásbázist úgy szerkesztette, hogy több kérdést támogasson, és a név/érték párokat is támogassa a keresés során a legjobb válasz vagy utófeldolgozó keresésekor a válasz vagy válaszok visszaadása után.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a következő rövid útmutatót, törölje a QnA Maker és a bot Framework erőforrásait a Azure Portalban.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Válasz kérése Postman vagy cURL használatával](get-answer-from-knowledge-base-using-url-tool.md)
