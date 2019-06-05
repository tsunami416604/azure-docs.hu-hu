---
title: Létrehozását, betanítását és közzététele a Tudásbázis – QnA Maker
titleSuffix: Azure Cognitive Services
description: Létrehozhat egy QnA Maker-tudásbázist a saját tartalmak, például gyakori kérdések és termékkézikönyvek alapján. A QnA Maker Tudásbázis ebben a példában egy egyszerű – gyakori kérdések weblap kérdésre, a BitLocker-helyreállítás jön létre.
author: diberry
manager: nitinme
services: cognitive-services
ms.service: cognitive-services
ms.subservice: qna-maker
ms.topic: quickstart
ms.date: 05/10/2019
ms.author: diberry
ms.openlocfilehash: 609d71898d8db027f1cfee9e1b73039367ec94f4
ms.sourcegitcommit: 18a0d58358ec860c87961a45d10403079113164d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/05/2019
ms.locfileid: "66693372"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>A QnA Maker-tudásbázis létrehozása, betanítása és közzététele

Létrehozhat egy QnA Maker-tudásbázist a saját tartalmak, például gyakori kérdések és termékkézikönyvek alapján. Ez a cikk tartalmaznak egy kérdésre, a BitLocker-helyreállítás egy egyszerű – gyakori kérdések weblapon, QnA Maker Tudásbázis hoz létre.

## <a name="prerequisite"></a>Előfeltétel

> [!div class="checklist"]
> * Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-qna-maker-knowledge-base"></a>QnA Maker-tudásbázis létrehozása

