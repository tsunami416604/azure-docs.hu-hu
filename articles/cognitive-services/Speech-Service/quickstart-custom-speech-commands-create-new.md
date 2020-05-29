---
title: 'Gyors útmutató: egyéni parancsok előzetes verziójának létrehozása – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben egy futtatott egyéni parancsok előzetes verzióját fogja létrehozni és tesztelni. Az alkalmazás feldolgozza a hosszúságú kimondott szöveg.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: 80111ff370f3a5412b45adc04c82c9dee103c01d
ms.sourcegitcommit: f0b206a6c6d51af096a4dc6887553d3de908abf3
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/28/2020
ms.locfileid: "84142350"
---
# <a name="quickstart-create-a-custom-commands-preview-app"></a>Gyors útmutató: egyéni parancsok előnézeti alkalmazásának létrehozása

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és tesztelheti az egyéni parancsok alkalmazását.
Az alkalmazás feldolgozza a hosszúságú kimondott szöveg, például a "TV bekapcsolása" és a válasz egy egyszerű üzenettel, például "ok, a TV bekapcsolása".

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech-erőforrás létrehozása<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > Jelenleg az egyéni parancsok csak a westus-, westus2-és neur-régiókban lévő beszédfelismerési előfizetéseket támogatják.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Ugrás a Speech Studio egyéni parancsaihoz

