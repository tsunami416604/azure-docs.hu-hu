---
title: "\"\"Helló világ!\"alkalmazás\" Conversation Learner modell létrehozása – Microsoft Cognitive Services | Microsoft Docs"
titleSuffix: Azure
description: Megtudhatja, hogyan hozhat létre ""Helló világ!"alkalmazás" Conversation Learner modellt.
services: cognitive-services
author: nitinme
manager: nolachar
ms.service: cognitive-services
ms.subservice: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: nitinme
ROBOTS: NOINDEX
ms.openlocfilehash: bb1d053af8813f05872c56d3b5609f2d7d7d5d8c
ms.sourcegitcommit: ad9120a73d5072aac478f33b4dad47bf63aa1aaa
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/01/2019
ms.locfileid: "68705640"
---
# <a name="how-to-create-a-hello-world-model-with-conversation-learner"></a>""Helló világ!"alkalmazás" modell létrehozása Conversation Learner

Ez az oktatóanyag bemutatja, hogyan kezdheti el a Conversation Learnert, beleértve a műveletek létrehozását, a robot interaktív tanítását, valamint a naplózott párbeszédpanelek javítási feltételeit, amelyek a végfelhasználók számára jönnek létre.

## <a name="video"></a>Videó

[!["Helló világ!" alkalmazás oktatóanyag előzetes verziója](https://aka.ms/cl_Tutorial_v3_HelloWorld_Preview)](https://aka.ms/cl_tutorial_v3_helloworld)


## <a name="requirements"></a>Követelmények
Ha még nem tette meg, először ellenőrizze, hogy befejeződött-e az összes telepítési lépés `.env` , beleértve a Luis authoring key-fájl létrehozását is.  Részletek [a gyors](../quickstart.md) útmutatóban.

Ehhez az oktatóanyaghoz az általános oktatóanyag robotjának futtatására van szükség

    npm run tutorial-general

## <a name="steps"></a>Lépések

Indítsa el a kezdőlapot a webes KEZELŐFELÜLETen.

### <a name="create-the-model"></a>A modell létrehozása
1. Kattintson az "új modell" gombra.
2. A "név" mezőben adja meg a ""Helló világ!"alkalmazás" értéket.
3. Kattintson a "létrehozás" gombra.

Ekkor megjelenik a létrehozott modell nézete.

### <a name="create-an-action"></a>Művelet létrehozása
1. A bal oldali panelen kattintson a "műveletek", majd az "új művelet" gombra.
    - Egy művelet lehet szöveges üzenet, amely Conversation Learner visszatér a felhasználóhoz, egy API-híváshoz vagy egy kártyához.
2. A "bot válaszában..." "Hello" típusú mező.
    - Ez a válasz, amelyet a robot vissza fog adni.
3. Kattintson a "létrehozás" gombra.

Létrehozta az első műveletet, amelyet a bot végrehajthat, azaz szöveges választ adhat vissza.

### <a name="train-dialogs"></a>Betanítási párbeszédpanelek
Itt taníthatja be a modellt a felhasználói hosszúságú kimondott szöveg való reagálásra.

#### <a name="first-training-dialog"></a>Első betanítási párbeszédpanel

1. A bal oldali panelen kattintson a "betanítási párbeszédablakok", majd az "új vonat párbeszédpanel" gombra.
2. Írja be a "Hi" kifejezést, majd nyomja meg az ENTER billentyűt
    - Példa arra, hogy a felhasználó milyen módon jelenhet meg egy beszélgetés elején.
3. Kattintson a "pontszám műveletek" gombra.
4. Válassza a "Hello" lehetőséget.
    - Ebben a példában csak egy teljes bekapcsolást végzett. 
5. Gépelje be a "búcsú" nevű felhasználói választ.
6. Kattintson a "pontszám műveletek" gombra.
7. Kattintson a "+ művelet" gombra.
8. Írja be a "Goodbye!" kifejezést. a "bot válaszában..." mezőben, majd kattintson a "létrehozás" gombra.
    - Figyelje meg, hogy a robot az imént létrehozott művelettel válaszolt.
9. Kattintson a Save (Mentés) gombra. 
    - Ez véget ért, és menti a betanítási párbeszédpanelt.

Most már rendelkezik egy betanítási párbeszédpanellel a modellben, valamint egyetlen entitással és két művelettel.

#### <a name="second-training-dialog"></a>Második betanítási párbeszédpanel
Lássunk még egy tanítást, és nézzük meg, hogyan reagál a robot.

1. Kattintson az "új vonat párbeszédpanel" gombra.
2. Írja be a következőt: "Hi"
    - Ez hasonló az első párbeszédpanelhez, és vártunk egy jó pontszámot a robotból.
3. Kattintson a "pontszám műveletek" gombra.
    - Előfordulhat, hogy a pozíció és a pontszám még nem elég pontos, és további képzésre is szükség lehet.
4. Válassza a "Hello" lehetőséget.
5. Írja be a felhasználó válaszát, "Bye".
6. Kattintson a "pontszám műveletek" gombra.
7. Válassza a "búcsú!" lehetőséget.
8. Kattintson a Save (Mentés) gombra.

### <a name="log-dialogs"></a>Napló párbeszédpanelek
Itt tesztelheti, megtekintheti és kijavíthatja azokat a beszélgetéseket, amelyekkel Ön vagy a valódi felhasználója már használta a robotját.

#### <a name="test-the-model-as-an-end-user"></a>Modell tesztelése végfelhasználóként
1. A bal oldali panelen kattintson a "naplózási párbeszédpanelek" elemre, majd az "új napló párbeszédpanel" gombra.
2. Írja be a "Hello There" kifejezést.
3. Várjon egy rövid ideig, a robotnak automatikusan kell válaszolnia a "Hello" kifejezéssel
4. Adja meg a "byebye" értéket
5. Várjon egy rövid ideig, a robotnak automatikusan válaszolnia kell a "Hello" értékre.
6. Kattintson a "kész tesztelés" gombra.

#### <a name="view-and-correct-a-user-conversation"></a>Felhasználói beszélgetés megtekintése és javítása
A log párbeszédablakok segítségével megtekintheti a robottal tartott beszélgetések felhasználói listáját. A robot válaszainak kijavítani és az interakciókat betanítási Párbeszédablakként is szerkesztheti. Ehhez tegye a következőket:
1. A rácsban kattintson a beszélgetés naplója elemre.
2. Kattintson az utolsó bot műveletre, például: "Hello".
3. Válassza a "búcsú!" lehetőséget. a robot kijavítani.
4. Kattintson a "Mentés a betanításra" gombra.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Bevezetés a betanításba](./02-intro-to-training.md)