1. Jelentkezzen be a [QnAMaker.ai](https://QnAMaker.ai) portál az Azure hitelesítő adataival.

1. Jelölje be a QnA Maker portal **Tudásbázis létrehozása**.

   ![Képernyőfelvétel: a QnA Maker portal](../media/qna-maker-create-kb.png)

1. A **Create** (Létrehozás) oldalon az 1. lépésben válassza a **Create a QnA service** (QnA Maker-szolgáltatás létrehozása) lehetőséget. A rendszer átirányítja az [Azure Portalra](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), ahol be kell állítania az előfizetésében egy QnA Maker-szolgáltatást. Ha az Azure Portalon időtúllépés fordul elő, kattintson a webhelyen a **Try Again** (Újrapróbálkozás) elemre. Miután csatlakozott, megjelenik az Azure irányítópultja.

1. Miután az Azure-ban sikeresen létrehozott egy új QnA Maker-szolgáltatást, lépjen vissza a qnamaker.ai/create webhelyre. Válassza ki a QnA Maker szolgáltatást a 2. lépésben a legördülő listából. Ha létrehozott egy új QnA Maker szolgáltatást, mindenképpen frissítse az oldalt.

   ![Képernyőkép a QnA Maker szolgáltatás Tudásbázis kiválasztása](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. A 3. lépésben, nevezze el a Tudásbázis **saját minta QnA KB**.

1. Tartalom hozzáadása a Tudásbázis, válassza a három típusú adatforrásokat. A 4. lépésben a **Populate your KB** (Tudásbázis feltöltése) területen adja meg a [BitLocker-helyreállítás gyakori kérdéseinek](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) URL-címét az **URL** mezőben.

   ![Képernyőkép az adatforrások hozzáadása](../media/qnamaker-quickstart-kb/add-datasources.png)

1. A 5. lépésben válassza a **Create your KB** (Tudásbázis létrehozása) lehetőséget.

1. A QnA Maker a Tudásbázis hoz létre, amíg egy előugró ablak jelenik meg. A kinyerési folyamatnak néhány percbe telik, hogy beolvassa a HTML-oldalt és azonosítsa a kérdéseket és válaszokat.

1. Miután a QnA Maker sikeresen létrehozta a Tudásbázis a **Tudásbázis** lap megnyitásakor. Ezen az oldalon a Tudásbázis tartalmát szerkesztheti.

## <a name="edit-the-knowledge-base"></a>A Tudásbázis szerkesztése

1. A QnA Maker Portal az a **szerkesztése** szakaszban jelölje be **hozzáadása kérdés-válasz párt** hozzáadása egy új sort a Tudásbázis. A **Question** (Kérdés) területen adja meg: **Üdv.** Az **Answer** (Válasz) területen adja meg: **Üdvözlöm. A BitLocker kérdéseket fel.**

    ![Képernyőfelvétel: a QnA Maker portal](../media/qnamaker-quickstart-kb/add-qna-pair.png)

1. Kattintson a jobb felső sarokban a **Save and train** (Mentés és betanítás) elemre, amely menti a módosításokat és betanítja a QnA Maker-modellt. A rendszer a módosításokat csak akkor őrzi meg, ha menti őket.

## <a name="test-the-knowledge-base"></a>A Tudásbázis tesztelése

1. A QnA Maker portálon kattintson a jobb felső sarokban, válassza ki a **tesztelése** tesztelése, hogy a végzett módosítások érvénybe tartott-e. Adjon meg `hi there` a box és a select adjon meg. Meg kell jelennie a létrehozott válasznak.

1. Válassza az **Inspect** (Vizsgálat) lehetőséget a válasz részletesebb vizsgálatához. A Teszt ablak segítségével tesztelheti a módosításokat a Tudásbázis, mielőtt azok van közzétéve.

    ![Teszt panel képernyőképe](../media/qnamaker-quickstart-kb/inspect.png)

1. Válassza ismét a **Test** (Tesztelés) gombot a **Test** (Tesztelés) előugró ablak bezárásához.

## <a name="publish-the-knowledge-base"></a>A tudásbázis közzététele

Tudásbázis közzététele, amikor a Tudásbázis kérdés és válasz tartalma áthelyezi a teszt indexből egy éles indexet az Azure Search szolgáltatásban.

![A Tudásbázis tartalmának áthelyezésével képernyőképe](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. A QnA Maker Portal, a menüben a **szerkesztése**válassza **közzététel**. Ezután a megerősítéshez válassza az oldalon a **Publish** (Közzététel) lehetőséget.

1. Ezzel a QnA Maker-szolgáltatás sikeresen közzé lett téve. Mostantól használhatja a végpontot az alkalmazásában vagy robotkódjában.

    ![Képernyőkép a sikeres közzététel](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="create-a-bot"></a>Hozzon létre egy robot

A közzététel után, a robotprogramok hozhat létre a **közzététel** oldalon: 

* Létrehozhat több robotok gyors, a különböző régiókban azonos Tudásbázisban mutató összes vagy díjszabása az egyes robotokat terveket. 
* Ha azt szeretné, csak egy robot számára a Tudásbázis, használja a **minden a robotok megtekintése az Azure Portalon** hivatkozásra kattintva a jelenlegi robotok listáját. 

Ha módosítja a Tudásbázis és újra közzétettük, nem kell további műveleteket a robottal végrehajtania. A Tudásbázis dolgozhat már konfigurálva van, és működik együtt a Tudásbázis jövőbeli módosításai. Minden alkalommal, amikor közzéteszi a Tudásbázis, az ahhoz kapcsolódó összes robotokat automatikusan frissülnek.

1. A QnA Maker Portal az a **közzététel** lapon jelölje be **létrehozni a bot**. Ez a gomb csak a Tudásbázis közzététele után jelenik meg.

    ![A robot létrehozásának képernyőképe](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Az Azure Portalon, az Azure Bot Service-létrehozási oldalon megnyílik egy böngészőablak. Az Azure bot service konfigurálása. Ezeket a konfigurációs beállításokat a további információkért lásd: [hozzon létre egy QnA robotot az Azure Bot Service v4](../tutorials/create-qna-bot.md).
    
    * Az Azure Portalon a következő beállítások nem változnak, a robot létrehozásakor. Azok a rendszer előre kitölti az a meglévő Tudásbázis: 
        * A QnA hitelesítési kulcs
        * App service-csomagot és helye
        * Azure Storage
    * A robot és a QnA Maker megoszthatja a web app service-csomag, de nem oszthat meg a webalkalmazás. Ez azt jelenti, hogy a **alkalmazásnév** az alkalmazás nevét, a QnA Maker szolgáltatás létrehozásakor használt különbözőnek kell lennie. 


## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása](../How-To/create-knowledge-base.md)
