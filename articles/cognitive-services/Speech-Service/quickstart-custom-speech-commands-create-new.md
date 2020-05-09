---
title: 'Gyors útmutató: Egyéni parancs létrehozása (előzetes verzió) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben egy üzemeltetett egyéni parancsok alkalmazását hozza létre és teszteli.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 04/30/2020
ms.author: donkim
ms.openlocfilehash: f31d7279b73bab7aefda4c4b6570500d05cb89d7
ms.sourcegitcommit: f57297af0ea729ab76081c98da2243d6b1f6fa63
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/06/2020
ms.locfileid: "82872518"
---
# <a name="quickstart-create-a-custom-commands-app-preview"></a>Gyors útmutató: egyéni parancsok alkalmazás létrehozása (előzetes verzió)

Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és tesztelheti az egyéni parancsok alkalmazását.
A létrehozott alkalmazás olyan hosszúságú kimondott szöveg dolgoz fel, mint a "TV bekapcsolása", és a válasz egy egyszerű üzenettel "ok, a TV bekapcsolása".

## <a name="prerequisites"></a>Előfeltételek

> [!div class="checklist"]
> * <a href="https://ms.portal.azure.com/#create/Microsoft.CognitiveServicesSpeechServices" target="_blank">Azure Speech-erőforrás létrehozása<span class="docon docon-navigate-external x-hidden-focus"></span></a>

  > [!NOTE]
  > Jelenleg az egyéni parancsok csak a westus-, westus2-és neur-régiókban lévő Speech-előfizetéseket támogatják.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Ugrás a Speech Studio egyéni parancsaihoz

