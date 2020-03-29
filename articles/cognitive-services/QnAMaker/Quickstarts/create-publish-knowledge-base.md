---
title: 'Rövid útmutató: Tudásbázis létrehozása, betanítása és közzététele – QnA Maker'
description: Létrehozhat egy QnA Maker-tudásbázist a saját tartalmak, például gyakori kérdések és termékkézikönyvek alapján. Ez a cikk egy példát tartalmaz a QnA Maker tudásbázis létrehozására egy egyszerű GYIK weboldalról, a QnA Maker kérdéseinek megválaszolásához.
ms.topic: conceptual
ms.date: 02/08/2020
ms.openlocfilehash: b1f80006e2dc72b5b623f4c29c093c734dc1efea
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220577"
---
# <a name="quickstart-create-train-and-publish-your-qna-maker-knowledge-base"></a>Rövid útmutató: A QnA Maker tudásbázisának létrehozása, betanítása és közzététele

Létrehozhat egy QnA Maker-tudásbázist a saját tartalmak, például gyakori kérdések és termékkézikönyvek alapján. Ez a cikk egy példát tartalmaz a QnA Maker tudásbázis létrehozására egy egyszerű GYIK weboldalról, a QnA Maker kérdéseinek megválaszolásához.

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot,](https://azure.microsoft.com/free/?WT.mc_id=A261C142F) mielőtt elkezdené.
> * Az Azure Portalon létrehozott QnA [Maker-erőforrás.](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker) Ne feledje, hogy az Azure Active Directory-azonosító, előfizetés, QnA erőforrás nevét, amikor létrehozta az erőforrást.

## <a name="create-your-first-qna-maker-knowledge-base"></a>Az első QnA Maker tudásbázis létrehozása

1. Jelentkezzen be a [QnAMaker.ai](https://QnAMaker.ai) portálra az Azure-hitelesítő adatokkal.

1. A QnA Maker portálon válassza **a Tudásbázis létrehozása**lehetőséget.

1. A **Létrehozás** lapon ugorja ki **az 1.**

    Ha még nem hozta létre az **erőforrást, válassza a QnA szolgáltatás létrehozása lehetőséget.** A rendszer átirányítja az [Azure Portalra](https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesQnAMaker), ahol be kell állítania az előfizetésében egy QnA Maker-szolgáltatást. Ne feledje, hogy az Azure Active Directory-azonosító, előfizetés, QnA erőforrás nevét, amikor létrehozta az erőforrást.

    Ha végzett az erőforrás létrehozásával az Azure Portalon, térjen vissza a QnA Maker portálra, frissítse a böngészőlapot, és folytassa a **2.**

1. **Lépésben**válassza ki az Active directoryt, az előfizetést, a szolgáltatást (erőforrást) és a szolgáltatásban létrehozott összes tudásbázis nyelvét.

   ![Képernyőkép a QnA Maker szolgáltatás tudásbázisának kiválasztásáról](../media/qnamaker-quickstart-kb/qnaservice-selection.png)

1. **Lépésben**nevezze el tudásbázisát A **Minta QnA KB nevet.**

1. **A 4.**

    |Beállítás|Érték|
    |--|--|
    |**Többfordulatos kinyerés engedélyezése URL-címekből, .pdf vagy .docx fájlokból.**|Bejelölve|
    |**Alapértelmezett válaszszöveg**| `Quickstart - default answer not found.`|
    |**+ URL hozzáadása**|`https://docs.microsoft.com/azure/cognitive-services/qnamaker/troubleshooting`|
    |**Chit-chat**|Válassza a **Professional** lehetőséget|

1. **Az 5.** **Create your KB**

    A kivonási folyamat néhány percet vesz igénybe a dokumentum elolvasásához és a kérdések és válaszok azonosításához.

    Miután a QnA Maker sikeresen létrehozza a tudásbázist, megnyílik a **Tudásbázis** lap. Ezen az oldalon módosíthatja a tudásbázis tartalmát.

## <a name="add-a-new-question-and-answer-set"></a>Új kérdés-válasz készlet hozzáadása

1. A QnA Maker portál **Szerkesztés lapján** válassza a környezeti eszköztár **+ QnA-pár hozzáadása** lehetőséget.
1. Adja hozzá a következő kérdést:

    `How many Azure services are used by a knowledge base?`

1. Adja hozzá a _jelölést_formázó választ:

    ` * Azure QnA Maker service\n* Azure Cognitive Search\n* Azure web app\n* Azure app plan`

    ![ Adja hozzá a kérdést szövegként, és a választ markdown-nal formázva.](../media/qnamaker-create-publish-knowledge-base/add-question-and-answer.png)

    A jelleés `*`szimbólum a felsoroláspontokhoz használatos. Az `\n` új sorhoz használatos.

    A **Szerkesztés** lapon látható a markdown. Ha később használja a **Teszt** panelt, a markdown megfelelően jelenik meg.

## <a name="save-and-train"></a>Mentés és betanítás

A jobb felső sarokban válassza a **Mentés és betanítás** lehetőséget a szerkesztések mentéséhez és a QnA Maker betanításához. A rendszer a módosításokat csak akkor őrzi meg, ha menti őket.

## <a name="test-the-knowledge-base"></a>A tudásbázis tesztelése

1. A QnA Maker portál jobb felső részén válassza a **Tesztelés** lehetőséget, hogy ellenőrizze, hogy a módosítások érvénybe léptek-e.
1. Írjon be egy példa felhasználói lekérdezést a szövegmezőbe.

    `How many Azure services are used by a knowledge base?`

    ![ Írjon be egy példa felhasználói lekérdezést a szövegmezőbe. ](../media/qnamaker-create-publish-knowledge-base/test-panel-in-qna-maker.png)

1. Válassza az **Inspect** (Vizsgálat) lehetőséget a válasz részletesebb vizsgálatához. A tesztablak a tudásbázis módosításai tesztelésére szolgál a tudásbázis közzététele előtt.

1. A **Teszt** panel bezárásához válassza a **Teszt** ismét lehetőséget.

## <a name="publish-the-knowledge-base"></a>A tudásbázis közzététele

Tudásbázis közzétételekor a tudásbázis tartalma az indexről `test` az `prod` Azure-keresésindexre kerül.

![Képernyőkép a tudásbázis tartalmának áthelyezéséről](../media/qnamaker-how-to-publish-kb/publish-prod-test.png)

1. A QnA Maker portálon válassza a **Közzététel**lehetőséget. Ezután a megerősítéshez válassza az oldalon a **Publish** (Közzététel) lehetőséget.

    Ezzel a QnA Maker-szolgáltatás sikeresen közzé lett téve. Mostantól használhatja a végpontot az alkalmazásában vagy robotkódjában.

    ![A sikeres közzététel képernyőképe](../media/qnamaker-create-publish-knowledge-base/publish-knowledge-base-to-endpoint.png)

## <a name="create-a-bot"></a>Hozzon létre egy bot

A közzététel után létrehozhat egy robotot a **Közzététel** lapon:

* Gyorsan létrehozhat több robotot, amelyek mindegyike ugyanarra a tudásbázisra mutat a különböző régiókhoz vagy az egyes robotok árképzési terveihez.
* Ha csak egy robotot szeretne a tudásbázishoz, használja **az Összes robot megtekintése az Azure Portalon** hivatkozással az aktuális robotok listájának megtekintéséhez.

Amikor módosítja a tudásbázist, és újra közzéteszi, nem kell további műveleteket végrehajtania a robottal. Már be van állítva a tudásbázissal való együttműködésre, és a tudásbázis minden jövőbeli módosításával működik. Minden alkalommal, amikor közzétesz egy tudásbázist, a hozzá kapcsolódó összes robot automatikusan frissül.

1. A QnA Maker portál **Közzététel** lapján válassza a **Robot létrehozása lehetőséget.** Ez a gomb csak a tudásbázis közzététele után jelenik meg.

    ![Képernyőkép: robot létrehozása](../media/qnamaker-create-publish-knowledge-base/create-bot-from-published-knowledge-base-page.png)

1. Megnyílik egy új böngészőlap az Azure Portalon, az Azure Bot Service létrehozási lapjával. Konfigurálja az Azure-robot szolgáltatást. A robot és a QnA Maker megoszthatja a webalkalmazás-szolgáltatáscsomag, de nem tudja megosztani a webapp. Ez azt jelenti, hogy a robot **alkalmazásnevének** különböznie kell a QnA Maker szolgáltatás alkalmazásnevétől.

    * **Nem**
        * Változás bot fogantyú - ha nem egyedi.
        * Válassza az SDK-nyelv lehetőséget. A robot létrehozása után letöltheti a kódot a helyi fejlesztési környezetbe, és folytathatja a fejlesztési folyamatot.
    * **Nem ajánlott**
        * módosítsa a következő beállításokat az Azure Portalon a robot létrehozásakor. Ezek előre ki vannak töltve a meglévő tudásbázishoz:
           * QnA hitelesítési kulcs
           * Alkalmazásszolgáltatási csomag és hely


1. A robot létrehozása után nyissa meg a **Bot szolgáltatás** erőforrás.
1. A **Bot Management csoportban**válassza **a Teszt a webes csevegésben**lehetőséget.
1. A Csevegés üzenetének **beírása**mezőbe írja be az üzenetet:

    `Azure services?`

    A csevegőrobot a tudásbázisod válaszával válaszol.

    ![Adjon meg egy felhasználói lekérdezést a teszt webcsevegésbe.](../media/qnamaker-create-publish-knowledge-base/test-web-chat.png)

## <a name="what-did-you-accomplish"></a>Mit ért el?

Létrehozott egy új tudásbázist, nyilvános URL-címet adott hozzá a tudásbázishoz, hozzáadta a saját QnA-készletét, betanította, tesztelte és közzétette a tudásbázist.

A tudásbázis közzététele után létrehozott egy robotot, és tesztelte a robotot.

Ez mind néhány perc alatt megvalósult anélkül, hogy kódot kellett volna írnia, vagy meg kellett tisztítania a tartalmat.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha nem folytatja a következő rövid útmutatót, törölje a QnA Maker és a Robot keretrendszer erőforrásait az Azure Portalon.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Metaadatokkal rendelkező kérdések felvétele](add-question-metadata-portal.md)

További információk:

* [Markdown formátum válaszokban](../reference-markdown-format.md)
* QnA Maker [adatforrások](../concepts/knowledge-base.md).


