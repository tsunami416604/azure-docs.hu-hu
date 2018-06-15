---
title: A Service Fabric-alkalmazás frissítési oktatóanyag |} Microsoft Docs
description: Ez a cikk végigvezeti a Service Fabric-alkalmazás telepítése, kódjának a módosítása és működés közbeni frissítés ki a Visual Studio használatával élménye.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: timlt
editor: ''
ms.assetid: a3181a7a-9ab1-4216-b07a-05b79bd826a4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 0a94dc91308f21d9e2b440f9638829ee03a8b903
ms.sourcegitcommit: eb75f177fc59d90b1b667afcfe64ac51936e2638
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/16/2018
ms.locfileid: "34205195"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>A Service Fabric frissítés oktatóanyag a Visual Studio használatával
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Az Azure Service Fabric egyszerűbbé teszi a felhőalapú alkalmazások fejlesztésével győződjön meg arról, hogy csak a módosított szolgáltatások frissíti, és, hogy az alkalmazás állapotának a frissítési folyamat során a rendszer figyeli. Azt is automatikusan visszaállítja az alkalmazás problémák észlelése esetén az előző verziójára. A Service Fabric-alkalmazás frissítéseket *nulla állásidő*, mert az alkalmazás frissítése állásidő nélkül. Ez az oktatóanyag bemutatja, hogyan adhat a Visual Studio eszközből a működés közbeni frissítés befejezéséhez.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>1. lépés: Összeállítása, és tegye közzé a Visual objektumok minta
Első lépésként töltse le a [Visual objektumok](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) alkalmazás a Githubról. Ezután összeállítása, és tegye közzé az alkalmazást, kattintson a jobb gombbal a projektet, a **VisualObjects**, majd válassza a **közzététel** a Service Fabric menüpont parancsot.

![A Service Fabric-alkalmazás a helyi menü][image1]

Kiválasztása **közzététel** megjelenik egy előugró ablak, és állíthatja be a **céloz profil** való **PublishProfiles\Local.xml**. Az ablak kell a következőhöz hasonló, végül kattintson **közzététel**.

![A Service Fabric-alkalmazás közzététele][image2]

