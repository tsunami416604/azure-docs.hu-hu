---
title: 'Rövid útmutató: Egyéni parancs létrehozása (előzetes verzió) – Beszédszolgáltatás'
titleSuffix: Azure Cognitive Services
description: Ebben a cikkben hozzon létre és teszteljen egy üzemeltetett egyéni parancsok alkalmazást.
services: cognitive-services
author: don-d-kim
manager: yetian
ms.service: cognitive-services
ms.subservice: speech-service
ms.topic: conceptual
ms.date: 12/09/2019
ms.author: donkim
ms.openlocfilehash: 4ae8f13b4887bbc41b17defa3f9a20c07ed0cb45
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "76155587"
---
# <a name="quickstart-create-a-custom-command-preview"></a>Rövid útmutató: Egyéni parancs létrehozása (előzetes verzió)

Ebből a cikkből megtudhatja, hogyan hozhat létre és tesztelheti a hosztolt egyéni parancsok alkalmazást.
Az alkalmazás felismeri a kimondott szöveg, mint a "kapcsolja be a tv", és válaszoljon egy egyszerű üzenet "Ok, bekapcsolja a tv".

## <a name="prerequisites"></a>Előfeltételek

- Beszéd-előfizetés.

Ha nem rendelkezik beszédalapú előfizetéssel, létrehozhat egyet, ha a [Beszédstúdióba](https://speech.microsoft.com/) navigál, és a **Beszédfelismerési erőforrás létrehozása**lehetőséget választja.

  > [!div class="mx-imgBorder"]
  > [![Projekt](media/custom-speech-commands/create-new-subscription.png) létrehozása](media/custom-speech-commands/create-new-subscription.png#lightbox)

  > [!NOTE]
  > Az előzetes verzió ban csak a westus2 régió támogatott.

## <a name="go-to-the-speech-studio-for-custom-commands"></a>A Beszédstúdió egyéni parancsokra való felolvasása

1. Nyissa meg a webböngészőt, és keresse meg a [Beszédstúdiót](https://speech.microsoft.com/)
1. Adja meg hitelesítő adatait a portálra való bejelentkezéshez

   - Az alapértelmezett nézet a beszédfelismerési előfizetések listája
     > [!NOTE]
     > Ha nem látja a select subscription lapot, a felső sáv beállítások menüjének "Beszédforrások" parancsával navigálhat ott.

1. Válassza ki a Beszédfelismerés-előfizetést, majd válassza **az Ugrás a Stúdióba** lehetőséget.
1. **Egyéni parancsok kijelölése (előnézet)**

Az alapértelmezett nézet a létrehozott egyéni parancsok alkalmazásainak listája.

## <a name="create-a-custom-commands-project"></a>Egyéni parancsok projekt létrehozása

1. Új projekt létrehozásához válassza az **Új projekt** lehetőséget

   > [!div class="mx-imgBorder"]
   > ![Projekt létrehozása](media/custom-speech-commands/create-new-project.png)

1. Adja meg a projekt nevét és nyelvét.
1. Jelöljön ki egy szerzői erőforrást. Ha nincsenek érvényes szerzői erőforrások, hozzon létre egyet az **Új erőforrás létrehozása**lehetőséget választva.

   > [!div class="mx-imgBorder"]
   > ![Erőforrás létrehozása](media/custom-speech-commands/create-new-resource.png)

   1. Adja meg az erőforrás nevét, csoportját, helyét és tarifacsomagát.

         > [!NOTE]
         > Erőforráscsoportokat úgy hozhat létre, hogy beírja a kívánt erőforráscsoport nevét az "Erőforráscsoport" mezőbe. Az erőforráscsoport a **Létrehozás jelölőnégyzet bekerülésekénként** jön létre.

1. A projekt létrehozásához kattintson a **Létrehozás** gombra.
1. A létrehozás után válassza ki a projektet.

A nézetnek most antól áttekintést kell adnia az Egyéni parancsok alkalmazásról.

## <a name="update-luis-resources-optional"></a>LUIS-erőforrások frissítése (nem kötelező)

Frissítheti a szerzői erőforrás-készletet az új projektablakban, és beállíthat egy előrejelzési erőforrást, amely a bemenetek futásidőben történő felismeréséhez használható.

> [!NOTE]
> Be kell állítania egy előrejelzési erőforrást, mielőtt az alkalmazás a szerzői erőforrás által biztosított 1000 kérelemen túli előrejelzéseket kér.

> [!div class="mx-imgBorder"]
> ![LUIS-erőforrások beállítása](media/custom-speech-commands/set-luis-resources.png)

1. Keresse meg a LUIS-erőforrások ablaktáblát a bal oldali ablaktáblában a **Beállítások,** majd a középső ablaktáblán a **LUIS-erőforrások** elemre.
1. Jelöljön ki egy előrejelzési erőforrást, vagy hozzon létre egyet az **Új erőforrás létrehozása** lehetőség kiválasztásával
1. **Mentés** kiválasztása

## <a name="create-a-new-command"></a>Új parancs létrehozása

Most már létrehozhat egy parancsot. Használjunk egy példát, amely egyetlen kimondott szöveget vesz fel, `turn on the tv`és válaszoljon az üzenettel. `Ok, turning on the TV`

1. Új parancs létrehozása a `+` parancsok melletti ikon kiválasztásával, és adja meg a nevet`TurnOn`
1. **Mentés** kiválasztása

> [!div class="mx-imgBorder"]
> ![Parancs létrehozása](media/custom-speech-commands/create-add-command.png)

A parancs a következők ből áll:

| Csoport            | Leírás                                                                                                                 |
| ---------------- | --------------------------------------------------------------------------------------------------------------------------- |
| Minta mondatok | Példa utterances a felhasználó azt mondja, hogy indítsa el ezt a parancsot                                                                 |
| Paraméterek       | A parancs teljesítéséhez szükséges információk                                                                                |
| Befejezési szabályok | A parancs teljesítése érdekében meghozandó intézkedések. Például a felhasználónak való válaszadás vagy egy másik webszolgáltatással való kommunikáció |
| Speciális szabályok   | További szabályok a konkrétabb vagy összetettebb helyzetek kezelésére                                                              |

### <a name="add-a-sample-sentence"></a>Mintamondat hozzáadása

Kezdjük a mintamondatokkal, és mutassunk példát arra, hogy mit mondhat a felhasználó:

```
turn on the tv
```

Egyelőre nincsenek paramétereink, így továbbléphetünk a befejezési szabályokra.

### <a name="add-a-completion-rule"></a>Befejezési szabály hozzáadása

Most adjon hozzá egy befejezési szabályt, hogy válaszoljon a felhasználónak, jelezve, hogy egy művelet folyamatban van.

1. Új befejezési szabály létrehozása `+` a Befejezési szabályok melletti ikon kiválasztásával
1. A szabály nevének megadása
1. Művelet hozzáadása
   1. Új beszéd-válaszművelet létrehozása `+` a Műveletek ikonra kattintva, és válassza a`SpeechResponse`
   1. Adja meg a választ

   > [!NOTE]
   > A normál szövegnek kötőjellel kell kezdődnie. További részletekért [látogasson](https://aka.ms/sc-lg-format) el ide

   > [!div class="mx-imgBorder"]
   > ![Beszédválasz létrehozása](media/custom-speech-commands/create-speech-response-action.png)

1. A szabály mentéséhez kattintson a **Mentés** gombra

> [!div class="mx-imgBorder"]
> ![Befejezési szabály létrehozása](media/custom-speech-commands/create-basic-completion-response-rule.png)

| Beállítás    | Ajánlott érték                          | Leírás                                        |
| ---------- | ---------------------------------------- | -------------------------------------------------- |
| Szabály neve  | "ConfirmationResponse"                   | A szabály célját leíró név          |
| Feltételek | None                                     | Feltételek, amelyek meghatározzák, hogy a szabály mikor futhat    |
| Műveletek    | SpeechResponse "- Ok, a TV bekapcsolása" | A szabályfeltétel igaz állapotában végrehajtandó művelet |

## <a name="try-it-out"></a>Próba

Tesztelje a viselkedést a Csevegés tesztelése panelen.

> [!div class="mx-imgBorder"]
> ![Tesztelés webes csevegéssel](media/custom-speech-commands/create-basic-test-chat.png)

- Írja be: "kapcsolja be a tv"-
- Várható válasz: "Ok, a tv bekapcsolása"

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Rövid útmutató: Egyéni parancs létrehozása paraméterekkel (előzetes verzió)](./quickstart-custom-speech-commands-create-parameters.md)
