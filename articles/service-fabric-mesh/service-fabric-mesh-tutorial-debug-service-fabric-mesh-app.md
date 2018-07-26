---
title: 'Oktatóanyag: Hibakeresés az Azure Service Fabric mesh-alkalmazásokban | Microsoft Docs'
description: Ebben az oktatóanyagban egy olyan Azure Service Fabric mesh-alkalmazásban végez hibakeresést, amely a helyi fürtön fut.
services: service-fabric-mesh
documentationcenter: .net
author: TylerMSFT
manager: jeconnoc
editor: ''
ms.assetid: ''
ms.service: service-fabric-mesh
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 07/17/2018
ms.author: twhitney
ms.custom: mvc, devcenter
ms.openlocfilehash: e39e5861c0606c43a869fe02a7de2dc9b6f489ea
ms.sourcegitcommit: b9786bd755c68d602525f75109bbe6521ee06587
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/18/2018
ms.locfileid: "39125653"
---
# <a name="tutorial-debug-a-service-fabric-mesh-web-application"></a>Oktatóanyag: Hibakeresés a Service Fabric mesh-webalkalmazásokban

Ez az oktatóanyag a sorozat második része, amely az Azure Service Fabric mash-webalkalmazások helyi fejlesztői fürtön történő hibakeresését ismerteti.

Ebben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * Mi történik az Azure Service Fabric mash-alkalmazások fejlesztésekor
> * Hogyan állítható be egy töréspont a szolgáltatások közti hívás figyelése céljából

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [Service Fabric mesh-alkalmazás fejlesztése](service-fabric-mesh-tutorial-create-dotnetcore.md)
> * Helyi hibakeresés az alkalmazásban
> * [Az alkalmazás közzététele az Azure-ban](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)

[!INCLUDE [preview note](./includes/include-preview-note.md)]

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, kezdés előtt [létrehozhat egy ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

* Győződjön meg arról, hogy [beállította a fejlesztési környezetet](service-fabric-mesh-howto-setup-developer-environment-sdk.md), és azon belül elvégezte a Service Fabric futtatókörnyezet, az SDK, a Docker és a Visual Studio 2017 telepítését.

## <a name="download-the-to-do-sample-application"></a>A teendőlista-mintaalkalmazás letöltése

Ha nem hozta létre a teendőlista-mintaalkalmazást az [oktatóanyag-sorozat első részében](service-fabric-mesh-tutorial-create-dotnetcore.md), le is töltheti azt. Egy parancssori ablakban futtassa a következő parancsot a mintaalkalmazás-adattár helyi számítógépre történő klónozásához.

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

**Hibakeresési tippek**

* Ha **Nem fut helyi Service Fabric-fürt** tartalmú hibaüzenetet kap, győződjön meg arról, hogy fut a Service Local Cluster Manager (SLCM), majd kattintson a tálca SLCM ikonjára a jobb gombbal, és kattintson a **Helyi fürt indítása** elemre. A fürt elindítása után térjen vissza a Visual Studióba, és nyomja meg az **F5** billentyűt.
* Ha **404-es** hibát kap az alkalmazás indításakor, elképzelhető, hogy helytelenek a **service.yaml** környezeti változói. Ellenőrizze, hogy az `AppName`, az `ApiHostPort` és a `ServiceName` helyesen van-e beállítva a [Környezeti változók beállítása](#set-environment-variables) című cikk utasításai szerint.
* Ha felépítési hibákat kap a **service.yaml** fájlban, ellenőrizze, hogy tabulátorok helyett szóközöket használ-e a sorok behúzására.

### <a name="debug-in-visual-studio"></a>Hibakeresés a Visual Studióban

A Service Fabric mesh-alkalmazás Visual Studióban történő hibakeresése során egy helyi Service Fabric fejlesztési fürtöt használunk. Annak ellenőrzéséhez, hogy a rendszer miként olvassa be a teendőket a háttérszolgáltatásból, végezze el az OnGet() metódus hibakeresését.
1. A **WebFrontEnd** projektben nyissa meg az **Oldalak** > **Index.cshtml** > **Index.cshtml.cs** fájlt, és állítson be egy töréspontot a **Get** metódusban (17. sor).
2. A **ToDoService** projektben nyissa meg a **TodoController.cs** fájlt, és állítson be egy töréspontot az **OnGet** metódusban (15. sor).
3. Térjen vissza a böngészőbe, és frissítse az oldalt. Az első töréspont a webes kezelőfelület `OnGet()` metódusában jelentkezik. A `backendUrl` változó ellenőrzésével megvizsgálhatja, hogy a rendszer hogyan vonja össze a **service.yaml** fájlban megadott környezeti változókat a háttérszolgáltatással való kapcsolatfelvételhez használt URL-ben.
4. Lépje át a `client.GetAsync(backendUrl).GetAwaiter().GetResult())` hívást (F10), és a következő töréspont a vezérlő `Get()`metódusában jelentkezik. Ezzel a módszerrel láthatja, hogy a rendszer hogyan olvassa be a teendők listáját a memóriabeli listából.
5. Ha végzett, a **Shift + F5** billentyűkombináció használatával állíthatja le a projekt hibakeresését a Visual Studióban.
 
## <a name="next-steps"></a>További lépések

Az oktatóanyag jelen része az alábbiakat ismertette:

> [!div class="checklist"]
> * Mi történik az Azure Service Fabric mash-alkalmazások fejlesztésekor
> * Hogyan állítható be egy töréspont a szolgáltatások közti hívás figyelése céljából

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [Service Fabric mesh-webalkalmazás közzététele](service-fabric-mesh-tutorial-deploy-service-fabric-mesh-app.md)