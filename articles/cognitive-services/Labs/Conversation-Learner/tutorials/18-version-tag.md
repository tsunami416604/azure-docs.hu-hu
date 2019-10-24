---
title: A verzió címkézésének használata Conversation Learner modellel – Azure Cognitive Services | Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan használhatja a verziószámozást és a címkézést egy Conversation Learner modellel.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: 0279363c039e3ec3c2deac3bc7f71c32c547e9d1
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68703384"
---
# <a name="how-to-use-version-tagging"></a>A verziók címkézésének használata

Ez az oktatóanyag bemutatja, hogyan címkézheti Conversation Learner modelljét, és hogyan állíthatja be az "élő" verziót.  

## <a name="requirements"></a>Követelmények
Ehhez az oktatóanyaghoz a bot Framework Emulatort kell használnia a log párbeszédpanelek létrehozásához, nem pedig a webes felhasználói felületet.  

Ehhez az oktatóanyaghoz az általános oktatóanyag robotjának futtatására van szükség:

    npm run tutorial-general

## <a name="details"></a>Részletek

A modell címkézett verziói statikusak; nem szerkesztheti és nem módosíthatja azokat. A modell szerkesztésekor mindig a fő verziót szerkeszti. Új címke hozzáadásakor Conversation Learner rögzíti a modell pillanatfelvételét az adott időpontban. 

A robot az "élő" verzióként kiválasztott modell verzióját fogja használni, de az összes beszélgetést csak akkor fogja megtekinteni, ha a "szerkesztési címke" a "Master" értékre van állítva. Ha a modell "szerkesztési címke" tulajdonsága "Master" értékre van állítva, akkor megtekintheti a modell pillanatképét, de semmilyen módon nem módosítható.

## <a name="steps"></a>Lépések

### <a name="install-the-bot-framework-emulator"></a>A bot Framework Emulator telepítése

1. Nyissa meg a következőt: [https://github.com/Microsoft/BotFramework-Emulator](https://github.com/Microsoft/BotFramework-Emulator).
2. Töltse le és telepítse az emulátort.

### <a name="create-a-model"></a>Modell létrehozása

1. A modell-lista kezdőlapján kattintson a `New Model` gombra.
2. A mezőbe `Name` írja be a "tutorial-18-Versioning" kifejezést, és nyomja meg az ENTER billentyűt.
4. A bal oldali panelen kattintson a "beállítások" elemre.
5. Másolja a CONVERSATION_LEARNER_MODEL_ID mező tartalmát a vágólapra.

### <a name="configure-the-emulator"></a>Az emulátor konfigurálása

1. A Conversation Learner gyökérkönyvtárában nyissa meg a ". env" fájlt.
2. Adjon hozzá egy sort a ". env" fájlhoz, például:
    - `CONVERSATION_LEARNER_MODEL_ID=[paste-model-id-from-clipboard-here]`
3. Indítsa újra a Conversation Learner szolgáltatást a parancssorból való kilépéssel, majd futtassa újra a következőt:
    - `npm run tutorial-general`
4. A bot Framework Emulatorban hozzon létre egy új robot-konfigurációt, állítsa be a végpont URL-címét a következőre:`http://localhost:3978/api/messages`

### <a name="version-1"></a>1\. verzió

Egyetlen műveletet hozunk létre az 1. verzióhoz.

1. A webes felhasználói felület bal oldali paneljén kattintson a "műveletek" elemre, majd `New Action` a gombra.
2. A "bot válasza" mezőben adja meg a "Sziasztok (1. verzió)" értéket.
3. Kattintson a `Save` gombra.

Most ezt a modellt "1. verzióként" címkézjük.

1. A bal oldali panelen kattintson a "beállítások" elemre, majd kattintson ![](../media/tutorial18_version_tags.PNG)a "verzió címkék" ikonra `New Tag` a gombra kattintva.
    - Név: "1. verzió"
1. Az "élő címke" legördülő menüből válassza az "1. verzió" lehetőséget.  
    - A robotot használó csatornák mostantól a modell "1. verzióját" fogják használni.
    - Az 1. verziójú modell entitások, műveletek és betanítási párbeszédpanelei már nem módosíthatók.
    - Ha az "1. verzió" lehetőséget választja "szerkesztési címkeként", csak a modellt fogja tudni megtekinteni, és nem szerkesztheti.
    - A "Master" értékre állítsa a "címke szerkesztése" lehetőséget, ez a modell egyetlen olyan verziója, amely szerkeszthető.

Most az "Version 1" (verzió címkék) rácsban jelenik meg.

### <a name="version-2"></a>2\. verzió

Most pedig szerkesztjük a modellt, hogy megkülönböztessük az 1. verziótól.

1. A bal oldali panelen kattintson a "műveletek" elemre.
2. A műveletek rácsban kattintson a "Sziasztok (1. verzió)" elemre.
3. Módosítsa a "bot válasza" mezőt a "Sziasztok (2. verzió)" értékre.
4. Kattintson a `Save` gombra.
5. Kattintson a `New Action` gombra.
6. A "bot válasza" mezőjében írja be a következőt: "Bye bye (2. verzió)".

### <a name="confirm-bot-framework-emulator-is-using-version-1"></a>Ellenőrizze, hogy a bot Framework Emulator az 1. verziót használja-e

1. A bot Framework Emulatorban írja be a "Hey There" üzenetet.
2. Figyelje meg, hogy a bot a "Hi there (1. verzió)" kifejezésre válaszol.
    - Ez ellenőrzi, hogy az 1. verzió "élő".

### <a name="view-the-conversation-logs-in-conversation-learner-web-ui"></a>A beszélgetési naplók megtekintése Conversation Learner webes felhasználói felületen

1. A bal oldali panelen kattintson a "naplózási párbeszédpanelek" elemre.
    - Ha nem lát párbeszédpanelt, kattintson a frissítés gombra.
2. Figyelje meg a rács "Version 1" címkéjét.
3. A rácsban kattintson a "Hi there (1. verzió)" elemre.

> [!NOTE]
> Az összes jelenleg elérhető Conversation Learner funkció közül választhatunk, de ezek a módosítások a főkiszolgálón, és nem az 1-es verzióban lesznek végrehajtva.

Megismerte, hogyan működik a verziószámozás, és hogyan használhatja a robotot a bot Framework Emulator használatával.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Entitások enumerálása és entitás-műveletek beállítása](./tutorial-enum-set-entity.md)
