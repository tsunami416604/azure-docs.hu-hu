---
title: Beszélgetéstanuló modell, a virtuális valóságban alkalmazásindító - a Microsoft Cognitive Services bemutató |} A Microsoft Docs
titleSuffix: Azure
description: Megtudhatja, hogyan hozhat létre egy bemutató Beszélgetéstanuló modell.
services: cognitive-services
author: v-jaswel
manager: nolachar
ms.service: cognitive-services
ms.component: conversation-learner
ms.topic: article
ms.date: 04/30/2018
ms.author: v-jaswel
ms.openlocfilehash: 896ec007c03e30e5c20a5344430be040271bc00b
ms.sourcegitcommit: 4e5ac8a7fc5c17af68372f4597573210867d05df
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/20/2018
ms.locfileid: "39171148"
---
# <a name="demo-virtual-reality-app-launcher"></a>Bemutató: Virtuális valóságban appindítója

Ez a bemutató mutatja be a virtuális valóságban modell indítója, amely támogatja a parancsok, például a "start Skype és üzenőfalán helyezni." A felhasználónak kell tegyük fel, hogy az alkalmazás nevét és helyét az alkalmazás indításához. Kezeli a modell elindítása egy API-hívás által. Ha az alkalmazás nevét a rendszer a felhasználó ismeri fel, a entityDetectionCallback ellenőrzi a-e a kért alkalmazás megfelel-e a telepített alkalmazások listájában egy vagy több alkalmazást. Kezeli az esetben, ha a kért alkalmazás nincs telepítve, és ha az alkalmazás neve nem egyértelmű (megegyezik az egynél több telepített alkalmazást).

## <a name="video"></a>Videó

[![Bemutató VR alkalmazás előnézete](http://aka.ms/cl-demo-vrapp-preview)](http://aka.ms/blis-demo-vrapp)

## <a name="requirements"></a>Követelmények

Ehhez az oktatóanyaghoz, hogy fut-e a VRAppLauncher robot:

    npm run demo-vrapp
    
### <a name="open-the-demo"></a>Nyissa meg a bemutatót

A webes felhasználói felületen modell-lista kattintson a VRAppLauncher. 

## <a name="entities"></a>Entitások

Négy entitások létrehoztunk Önnek:

- Alkalmazásnév: például Skype
- PlacementLocation: Példa valós a
- UnknownAppName: egy szoftveres entitás, amely a rendszer állítja be, ha nem ismeri fel az entitás neve a felhasználó szerint, például mert nem lett telepítve.
- DisAmbigAppNames: legalább két telepített alkalmazást, melyek nevei egyeznek a felhasználó tömbjét mondta. 

![](../media/tutorial_vrapplauncher_entities.PNG)

### <a name="actions"></a>Műveletek

Műveletek egy csoportját, amely tartalmazza az API-k LaunchApp, a függvény hívásához szükséges, indítsa el az alkalmazást elindító nevű hoztunk létre.

![](../media/tutorial_vrapplauncher_actions.PNG)

### <a name="training-dialogs"></a>Képzési párbeszédpanelek
Hogy definiáltuk képzési párbeszédpanelek számos.

![](../media/tutorial_vrapplauncher_dialogs.PNG)

Tegyük fel próbáljon meg egy tanítási munkamenet.

1. Kattintson a vonat párbeszédpanelek, majd az új Train párbeszédpanel.
1. Adja meg "hi".
2. Kattintson a pontszám művelet.
3. Válassza ki, kattintson a "melyik alkalmazás szeretné elindítani?"
4. Adja meg a "outlook".
    - A LUIS felismeri egy egységként.
5. Kattintson a pontszám műveletek.
3. Jelölje be a "hol szeretné elhelyezni,?"
4. Írja be a "a fali".
    - A LUIS, egy PlacementLocation felismeri.
2. Adja meg a pontszám műveleteket.
6. Válassza ki a "LaunchApp"
7. Rendszer: "indítása az outlook üzenőfalán".
    - Ez aktiválódik egy API-hívás. Ez a hívás a kód jön létre a C:\<\installedpath > \src\demos\demoVRAppLauncher.ts. Azonban nem ténylegesen tartalmaz a kódot, és elindítja az Outlookot ebben a bemutatóban.
    - Ez törli az AppName és PlacementLocation entitásokat. Az értéket ad vissza válaszként a fenti karakterláncot.
4. Kattintson a tanítási kész gombra.

![](../media/tutorial_vrapplauncher_callbackcode.PNG)

Először egy másik az oktatás munkamenete ismeretlen, nem egyértelmű entitások kezelésére.

1. Kattintson az új Train párbeszédpanel.
1. Adja meg a "start OneNote". 
    - A modell felismeri a OneNote-ban az alkalmazás neveként. A `EntityDetectionCallback` a kódban meghatározott függvény oldja fel az alkalmazáslistából, a kódban megadott történő az alkalmazás nevét, a felhasználó által megadott nevét. Ezután az összes egyező alkalmazások készletét adja vissza. 
    - Ha a megfeleltetések listáját nulla, ez azt jelenti, az alkalmazás nincs telepítve. Mindezt unknownAppName.
    - Ha egynél több alkalmazást is talál, másolja át őket a `DisambigAppNames` és törli az AppName entitás.
2. Kattintson a pontszám művelet.
3. Jelölje be a "Sajnos nem tudja az alkalmazás $UknownAppName."
4. Adja meg a "start Amazon". Az elérési út megpróbálunk.
5. Kattintson a pontszám műveletek.
    - Amazon-Video- és Amazon zene, mostantól `DisambigAppNames` megszűnt a memória és a OneNote-ban.
3. Válassza ki a kattintson a "Nincsenek néhány olyan alkalmazásokat, amelyek a hang-, mint..."
    - A pontszám azért nem nagyon magas, mert néhány képzési párbeszédpanelek, addig a pontig, csak meghatározott rendelkezik. További betanítási párbeszédpanelek meghatározása teszi a modell több döntő.
2. Adja meg a pontszám műveleteket.
4. Kattintson a tanítási kész gombra.

Most láthatta entitások megoldási módjáról. A bemutató is API visszahívások célkiszolgálóról, és bemutatta, egy sablon adatainak összegyűjtése, jelenléte és félreérthetőség keresése, és ennek megfelelően véve a megfelelő művelet.

## <a name="next-steps"></a>További lépések

> [!div class="nextstepaction"]
> [Beszélgetéstanuló robotprogramok üzembe helyezése](../deploy-to-bf.md)
