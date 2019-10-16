---
title: 'Gyors útmutató: Tudásbázis létrehozása, betanítása és közzététele – QnA Maker'
titleSuffix: Azure Cognitive Services
description: Létrehozhat egy QnA Maker-tudásbázist a saját tartalmak, például gyakori kérdések és termékkézikönyvek alapján. Ebben a példában a QnA Maker tudásbázist egy egyszerű gyakori kérdések weboldaláról hozza létre a BitLocker-kulcs helyreállításával kapcsolatos kérdések megválaszolásához.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 10/14/2019
ms.author: diberry
ms.openlocfilehash: 5d807f185a8cdb181093d37dac35ee2e467fdba9
ms.sourcegitcommit: 1d0b37e2e32aad35cc012ba36200389e65b75c21
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/15/2019
ms.locfileid: "72328088"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Rövid útmutató: a QnA Maker Tudásbázis létrehozása, betanítása és közzététele

Létrehozhat egy QnA Maker-tudásbázist a saját tartalmak, például gyakori kérdések és termékkézikönyvek alapján. Ebből a cikkből megtudhatja, hogyan hozhat létre egy QnA Maker tudásbázist egy egyszerű gyakori kérdések weblapján, hogy válaszoljon a BitLocker-kulcsok helyreállításával kapcsolatos kérdésekre.

Egy Chit-Chat-személyiséggel is elvégezheti a tudását, így jobban megtarthatja a felhasználókat.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisite"></a>Előfeltétel

> [!div class="checklist"]
> * Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-new-qna-maker-knowledge-base"></a>Új QnA Maker Tudásbázis létrehozása

