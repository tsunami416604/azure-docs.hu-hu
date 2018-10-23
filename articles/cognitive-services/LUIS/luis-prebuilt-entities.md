---
title: Előre összeállított entitások a Language Understanding (LUIS)
titleSuffix: Azure Cognitive Services
description: LUIS az általános típusú adatok, például a dátumok, időpontok, számok, mértékek és pénznem FELISMERVE előre összeállított entitások egy készletét tartalmazza. Előre összeállított entitások támogatása a LUIS-alkalmazás kulturális környezete eltérő.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: article
ms.date: 10/18/2018
ms.author: diberry
ms.openlocfilehash: 2f7c724b14efd569a5993f9a9319c9004874bc43
ms.sourcegitcommit: ccdea744097d1ad196b605ffae2d09141d9c0bd9
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/23/2018
ms.locfileid: "49647595"
---
# <a name="prebuilt-entities-to-recognize-common-data-types"></a>Előre összeállított entitások felismerésére általános adattípusok

LUIS az általános típusú adatok, például a dátumok, időpontok, számok, mértékek és pénznem FELISMERVE előre összeállított entitások egy készletét tartalmazza. 

## <a name="add-a-prebuilt-entity"></a>Egy előre létrehozott entitás hozzáadása

1. Nyissa meg az alkalmazás nevére kattintva **saját alkalmazások** lapon, és kattintson a **entitások** a bal oldalon. 

1. Az a **entitások** kattintson **előre összeállított entitások kezelése**.

1. A **előre összeállított entitások hozzáadása** párbeszédpanelen jelölje ki a datetimeV2 előre összeállított entitások. 

    ![Előre összeállított entitások párbeszédpanel hozzáadása](./media/luis-use-prebuilt-entity/add-prebuilt-entity-dialog.png)

1. Válassza a **Done** (Kész) lehetőséget.

## <a name="publish-the-app"></a>Az alkalmazás közzététele

Előre összeállított entitások értékének megtekintéséhez a legegyszerűbb módja az, hogy a közzétett végpontról lekérdezés. 

1. A felső eszköztáron válassza **közzététel**. Közzététele **éles**. 

1. Ha a zöld, sikeres értesítés jelenik meg, válassza ki a **tekintse meg a végpontok listáját** hivatkozásra kattintva megtekintheti a végpontok.

1. Válasszon végpontot. Egy új böngészőlapon nyílik meg, hogy a végpont. Ne zárja be a böngészőlapot, és továbbra is a **teszt** szakaszban.

## <a name="test"></a>Tesztelés
Az entitás hozzáadása után nem kell az alkalmazás betanításához. 

Teszt új célja a végponton által hozzáadott értéket a **q** paraméter. Használja az alábbi táblázat a javasolt utterances **q**:

|Tesztelje az utterance (kifejezés)|Entitás érték|
|--|:--|
|Könyv holnap repülőjegyet|2018-10-19|
|a találkozó március 3-án megszakítása|A LUIS a legutóbbi március 3 korábban visszaadott (2018-03-03) és március 3 a jövőben (a 2019-03-03), mert az utterance (kifejezés) nem adott meg egy év.|
|10-kor értekezlet ütemezése|10:00:00|


## <a name="next-steps"></a>További lépések
> [!div class="nextstepaction"]
> [Előre összeállított entitások bemutatása](./luis-reference-prebuilt-entities.md)
