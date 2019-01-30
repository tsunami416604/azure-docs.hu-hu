---
title: Új alkalmazás létrehozása
titleSuffix: Language Understanding - Azure Cognitive Services
description: Létrehozhat és kezelhet az alkalmazások, a Language Understanding (LUIS) weblapon.
services: cognitive-services
author: diberry
manager: cgronlun
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: article
ms.date: 01/23/2019
ms.author: diberry
ms.openlocfilehash: 3c35bb96c3ba5dbf1c3302836b2c73cf15128937
ms.sourcegitcommit: 95822822bfe8da01ffb061fe229fbcc3ef7c2c19
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/29/2019
ms.locfileid: "55215172"
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


## <a name="next-steps"></a>További lépések

Az első az alkalmazás feladata [leképezések hozzáadása](luis-how-to-add-intents.md).
