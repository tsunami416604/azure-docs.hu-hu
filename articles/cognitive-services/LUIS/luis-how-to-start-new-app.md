---
title: Új alkalmazás létrehozása – LUIS
titleSuffix: Azure Cognitive Services
description: Alkalmazások létrehozása és kezelése a Language Understanding (LUIS) weblapon.
services: cognitive-services
author: diberry
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: conceptual
ms.date: 10/25/2019
ms.author: diberry
ms.openlocfilehash: 227efcdbcb7d8e776dd77b38c5d1dedd54d71b6b
ms.sourcegitcommit: c22327552d62f88aeaa321189f9b9a631525027c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2019
ms.locfileid: "73500315"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Új LUIS-alkalmazás létrehozása a LUIS portálon
A LUIS-alkalmazások többféleképpen is létrehozhatók. Luis-alkalmazást a [Luis](https://www.luis.ai) -portálon vagy a Luis authoring [API](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f)-kon keresztül hozhat létre.

[!INCLUDE [Waiting for LUIS portal refresh](./includes/wait-v3-upgrade.md)]

## <a name="using-the-luis-portal"></a>A LUIS-portál használata

A LUIS-portálon többféleképpen is létrehozhat egy új alkalmazást:

* Kezdjen egy üres alkalmazással, és hozzon létre leképezéseket, hosszúságú kimondott szöveg és entitásokat.
* Kezdjen egy üres alkalmazással, és adjon hozzá egy [előre elkészített tartományt](luis-how-to-use-prebuilt-domains.md).
* Importáljon egy LUIS-alkalmazást egy olyan JSON-fájlból, amely már tartalmaz szándékokat, hosszúságú kimondott szöveg és entitásokat.

## <a name="using-the-authoring-apis"></a>A szerzői API-k használata
Több módon is létrehozhat egy új alkalmazást a szerzői API-kkal:

* [Kezdjen](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/5890b47c39e2bb052c5b9c2f) egy üres alkalmazással, és hozzon létre leképezéseket, hosszúságú kimondott szöveg és entitásokat.
* [Kezdje](https://westus.dev.cognitive.microsoft.com/docs/services/5890b47c39e2bb17b84a55ff/operations/59104e515aca2f0b48c76be5) egy előre elkészített tartománnyal.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Új alkalmazás létrehozása a LUIS-ban

1. A **saját alkalmazások** oldalon válassza a **+ Létrehozás**elemet.

    ![LUIS-alkalmazások listája](./media/luis-create-new-app/apps-list.png)


2. A párbeszédpanelen nevezze el az alkalmazást "TravelAgent" néven.

    ![Új alkalmazás létrehozása párbeszédpanel](./media/luis-create-new-app/create-app.png)

3. Válassza ki az alkalmazás kulturális környezetét (a TravelAgent alkalmazáshoz válassza az angol nyelvet), majd kattintson a **kész**gombra. 

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
1. Ezt a fájlt a [Luis-tárolóval](luis-container-howto.md)használhatja. 

    Ha olyan betanított, de még nem közzétett modellt szeretne exportálni a LUIS-tárolóval való használatra, nyissa meg a **verziók** lapot, és onnan exportálja. 

## <a name="delete-app"></a>Alkalmazás törlése

1. A **saját alkalmazások** oldalon válassza az alkalmazás sor végén található három pontot (...).
1. A menüből válassza a **Törlés** lehetőséget.
1. A megerősítő ablakban kattintson az **OK gombra** .

## <a name="next-steps"></a>További lépések

Az alkalmazás első feladata a [leképezések hozzáadása](luis-how-to-add-intents.md).
