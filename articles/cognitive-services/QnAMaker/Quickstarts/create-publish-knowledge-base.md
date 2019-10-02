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
ms.date: 10/01/2019
ms.author: diberry
ms.openlocfilehash: cafc1e2f3f195301a6c0f9485ebaa10111b08c7d
ms.sourcegitcommit: 4f3f502447ca8ea9b932b8b7402ce557f21ebe5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/02/2019
ms.locfileid: "71803050"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Gyors útmutató: A QnA Maker-tudásbázis létrehozása, betanítása és közzététele

Létrehozhat egy QnA Maker-tudásbázist a saját tartalmak, például gyakori kérdések és termékkézikönyvek alapján. Ebből a cikkből megtudhatja, hogyan hozhat létre egy QnA Maker tudásbázist egy egyszerű gyakori kérdések weblapján, hogy válaszoljon a BitLocker-kulcsok helyreállításával kapcsolatos kérdésekre.

[!INCLUDE [Custom subdomains notice](../../../../includes/cognitive-services-custom-subdomains-note.md)]

## <a name="prerequisite"></a>Előfeltétel

> [!div class="checklist"]
> * Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-qna-maker-knowledge-base"></a>QnA Maker-tudásbázis létrehozása

1. Jelentkezzen be az [QnAMaker.ai](https://QnAMaker.ai) -portálra az Azure-beli hitelesítő adataival.

1. A QnA Maker portálon válassza a **Tudásbázis létrehozása**elemet.

   ![Képernyőkép a QnA Maker-portálról](../media/qna-maker-create-kb.png)

1. A **Create** (Létrehozás) oldalon az 1. lépésben válassza a **Create a QnA service** (QnA Maker-szolgáltatás létrehozása) lehetőséget. A rendszer átirányítja az [Azure Portalra](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), ahol be kell állítania az előfizetésében egy QnA Maker-szolgáltatást. Ha az Azure Portalon időtúllépés fordul elő, kattintson a webhelyen a **Try Again** (Újrapróbálkozás) elemre. Miután csatlakozott, megjelenik az Azure irányítópultja.

1. Miután az Azure-ban sikeresen létrehozott egy új QnA Maker-szolgáltatást, lépjen vissza a qnamaker.ai/create webhelyre. Válassza ki a QnA Maker szolgáltatást a 2. lépésben legördülő listában. Ha új QnA Maker szolgáltatást hozott létre, frissítse a lapot.

   ![Képernyőkép a QnA Maker szolgáltatás tudásbázisának kiválasztásáról](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. A 3. lépésben nevezze el az Ön tudásbázisát a **minta QNA kb**-ra.

1. Ha tartalmat szeretne hozzáadni a tudásbázishoz, válasszon három adatforrást. A 4. lépésben a **Populate your KB** (Tudásbázis feltöltése) területen adja meg a [BitLocker-helyreállítás gyakori kérdéseinek](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) URL-címét az **URL** mezőben.

   ![Az adatforrások hozzáadásának képernyőképe](../media/qnamaker-quickstart-kb/add-datasources.png)

1. A 5. lépésben válassza a **Create your KB** (Tudásbázis létrehozása) lehetőséget.

1. Míg a QnA Maker létrehozza a tudásbázist, megjelenik egy előugró ablak. A kinyerési folyamatnak néhány percbe telik, hogy beolvassa a HTML-oldalt és azonosítsa a kérdéseket és válaszokat.

1. Miután QnA Maker sikeresen létrehozta a tudásbázist, megnyílik a **Tudásbázis** lap. Ezen a lapon szerkesztheti a Tudásbázis tartalmát.

## <a name="edit-the-knowledge-base"></a>A Tudásbázis szerkesztése

1. A QnA Maker portál **Szerkesztés** szakaszában válassza a **QnA-pár hozzáadása** lehetőséget, hogy új sort adjon hozzá a tudásbázishoz. A **Question** (Kérdés) területen adja meg: **Üdv.** Az **Answer** (Válasz) területen adja meg: **Üdvözlöm. Kérdezze meg a BitLocker kérdéseit.**

    ![Képernyőkép a QnA Maker-portálról](../media/qnamaker-quickstart-kb/add-qna-pair.png)

1. Kattintson a jobb felső sarokban a **Save and train** (Mentés és betanítás) elemre, amely menti a módosításokat és betanítja a QnA Maker-modellt. A rendszer a módosításokat csak akkor őrzi meg, ha menti őket.

## <a name="test-the-knowledge-base"></a>A Tudásbázis tesztelése

1. A QnA Maker portál jobb felső részén válassza a **tesztelés** lehetőséget, hogy a módosítások érvénybe léptetése megtörténjen. Írja `hi there` be a mezőbe, majd kattintson az ENTER gombra. Meg kell jelennie a létrehozott válasznak.

1. Válassza az **Inspect** (Vizsgálat) lehetőséget a válasz részletesebb vizsgálatához. A teszt ablak a közzétett Tudásbázisban végzett módosítások tesztelésére szolgál.

    ![A teszt panel képernyőképe](../media/qnamaker-quickstart-kb/inspect.png)

1. Válassza ismét a **Test** (Tesztelés) gombot a **Test** (Tesztelés) előugró ablak bezárásához.

## <a name="publish-the-knowledge-base"></a>A tudásbázis közzététele

Ha közzétesz egy tudásbázist, a Tudásbázisban a kérdés és a válasz tartalma a tesztelési indexből egy éles indexbe kerül az Azure Search-ben.

![Képernyőkép a Tudásbázis tartalmának áthelyezéséről](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. A QnA Maker portálon, a **Szerkesztés**elem melletti menüben válassza a **Közzététel**lehetőséget. Ezután a megerősítéshez válassza az oldalon a **Publish** (Közzététel) lehetőséget.

1. Ezzel a QnA Maker-szolgáltatás sikeresen közzé lett téve. Mostantól használhatja a végpontot az alkalmazásában vagy robotkódjában.

    ![A sikeres közzététel képernyőképe](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="create-a-bot"></a>Robot létrehozása

A közzététel után létrehozhat egy robotot a **közzétételi lapról:** 

* Több robotot is létrehozhat, amelyek mindegyike azonos tudásbázisra mutat a különböző régiókban vagy díjszabási tervekben az egyes robotok számára. 
* Ha csak egy robotot szeretne használni a Tudásbázisban, használja a Azure Portal hivatkozásán látható **összes robot megtekintését** az aktuális robotok listájának megtekintéséhez. 

Ha módosítja a tudásbázist, és újból közzéteszi, nem kell további műveleteket végrehajtania a robottal. Már konfigurálva van a Tudásbázisban való együttműködésre, és a Tudásbázis minden jövőbeli módosításával működik. Minden alkalommal, amikor közzétesz egy tudásbázist, az ahhoz kapcsolódó összes robot automatikusan frissül.

1. A QnA Maker portál **Közzététel** lapján válassza a **robot létrehozása**lehetőséget. Ez a gomb csak azután jelenik meg, hogy közzétette a tudásbázist.

    ![A robot létrehozásának képernyőképe](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Megnyílik egy új böngésző lap a Azure Portal számára a Azure Bot Service létrehozási oldalával. Konfigurálja az Azure bot Service-t. További információ ezekről a konfigurációs beállításokról: [QnA-robot létrehozása Azure bot Service v4](../tutorials/create-qna-bot.md)-mel.
    
    * A robot létrehozásakor ne módosítsa a Azure Portal következő beállításait. A meglévő Tudásbázisban előre ki vannak töltve: 
        * QnA-hitelesítési kulcs
        * App Service-csomag és-hely
        * Azure Storage
    * A robot és a QnA Maker megoszthatják a Web App Service-csomagot, de nem oszthatják meg a webalkalmazást. Ez azt jelenti, hogy az **alkalmazás nevének** eltérőnek kell lennie az QnA Maker szolgáltatás létrehozásakor használt alkalmazás nevével. 


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása](../How-To/create-knowledge-base.md)
