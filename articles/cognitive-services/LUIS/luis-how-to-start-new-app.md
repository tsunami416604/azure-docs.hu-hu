---
title: Új alkalmazás létrehozása – LUIS
titleSuffix: Azure Cognitive Services
description: Hozza létre és kezelje alkalmazásait a Nyelvi megértés (LUIS) weblapon.
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
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "79220830"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Új LUIS-alkalmazás létrehozása a LUIS portálon
A LUIS-alkalmazások többféleképpen is létrehozható. Létrehozhat egy LUIS-alkalmazást a LUIS-portálon vagy a LUIS szerzői [API-kon](developer-reference-resource.md)keresztül.

[!INCLUDE [Uses preview portal](includes/uses-portal-preview.md)]

## <a name="using-the-luis-portal"></a>A LUIS portál használata

Az előnézeti portálon többféleképpen hozhat létre új alkalmazást:

* Kezdje egy üres alkalmazással, és hozzon létre szándékokat, kimondott szövegeket és entitásokat.
* Kezdje egy üres alkalmazással, és adjon hozzá egy [előre összeállított tartományt.](luis-how-to-use-prebuilt-domains.md)
* Luis-alkalmazás importálása `.lu` `.json` olyan fájlból, amely már tartalmaz leképezéseket, kimondott szövegeket és entitásokat.

## <a name="using-the-authoring-apis"></a>A szerzői API-k használata
A szerzői API-kkal többféleképpen hozhat létre új alkalmazást:

* [Alkalmazás hozzáadása](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) – kezdje egy üres alkalmazással, és hozzon létre leképezéseket, kimondott szövegeket és entitásokat.
* [Előre összeállított alkalmazás hozzáadása](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) – egy előre összeállított tartománnyal kezdődjön, beleértve a szándékokat, az utterances-t és az entitásokat.  


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>
 

[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Új alkalmazás létrehozása a LUIS-ban

1. A **Saját alkalmazások** lapon jelölje ki az előfizetést, majd a szerzői **erőforrást,** majd a + Create . Ha ingyenes próbakulcsot használ, ismerje meg, hogyan [hozhat létre szerzői erőforrást.](luis-how-to-azure-subscription.md#create-resources-in-the-azure-portal)

    ![LUIS-alkalmazások listája](./media/create-app-in-portal.png)


1. A párbeszédpanelen adja meg az alkalmazás nevét, például `Pizza Tutorial`.

    ![Új alkalmazás párbeszédpanel létrehozása](./media/create-pizza-tutorial-app-in-portal.png)

1. Válassza ki az alkalmazáskulturális környezetet, majd válassza **a Kész**lehetőséget. A leírás és az előrejelzési erőforrás nem kötelező ezen a ponton. Ezután bármikor beállítható a portál **Kezelés** szakaszában.

    > [!NOTE]
    > A kulturális környezet az alkalmazás létrehozása után nem módosítható. 

    Az alkalmazás létrehozása után a LUIS-portál megjeleníti `None` a **szándékok** listáját az Ön számára már létrehozott szándékkal. Most már van egy üres alkalmazás. 
    
    > [!div class="mx-imgBorder"]
    > ![Leképezések lista nincs szándékkal létrehozott példa utterances nélkül.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available"></a>Egyéb rendelkezésre álló intézkedések

A környezet eszköztár a következő műveleteket tartalmazza:

* Alkalmazás átnevezése
* Importálás fájlból `.lu` a vagy`.json`
* Alkalmazás `.lu` exportálása [(LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)esetén), `.json`vagy `.zip` [(LUIS-tároló](luis-container-howto.md)esetén)
* Tárolóvégpont-naplók importálása a végpontkimondott szöveg áttekintéséhez
* Végpontnaplók exportálása `.csv`kapcsolat nélküli elemzéshez
* Alkalmazás törlése

## <a name="next-steps"></a>További lépések

Ha az alkalmazás terv magában foglalja a szándékészlelés, [hozzon létre új leképezések,](luis-how-to-add-intents.md)és adja hozzá a példa utterances. Ha az alkalmazás terv csak az adatok kinyerése, adja hozzá a példa utterances a Nincs szándék, majd [hozzon létre entitásokat,](luis-how-to-add-example-utterances.md)és címkézze a példa utterances ezekkel az entitásokkal. 
