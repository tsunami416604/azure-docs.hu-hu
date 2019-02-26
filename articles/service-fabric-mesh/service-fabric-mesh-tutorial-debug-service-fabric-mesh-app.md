---
title: Oktatóanyag – Hibakeresés egy helyi fejlesztési fürtben futó Azure Service Fabric Mesh-webalkalmazásban | Microsoft Docs
description: Ebben az oktatóanyagban egy olyan Azure Service Fabric mesh-alkalmazásban végez hibakeresést, amely a helyi fürtön fut.
services: service-fabric-mesh
documentationcenter: .net
author: dkkapur
manager: chakdan
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 10/31/2018
ms.author: dekapur
ms.custom: mvc, devcenter
ms.openlocfilehash: bef86b189064a82b6605e8b99a374b1ee92682e2
ms.sourcegitcommit: 7f7c2fe58c6cd3ba4fd2280e79dfa4f235c55ac8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/25/2019
ms.locfileid: "56805120"
---
# <a name="tutorial-debug-a-service-fabric-mesh-application-running-in-your-local-development-cluster"></a>Oktatóanyag: A helyi fejlesztési fürtön futó Service Fabric-háló alkalmazások hibakeresése

Ez az oktatóanyag a sorozat második része, amely az Azure Service Fabric Mesh-alkalmazások helyi fejlesztői fürtön történő felépítését és hibakeresését ismerteti.

Ebben az oktatóanyagban elsajátíthatja:

