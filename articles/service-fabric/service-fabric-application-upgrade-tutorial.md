---
title: A Service Fabric alkalmazásfrissítési oktatóanyaga
description: Ez a cikk bemutatja a Service Fabric-alkalmazások üzembe helyezésének, a kód módosításának és a frissítés visual studió használatával történő üzembe helyezésének élményét.
ms.topic: conceptual
ms.date: 2/23/2018
ms.openlocfilehash: db814b972db1aee56be0858c9ff5d1c382640642
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "75464828"
---
# <a name="service-fabric-application-upgrade-tutorial-using-visual-studio"></a>Service Fabric alkalmazásfrissítési oktatóanyag a Visual Studio használatával
> [!div class="op_single_selector"]
> * [Powershell](service-fabric-application-upgrade-tutorial-powershell.md)
> * [Vizuális stúdió](service-fabric-application-upgrade-tutorial.md)
> 
> 

<br/>

Az Azure Service Fabric leegyszerűsíti a felhőalapú alkalmazások frissítésének folyamatát, biztosítva, hogy csak a módosított szolgáltatások frissüljenek, és hogy az alkalmazás állapotát a frissítési folyamat során figyeljék. Azt is automatikusan visszaállítja az alkalmazást az előző verzióba problémák találkozásakor. A Service Fabric alkalmazásfrissítései *nulla állásidő,* mivel az alkalmazás karbantartás nélkül frissíthető. Ez az oktatóanyag bemutatja, hogyan végezheti el a Visual Studio folyamatos frissítését.