1. Nyissa meg a webböngészőt, és navigáljon a [Speech studióhoz](https://speech.microsoft.com/)
1. Adja meg a hitelesítő adatait a Portalra való bejelentkezéshez.

   - Az alapértelmezett nézet a beszédfelismerési előfizetések listája.
     > [!NOTE]
     > Ha nem jelenik meg az előfizetés kiválasztása lap, akkor a felső sáv beállítások menüjében válassza a "Speech Resources" (beszédfelismerési erőforrások) lehetőséget.

1. Válassza ki a beszédfelismerési előfizetést, majd válassza a **Ugrás a studióba**lehetőséget.
1. Válassza az **egyéni parancsok**lehetőséget.

     - Az alapértelmezett nézet a kiválasztott előfizetéshez tartozó egyéni parancsok alkalmazásának listája.

## <a name="create-a-custom-commands-project"></a>Egyéni parancsok projekt létrehozása

1. Új projekt létrehozásához válassza az **új projekt** lehetőséget.

   > [!div class="mx-imgBorder"]
   > ![Projekt létrehozása](media/custom-speech-commands/create-new-project.png)

1. Adja meg a projekt nevét.
1. Válassza a nyelv lehetőséget a legördülő menüből.
1. Válasszon ki egy authoring-erőforrást a legördülő menüből. Ha nincsenek érvényes authoring-erőforrások, hozzon létre egyet az **új Luis authoring Resource (létrehozás**) gombra kattintva.

   > [!div class="mx-imgBorder"]
   > ![Erőforrás létrehozása](media/custom-speech-commands/create-new-resource.png)

   - Adja meg az erőforrás nevét és az erőforráscsoportot.
   - A legördülő listából válassza a hely és az árképzési réteg értékét.

      > [!NOTE]
      > Az erőforráscsoportok létrehozásához írja be a kívánt erőforráscsoport nevét az "erőforráscsoport" mezőbe. Az erőforráscsoport akkor jön létre, amikor a **Létrehozás** beállítás van kiválasztva.

1. Ezután válassza a **Létrehozás** lehetőséget a projekt létrehozásához.
1. A létrehozást követően válassza ki a projektet.

    - A nézetnek most az újonnan létrehozott egyéni parancsok alkalmazás áttekintését kell áttekintenie.

## <a name="update-luis-resources-optional"></a>LUIS-erőforrások frissítése (nem kötelező)

Frissítheti az új projekt ablakban beállított szerzői erőforrást, és beállíthatja az előrejelzési erőforrásokat. A prediktív erőforrás az egyéni parancsok alkalmazás közzétételekor való felismerésre szolgál. Nincs szüksége előrejelzési erőforrásra a fejlesztési és tesztelési fázisokhoz.

1. A bal oldali ablaktáblán válassza a **Beállítások** lehetőséget, majd a középső ablaktáblán navigáljon a **Luis-erőforrások** szakaszhoz.
1. Válasszon előrejelzési erőforrást, vagy hozzon létre egyet az **új erőforrás létrehozása**lehetőség kiválasztásával.
1. Kattintson a **Mentés** gombra.
    
    > [!div class="mx-imgBorder"]
    > ![LUIS-erőforrások beállítása](media/custom-speech-commands/set-luis-resources.png)


> [!NOTE]
> Mivel a szerzői erőforrás csak 1 000 előrejelzési végponti kérést támogat havonta, kötelezően kell beállítania a LUIS előrejelzési erőforrást az egyéni parancsok alkalmazásának közzététele előtt.


## <a name="create-a-new-command"></a>Új parancs létrehozása

Hozzunk létre egy egyszerű parancsot, amely egyetlen Kimondás `turn on the tv`után válaszol az üzenetre. `Ok, turning on the tv`

1. Hozzon létre egy új parancsot a `+ New command` bal szélső ablaktábla tetején található ikon kiválasztásával. Egy új előugró ablak jelenik meg az **új parancs**címmel.
1. Adja meg a **név** mező értékét a `TurnOn`következőként:.
1. Kattintson a **Létrehozás** gombra.

A középső ablaktábla a parancs különböző tulajdonságait veszi igénybe:


| Konfiguráció            | Leírás                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Példa mondatokra | Példa arra, hosszúságú kimondott szöveg, hogy a felhasználó el tudja indítani ezt a parancsot                                                                 |
| Paraméterek       | A parancs végrehajtásához szükséges információk                                                                                |
| Befejezési szabályok | A parancs teljesítéséhez végrehajtandó műveletek. Például a felhasználónak való válaszadásra vagy egy másik webszolgáltatással való kommunikációra. |
| Interakciós szabályok   | További szabályok a konkrétabb vagy összetett helyzetek kezelésére                                                              |


> [!div class="mx-imgBorder"]
> ![Parancs létrehozása](media/custom-speech-commands/create-add-command.png)


### <a name="add-example-sentences"></a>Példa mondatok hozzáadása

Kezdjük például a mondatok szakaszát, és példát arra, hogy mit tehet a felhasználó.

1. Válassza a középső ablaktábla **példa mondatok** szakaszát, és a jobb oldali ablaktáblán adja meg a példákat:

    ```
    turn on the tv
    ```

1. Válassza `Save` ki az ikont a panel tetején.

Egyelőre nem rendelkezünk paraméterekkel, így a **befejezési szabályok** szakaszra léphetünk.

### <a name="add-a-completion-rule"></a>Befejezési szabály hozzáadása

Most adjon hozzá egy befejezési szabályt a következő konfigurációval. Ez a szabály azt jelzi, hogy a felhasználó egy teljesítési műveletet hajt végre.


| Beállítás    | Ajánlott érték                          | Leírás                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Szabály neve  | ConfirmationResponse                  | A szabály célját leíró név          |
| Feltételek | Nincs                                     | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |
| Műveletek    | SpeechResponse "– ok, bekapcsolás a tévén" | A szabály feltételének teljesülésekor végrehajtandó művelet |

1. Hozzon létre egy új befejezési szabályt `+Add` a középső ablaktábla tetején látható ikon kiválasztásával.
1. Adjon meg értéket a **Name (név** ) szakaszban.
1. Művelet hozzáadása
   1. Hozzon létre egy új műveletet a **műveletek** szakaszban a **+ művelet hozzáadása lehetőség** kiválasztásával.
   1. Az **új művelet** előugró ablakában válassza `Send speech response` a **típus**legördülő menüből a lehetőséget.
   1. Válassza `Simple editor` a **Válasz** mezőt.
       - Az **első változat** mezőben adja meg a válasz értékét`Ok, turning on the tv`

   > [!div class="mx-imgBorder"]
   > ![Beszédfelismerési válasz létrehozása](media/custom-speech-commands/create-speech-response-action.png)

1. A szabály mentéséhez kattintson a **Save (Mentés** ) gombra.
1. A **befejezési szabályok** szakaszban kattintson a **Mentés** gombra a módosítások mentéséhez. 

> [!div class="mx-imgBorder"]
> ![Befejezési szabály létrehozása](media/custom-speech-commands/create-basic-completion-response-rule.png)



## <a name="try-it-out"></a>Próba

A viselkedés tesztelése a csevegési teszt panel használatával
1. Válassza `Train` a jobb oldali ablaktábla tetején található ikont.
1. Ha a képzés befejeződik, válassza `Test`a lehetőséget. Ekkor megjelenik egy új **teszt az alkalmazás** ablakában.
    - Írja be a következőt: TV bekapcsolása
    - Várt válasz: ok, bekapcsolás a TV-re


> [!div class="mx-imgBorder"]
> ![Tesztelés webes csevegéssel](media/custom-speech-commands/create-basic-test-chat.png)

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Gyors útmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió)](./quickstart-custom-speech-commands-create-parameters.md)