1. Jelentkezzen be az [QnAMaker.ai](https://QnAMaker.ai) -portálra az Azure-beli hitelesítő adataival.

1. A QnA Maker portálon válassza a **Tudásbázis létrehozása**elemet.

1. A **Létrehozás** lapon válassza a **QnA szolgáltatás létrehozása**lehetőséget. A rendszer átirányítja az [Azure Portalra](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), ahol be kell állítania az előfizetésében egy QnA Maker-szolgáltatást. 

1. A QnA Maker portálon válassza ki a QnA Maker szolgáltatást a legördülő listából. Ha új QnA Maker szolgáltatást hozott létre, frissítse a lapot.

   ![Képernyőkép a QnA Maker szolgáltatás tudásbázisának kiválasztásáról](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. Nevezze el a Tudásbázisban a **minta QNA kb-ot**.

1. Mintaként szolgáló Word-dokumentum hozzáadása URL-címként: 

    `https://docs.microsoft.com/en-us/azure/cognitive-services/qnamaker/troubleshooting`

1. Válassza a(z) `+ Add URL` lehetőséget.

1. Vegyen fel  **_professzionális_ Chit-csevegést** a kb-ra. 

1. Válassza **a kb létrehozása**lehetőséget.

    A kinyerési folyamat eltarthat néhány percig, hogy beolvassa a dokumentumot, és azonosítsa a kérdéseket és a válaszokat.

    Miután QnA Maker sikeresen létrehozta a tudásbázist, megnyílik a **Tudásbázis** lap. Ezen a lapon szerkesztheti a Tudásbázis tartalmát.

## <a name="add-a-new-question-and-answer-set"></a>Új kérdés és válaszfájl hozzáadása

1. A QnA Maker portál **Szerkesztés** lapján válassza a **QnA pár hozzáadása**elemet.
1. Adja hozzá a következő kérdést: 

    `How many Azure services are used by a knowledge base?`

1. Adja hozzá a _Markdown_formázott választ:

    ` * Azure QnA Maker service\n* Azure Search\n* Azure web app\n* Azure app plan`

    ![ Adja hozzá a kérdést szövegként és a Markdown formázott választ.](../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png)

    A Markdown szimbólum (`*`) a felsorolásjeles pontokhoz használatos. A `\n` egy új sorhoz használatos.  

    A **Szerkesztés** oldalon látható a Markdown. Amikor később a **tesztelési** panelt használja, látni fogja, hogy a Markdown megfelelően jelenik-e meg. 

## <a name="save-and-train"></a>Mentés és betanítás

Kattintson a jobb felső sarokban a **Save and train** (Mentés és betanítás) elemre, amely menti a módosításokat és betanítja a QnA Maker-modellt. A rendszer a módosításokat csak akkor őrzi meg, ha menti őket.

## <a name="test-the-knowledge-base"></a>A Tudásbázis tesztelése

1. A QnA Maker portál jobb felső részén válassza a **tesztelés** lehetőséget, hogy a módosítások érvénybe léptetése megtörténjen. 
1. Írjon be egy példát egy felhasználói lekérdezésre a szövegmezőben. 

    `How many Azure services are used by a knowledge base?`  

    ![ Írjon be egy példát egy felhasználói lekérdezésre a szövegmezőben. ](../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png)

1. Válassza az **Inspect** (Vizsgálat) lehetőséget a válasz részletesebb vizsgálatához. A test (teszt) ablak segítségével tesztelheti a Tudásbázis módosításait, mielőtt közzéteszi a tudásbázist.

1. Kattintson ismét a **test** **(teszt)** elemre a teszt panel bezárásához.

## <a name="publish-the-knowledge-base"></a>A tudásbázis közzététele

A Tudásbázis közzétételekor a Tudásbázis tartalma a `test` indexből egy `prod` indexbe kerül az Azure Search-ben.

![Képernyőkép a Tudásbázis tartalmának áthelyezéséről](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. A QnA Maker portálon válassza a **Közzététel**lehetőséget. Ezután a megerősítéshez válassza az oldalon a **Publish** (Közzététel) lehetőséget.

    Ezzel a QnA Maker-szolgáltatás sikeresen közzé lett téve. Mostantól használhatja a végpontot az alkalmazásában vagy robotkódjában.

    ![A sikeres közzététel képernyőképe](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Robot létrehozása

A közzététel után létrehozhat egy robotot a **közzétételi lapról:** 

* Több robotot is létrehozhat, amelyek mindegyike azonos tudásbázisra mutat a különböző régiókban vagy díjszabási tervekben az egyes robotok számára. 
* Ha csak egy robotot szeretne használni a Tudásbázisban, használja a Azure Portal hivatkozásán látható **összes robot megtekintését** az aktuális robotok listájának megtekintéséhez. 

Ha módosítja a tudásbázist, és újból közzéteszi, nem kell további műveleteket végrehajtania a robottal. Már konfigurálva van a Tudásbázisban való együttműködésre, és a Tudásbázis minden jövőbeli módosításával működik. Minden alkalommal, amikor közzétesz egy tudásbázist, az ahhoz kapcsolódó összes robot automatikusan frissül.

1. A QnA Maker portál **Közzététel** lapján válassza a **robot létrehozása**lehetőséget. Ez a gomb csak azután jelenik meg, hogy közzétette a tudásbázist.

    ![A robot létrehozásának képernyőképe](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Megnyílik egy új böngésző lap a Azure Portal számára a Azure Bot Service létrehozási oldalával. Konfigurálja az Azure bot Service-t. 
    
    * A robot létrehozásakor ne módosítsa a Azure Portal következő beállításait. A meglévő Tudásbázisban előre ki vannak töltve: 
        * QnA-hitelesítési kulcs
        * App Service-csomag és-hely
    * A robot és a QnA Maker megoszthatják a Web App Service-csomagot, de nem oszthatják meg a webalkalmazást. Ez azt jelenti, hogy a robot **alkalmazásának neve** nem lehet a QnA Maker szolgáltatás alkalmazásának neve. 

1. A robot létrehozása után nyissa meg a **bot Service** -erőforrást. 
1. A **bot Management**alatt válassza **a tesztelés webes csevegésben**lehetőséget.
1. Írja be a következőt a csevegési **üzenetbe**:

    `Azure services?`

    A csevegési robot a Tudásbázisban válaszol. 

    ![Adjon meg egy felhasználói lekérdezést a teszt webes csevegésben.](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Törölje a QnA Maker és a bot Framework erőforrásait a Azure Portalban. 

## <a name="next-steps"></a>Következő lépések

További információ:

* [Markdown-formátum a válaszokban](../concepts/data-sources-supported.md)
* [A Markdown tesztelése](../concepts/data-sources-supported.md#testing-your-markdown)
* QnA Maker [adatforrások](../Concepts/data-sources-supported.md). 
* A [robot erőforrás-konfigurációs beállításai](../tutorials/create-qna-bot.md).

> [!div class="nextstepaction"]
> [Metaadatokkal kapcsolatos kérdések hozzáadása](add-question-metadata-portal.md)

