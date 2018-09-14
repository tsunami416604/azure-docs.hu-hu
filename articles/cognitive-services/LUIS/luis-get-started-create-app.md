---
title: Az első Language Understanding- (LUIS-) alkalmazás létrehozása 10 perc alatt – Cognitive Services LUIS | Microsoft Docs
description: Ebben a rövid útmutatóban egy LUIS-alkalmazást hoz létre, amely az előre összeállított `HomeAutomation` tartományt használja a világítás és a berendezések be- és kikapcsolásához. Ez az előre összeállított tartomány szándékokat, entitásokat és kimondott szövegek példáit tartalmazza. Amikor végzett, egy felhőben futó LUIS-végponttal fog rendelkezni.
services: cognitive-services
author: diberry
manager: cjgronlund
ms.service: cognitive-services
ms.component: language-understanding
ms.topic: quickstart
ms.date: 08/22/2018
ms.author: diberry
ms.openlocfilehash: 457f23936dec0cf85e9aebbf3e54bba37c2f3ca3
ms.sourcegitcommit: b5ac31eeb7c4f9be584bb0f7d55c5654b74404ff
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/23/2018
ms.locfileid: "43769941"
---
# <a name="quickstart-use-prebuilt-home-automation-app"></a>Rövid útmutató: Előre összeállított otthonautomatizálási alkalmazás használata

Ebben a rövid útmutatóban egy LUIS-alkalmazást hoz létre, amely az előre összeállított `HomeAutomation` tartományt használja a világítás és a berendezések be- és kikapcsolásához. Ez az előre összeállított tartomány szándékokat, entitásokat és kimondott szövegek példáit tartalmazza. Amikor végzett, egy felhőben futó LUIS-végponttal fog rendelkezni.

## <a name="prerequisites"></a>Előfeltételek

Ehhez a cikkhez egy ingyenes LUIS-fiókra van szüksége, amely a [http://www.luis.ai](http://www.luis.ai) címen elérhető LUIS portálon hozható létre. 

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

A bal oldali navigációs ablaktáblán válassza az **Intents** (Szándékok) elemet a Home Automation-tartomány leképezéseinek megtekintéséhez. 

[ ![](media/luis-quickstart-new-app/home-automation-intents.png "A szándékok listájának képernyőképe, a táblában a szándéknevek kiemelve")](media/luis-quickstart-new-app/home-automation-intents.png)

Minden szándék kimondottszöveg-mintákkal rendelkezik.

> [!NOTE]
> A **None** (Nincs) az összes LUIS-alkalmazásban szereplő szándék. Olyan kimondott szövegek kezelésére szolgál, amelyek nem felelnek meg az alkalmazás által nyújtott funkcióknak. 

Válassza a **HomeAutomation.TurnOff** szándékot. Láthatja, hogy a szándék olyan kimondott szövegek listáját tartalmazza, amelyek entitásokkal vannak feliratozva.

[![](media/luis-quickstart-new-app/home-automation-turnon.png "A HomeAutomation.TurnOff szándék képernyőképe")](media/luis-quickstart-new-app/home-automation-turnon.png)

## <a name="train-your-app"></a>Az alkalmazás betanítása

A felső navigációs ablakban válassza a **Betanítás** elemet.

[![](media/luis-quickstart-new-app/trained.png "A HomeAutomation.TurnOff szándék képernyőképe zöld, sikert jelölő értesítéssel")](media/luis-quickstart-new-app/trained.png)

## <a name="test-your-app"></a>Az alkalmazás tesztelése
Miután betanította az alkalmazását, tesztelheti is. A felső navigációs ablakban válassza a **Test** (Tesztelés) elemet. Írjon be egy kimondott tesztszöveget (például „A világítás kikapcsolása”) az Interaktív tesztelés ablaktáblán, és nyomja le az Enter billentyűt. 

```
Turn off the lights
```

Ellenőrizze, hogy a legmagasabb pontszámú leképezés megfelel-e az egyes kimondott tesztszövegek várt leképezésének.

Ebben a példában „A világítás kikapcsolását” a rendszer helyesen azonosítja a „HomeAutomation.TurnOff” legmagasabb pontszámú leképezéseként.

[![](media/luis-quickstart-new-app/test.png "A tesztelési ablaktábla képernyőképe a kiemelt kimondott szöveggel")](media/luis-quickstart-new-app/test.png)


Válassza ismét a **Test** (Tesztelés) gombot a tesztpanel összecsukásához. 

## <a name="publish-your-app"></a>Az alkalmazás közzététele
A felső navigációs ablakban válassza a **Publish** (Közzététel) elemet. 

[![](media/luis-quickstart-new-app/publish.png "Az alkalmazás képernyőképe a kiemelt Publish (Közzététel) gombbal")](media/luis-quickstart-new-app/publish.png)

Válasza a Production (Termelés) helyet, és kattintson a **Publish** (Közzététel) gombra.

A felül lévő zöld értesítési sáv jelzi, hogy az alkalmazás közzététele sikerült.

[![](media/luis-quickstart-new-app/published.png "Az alkalmazás képernyőképe a sikeres közzétételről szóló értesítéssel")](media/luis-quickstart-new-app/published.png)

A sikeres közzététel után a **Publish app** (Alkalmazás közzététele) lapon megjelenő végponti URL-címet használhatja.

[![](media/luis-quickstart-new-app/endpoint.png "A Publish (Közzététel) lap képernyőképe a kiemelt végponti URL-címmel")](media/luis-quickstart-new-app/endpoint.png)

## <a name="use-your-app"></a>Az alkalmazás használata
A létrejött URL-lel tesztelheti a közzétett végpontot egy böngészőben. Nyissa meg ezt az URL-címet egy böngészőben, és állítsa be a „&q” URL-paramétert a tesztlekérdezéshez. Adja például a `turn off the living room light` szöveget az URL végéhez, majd nyomja le az Enter billentyűt. A böngésző megjeleníti a HTTP-végpont JSON-válaszát.


[![](media/luis-quickstart-new-app/turn-off-living-room.png "A böngésző képernyőképe, amelyen a JSON-eredmény a TurnOff szándékot észleli")](media/luis-quickstart-new-app/turn-off-living-room.png)

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Ha már nincs rá szükség, törölje a LUIS-alkalmazást. Ehhez válassza az alkalmazáslistában az alkalmazás neve mellett jobbra található három pontot (***...***), majd a **Delete** (Törlés) lehetőséget. A **Delete app?** (Törli az alkalmazást?) előugró párbeszédpanelen válassza az **OK** lehetőséget.

## <a name="next-steps"></a>További lépések

Behívhatja a végpontot kódból:

> [!div class="nextstepaction"]
> [LUIS-végpont hívása kóddal](luis-get-started-cs-get-intent.md)