Most kattintson **közzététel** a párbeszédpanelen. Használhat [a fürt és az alkalmazás megtekintése a Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). A vizuális objektumok alkalmazásnak egy webszolgáltatás, amelyet léphet, írja be a [ http://localhost:8081/visualobjects/ ](http://localhost:8081/visualobjects/) a böngésző címsorába.  A képernyőn megjelenő Navigálás 10 lebegőpontos visual objektumok kell megjelennie.

**Megjegyzés:** Ha üzembe `Cloud.xml` profil (Azure Service Fabric), az alkalmazás érhető el kell **http://{ServiceFabricName}. { Region}.cloudapp.Azure.com:8081/visualobjects/**. Győződjön meg arról, hogy `8081/TCP` konfigurált a terheléselosztó (Keresés a terheléselosztó ugyanabban az erőforráscsoportban a Service Fabric-példányként).

## <a name="step-2-update-the-visual-objects-sample"></a>2. lépés: A Visual objektumok minta frissítése
Bizonyára észrevette, hogy, hogy az 1. lépésben telepített verziójával, a visual objektumok nem elforgatása. Most frissítse az alkalmazás olyanra, ahol a vizuális objektumok is elforgatása.

Válassza ki a VisualObjects.ActorService projektet a VisualObjects megoldáson, és nyissa meg a **VisualObjectActor.cs** fájlt. A fájlban, folytassa a `MoveObject`, megjegyzéssé `visualObject.Move(false)`, és állítsa vissza `visualObject.Move(true)`. A kód módosítása az objektumok elforgatja, a szolgáltatás frissítése után.  **Most hozhat létre (nem rebuild) a megoldás**, amely létrehozza a módosított projektek. Ha *építse újra az összes*, a verziók a projektek frissítenie kell.

Is szükséges verzió az alkalmazás. A után kattintson a jobb gombbal a verzió a módosításokat a **VisualObjects** projekt, használhatja a Visual Studio **Manifest verziók szerkesztése** beállítás. E beállítás megadásával megjeleníti a párbeszédpanelt edition verziók az alábbiak szerint:

![Versioning párbeszédpanel][image3]

A verziófrissítés a módosított projektek és a kód csomagokat, a alkalmazás verzióra 2.0.0 együtt. A módosításokat, miután a jegyzék a következőket kell hasonlítania (félkövér bizonyos részei a módosításoknak):

![Verziók frissítése][image4]

A Visual Studio eszközök teheti az automatikus frissítések kiválasztása után verzióinak **automatikus frissítése az alkalmazás és szolgáltatás verziók**. Ha [SemVer](http://www.semver.org), frissítenie kell a kódot és/vagy a konfigurációs csomag verziója önmagában, ha a beállítást.

A módosítások mentéséhez, és most ellenőrizzük a **az alkalmazás frissítése** mezőbe.

## <a name="step-3--upgrade-your-application"></a>3. lépés: Az alkalmazás frissítése
Ismerje meg, a [alkalmazás frissítési paraméterei](service-fabric-application-upgrade-parameters.md) és a [frissítési folyamat](service-fabric-application-upgrade.md) lekérni a különböző frissítési paraméterek időtúllépéseket és alkalmazható egészségügyi feltételnek beható ismerete. A forgatókönyv a szolgáltatás állapotának kiértékelési feltétel értéke az alapértelmezett (a nem figyelt mód). Ezeket a beállításokat konfigurálhatja kiválasztásával **beállítások konfigurálása** módosításával a kívánt paramétereket.

Most a folyamatban, minden beállítás kiválasztásával az alkalmazásfrissítés indításához **közzététel**. Ez a beállítás a alkalmazás verzióra 2.0.0, az objektumok forgassa el frissíti. A Service Fabric frissíti a frissítési tartományok (néhány objektum frissíti először, mások követ) egyszerre, és a frissítés során a szolgáltatás elérhető marad. A szolgáltatás az ügyfél (böngésző) keresztül ellenőrizhetők.  

Most, mint az alkalmazás frissítési telepítése folytatódik, keresztül figyelheti a Service Fabric Explorerrel használatával a **folyamatban lévő frissítések** lapon az alkalmazások.

Néhány perc múlva minden frissítés tartományok (elvégezve) kell frissíteni, és a Visual Studio kimeneti ablakában is tartalmaznia kell, hogy a frissítés befejeződött. Keresse meg, és *összes* a böngészőablakban visual objektumok vannak most elforgatása!

Érdemes lehet próbálja meg módosítani a verziók és verzió 2.0.0 3.0.0 egy gyakorlatot, verzióra, vagy akár vissza verzióról 1.0.0 2.0.0 verziója. Lejátszás a időtúllépéseket és az állapotházirendeket el saját magának ismeri a őket. A helyi fürt szemben Azure fürthöz való telepítésekor használt paraméterek lehet térnek el egymástól. Azt javasoljuk, hogy állítsa a időtúllépéseket konzervatív módon.

## <a name="next-steps"></a>További lépések
[Az alkalmazás használatával PowerShell frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti Önt az alkalmazásfrissítés PowerShell használatával.

Szabályozza, hogyan az alkalmazás frissítés használatával [frissítési paraméterek](service-fabric-application-upgrade-parameters.md).

Az alkalmazásfrissítéseket által használatának megtanulása kompatibilissé [adatszerializálás](service-fabric-application-upgrade-data-serialization.md).

Összetettebb funkciók használata az alkalmazás frissítésekor szakaszra [témakörök speciális](service-fabric-application-upgrade-advanced.md).

Alkalmazásfrissítések gyakori problémáinak megoldásához hajtsa végre a hivatkozással [alkalmazásfrissítések hibaelhárítási](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
