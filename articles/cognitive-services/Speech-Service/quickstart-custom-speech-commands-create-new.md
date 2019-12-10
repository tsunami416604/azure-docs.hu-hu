---
title: 'Gyors útmutató: Egyéni parancs létrehozása (előzetes verzió) – beszédfelismerési szolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben egy üzemeltetett egyéni parancsok alkalmazását hozza létre és teszteli.
services: cognitive-services
author: donkim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 10/09/2019
ms.author: donkim
ms.openlocfilehash: 081327e7c70128dad764d66942f1703a889f77fd
ms.sourcegitcommit: 5b9287976617f51d7ff9f8693c30f468b47c2141
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/09/2019
ms.locfileid: "74951232"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Gyors útmutató: Egyéni parancs létrehozása (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan hozhat létre és tesztelheti a futtatott egyéni parancsok alkalmazását.
Az alkalmazás felismeri a "TV bekapcsolása" kifejezést, és egy egyszerű üzenettel válaszol, amely "ok, bekapcsolja a TV-t".

## <a name="prerequisites"></a>Előfeltételek

- Egy beszédfelismerési előfizetés. [Próbálja ki ingyenesen a Speech szolgáltatást](~/articles/cognitive-services/speech-service/get-started.md).

  > [!NOTE]
  > Az előzetes verzióban csak az westus2 régió támogatott az előfizetési kulcsok esetében.

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
   > ![hozzon létre egy projektet](media/custom-speech-commands/create-new-project.png)

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

## <a name="create-a-new-command"></a>Új parancs létrehozása

Most létrehozhat egy parancsot. Tegyük fel, hogy olyan példát használ, amely egyetlen Kimondás, `turn on the tv`és a `Ok, turning on the TV`üzenettel válaszol.

1. Hozzon létre egy új parancsot a parancsok melletti `+` ikon kiválasztásával, és adja meg a nevet `TurnOn`
1. Kattintson a **Mentés** gombra

> [!div class="mx-imgBorder"]
> ![parancs létrehozása](media/custom-speech-commands/create-add-command.png)

A parancsok a következőket foglalják magukban:

| Csoport            | Leírás                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Példamondatok | Példa arra, hosszúságú kimondott szöveg, hogy a felhasználó el tudja indítani ezt a parancsot                                                                 |
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

1. Hozzon létre egy új befejezési szabályt a befejezési szabályok melletti `+` ikon kiválasztásával
1. Adja meg a szabály nevét
1. Művelet hozzáadása
   1. Hozzon létre egy új beszédfelismerési műveletet a műveletek melletti `+` ikon kiválasztásával, majd válassza a `SpeechResponse`
   1. Adja meg a választ

   > [!NOTE]
   > A normál szövegnek kötőjeltel kell kezdődnie. További részletekért látogasson el [ide](https://aka.ms/sc-lg-format)

   > [!div class="mx-imgBorder"]
   > ![beszédfelismerési válasz létrehozása](media/custom-speech-commands/create-speech-response-action.png)

1. A szabály mentéséhez kattintson a **Save (Mentés** ) gombra.

> [!div class="mx-imgBorder"]
> ![befejezési szabály létrehozása](media/custom-speech-commands/create-basic-completion-response-rule.png)


| Beállítás    | Ajánlott érték                        | Leírás                                        |
| ---------- | -------------------------------------- | -------------------------------------------------- |
| Szabály neve  | "ConfirmationResponse"                 | A szabály célját leíró név          |
| Feltételek | None                                   | Feltételek, amelyek meghatározzák, hogy a szabály futtatható-e    |
| Műveletek    | SpeechResponse "– ok, bekapcsolás a TÉVÉn" | A szabály feltételének teljesülésekor végrehajtandó művelet |

## <a name="try-it-out"></a>Próbálja ki!

Tesztelje a viselkedést a test chat panel használatával.

> [!div class="mx-imgBorder"]
> ![tesztelése a webes csevegési](media/custom-speech-commands/create-basic-test-chat.png)

- Gépelje be a következőt: "a TV bekapcsolása"
- Várt válasz: "ok, bekapcsolás a televízióban"

## <a name="next-steps"></a>Következő lépések

> [!div class="nextstepaction"]
> [Gyors útmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió)](./quickstart-custom-speech-commands-create-parameters.md)
