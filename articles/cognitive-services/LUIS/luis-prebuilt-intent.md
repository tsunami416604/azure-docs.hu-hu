---
title: Előre összeállított leképezések a Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: A LUIS gyorsan hozzáadásához a közös, természetes nyelvi felhasználói esetek előre összeállított leképezések készletét tartalmazza.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: 83cbc9fbe5262700a724148c2b63a2e46c489583
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49651881"
---
# <a name="add-prebuilt-intents-for-common-intents"></a>A közös leképezések előre összeállított leképezések hozzáadása 

A LUIS tartalmaz egy, az előre összeállított tartományok gyorsan a közös leképezések és beszédmódok hozzáadása az előre összeállított leképezések. Ez az egy könnyen és gyorsan, anélkül, hogy ezek a képességek a modelleket tervezhet kellene képességek hozzáadása a természetes nyelvi ügyfélalkalmazás. 

## <a name="add-a-prebuilt-intent"></a>Egy előre elkészített leképezés hozzáadása

1. Az a **saját alkalmazások** lapon, válassza ki az alkalmazást. Ekkor megnyílik az alkalmazás a **összeállítása** szakaszában. 

1. Az a **leképezések** lapon jelölje be **előre összeállított leképezés hozzáadása** leképezések lista fölötti eszköztárból. 

1. Válassza ki a **Utilities.Cancel** szándék az előugró párbeszédpanelen. 

    ![Előre összeállított leképezés hozzáadása](./media/luis-prebuilt-intents/prebuilt-intents-ddl.png)

1. Válassza ki a **kész** gombra.

## <a name="train-and-test"></a>Tanítási és tesztelési

1. A leképezés hozzáadása után az alkalmazás betanításához kiválasztásával **betanításához** TOP, jobb gombbal az eszköztáron. 

1. Az új leképezés tesztelése kiválasztásával **teszt** a jobb oldali eszköztáron. 

1. A szövegmezőbe írja be a megszakítása a kimondott szöveg:

    |Tesztelje az utterance (kifejezés)|Előrejelzési pontszám|
    |--|:--|
    |Szeretnék saját repülési megszakítása.|0.67|
    |A vásárlás megszakítása|0.52|
    |Az értekezlet törlése.|0.56|

    ![Előre összeállított leképezés tesztelése](./media/luis-prebuilt-intents/test.png)

## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Előre összeállított entitások](./luis-prebuilt-entities.md)