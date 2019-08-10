---
title: Új alkalmazás létrehozása – LUIS
titleSuffix: Azure Cognitive Services
description: Létrehozhat és kezelhet az alkalmazások, a Language Understanding (LUIS) weblapon.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 05/07/2019
ms.author: diberry
ms.openlocfilehash: 87244169aa8e50ddd503086121dd84f5d50c5df4
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68932774"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Új LUIS-alkalmazás létrehozása a LUIS portálon
Nincsenek többféle módon LUIS-alkalmazások létrehozásához. A LUIS-alkalmazásokon hozhat létre a [LUIS](https://www.luis.ai) portál, vagy a LUIS szerzői [API-k](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f).

## <a name="using-the-luis-portal"></a>A LUIS-portál használatával
A LUIS portálon többféle módon hozhat létre egy új alkalmazást:

* Üres alkalmazás kezdődhet, és szándék fog vonatkozni, utterances és entitások létrehozásához.
* Kezdje egy üres alkalmazást, és adja hozzá a [előre összeállított tartomány](luis-how-to-use-prebuilt-domains.md).
* A LUIS-alkalmazás importálása egy JSON-fájlt, amely már tartalmazza a szándék fog vonatkozni, utterances és entitásokat.

## <a name="using-the-authoring-apis"></a>Az Authoring Tool API-k használatával
Az Authoring Tool API-khoz többféle módon hozhat létre egy új alkalmazást:

* [Indítsa el](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) egy üres alkalmazást és szándék fog vonatkozni, utterances és entitások létrehozásához.
* [Indítsa el](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5) egy előre elkészített tartománnyal.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

## <a name="create-new-app-in-luis"></a>Új alkalmazás létrehozása a LUIS

1. A **saját alkalmazások** lapon jelölje be **új alkalmazás létrehozása**.

    ![LUIS-alkalmazások listája](./media/luis-create-new-app/apps-list.png)


2. A párbeszédpanelen adjon nevet az alkalmazás "TravelAgent".

    ![Hozzon létre új alkalmazás párbeszédpanel](./media/luis-create-new-app/create-app.png)

3. Válassza ki az alkalmazás kulturális környezet (TravelAgent alkalmazások esetén válassza az angol nyelvű), majd válassza ki **kész**. 

    > [!NOTE]
    > A kulturális környezet az alkalmazás létrehozása után nem módosítható. 

## <a name="import-an-app-from-file"></a>Alkalmazás importálása fájlból

1. **Az alkalmazások** lapon válassza az **új alkalmazás importálása**lehetőséget.
1. Az előugró párbeszédpanelen válasszon ki egy érvényes JSON-fájlt, majd válassza a **kész**lehetőséget.

### <a name="import-errors"></a>Importálási hibák

Lehetséges hibák: 

* Már létezik ilyen nevű alkalmazás. A probléma megoldásához importálja újra az alkalmazást, és adja meg a **választható nevet** egy új névre. 

## <a name="export-app-for-backup"></a>Alkalmazás exportálása biztonsági mentésre

1. **Az alkalmazások** lapon válassza az **Exportálás**lehetőséget.
1. Válassza **az EXPORTÁLÁS JSON-ként**lehetőséget. A böngésző letölti az alkalmazás aktív verzióját.
1. Adja hozzá ezt a fájlt a biztonsági mentési rendszeren a modell archiválásához.

## <a name="export-app-for-containers"></a>Alkalmazás exportálása tárolók számára

1. **Az alkalmazások** lapon válassza az **Exportálás**lehetőséget.
1. Válassza az **Exportálás tárolóként** lehetőséget, majd válassza ki, hogy melyik közzétett tárolóhelyet (éles vagy fázis) szeretné exportálni.
1. Ezt a fájlt a [Luis](luis-container-howto.md)-tárolóval használhatja. 

    Ha olyan betanított, de még nem közzétett modellt szeretne exportálni a LUIS-tárolóval való használatra, nyissa meg a **verziók** lapot, és onnan exportálja. 

## <a name="delete-app"></a>Alkalmazás törlése

1. A **saját alkalmazások** oldalon válassza az alkalmazás sor végén található három pontot (...).
1. A menüből válassza a **Törlés** lehetőséget.
1. A megerősítő ablakban kattintson az **OK gombra** .

## <a name="next-steps"></a>További lépések

Az első az alkalmazás feladata [leképezések hozzáadása](luis-how-to-add-intents.md).
