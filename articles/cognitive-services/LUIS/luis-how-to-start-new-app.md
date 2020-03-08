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
ms.date: 11/08/2019
ms.author: diberry
ms.openlocfilehash: d3f8696388a33a8ea112aae438c6bbe9af520c61
ms.sourcegitcommit: 509b39e73b5cbf670c8d231b4af1e6cfafa82e5a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2020
ms.locfileid: "78390070"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Új LUIS-alkalmazás létrehozása a LUIS portálon
Nincsenek többféle módon LUIS-alkalmazások létrehozásához. Luis-alkalmazást a LUIS-portálon vagy a LUIS authoring [API](developer-reference-resource.md)-kon keresztül hozhat létre.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="using-the-luis-portal"></a>A LUIS-portál használatával

A betekintő portálon többféleképpen is létrehozhat egy új alkalmazást:

* Üres alkalmazás kezdődhet, és szándék fog vonatkozni, utterances és entitások létrehozásához.
* Kezdjen egy üres alkalmazással, és adjon hozzá egy [előre elkészített tartományt](luis-how-to-use-prebuilt-domains.md).
* Egy LUIS-alkalmazás importálása `.lu` vagy `.json` fájlból, amely már tartalmaz szándékokat, hosszúságú kimondott szöveg és entitásokat.

## <a name="using-the-authoring-apis"></a>Az Authoring Tool API-k használatával
Az Authoring Tool API-khoz többféle módon hozhat létre egy új alkalmazást:

* [Alkalmazás hozzáadása](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) – kezdje üres alkalmazással, és hozzon létre leképezéseket, hosszúságú kimondott szöveg és entitásokat.
* Előre elkészített [alkalmazás hozzáadása](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) – kezdés egy előre elkészített tartománnyal, beleértve a szándékokat, a hosszúságú kimondott szöveg és az entitásokat.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Új alkalmazás létrehozása a LUIS

1. A **saját alkalmazások** oldalon válassza ki az előfizetését, és **hozzon létre**egy erőforrást, majd a + létrehozás elemet. Ha ingyenes próbaverziós kulcsot használ, Ismerje meg, hogyan [hozhat létre authoring-erőforrásokat](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal).

    ![LUIS-alkalmazások listája](./media/create-app-in-portal.png)


1. A párbeszédpanelen adja meg az alkalmazás nevét, például `Pizza Tutorial`.

    ![Hozzon létre új alkalmazás párbeszédpanel](./media/create-pizza-tutorial-app-in-portal.png)

1. Válassza ki az alkalmazás kulturális környezetét, majd válassza a **kész**lehetőséget. A leírás és az előrejelzési erőforrás ezen a ponton nem kötelező. Ezt követően bármikor megadhatja a portál **kezelés** szakaszában.

    > [!NOTE]
    > A kulturális környezet az alkalmazás létrehozása után nem módosítható. 

    Az alkalmazás létrehozása után a LUIS-portál megjeleníti a **leképezések** listáját a már létrehozott `None` szándékkal. Most már van egy üres alkalmazás. 
    
    > [!div class="mx-imgBorder"]
    > ![leképezések listája, amely nem hoz létre példa nélküli hosszúságú kimondott szöveg.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available"></a>Egyéb elérhető műveletek

A környezeti eszköztár más műveleteket is tartalmaz:

* Alkalmazás átnevezése
* Importálás fájlból `.lu` vagy `.json` használatával
* Alkalmazás exportálása `.lu`ként ( [LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)), `.json`vagy `.zip` ( [Luis-tároló](luis-container-howto.md)esetén)
* Tároló-végponti naplók importálása az Endpoint hosszúságú kimondott szöveg áttekintéséhez
* Végponti naplók exportálása `.csv`kapcsolat nélküli elemzéshez
* Alkalmazás törlése

## <a name="next-steps"></a>Következő lépések

Ha az alkalmazás kialakítása magában foglalja a szándék észlelését, [hozzon létre új leképezéseket](luis-how-to-add-intents.md), és adja hozzá például a hosszúságú kimondott szöveg. Ha az alkalmazás kialakítása csak az kibontást tartalmazza, adjon hozzá például hosszúságú kimondott szöveg a none szándékhoz, majd [hozzon létre entitásokat](luis-how-to-add-example-utterances.md), és címkézze fel a példát a hosszúságú kimondott szöveg. 
