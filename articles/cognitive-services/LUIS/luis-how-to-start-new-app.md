---
title: Új alkalmazás létrehozása
titleSuffix: Language Understanding - Azure Cognitive Services
description: Létrehozhat és kezelhet az alkalmazások, a Language Understanding (LUIS) weblapon.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 04/16/2019
ms.author: diberry
ms.openlocfilehash: daffbe3f3158bb232f7db7ac90d766661e937643
ms.sourcegitcommit: c3d1aa5a1d922c172654b50a6a5c8b2a6c71aa91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/17/2019
ms.locfileid: "59679645"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>A LUIS-portálon új LUIS-alkalmazás létrehozása
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

1. A **saját alkalmazások** lapon jelölje be **importálása új alkalmazás**.
1. Az előugró párbeszédpanelen egy érvényes JSON-fájlt, majd válassza ki és **kész**.

### <a name="import-errors"></a>Importálási hiba

Hibák a következők: 

* Már létezik ilyen nevű alkalmazás. Importálja újra az alkalmazást, és állítsa be a **nem kötelező neve** egy új nevet. 

## <a name="export-app-for-backup"></a>Biztonsági mentés készült alkalmazás exportálása

1. A **saját alkalmazások** lapon jelölje be **exportálása**.
1. Válassza ki **JSON-fájlként exportálja**. A böngésző letölti az alkalmazás aktív verzióját.
1. Ez a fájl hozzáadása a biztonsági mentési rendszer archiválása a modellt.

## <a name="export-app-for-containers"></a>Exportálás app for containers szolgáltatásban

1. A **saját alkalmazások** lapon jelölje be **exportálása**.
1. Válassza ki **tárolóként exportálása** majd válassza ki az exportálni kívánt mely közzétett tárolóhely (éles vagy fázis).
1. Ezzel a fájllal együtt a [LUIS tároló](luis-container-howto.md). 

    Ha érdekli exportálása egy betanított viszont nem, de a közzétett modell használata a LUIS-tároló, folytassa a **verziók** lapon, és exportálni onnan. 

## <a name="delete-app"></a>Alkalmazás törlése

1. A **saját alkalmazások** lapra, jelölje be az alkalmazások sor végén található három pontra (...).
1. Válassza ki **törlése** a menüből.
1. Válassza ki **Ok** a megerősítési ablakban.

## <a name="next-steps"></a>További lépések

Az első az alkalmazás feladata [leképezések hozzáadása](luis-how-to-add-intents.md).
