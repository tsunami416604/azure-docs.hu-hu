---
title: 'Gyors útmutató: alkalmazás létrehozása'
titleSuffix: Language Understanding - Azure Cognitive Services
description: Hozzon létre egy LUIS-alkalmazást, amely az előre összeállított `HomeAutomation` tartományt használja a világítás és a berendezések be- és kikapcsolásához. Ez az előre összeállított tartomány szándékokat, entitásokat és kimondott szövegek példáit tartalmazza. Amikor végzett, egy felhőben futó LUIS-végponttal fog rendelkezni.
services: cognitive-services
author: diberry
ms.custom: seodec18
manager: nitinme
ms.service: cognitive-services
ms.subservice: language-understanding
ms.topic: quickstart
ms.date: 01/09/2019
ms.author: diberry
ms.openlocfilehash: 347de9b575e3b47ee795026115fd2452c28b866b
ms.sourcegitcommit: 90cec6cccf303ad4767a343ce00befba020a10f6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/07/2019
ms.locfileid: "55877337"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Gyors útmutató: Előre összeállított kezdőlap automation alkalmazás használata

Ebben a rövid útmutatóban egy LUIS-alkalmazást hoz létre, amely az előre összeállított `HomeAutomation` tartományt használja a világítás és a berendezések be- és kikapcsolásához. Ez az előre összeállított tartomány szándékokat, entitásokat és kimondott szövegek példáit tartalmazza. Amikor végzett, egy felhőben futó LUIS-végponttal fog rendelkezni.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a cikkhez egy ingyenes LUIS-fiókra van szüksége, amely a [https://www.luis.ai](https://www.luis.ai) címen elérhető LUIS portálon hozható létre. 

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása
A **My Apps** (Saját alkalmazások) lapon hozhatja létre és kezelheti az alkalmazásokat. 

1. Jelentkezzen be a LUIS portálra.

2. Válassza a **Create new app** (Új alkalmazás létrehozása) lehetőséget.

    [![Képernyőkép az alkalmazások listájának](media/luis-quickstart-new-app/app-list.png "Alkalmazáslista képernyőképe")](media/luis-quickstart-new-app/app-list.png)

3. A párbeszédpanelen adja a „Home Automation” nevet az alkalmazásnak.

    [![Képernyőkép, hozzon létre új alkalmazás felugró párbeszédpanel](media/luis-quickstart-new-app/create-new-app-dialog.png "létrehozása képernyőképe új alkalmazás felugró párbeszédpanel")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. Válassza ki az alkalmazás kulturális környezetét. Ehhez az otthonautomatizálási alkalmazáshoz válassza a magyar nyelvet. Ezután válassza a **Done** (Kész) elemet. A LUIS létrehozza az otthonautomatizálási alkalmazást. 

    >[!NOTE]
    >A kulturális környezet az alkalmazás létrehozása után nem módosítható. 

## <a name="add-prebuilt-domain"></a>Előre összeállított tartomány hozzáadása

A bal oldali navigációs ablaktáblán válassza a **Prebuilt domains** (Előre összeállított tartományok) elemet. Ezután keressen a „Home” kifejezésre. Válassza az **Add domain** (Tartomány hozzáadása) lehetőséget.

[![Képernyőkép a kezdőlap Automation tartomány feltüntettük az előre összeállított tartomány menü](media/luis-quickstart-new-app/home-automation.png "képernyőképe a kezdőlap Automation tartomány feltüntettük az előre összeállított tartomány menü")](media/luis-quickstart-new-app/home-automation.png)

A tartomány sikeres hozzáadása után az előre összeállított tartomány mezőben megjelenik egy **Remove domain** (Tartomány eltávolítása) gomb.

[![Eltávolítás gomb képernyőképe a kezdőlap Automation-tartomány](media/luis-quickstart-new-app/remove-domain.png "Eltávolítás gomb képernyőképe a kezdőlap Automation-tartomány")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Szándékok és entitások

A bal oldali navigációs ablaktáblán válassza az **Intents** (Szándékok) elemet a Home Automation-tartomány leképezéseinek megtekintéséhez. Minden szándék kimondottszöveg-mintákkal rendelkezik.

> [!NOTE]
> A **None** (Nincs) az összes LUIS-alkalmazásban szereplő szándék. Olyan kimondott szövegek kezelésére szolgál, amelyek nem felelnek meg az alkalmazás által nyújtott funkcióknak. 

Válassza a **HomeAutomation.TurnOff** szándékot. Láthatja, hogy a szándék olyan kimondott szövegek listáját tartalmazza, amelyek entitásokkal vannak feliratozva.

[![Képernyőkép a HomeAutomation.TurnOff szándékot](media/luis-quickstart-new-app/home-automation-turnon.png "képernyőképe a HomeAutomation.TurnOff leképezés")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Az alkalmazás tesztelése
Miután betanította az alkalmazását, tesztelheti is. A felső navigációs ablakban válassza a **Test** (Tesztelés) elemet. Írjon be egy kimondott tesztszöveget (például „A világítás kikapcsolása”) az Interaktív tesztelés ablaktáblán, és nyomja le az Enter billentyűt. 

```
Turn off the lights
```

Ellenőrizze, hogy a legmagasabb pontszámú leképezés megfelel-e az egyes kimondott tesztszövegek várt leképezésének.

Ebben a példában „A világítás kikapcsolását” a rendszer helyesen azonosítja a „HomeAutomation.TurnOff” legmagasabb pontszámú leképezéseként.

[![Képernyőfelvétel: a teszt panel az utterance (kifejezés) kiemelésével](media/luis-quickstart-new-app/test.png "képernyőképe a teszt panel az utterance (kifejezés) kiemelésével")](media/luis-quickstart-new-app/test.png)


Válassza ismét a **Test** (Tesztelés) gombot a tesztpanel összecsukásához. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>A végpont lekérdezése egy másik kimondott szöveggel

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Lépjen az URL-cím végéhez, és írja be a következőt: `turn off the living room light`, majd nyomja le az Enter billentyűt. A böngésző megjeleníti a HTTP-végpont JSON-válaszát.

    [![Képernyőkép a böngészőben a JSON-eredményt észleli a szándék Kikapcsolás](media/luis-quickstart-new-app/turn-off-living-room.png "Képernyőkép a böngészőben a JSON-eredményt észleli a szándék kikapcsolása")](media/luis-quickstart-new-app/turn-off-living-room.png)
    
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Behívhatja a végpontot kódból:

> [!div class="nextstepaction"]
> [LUIS-végpont hívása kóddal](luis-get-started-cs-get-intent.md)