1. A böngészőben nyissa meg a [Speech studiót](https://speech.microsoft.com/).
1. Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez.

   Az alapértelmezett nézet a beszédfelismerési előfizetések listája.
    > [!NOTE]
    > Ha nem látja az előfizetés kiválasztása oldalt, a képernyő felső részén található beállítások menüben kiválaszthatja a **beszédfelismerési erőforrások** lehetőséget.

1. Válassza ki a beszédfelismerési előfizetést, majd válassza a **Ugrás a studióba**lehetőséget.
1. Válassza az **egyéni parancsok**lehetőséget.

     Az alapértelmezett nézet a kijelölt előfizetésben található egyéni parancsok alkalmazásának listája.

## <a name="create-a-custom-commands-project"></a>Egyéni parancsok projekt létrehozása

1. Projekt létrehozásához válassza az **új projekt** lehetőséget.

   > [!div class="mx-imgBorder"]
   > ![Projekt létrehozása](media/custom-speech-commands/create-new-project.png)

1. A **név** mezőbe írja be a projekt nevét.
1. A **nyelv** listából válassza ki a kívánt nyelvet.
1. A **Luis authoring Resource** listán válasszon ki egy authoring-erőforrást. Ha nincsenek érvényes authoring-erőforrások, hozzon létre egyet az **új Luis authoring-erőforrás létrehozása**lehetőség kiválasztásával.

   > [!div class="mx-imgBorder"]
   > ![Erőforrás létrehozása](media/custom-speech-commands/create-new-resource.png)

   1. Az **Erőforrás neve** mezőbe írja be az erőforrás nevét.
   1. Az **erőforráscsoport** listában válasszon ki egy erőforráscsoportot.
   1. A **hely** listában válasszon ki egy helyet.
   1. A **díjszabási** csomag listáról válassza ki a kívánt szintet.

      > [!NOTE]
      > Hozzon létre egy erőforráscsoportot egy erőforráscsoport-név megadásával az **erőforráscsoport** mezőben. Az erőforráscsoport a **Létrehozás**gombra kattintva lesz létrehozva.

1. Kattintson a **Létrehozás** gombra.
1. A projekt létrehozása után válassza ki azt.

    Ekkor megjelenik az új egyéni parancsok alkalmazás áttekintése.

## <a name="update-luis-resources-optional"></a>LUIS-erőforrások frissítése (nem kötelező)

Frissítheti az **új projekt** ablakban kiválasztott szerzői erőforrást, és megadhat egy előrejelzési erőforrást. Az előrejelzési erőforrás az egyéni parancsok alkalmazás közzétételekor való felismerésre szolgál. A fejlesztési és tesztelési fázisokban nincs szükség előrejelzési erőforrásra.

1. A bal oldali ablaktáblán válassza a **Beállítások** lehetőséget, majd a középső ablaktáblán válassza a **Luis-erőforrások** lehetőséget.
1. Válasszon előrejelzési erőforrást, vagy hozzon létre egyet az **új erőforrás létrehozása**lehetőség kiválasztásával.
1. Kattintson a **Mentés** gombra.
    
    > [!div class="mx-imgBorder"]
    > ![LUIS-erőforrások beállítása](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> Mivel a szerzői erőforrás csak a 1 000-es előrejelzési végponti kérelmeket támogatja havonta, meg kell adnia egy LUIS előrejelzési erőforrást, mielőtt közzéteszi az egyéni parancsok alkalmazását.


## <a name="create-a-command"></a>Parancs létrehozása

Hozzunk létre egy egyszerű parancsot, amely egyetlen Kimondás után `turn on the tv` válaszol az üzenetre `Ok, turning on the tv` .

1. Hozzon létre egy parancsot a bal oldali ablaktábla tetején található **új parancs** kiválasztásával. Megnyílik az **új parancssori** ablak.
1. A név mezőbe írja be a **TurnOn** **nevet** .
1. Kattintson a **Létrehozás** gombra.

A középső ablaktábla a parancs tulajdonságait sorolja fel:


| Konfiguráció            | Description                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| **Példa mondatokra** | Példák arra, hosszúságú kimondott szöveg, hogy a felhasználó elmondja a parancs aktiválását.                                                                 |
| **Paraméterek**       | A parancs végrehajtásához szükséges információk.                                                                                |
| **Befejezési szabályok** | A parancs teljesítéséhez végrehajtandó műveletek. Például válaszolhat a felhasználóra, vagy kommunikálhat egy másik webszolgáltatással. |
| **Interakciós szabályok**   | További szabályok a konkrétabb vagy összetett helyzetek kezelésére.                                                              |


> [!div class="mx-imgBorder"]
> ![Parancs létrehozása](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>Példa mondatok hozzáadása

Kezdjük **például a mondatok** szakaszát. Példát mutatunk arra, hogy mit tehet a felhasználó.

1. Válassza ki **például a mondatokat** a középső ablaktáblán. 
1. A jobb oldali ablaktáblán adjon hozzá példákat:

    ```
    turn on the tv
    ```

1. Válassza a **Mentés** lehetőséget a panel tetején.

Egyelőre nem rendelkezünk paraméterekkel, így átléphetünk a **befejezési szabályok** szakaszra.

### <a name="add-a-completion-rule"></a>Befejezési szabály hozzáadása

Most adjon hozzá egy befejezési szabályt, amely a következő konfigurációval rendelkezik. Ez a szabály azt jelzi, hogy a felhasználó teljesíti a teljesítési műveletet.


| Beállítás    | Ajánlott érték                          | Description                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| **Név**  | **ConfirmationResponse**                  | A szabály célját leíró név.          |
| **Feltételek** | Nincs                                     | Azok a feltételek, amelyek meghatározzák, hogy mikor fusson a szabály.    |
| **Műveletek**    | **Beszédfelismerési válasz küldése – > ok, a TV bekapcsolása** | A szabály feltételének teljesülésekor végrehajtandó művelet. |

1. Hozzon létre egy új befejezési szabályt a középső ablaktábla tetején található **Hozzáadás gombra** kattintva.
1. A **Név** mezőben adja meg a megfelelő nevet.
1. Adjon hozzá egy műveletet.
   1. Hozzon létre egy műveletet a **műveletek** szakaszban a **művelet hozzáadása lehetőség** kiválasztásával.
   1. A **művelet szerkesztése** ablak **típus** listájában válassza a **beszédfelismerési válasz küldése**lehetőséget.
   1. A **Válasz**területen válassza az **egyszerű szerkesztő**lehetőséget. Az **első változat** mezőben adja meg az **OK gombot, és kapcsolja be a TV**-t.

   > [!div class="mx-imgBorder"]
   > ![Válasz létrehozása](media/custom-speech-commands/create-speech-response-action.png)

1. A szabály mentéséhez válassza a **Mentés** lehetőséget.
1. A **befejezési szabályok** szakaszban kattintson a **Mentés** gombra a módosítások mentéséhez. 

> [!div class="mx-imgBorder"]
> ![Befejezési szabály létrehozása](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>Próba

Tesztelje a viselkedést a test chat panel használatával.
1. A jobb oldali ablaktábla tetején válassza a **betanítás** lehetőséget.
1. A betanítás befejezése után válassza a **teszt**lehetőséget. Megjelenik egy új **teszt az alkalmazás** ablakában.
    - Adja meg **a TV bekapcsolását**
    - Várt válasz: **OK, bekapcsolás a TV-** re


> [!div class="mx-imgBorder"]
> ![A viselkedés tesztelése](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Gyors útmutató: egyéni parancsok előzetes alkalmazásának létrehozása paraméterekkel](./quickstart-custom-speech-commands-create-parameters.md)
