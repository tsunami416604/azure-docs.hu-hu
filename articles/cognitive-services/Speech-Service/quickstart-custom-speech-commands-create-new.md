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
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 4ae8f13b4887bbc41b17defa3f9a20c07ed0cb45
ms.sourcegitcommit: 58faa9fcbd62f3ac37ff0a65ab9357a01051a64f
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/29/2020
ms.locfileid: "76155587"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Gyors útmutató: Egyéni parancs létrehozása (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan hozhat létre és tesztelheti a futtatott egyéni parancsok alkalmazását.
Az alkalmazás felismeri a "TV bekapcsolása" kifejezést, és egy egyszerű üzenettel válaszol, amely "ok, bekapcsolja a TV-t".

## <a name="prerequisites"></a>Előfeltételek

- Egy beszédfelismerési előfizetés.

Ha nem rendelkezik beszédfelismerési előfizetéssel, létrehozhat egyet a [Speech Studióval](https://speech.microsoft.com/) , és kiválaszthatja **a beszédfelismerési erőforrás létrehozása**lehetőséget.

  > [!div class="mx-imgBorder"]
  > [![Projekt](media/custom-speech-commands/create-new-subscription.png) létrehozása](media/custom-speech-commands/create-new-subscription.png#lightbox)

  > [!NOTE]
  > Az előzetes verzióban csak a westus2 régió támogatott.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>Ugrás a Speech Studio egyéni parancsaihoz

1. Nyissa meg a webböngészőt, és navigáljon a [Speech studióhoz](https://speech.microsoft.com/)
1. Adja meg a hitelesítő adatait a portálra való bejelentkezéshez

   - Az alapértelmezett nézet a beszédfelismerési előfizetések listája
     > [!NOTE]
     > Ha nem jelenik meg az előfizetés kiválasztása lap, akkor a felső sáv beállítások menüjében válassza a "Speech Resources" (beszédfelismerési erőforrások) lehetőséget.

1. Válassza ki a beszédfelismerési előfizetést, majd válassza a **Ugrás a studióhoz** lehetőséget.
1. **Egyéni parancsok kiválasztása (előzetes verzió)**

Az alapértelmezett nézet az egyéni parancsok által létrehozott alkalmazások listája.

## <a name="create-a-custom-commands-project"></a>Egyéni parancsok projekt létrehozása

1. Új projekt létrehozásához válassza az **új projekt** lehetőséget

   > [!div class="mx-imgBorder"]
   > ![Projekt létrehozása](media/custom-speech-commands/create-new-project.png)

1. Adja meg a projekt nevét és nyelvét.
1. Válasszon authoring-erőforrást. Ha nincsenek érvényes authoring-erőforrások, hozzon létre egyet az **új erőforrás létrehozása**lehetőség kiválasztásával.

   > [!div class="mx-imgBorder"]
   > ![Erőforrás létrehozása](media/custom-speech-commands/create-new-resource.png)

   1. Adja meg az erőforrás nevét, a csoportot, a helyet és az árképzési szintet.

         > [!NOTE]
         > Az erőforráscsoportok létrehozásához írja be a kívánt erőforráscsoport nevét az "erőforráscsoport" mezőbe. Az erőforráscsoport akkor jön létre, amikor a **Létrehozás** beállítás van kiválasztva.

1. A projekt létrehozásához kattintson a **Létrehozás** gombra.
1. A létrehozást követően válassza ki a projektet.

A nézetnek most az egyéni parancsok alkalmazás áttekintését kell használnia.

## <a name="update-luis-resources-optional"></a>LUIS-erőforrások frissítése (nem kötelező)

Frissítheti a szerzői erőforrás készletét az új projekt ablakban, és beállíthat egy előrejelzési erőforrást, amely a bemenetek felismeréséhez használatos a futtatókörnyezetben.

> [!NOTE]
> Meg kell adnia egy előrejelzési erőforrást, mielőtt az alkalmazás megkezdi az előrejelzések előrejelzését az authoring Resource által biztosított 1 000-kérelmek után.

> [!div class="mx-imgBorder"]
> ![LUIS-erőforrások beállítása](media/custom-speech-commands/set-luis-resources.png)

1. Navigáljon a LUIS-erőforrások ablaktáblára a bal oldali ablaktábla **Beállítások** elemének kiválasztásával, majd a középső ablaktáblán található **Luis-erőforrásokkal** .
1. Válasszon előrejelzési erőforrást, vagy hozzon létre egyet az **új erőforrás létrehozása** lehetőség kiválasztásával.
1. **Mentés** kiválasztása

## <a name="create-a-new-command"></a>Új parancs létrehozása

Most létrehozhat egy parancsot. Lássunk egy példát, amely egyetlen Kimondás `turn on the tv`után válaszol az üzenetre. `Ok, turning on the TV`

1. Hozzon létre egy új parancsot a `+` parancsok melletti ikonra kattintva, és adja meg a nevet`TurnOn`
1. **Mentés** kiválasztása

> [!div class="mx-imgBorder"]
> ![Parancs létrehozása](media/custom-speech-commands/create-add-command.png)

A parancsok a következőket foglalják magukban:

| Csoport            | Leírás                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Példák a mondatokra | Példa arra, hosszúságú kimondott szöveg, hogy a felhasználó el tudja indítani ezt a parancsot                                                                 |
| Paraméterek       | A parancs végrehajtásához szükséges információk                                                                                |
| Befejezési szabályok | A parancs teljesítéséhez végrehajtandó műveletek. Például a felhasználónak való válaszadásra vagy egy másik webszolgáltatással való kommunikációra |
| Speciális szabályok   | További szabályok a konkrétabb vagy összetett helyzetek kezelésére                                                              |

### <a name="add-a-sample-sentence"></a>Minta mondat hozzáadása

Kezdjük a minta mondatokkal, és példát arra, hogy mit tehet a felhasználó:

```
turn on the tv
```

Egyelőre nem rendelkezünk paraméterekkel, így a befejezési szabályokra lehet lépni.

### <a name="add-a-completion-rule"></a>Befejezési szabály hozzáadása

Most adjon hozzá egy befejezési szabályt, amely arra figyelmezteti a felhasználót, hogy műveletet hajt végre.

1. Hozzon létre egy új befejezési szabályt `+` a befejezési szabályok melletti ikonra kattintva.
1. Adja meg a szabály nevét
1. Művelet hozzáadása
   1. Hozzon létre egy új beszédfelismerési műveletet a `+` műveletek melletti ikonra kattintva, majd válassza a`SpeechResponse`
   1. Adja meg a választ

   > [!NOTE]
   > A normál szövegnek kötőjeltel kell kezdődnie. További részletekért látogasson el [ide](https://aka.ms/sc-lg-format)

   > [!div class="mx-imgBorder"]
   > ![Beszédfelismerési válasz létrehozása](media/custom-speech-commands/create-speech-response-action.png)

1. A szabály mentéséhez kattintson a **Save (Mentés** ) gombra.

> [!div class="mx-imgBorder"]
> ![Befejezési szabály létrehozása](media/custom-speech-commands/create-basic-completion-response-rule.png)

| Beállítás    | Ajánlott érték                          | Leírás                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Szabály neve  | "ConfirmationResponse"                   | A szabály célját leíró név          |
| Feltételek | None                                     | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |
| Műveletek    | SpeechResponse "– ok, bekapcsolás a TÉVÉn" | A szabály feltételének teljesülésekor végrehajtandó művelet |

## <a name="try-it-out"></a>Próba

Tesztelje a viselkedést a test chat panel használatával.

> [!div class="mx-imgBorder"]
> ![Tesztelés webes csevegéssel](media/custom-speech-commands/create-basic-test-chat.png)

- Gépelje be a következőt: "a TV bekapcsolása"
- Várt válasz: "ok, bekapcsolás a televízióban"

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Gyors útmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió)](./quickstart-custom-speech-commands-create-parameters.md)
