---
title: Új alkalmazás létrehozása – LUIS
titleSuffix: Azure Cognitive Services
description: Alkalmazások létrehozása és kezelése a Language Understanding (LUIS) weblapon.
services: cognitive-services
manager: nitinme
ms.custom: seodec18
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: how-to
ms.date: 05/18/2020
ms.openlocfilehash: fcc803e87b2efd3e94b90eb17258ab3bb156359f
ms.sourcegitcommit: f5580dd1d1799de15646e195f0120b9f9255617b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 09/29/2020
ms.locfileid: "91541432"
---
# <a name="create-a-new-luis-app-in-the-luis-portal"></a>Új LUIS-alkalmazás létrehozása a LUIS portálon
A LUIS-alkalmazások többféleképpen is létrehozhatók. Luis-alkalmazást a LUIS-portálon vagy a LUIS authoring [API](developer-reference-resource.md)-kon keresztül hozhat létre.

## <a name="using-the-luis-portal"></a>A LUIS-portál használata

Több módon is létrehozhat egy új alkalmazást a portálon:

* Kezdjen egy üres alkalmazással, és hozzon létre leképezéseket, hosszúságú kimondott szöveg és entitásokat.
* Kezdjen egy üres alkalmazással, és adjon hozzá egy [előre elkészített tartományt](luis-how-to-use-prebuilt-domains.md).
* Olyan vagy fájlból származó LUIS-alkalmazás importálása, `.lu` `.json` amely már tartalmaz szándékokat, hosszúságú kimondott szöveg és entitásokat.

## <a name="using-the-authoring-apis"></a>A szerzői API-k használata
Több módon is létrehozhat egy új alkalmazást a szerzői API-kkal:

* [Alkalmazás hozzáadása](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/5890b47c39e2bb052c5b9c2f) – kezdje üres alkalmazással, és hozzon létre leképezéseket, hosszúságú kimondott szöveg és entitásokat.
* Előre elkészített [alkalmazás hozzáadása](https://westeurope.dev.cognitive.microsoft.com/docs/services/luis-programmatic-apis-v3-0-preview/operations/59104e515aca2f0b48c76be5) – kezdés egy előre elkészített tartománnyal, beleértve a szándékokat, a hosszúságú kimondott szöveg és az entitásokat.


<a name="export-app"></a>
<a name="import-new-app"></a>
<a name="delete-app"></a>


[!INCLUDE [Sign in to LUIS](./includes/sign-in-process.md)]

## <a name="create-new-app-in-luis"></a>Új alkalmazás létrehozása a LUIS-ban

1. A **saját alkalmazások** oldalon válassza ki az **előfizetését**, és **hozzon létre**egy **erőforrást** , majd a + létrehozás elemet. 

> [!div class="mx-imgBorder"]
> ![LUIS-alkalmazások listája](./media/create-app-in-portal.png)

1. A párbeszédpanelen adja meg az alkalmazás nevét, például: `Pizza Tutorial` .

    ![Új alkalmazás létrehozása párbeszédpanel](./media/create-pizza-tutorial-app-in-portal.png)

1. Válassza ki az alkalmazás kulturális környezetét, majd válassza a **kész**lehetőséget. A leírás és az előrejelzési erőforrás ezen a ponton nem kötelező. Ezt követően bármikor megadhatja a portál **kezelés** szakaszában.

    > [!NOTE]
    > A kulturális környezet az alkalmazás létrehozása után nem módosítható.

    Az alkalmazás létrehozása után a LUIS-portál megjeleníti a **szándékok** listáját a `None` már létrehozott szándékkal. Most már van egy üres alkalmazás.

    > [!div class="mx-imgBorder"]
    > ![Olyan leképezések listája, amelyekhez nincs megadva példa hosszúságú kimondott szöveg.](media/pizza-tutorial-new-app-empty-intent-list.png)

## <a name="other-actions-available-on-my-apps-page"></a>Az alkalmazások oldalon elérhető egyéb műveletek

A környezeti eszköztár más műveleteket is tartalmaz:

* Alkalmazás átnevezése
* Importálás fájlból a `.lu` vagy a használatával `.json`
* Alkalmazás exportálása `.lu` ( [LUDown](https://github.com/microsoft/botbuilder-tools/tree/master/packages/Ludown)), `.json` vagy `.zip` (Luis- [tároló](luis-container-howto.md)esetén)
* Tároló-végponti naplók importálása az Endpoint hosszúságú kimondott szöveg áttekintéséhez
* Végponti naplók exportálása `.csv` Offline elemzéshez
* Alkalmazás törlése

## <a name="next-steps"></a>További lépések

Ha az alkalmazás kialakítása magában foglalja a szándék észlelését, [hozzon létre új leképezéseket](luis-how-to-add-intents.md), és adja hozzá például a hosszúságú kimondott szöveg. Ha az alkalmazás kialakítása csak az kibontást tartalmazza, adjon hozzá például hosszúságú kimondott szöveg a none szándékhoz, majd [hozzon létre entitásokat](luis-how-to-add-example-utterances.md), és címkézze fel a példát a hosszúságú kimondott szöveg.
