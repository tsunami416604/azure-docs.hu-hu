---
title: Tudásbázis létrehozása, betanítása és közzététele – QnA Maker
titleSuffix: Azure Cognitive Services
description: Létrehozhat egy QnA Maker-tudásbázist a saját tartalmak, például gyakori kérdések és termékkézikönyvek alapján. A jelen példában szereplő QnA Maker-tudásbázist egy egyszerű, gyakori kérdéseket tartalmazó weblap alapján hozzuk létre, a BitLocker-kulcsok helyreállításával kapcsolatos kérdések megválaszolásához.
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: qna-maker
ms.topic: quickstart
ms.date: 12/18/2018
ms.author: diberry
ms.openlocfilehash: 4b4f208524f18b98d44dc3d34e05359445fb2f17
ms.sourcegitcommit: 4eeeb520acf8b2419bcc73d8fcc81a075b81663a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/19/2018
ms.locfileid: "53598310"
---
# <a name="create-train-and-publish-your-qna-maker-knowledge-base"></a>A QnA Maker-tudásbázis létrehozása, betanítása és közzététele

Létrehozhat egy QnA Maker-tudásbázist a saját tartalmak, például gyakori kérdések és termékkézikönyvek alapján. A jelen példában szereplő QnA Maker-tudásbázist egy egyszerű, gyakori kérdéseket tartalmazó weblap alapján hozzuk létre, a BitLocker-kulcsok helyreállításával kapcsolatos kérdések megválaszolásához.

## <a name="prerequisite"></a>Előfeltétel

> [!div class="checklist"]
> * Ha nem rendelkezik Azure-előfizetéssel, mindössze néhány perc alatt létrehozhat egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) a virtuális gép létrehozásának megkezdése előtt.

## <a name="create-a-qna-maker-knowledge-base"></a>QnA Maker-tudásbázis létrehozása

1. Jelentkezzen be a QnAMaker.ai webhelyen az Azure-ban használt hitelesítő adataival.

2. A QnA Maker webhelyen válassza a **Create a knowledge base** (Tudásbázis létrehozása) lehetőséget.

   ![Új tudásbázis létrehozása](../media/qna-maker-create-kb.png)

3. A **Create** (Létrehozás) oldalon az 1. lépésben válassza a **Create a QnA service** (QnA Maker-szolgáltatás létrehozása) lehetőséget. A rendszer átirányítja az [Azure Portalra](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), ahol be kell állítania az előfizetésében egy QnA Maker-szolgáltatást. Ha az Azure Portalon időtúllépés fordul elő, kattintson a webhelyen a **Try Again** (Újrapróbálkozás) elemre. Miután csatlakozott, megjelenik az Azure irányítópultja.

4. Miután az Azure-ban sikeresen létrehozott egy új QnA Maker-szolgáltatást, lépjen vissza a qnamaker.ai/create webhelyre. Válassza ki a QnA-szolgáltatását a legördülő listából a 2. lépésben. Ha egy új QnA-szolgáltatást hozott létre, ne felejtse el frissíteni az oldalt.

   ![QnA-szolgáltatás tudásbázisának kiválasztása](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

5. A 3. lépésben adja a tudásbázisnak a **My Sample QnA KB** nevet.

6. A tudásbázis tartalmakkal való feltöltéséhez válasszon ki háromféle adatforrást. A 4. lépésben a **Populate your KB** (Tudásbázis feltöltése) területen adja meg a [BitLocker-helyreállítás gyakori kérdéseinek](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview-and-requirements-faq) URL-címét az **URL** mezőben.

   ![QnA-szolgáltatás tudásbázisának kiválasztása](../media/qnamaker-quickstart-kb/add-datasources.png)

7. A 5. lépésben válassza a **Create your KB** (Tudásbázis létrehozása) lehetőséget.

8. Amíg a rendszer létrehozza a tudásbázist, megjelenik egy előugró ablak. A kinyerési folyamatnak néhány percbe telik, hogy beolvassa a HTML-oldalt és azonosítsa a kérdéseket és válaszokat.

9. A tudásbázis sikeres létrehozása után megnyílik a **Knowledge base** (Tudásbázis) oldal. Itt szerkeszthető a tudásbázis tartalma.

10. Kattintson a jobb felső sarokban az **Add QnA pair** (Kérdés-válasz pár hozzáadása) lehetőségre, amely hozzáad egy új sort a tudásbázis **Editorial** (Szerkesztői) szakaszához. A **Question** (Kérdés) területen adja meg: **Üdv.** Az **Answer** (Válasz) területen adja meg: **Üdvözlöm. Tegyen fel kérdéseket a BitLockerről.**

   ![Kérdés-válasz pár hozzáadása](../media/qnamaker-quickstart-kb/add-qna-pair.png)

11. Kattintson a jobb felső sarokban a **Save and train** (Mentés és betanítás) elemre, amely menti a módosításokat és betanítja a QnA Maker-modellt. A rendszer a módosításokat csak akkor őrzi meg, ha menti őket.

12. Kattintson a jobb felső sarokban a **Test** (Tesztelés) elemre, amely teszteli, hogy az elvégzett módosítások életbe léptek-e. Adjon meg `hi there` a box és a select adjon meg. Meg kell jelennie a létrehozott válasznak.

13. Válassza az **Inspect** (Vizsgálat) lehetőséget a válasz részletesebb vizsgálatához. A tesztablakban tesztelheti a tudásbázis módosításait, mielőtt közzétenné azokat.

   ![Teszt panel](../media/qnamaker-quickstart-kb/inspect-panel.png)

14. Válassza ismét a **Test** (Tesztelés) gombot a **Test** (Tesztelés) előugró ablak bezárásához.

15. A menüben az **Edit** (Szerkesztés) lehetőség mellett válassza a **Publish** (Közzététel) elemet. Ezután a megerősítéshez válassza az oldalon a **Publish** (Közzététel) lehetőséget.

16. Ezzel a QnA Maker-szolgáltatás sikeresen közzé lett téve. Mostantól használhatja a végpontot az alkalmazásában vagy robotkódjában.

   ![Közzététel](../media/qnamaker-quickstart-kb/publish-sucess.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Tudásbázis létrehozása](../How-To/create-knowledge-base.md)
