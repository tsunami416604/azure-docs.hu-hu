---
title: Beszélgetés tanuló alkalmazás, virtuális valóságban alkalmazás indító - Microsoft kognitív szolgáltatásokat bemutató |} Microsoft Docs
titleSuffix: Azure
description: Útmutató a bemutató beszélgetés tanuló alkalmazás létrehozásához.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 3e41125bf7da9ee64d666d22cb275af01af54012
ms.sourcegitcommit: 95d9a6acf29405a533db943b1688612980374272
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/23/2018
ms.locfileid: "35348666"
---
# <a name="demo-virtual-reality-app-launcher"></a>Bemutató: Virtuális valóságban app indítója

Ez a bemutató mutatja be egy virtuális valóságban alkalmazásindító, támogató parancsok, például a "start Skype és üzenőfalán be." A felhasználó számára szükséges ahhoz, hogy indítsa el az alkalmazást egy alkalmazás nevének és helyének azaz. Alkalmazás elindítása kezelik a kulcsokat API-hívás által. Amikor a felhasználó ismeri fel az alkalmazás nevét, és van a entityDetectionCallback ellenőrzi, hogy a kért alkalmazás megegyezik-e a telepített alkalmazások közül egy vagy több alkalmazást. Ha a kért alkalmazás nincs telepítve, és ha az alkalmazásnév nem egyértelmű a helyzet kezelési (megegyezik az egynél több telepített alkalmazást).

## <a name="requirements"></a>Követelmények

Ez az oktatóanyag megköveteli, hogy fut-e a VRAppLauncher botot

    npm run demo-vrapp

### <a name="open-the-demo"></a>Nyissa meg a bemutató

A webes felhasználói felület alkalmazások listájának megtekintéséhez kattintson a VRAppLauncher. 

## <a name="entities"></a>Entitások

Létrehoztunk négy entitások:

- Alkalmazásnév: például Skype
- PlacementLocation: Példa fali a
- UnknownAppName: egy szoftveres entitás, amely a rendszer állítja be, ha nem ismeri fel az entitás neve a felhasználó szerint, például mert nem lett telepítve.
- DisAmbigAppNames: az említett két vagy több telepített alkalmazást melyek nevei egyeznek a felhasználó tömbje. 

![](../media/tutorial_vrapplauncher_entities.PNG)

### <a name="actions"></a>Műveletek

Létrehoztunk műveletek egy csoportját, amely tartalmazza az API-k LaunchApp, a függvény hívásához szükséges indíthatja el az alkalmazást elindító nevezik.

![](../media/tutorial_vrapplauncher_actions.PNG)

### <a name="training-dialogs"></a>Képzési párbeszédpanelek
Definiáltuk képzési párbeszédpanelek számos.

![](../media/tutorial_vrapplauncher_dialogs.PNG)

Tegyük fel próbáljuk meg egy tanítási munkamenet.

1. Kattintson a vonat párbeszédpanelek, majd új vonat párbeszédpanel.
1. Adja meg "hi".
2. Kattintson a pontszám művelet.
3. Ide kattintva válassza ki "melyik alkalmazás szeretné elindítani?"
4. Adja meg a "outlook".
    - Vegye figyelembe, hogy LUIS felismeri az entitás.
5. Kattintson a pontszám műveletek.
3. Jelölje be a "hol szeretné elhelyezni?"
4. Adja meg a "üzenőfalon".
    - Vegye figyelembe, hogy LUIS felismeri azt egy PlacementLocation.
2. Adja meg a pontszám műveletek.
6. Válassza ki a "LaunchApp"
7. Rendszer: "kezdő üzenőfalán outlook".
    - Vegye figyelembe, hogy a futtatása egy API-hívás. Ez a hívás a kód jelenleg C:\<\installedpath > \src\demos\demoVRAppLauncher.ts. Valójában nincs azonban elindíthatja az ebben a bemutatóban az outlook programot.
    - Az alkalmazásnév és PlacementLocation entitások törli azt. Az értéket ad vissza válaszként a fenti karakterláncot.
4. Kattintson a tanítási végezhető el.

![](../media/tutorial_vrapplauncher_callbackcode.PNG)

Kezdjük egy másik betanítás ismeretlen és a nem egyértelmű entitások kezelésére.

1. Kattintson az új vonat párbeszédpanel.
1. Adja meg a "start OneNote". 
    - Ismeri azt az alkalmazás nevét, mert a EntityDetectionCallback definiált kód vesz igénybe, a neve felhasználó került, és feloldja az az alkalmazás nevét a kódban definiált alkalmazáslistájában egyeztetve. Ezután az összes hozzá tartozó alkalmazások készletét adja vissza. 
    - Ha a lista a megfeleltetésekben nulla, ez azt jelenti, az alkalmazás nincs telepítve. Elhelyezi unknownAppName.
    - Ha egynél több alkalmazás talál, azt fogja másolja őket abba a DisambigAppNames, és az AppName entitás törlése.
2. Kattintson a pontszám művelet.
3. Jelölje be a "Sajnos nem tudja az alkalmazás $UknownAppName."
4. Adja meg a "start amazon". Azt látja a más elérési utat.
5. Kattintson a pontszám műveletek.
    - Megjegyzés: Amazon videó és Amazon zene jelenleg DisambigAppNames memória. És OneNote törölve lett.
3. Kattintással jelölje be "Van néhány olyan alkalmazásokat, amelyek hangkártya hasonlóan..."
    - Megjegyzés: mivel tudunk csak néhány képzési párbeszédpanelek a pontig, a pontszám nincs nagyon nagy. A jelek szerint igazolnia kell néhány kattintson ide a modell több döntő.
2. Adja meg a pontszám műveletek.
4. Kattintson a tanítási végezhető el.

Most láthatta entitás feloldási módjáról. A bemutató is API visszahívások mutatja, és bemutatta adatok gyűjtése, jelenléte és kétértelműség keresése és, amely alapján a megfelelő műveletek sablonját.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [A beszélgetés tanuló botot telepítése](../deploy-to-bf.md)
