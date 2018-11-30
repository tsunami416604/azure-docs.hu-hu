---
title: Az első LUIS-alkalmazás létrehozása 10 perc alatt
titleSuffix: Azure Cognitive Services
description: Hozzon létre egy LUIS-alkalmazást, amely az előre összeállított `HomeAutomation` tartományt használja a világítás és a berendezések be- és kikapcsolásához. Ez az előre összeállított tartomány szándékokat, entitásokat és kimondott szövegek példáit tartalmazza. Amikor végzett, egy felhőben futó LUIS-végponttal fog rendelkezni.
services: cognitive-services
author: diberry
manager: cgronlun
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 09/10/2018
ms.author: diberry
ms.openlocfilehash: 657f5b6879f7782cbd94588657dc0082eff1f9c5
ms.sourcegitcommit: c61c98a7a79d7bb9d301c654d0f01ac6f9bb9ce5
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/27/2018
ms.locfileid: "52423330"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Rövid útmutató: Előre összeállított otthonautomatizálási alkalmazás használata

Ebben a rövid útmutatóban egy LUIS-alkalmazást hoz létre, amely az előre összeállított `HomeAutomation` tartományt használja a világítás és a berendezések be- és kikapcsolásához. Ez az előre összeállított tartomány szándékokat, entitásokat és kimondott szövegek példáit tartalmazza. Amikor végzett, egy felhőben futó LUIS-végponttal fog rendelkezni.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a cikkhez egy ingyenes LUIS-fiókra van szüksége, amely a [https://www.luis.ai](https://www.luis.ai) címen elérhető LUIS portálon hozható létre. 

## <a name="create-a-new-app"></a>Új alkalmazás létrehozása
A **My Apps** (Saját alkalmazások) lapon hozhatja létre és kezelheti az alkalmazásokat. 

1. Jelentkezzen be a LUIS portálra.

2. Válassza a **Create new app** (Új alkalmazás létrehozása) lehetőséget.

    [![](media/luis-quickstart-new-app/app-list.png "Alkalmazáslista képernyőképe")](media/luis-quickstart-new-app/app-list.png)

3. A párbeszédpanelen adja a „Home Automation” nevet az alkalmazásnak.

    [![](media/luis-quickstart-new-app/create-new-app-dialog.png "A Create new app (Új alkalmazás létrehozása) felugró párbeszédpanel képernyőképe")](media/luis-quickstart-new-app/create-new-app-dialog.png)

4. Válassza ki az alkalmazás kulturális környezetét. Ehhez az otthonautomatizálási alkalmazáshoz válassza a magyar nyelvet. Ezután válassza a **Done** (Kész) elemet. A LUIS létrehozza az otthonautomatizálási alkalmazást. 

    >[!NOTE]
    >A kulturális környezet az alkalmazás létrehozása után nem módosítható. 

## <a name="add-prebuilt-domain"></a>Előre összeállított tartomány hozzáadása

A bal oldali navigációs ablaktáblán válassza a **Prebuilt domains** (Előre összeállított tartományok) elemet. Ezután keressen a „Home” kifejezésre. Válassza az **Add domain** (Tartomány hozzáadása) lehetőséget.

[![](media/luis-quickstart-new-app/home-automation.png "Az előre összeállított tartomány menüben meghívott Home Automation tartomány képernyőképe")](media/luis-quickstart-new-app/home-automation.png)

A tartomány sikeres hozzáadása után az előre összeállított tartomány mezőben megjelenik egy **Remove domain** (Tartomány eltávolítása) gomb.

[![](media/luis-quickstart-new-app/remove-domain.png "A Home Automation tartomány képernyőképe az eltávolítás gombbal")](media/luis-quickstart-new-app/remove-domain.png)

## <a name="intents-and-entities"></a>Szándékok és entitások

A bal oldali navigációs ablaktáblán válassza az **Intents** (Szándékok) elemet a Home Automation-tartomány leképezéseinek megtekintéséhez. Minden szándék kimondottszöveg-mintákkal rendelkezik.

> [!NOTE]
> A **None** (Nincs) az összes LUIS-alkalmazásban szereplő szándék. Olyan kimondott szövegek kezelésére szolgál, amelyek nem felelnek meg az alkalmazás által nyújtott funkcióknak. 

Válassza a **HomeAutomation.TurnOff** szándékot. Láthatja, hogy a szándék olyan kimondott szövegek listáját tartalmazza, amelyek entitásokkal vannak feliratozva.

[![](media/luis-quickstart-new-app/home-automation-turnon.png "A HomeAutomation.TurnOff szándék képernyőképe")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-the-luis-app"></a>A LUIS-alkalmazás betanítása

[!INCLUDE [LUIS How to Train steps](../../../includes/cognitive-services-luis-tutorial-how-to-train.md)]

## <a name="test-your-app"></a>Az alkalmazás tesztelése
Miután betanította az alkalmazását, tesztelheti is. A felső navigációs ablakban válassza a **Test** (Tesztelés) elemet. Írjon be egy kimondott tesztszöveget (például „A világítás kikapcsolása”) az Interaktív tesztelés ablaktáblán, és nyomja le az Enter billentyűt. 

```
Turn off the lights
```

Ellenőrizze, hogy a legmagasabb pontszámú leképezés megfelel-e az egyes kimondott tesztszövegek várt leképezésének.

Ebben a példában „A világítás kikapcsolását” a rendszer helyesen azonosítja a „HomeAutomation.TurnOff” legmagasabb pontszámú leképezéseként.

[![](media/luis-quickstart-new-app/test.png "A tesztelési ablaktábla képernyőképe a kiemelt kimondott szöveggel")](media/luis-quickstart-new-app/test.png)


Válassza ismét a **Test** (Tesztelés) gombot a tesztpanel összecsukásához. 

<a name="publish-your-app"></a>

## <a name="publish-the-app-to-get-the-endpoint-url"></a>Az alkalmazás közzététele a végpont URL-címének lekéréshez

[!INCLUDE [LUIS How to Publish steps](../../../includes/cognitive-services-luis-tutorial-how-to-publish.md)]

## <a name="query-the-endpoint-with-a-different-utterance"></a>A végpont lekérdezése egy másik kimondott szöveggel

1. [!INCLUDE [LUIS How to get endpoint first step](../../../includes/cognitive-services-luis-tutorial-how-to-get-endpoint.md)] 

2. Lépjen az URL-cím végéhez, és írja be a következőt: `turn off the living room light`, majd nyomja le az Enter billentyűt. A böngésző megjeleníti a HTTP-végpont JSON-válaszát.

    [![](media/luis-quickstart-new-app/turn-off-living-room.png "A böngésző képernyőképe, amelyen a JSON-eredmény a TurnOff szándékot észleli")](media/luis-quickstart-new-app/turn-off-living-room.png)
    
## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

[!INCLUDE [LUIS How to clean up resources](../../../includes/cognitive-services-luis-tutorial-how-to-clean-up-resources.md)]

## <a name="next-steps"></a>További lépések

Behívhatja a végpontot kódból:

> [!div class="nextstepaction"]
> [LUIS-végpont hívása kóddal](luis-get-started-cs-get-intent.md)
