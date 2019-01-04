---
title: Hogyan hozhat létre egy "Hello World" Beszélgetéstanuló modell – a Microsoft Cognitive Services |} A Microsoft Docs
titleSuffix: Azure
description: Ismerje meg, hogyan hozhat létre egy "Hello World" Beszélgetéstanuló modellt.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: c08e3d2e8f712f5eb7c56585507a283c7fd040c1
ms.sourcegitcommit: 295babdcfe86b7a3074fd5b65350c8c11a49f2f1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/27/2018
ms.locfileid: "53796803"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>Hogyan hozhat létre egy "Hello World" modellt Beszélgetéstanuló

Ez az oktatóanyag bemutatja, hogyan kezdheti el az Beszélgetéstanuló, beleértve a műveletek létrehozásához, a robot interaktív módon oktatási és korrigálás naplózott párbeszédpanelek, amelyeket a végfelhasználók számára biztosítja.

## <a name="video"></a>Videó

[![Hello World oktatóanyag előzetes verzió](https://aka.ms/cl_Tutorial_v3_HelloWorld_Preview)](https://aka.ms/cl_tutorial_v3_helloworld)


## <a name="requirements"></a>Követelmények
Ha még nem tette, először győződjön meg arról az összes telepítő lépés befejeződött, beleértve a létrehozása egy `.env` a LUIS szerzői kulcsot tartalmazó fájl.  Lásd: [rövid](../quickstart.md) részleteiről.

Ehhez az oktatóanyaghoz, hogy az általános oktatóanyag Bot fut.

    npm run tutorial-general

## <a name="steps"></a>Lépések

Indítsa el a kezdőlapon a webes felhasználói felületen.

### <a name="create-the-model"></a>A modell létrehozása
1. Az "Új modell" gombra.
2. A "Name" mezőben adja meg a "Hello World".
3. A "Létrehozás" gombra.

Meg kell jelennie a létrehozott modell nézetét.

### <a name="create-an-action"></a>Hozzon létre egy műveletet
1. A bal oldali panelen kattintson a "Műveletek", majd az "Új Action" gombra.
    - Művelet lehet, amely Beszélgetéstanuló visszaadja a felhasználó, egy API-hívás vagy kártya szöveges üzenetet.
2. A "Bot a válaszban..." mezőbe írja be a "Hello".
    - Ez az a választ, amely a robot adja vissza.
3. A "Létrehozás" gombra.

Létrehozta az első művelet, amely a robot hajthat végre, például az a szöveg választ adja vissza.

### <a name="train-dialogs"></a>Párbeszédpanelek betanítása
Ez az, ahol Ön betanítja a modellt, hogyan reagál a felhasználói kimondott szöveg.

#### <a name="first-training-dialog"></a>Első képzési párbeszédpanel

1. A bal oldali panelen kattintson a "Train-párbeszédpanelekhez", majd az "új Train" gomb.
2. Írja be "Hi", az enter.
    - Ahogy a felhasználó egy példát a következő lehet: a beszélgetés elején.
3. A "Score műveletek" gombra.
4. Válassza ki a "Hello".
    - Az imént befejezett ezen a párbeszédpanelen példát egy teljes kapcsolja. 
5. Írja be a felhasználói válasz, "Viszlát".
6. A "Score műveletek" gombra.
7. Kattintson a "+ művelet" gombra.
8. Írja be "Viszlát!" a "... robot a response" mezőt, majd kattintson a "Létrehozás" gombra.
    - Figyelje meg, hogy a robot válaszolt, hogy a művelet újonnan létrehozott.
9. A "Mentés" gombra. 
    - Ez befejezése, és mentse a betanítási párbeszédpanel.

Most már a modellt, és egy egyetlen entitást és két műveletet egy képzési párbeszédpanelje.

#### <a name="second-training-dialog"></a>Második képzési párbeszédpanel
Most ne egy további betanítási, és tekintse meg a robot reakciója.

1. A "Train párbeszédpanel" gombra.
2. Írja be a "Hi"
    - Ez hasonlít az első párbeszédpanel, és a egy jó pontszám lekérése a robot várhatóan.
3. A "Score műveletek" gombra.
    - A pozíció és pontszám még mindig nem feltétlenül pontos elég, és szükség lehet további.
4. Válassza ki a "Hello".
5. Írja be a felhasználói válasz "bye".
6. A "Score műveletek" gombra.
7. Válassza ki a "goodbye" értékre!
8. A "Mentés" gombra.

### <a name="log-dialogs"></a>Napló párbeszédpanelek
Ez az, ahol tesztelni, megtekinthető és javítsa ki, hogy Ön vagy a valódi felhasználók voltak a robot a beszélgetések.

#### <a name="test-the-model-as-an-end-user"></a>Teszteli a modellt, a felhasználó
1. A bal oldali panelen kattintson a "Log-párbeszédpanelekhez", majd az "új Log" gomb.
2. Írja be a "helló".
3. Várjon egy kis ideig, a Bot automatikusan kell válaszolnia a "Hello"
4. Adja meg a "byebye"
5. Várjon egy kis ideig, újra a robot kell válaszolnia automatikusan a "Hello".
6. Kattintson a "Végzett tesztelése" gombra.

#### <a name="view-and-correct-a-user-conversation"></a>Megtekintheti, és javítsa ki a felhasználó beszélgetés
A Log párbeszédpanelek, megtekintheti a témakörök listáját a robottal tárolt felhasználók. Szerkesztheti is annak érdekében, hogy javítsa ki a robot válaszokat, és képzési párbeszédpanelek interakciókat mentheti őket. Ehhez:
1. A rácsban kattintson a napló a párbeszéd.
2. Például kattintson az utolsó Bot művelet "Hello".
3. Válassza ki a "goodbye" értékre! a robot kijavításához.
4. A "Mentés másként Train párbeszédpanel" gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Képzések bemutatása](./02-intro-to-training.md)