> [!div class="checklist"]
> * Mi történik az Azure Service Fabric mash-alkalmazások fejlesztésekor
> * Hogyan állítható be egy töréspont a szolgáltatások közti hívás figyelése céljából

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Service Fabric Mesh-alkalmazás létrehozása a Visual Studióban](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * Egy helyi fejlesztési fürtben futó Service Fabric Mesh-alkalmazás hibakeresése
> * [Service Fabric Mesh-alkalmazás üzembe helyezése](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
> * [Service Fabric Mesh-alkalmazás frissítése](service-fabric-mesh-tutorial-upgrade.md)
> * [A Service Fabric Mesh erőforrásainak eltávolítása](service-fabric-mesh-tutorial-cleanup-resources.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Először [alakítsa ki a fejlesztési környezetet](service-fabric-mesh-howto-setup-developer-environment-sdk.md) a Service Fabric futtatókörnyezet, az SDK, a Docker, és a Visual Studio 2017 telepítésével.

## <a name="download-the-to-do-sample-application"></a>A teendőlista-mintaalkalmazás letöltése

Ha nem hozott létre a feladatlista mintaalkalmazáshoz [oktatóanyag-sorozat része](service-fabric-mesh-tutorial-create-dotnetcore.md), töltheti le. Egy parancssori ablakban futtassa a következő parancsot a mintaalkalmazás-adattár helyi számítógépre történő klónozásához.

```
git clone https://github.com/azure-samples/service-fabric-mesh
```

Az alkalmazás az `src\todolistapp` könyvtárban található.

## <a name="build-and-debug-on-your-local-cluster"></a>Fejlesztés és hibakeresés a helyi fürtön

Amint a projekt betölt, a rendszer automatikusan létrehozza és üzembe helyezi a Docker-rendszerképet a helyi fürtön. Ez a folyamat eltarthat egy ideig. Ha figyelni szeretné a folyamat előrehaladását a Visual Studio **Kimenet** ablaktábláján, állítsa be a kimeneti ablaktábla **Kimenet mutatása innen** legördülő listájában a **Service Fabric Tools** lehetőséget.

Az **F5** billentyű megnyomásával végezze el a fordítást és a szolgáltatás helyi futtatását. A projekt helyi futtatásakor és hibakeresésekor a Visual Studio az alábbiakat végzi el:

* Ellenőrzi, hogy a Windows rendszerhez készült Docker fut, és a Windowst használja tárolói operációs rendszerként.
* Letölti az összes hiányzó, alapként szolgáló Docker-rendszerképet. Ez a rész eltarthat egy ideig
* Létrehozza (vagy újraépíti) a kódprojekt üzemeltetésére szolgáló Docker-rendszerképet.
* Üzembe helyezi és futtatja a tárolót a helyi Service Fabric fejlesztési fürtön.
* Futtatja a szolgáltatásokat, és kezeli a beállított töréspontokat.

Miután a helyi üzembe helyezés befejeződött, és a Visual Studio elindította az alkalmazást, egy böngészőablakban megnyílik a mintául szolgáló alapértelmezett weboldal.

## <a name="debugging-tips"></a>Hibakeresési tippeket

Az első hibakeresés (F5) sokkal gyorsabban futtassa a következő témakör utasításait követve ellenőrizze [teljesítmény optimalizálása a Visual Studio](service-fabric-mesh-howto-optimize-vs.md).

Jelenleg egy olyan probléma, hatására a hívás `using (HttpResponseMessage response = client.GetAsync("").GetAwaiter().GetResult())` sikertelen csatlakozni a szolgáltatáshoz. Ez ekkor fordulhat elő, ha a gazdagép IP-címe megváltozik. A probléma megoldása:

1. Távolítsa el az alkalmazást a helyi fürtből (a Visual Studio esetében: **Build** > **Clean Solution** [Build > Megoldás eltávolítása]).
2. A Service Fabric Local Cluster Manager alkalmazásban válassza a **Stop Local Cluster** (Helyi fürt leállítása), majd a **Start Local Cluster** (Helyi fürt indítása) elemet.
3. Helyezze ismét üzembe az alkalmazást (a Visual Studióban nyomja le az **F5** gombot).

Ha **Nem fut helyi Service Fabric-fürt** tartalmú hibaüzenetet kap, győződjön meg arról, hogy fut a Service Fabric Local Cluster Manager (LCM), majd kattintson a tálca LCM ikonjára a jobb gombbal, és kattintson a **Start Local Cluster** (Helyi fürt indítása) elemre. A fürt elindítása után térjen vissza a Visual Studióba, és nyomja meg az **F5** billentyűt.

Ha **404-es** hibát kap az alkalmazás indításakor, elképzelhető, hogy helytelenek a **service.yaml** környezeti változói. Győződjön meg arról, hogy az `ApiHostPort` és a `ToDoServiceName` megfelelően, a [környezeti változók létrehozását](https://docs.microsoft.com/azure/service-fabric-mesh/service-fabric-mesh-tutorial-create-dotnetcore#create-environment-variables) ismertető részben leírtak szerint van beállítva.

Ha felépítési hibákat kap a **service.yaml** fájlban, ellenőrizze, hogy tabulátorok helyett szóközöket használ-e a sorok behúzására. Ezenkívül ebben az esetben angol területi beállításokkal kell létrehoznia az alkalmazást.

### <a name="debug-in-visual-studio"></a>Hibakeresés a Visual Studióban

Hibakeresése a Visual Studióban egy Service Fabric-háló alkalmazást, hogy egy helyi Service Fabric fejlesztési fürtöt használ. Annak ellenőrzéséhez, hogy a rendszer miként olvassa be a teendőket a háttérszolgáltatásból, végezze el az OnGet() metódus hibakeresését.
1. Az a **WebFrontEnd** projektben nyissa meg **oldalak** > **Index.cshtml** > **Index.cshtml.cs** és állítsa be a Töréspont az a **OnGet** metódus (17. sor).
2. Az a **ToDoService** projektben nyissa meg **TodoController.cs** és állítson be egy töréspontot a **első** metódus (15. sor).
3. Térjen vissza a böngészőbe, és frissítse az oldalt. Az első töréspont a webes kezelőfelület `OnGet()` metódusában jelentkezik. A `backendUrl` változó ellenőrzésével megvizsgálhatja, hogy a rendszer hogyan vonja össze a **service.yaml** fájlban megadott környezeti változókat a háttérszolgáltatással való kapcsolatfelvételhez használt URL-ben.
4. Lépje át a `client.GetAsync(backendUrl).GetAwaiter().GetResult())` hívást (F10), és a következő töréspont a vezérlő `Get()`metódusában jelentkezik. Ezzel a módszerrel láthatja, hogy a rendszer hogyan olvassa be a teendők listáját a memóriabeli listából.
5. Amikor végzett, nyomja le a Visual Studióban a project hibakeresésének leállításához **Shift + F5**.

## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen része az alábbiakat ismertette:

> [!div class="checklist"]
> * Mi történik az Azure Service Fabric mash-alkalmazások fejlesztésekor
> * Hogyan állítható be egy töréspont a szolgáltatások közti hívás figyelése céljából

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Service Fabric Mesh-alkalmazás üzembe helyezése](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)