## <a name="step-1-build-and-publish-the-visual-objects-sample"></a>1. lépés: A Vizuális objektumok minta összeállítása és közzététele
Először töltse le a [Visual Objects](https://github.com/Azure-Samples/service-fabric-dotnet-getting-started/tree/classic/Actors/VisualObjects) alkalmazást a GitHubról. Ezután hozza létre és tegye közzé az alkalmazást úgy, hogy a jobb gombbal az alkalmazásprojektre, a **VisualObjects**elemre kattint, és a Service Fabric **menüelemközzététel parancsát választja.**

![Service Fabric-alkalmazás helyi menüje][image1]

A **Közzététel** lehetőséget választva előugró ablak jelenik meg, és a **Célprofil t** **publishprofiles\Local.xml**mappába állíthatja. Az ablaknak a következőkre kell hasonlítaniaa, mielőtt a **Közzététel gombra kattintana.**

![Service Fabric-alkalmazás közzététele][image2]

Most a **párbeszédpanelen** a Közzététel gombra kattinthat. A Service Fabric Explorer segítségével [megtekintheti a fürtöt és az alkalmazást.](service-fabric-visualizing-your-cluster.md) A Visual Objects alkalmazás rendelkezik egy webszolgáltatással, amelyet a böngésző címsorába beírva [http://localhost:8081/visualobjects/](http://localhost:8081/visualobjects/) használhat.  A képernyőn 10 lebegő vizuális objektumnak kell lennie.

**MEGJEGYZÉS:** Ha a `Cloud.xml` profilra (Azure Service Fabric) telepíti, az alkalmazásnak elérhetőnek kell lennie a **http://{ServiceFabricName}.{ címen. Region}.cloudapp.azure.com:8081/visualobjects/**. Győződjön meg `8081/TCP` arról, hogy konfigurálta a terheléselosztó (keresse meg a terheléselosztó ugyanabban az erőforráscsoportban, mint a Service Fabric-példány).

## <a name="step-2-update-the-visual-objects-sample"></a>2. lépés: A vizuális objektumok mintája frissítése
Előfordulhat, hogy az 1. Frissítsük ezt az alkalmazást egy olyanra, ahol a vizuális objektumok is forognak.

Jelölje ki a VisualObjects.ActorService projektet a VisualObjects megoldáson belül, és nyissa meg a **VisualObjectActor.cs** fájlt. A fájlon belül nyissa `MoveObject`meg `visualObject.Move(false)`a metódust, megjegyzést fűzzön hozzá és megjegyzést fért el. `visualObject.Move(true)` Ez a kódmódosítás elforgatja az objektumokat a szolgáltatás frissítése után.  **Most már megépítheti (nem újraépítheti) a megoldást,** amely a módosított projekteket építi. Ha az *Összes újraépítése*lehetőséget választja, frissítenie kell az összes projekt verzióit.

Azt is meg kell változat alkalmazásunkat. Ha módosítani szeretné a verziót, miután a jobb gombbal a **VisualObjects** projektre kattintott, használhatja a Visual Studio **Szerkesztési jegyzékfájl-verziók** beállítást. Ha ezt a lehetőséget választja, a kiadásverziók párbeszédpanele a következőképpen jelenik meg:

![Verziószámozás párbeszédpanel][image3]

Frissítse a módosított projektek és kódcsomagok verzióit, valamint az alkalmazást a 2.0.0-s verzióra. A módosítások végrehajtása után a jegyzékfájlnak a következőkre kell hasonlítania (a félkövér részek a módosításokat mutatják):

![Verziók frissítése][image4]

A Visual Studio eszközei az **alkalmazás- és szolgáltatásverziók automatikus frissítése**lehetőséget követően automatikus verzióösszesítéseket tudnak végezni. Ha [a SemVer](http://www.semver.org)programot használja, csak a kódot és/vagy a konfigurációs csomag verzióját kell frissítenie, ha ez a beállítás be van jelölve.

Mentse a módosításokat, és most jelölje be **az Alkalmazás frissítése** jelölőnégyzetet.

## <a name="step-3--upgrade-your-application"></a>3. lépés: Az alkalmazás frissítése
Ismerkedjen meg az [alkalmazásfrissítési paraméterekkel](service-fabric-application-upgrade-parameters.md) és a [frissítési folyamattal,](service-fabric-application-upgrade.md) hogy jól megértse a különböző frissítési paramétereket, időtúlelőnyöket és az alkalmazható állapotkritériumot. Ebben a forgatókönyvben a szolgáltatás állapotkiértékelési feltétel van beállítva az alapértelmezett (nem figyelt módban). Ezeket a beállításokat úgy állíthatja be, hogy **kiválasztja a Frissítési beállítások konfigurálása** lehetőséget, majd igény szerint módosítja a paramétereket.

Most már minden készen áll, hogy indítsa el az alkalmazás frissítése kiválasztásával **Közzétesz**. Ez a beállítás az alkalmazást a 2.0.0-s verzióra frissíti, amelyben az objektumok forognak. A Service Fabric egyszerre egy frissítési tartományt frissít (egyes objektumok először frissülnek, majd mások), és a szolgáltatás továbbra is elérhető marad a frissítés során. A szolgáltatáshoz való hozzáférés az ügyfélen (böngészőn) keresztül ellenőrizhető.  

Most, az alkalmazás frissítésének előrehaladtával, figyelheti azt service Fabric Explorer, az alkalmazások alatti **Frissítések folyamatban** lapon.

Néhány percen belül az összes frissítési tartományt frissíteni kell (be kell fejezni), és a Visual Studio kimeneti ablakában azt is meg kell jelölnie, hogy a frissítés befejeződött. És meg kell találnia, hogy az *összes* vizuális tárgyak a böngésző ablakban most forog!

Próbálkozzon a verziók módosításával, majd a 2.0.0-s verzióról a 3.0.0-s verzióra váltson gyakorlatként, vagy akár a 2.0.0-s verzióról az 1.0.0-s verzióra. Játssz on time-outs és az egészségügyi politikák, hogy magát megismerni őket. Ha egy Azure-fürtre telepíti, nem pedig egy helyi fürt, a használt paraméterek eltérő lehet. Azt javasoljuk, hogy az időhosszabbításokat konzervatív módon állítsa be.

## <a name="next-steps"></a>További lépések
[Az alkalmazás PowerShell használatával történő frissítése](service-fabric-application-upgrade-tutorial-powershell.md) végigvezeti a PowerShell használatával történő alkalmazásfrissítésen.

Az alkalmazás [frissítési paramétereinek](service-fabric-application-upgrade-parameters.md)szabályozása.

Tegye kompatibilissé az alkalmazásfrissítéseket az [adatok szerializálásának](service-fabric-application-upgrade-data-serialization.md)használatával.

A speciális funkciók használata az alkalmazás frissítése során a [Speciális témakörökre](service-fabric-application-upgrade-advanced.md)hivatkozva.

Az alkalmazásfrissítések gyakori problémáit az [alkalmazásfrissítések hibaelhárítása](service-fabric-application-upgrade-troubleshooting.md)című témakör lépéseire hivatkozva oldhatja meg.

[image1]: media/service-fabric-application-upgrade-tutorial/upgrade7.png
[image2]: media/service-fabric-application-upgrade-tutorial/upgrade1.png
[image3]: media/service-fabric-application-upgrade-tutorial/upgrade5.png
[image4]: media/service-fabric-application-upgrade-tutorial/upgrade6.png
