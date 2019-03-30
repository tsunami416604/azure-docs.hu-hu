---
title: Frissítési útmutató a Service Fabric-alkalmazások |} A Microsoft Docs
description: Ez a cikk végigvezeti a élmény a Service Fabric-alkalmazás üzembe helyezése, a kódmódosítás és működés közbeni frissítés ki a Visual Studio használatával.
services: service-fabric
documentationcenter: .net
author: mani-ramaswamy
manager: chackdan
editor: ''
ms.assetid: a3181a7a-9ab1-4216-b07a-05b79bd826a4
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 2/23/2018
ms.author: subramar
ms.openlocfilehash: 8fe0bf9c8827b7248195f89377176fd834845e32
ms.sourcegitcommit: c6dc9abb30c75629ef88b833655c2d1e78609b89
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/29/2019
ms.locfileid: "58663672"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>A Service Fabric application frissítési oktatóanyag a Visual Studio használatával
> [!div class="op_single_selector"]
> * [PowerShell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Visual Studio](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Az Azure Service Fabric megkönnyíti a biztosítva, hogy csak a módosult szolgáltatások frissül, és, hogy az alkalmazás állapotának figyel-e a frissítési folyamat során a felhőalapú alkalmazások frissítésének folyamata. Azt is automatikusan visszaállítja a korábbi verzióra problémák észlelése esetén az alkalmazás. A Service Fabric alkalmazásfrissítések vannak *üzemszünet*, mivel az alkalmazás leállása nélkül is frissíthető. Ez az oktatóanyag bemutatja, hogyan végezheti el a működés közbeni frissítés a Visual Studióból.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>1. lépés: Hozhat létre, és tegye közzé a Visual objektumok minta
Először töltse le a [Visual objektumok](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) alkalmazást a Githubról. Ezután hozhat létre, és kattintson a jobb gombbal az alkalmazásprojektre, az alkalmazás közzététele **VisualObjects**, és kiválasztja a **közzététel** parancsot a Service Fabric menüpont.

![A Service Fabric-alkalmazás a helyi menü][image1]

Kiválasztásával **közzététel** megjelenik egy előugró ablak, és beállíthatja a **célprofilt** való **PublishProfiles\Local.xml**. Az ablak a következőhöz hasonlóan kell kinéznie, gombra való kattintás előtt **közzététel**.

![Service Fabric-alkalmazás közzététele][image2]

Most is kattinthat **közzététel** a párbeszédpanelen. Használhat [a fürt és az alkalmazás megtekintése a Service Fabric Explorer](service-fabric-visualizing-your-cluster.md). A vizuális objektumok alkalmazás rendelkezik egy webszolgáltatás, amelyet beírásával léphet [ http://localhost:8081/visualobjects/ ](http://localhost:8081/visualobjects/) az a böngésző címsorába.  A képernyőn a mozgó 10 lebegőpontos visual objektumokat kell megjelennie.

**MEGJEGYZÉS:** Ha telepíti, `Cloud.xml` profilt (az Azure Service Fabric), az alkalmazás elérhető kell **http://{ServiceFabricName}. { Region}.cloudapp.Azure.com:8081/visualobjects/**. Ellenőrizze, hogy `8081/TCP` konfigurálva az a Load Balancer (Keresés a terheléselosztó ugyanazt az erőforráscsoportot, a Service Fabric-példány).

## <a name="step-2-update-the-visual-objects-sample"></a>2. lépés: Frissítés a Visual objektumok minta
Észreveheti, hogy az 1. lépésben telepített verziójával, a vizuális objektumok nem elforgatása. Most frissítse az alkalmazás egy, a vizuális objektumokat is elforgatása.

Válassza ki a VisualObjects megoldáson belül a VisualObjects.ActorService projektet, és nyissa meg a **VisualObjectActor.cs** fájlt. Belül a fájlt, nyissa meg a metódus `MoveObject`, tegye megjegyzésbe `visualObject.Move(false)`, és vonja vissza `visualObject.Move(true)`. A kód módosítása elforgatása az objektumok, a szolgáltatás frissítése után.  **Most hozhat létre (nem újraépítése) a megoldás**, amely összeállítja a módosított projektek. Ha *újraépíti az összes*, az összes projekt-verziók frissítenie kell.

Azt is kell verzió az alkalmazást. Után a jobb gombbal a módosításokat a verzió a **VisualObjects** projekt, használhatja a Visual Studio **Manifest verziók szerkesztése** lehetőséget. Ezzel a beállítással kimenetei edition verziók párbeszédpaneljén a következőképpen:

![Verziókezelés párbeszédpanel][image3]

Frissítse a verziók a módosított projektek vagy kód csomagjaikat, az alkalmazás 2.0.0-s verziójával együtt. Után a módosításokat, a jegyzékfájlt a következőhöz hasonlóan kell kinéznie (a félkövérrel szedett részét a változások megjelenítéséhez):

![Verzió frissítése][image4]

A Visual Studio-eszközök automatikus kumulatív kiválasztásakor verzióját teheti **alkalmazás- és szolgáltatásverziók automatikus frissítése**. Ha [SemVer](http://www.semver.org), akkor frissítenie kell a kód és/vagy konfigurációs csomag verziója önálló, ha a beállítás van kiválasztva.

Mentse a módosításokat, és most ellenőrizze a **Upgradovat Aplikaci** mezőbe.

## <a name="step-3--upgrade-your-application"></a>3. lépés:  Az alkalmazás frissítése
Ismerje meg az a [alkalmazásfrissítési paraméterek](service-fabric-application-upgrade-parameters.md) és a [frissítési folyamat](service-fabric-application-upgrade.md) jól ismerik a különböző frissítési paraméterek, időtúllépéseket és egészségügyi feltételt, amely a lekérni a alkalmazni. Ebben a bemutatóban a service health értékelési feltétel értéke az alapértelmezett (a nem monitorozott módban). Ezeket a beállításokat konfigurálhat kiválasztásával **beállítások konfigurálása** annak módosításával igény szerint a paraméterek.

Most már készen állunk az alkalmazásfrissítés első lépésként válassza ki a **közzététel**. Ez a beállítás az alkalmazást, amelyben az objektumok elforgatása, 2.0.0-s verziójával frissíti. A Service Fabric egy frissítési tartományt frissít (néhány objektum frissíti először, mások által követett) egyszerre, és a frissítés során a szolgáltatás elérhető marad. A szolgáltatáshoz való hozzáférést az ügyfél (böngésző) keresztül ellenőrizhetők.  

Most, mint az alkalmazás frissítési telepítése folytatódik, nyomon követheti a Service Fabric Explorerrel használatával a **folyamatban lévő frissítések** az alkalmazások lapján.

Néhány perc múlva (befejeződött) összes frissítési tartományt kell frissíteni, és a Visual Studio kimeneti ablakában is tartalmaznia kell, hogy a frissítés befejeződött. És keresse meg, amely *összes* a böngészőablakban a vizuális objektumok mostantól Elforgatás vannak!

Próbálja meg módosítani a verziók, érdemes és 2.0.0-s verzió gyakorlatként 3.0.0-s verzióra, vagy akár vissza a 1.0.0-s verziójának 2.0.0-s verziójával. Időtúllépések és, hogy saját maga őket ismeri házirendek kísérletezhet. A helyi fürt helyett egy Azure-fürtön való telepítésekor használt paraméterek eltérő lehet. Azt javasoljuk, hogy állítsa a gondok konzervatív módon vegyen figyelembe.

## <a name="next-steps"></a>További lépések
[PowerShell-lel az alkalmazás frissítéséhez](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti egy alkalmazás frissítése a PowerShell használatával.

Szabályozhatja az alkalmazás a frissítés hogyan [frissítési paraméterek](service-fabric-application-upgrade-parameters.md).

Hogyan használja az alkalmazásfrissítések kompatibilissé [adatok szerializálása](service-fabric-application-upgrade-data-serialization.md).

Speciális funkciók használata közben lépésként tekintse át az alkalmazás frissítéséhez [Speciális témakörök](service-fabric-application-upgrade-advanced.md).

Az alkalmazásfrissítések gyakori problémák megoldása szerint hajtsa végre a hivatkozó [alkalmazásfrissítések hibaelhárítása](service-fabric-application-upgrade-troubleshooting.md).

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
