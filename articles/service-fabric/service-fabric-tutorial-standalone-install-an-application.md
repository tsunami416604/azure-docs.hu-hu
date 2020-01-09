---
title: Alkalmazás telepítése önálló fürtre
description: Ebből az oktatóanyagból megtudhatja, hogyan telepíthet alkalmazást önálló Service Fabric-fürtre.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 124fc6035c1d0ad504a34b7db227f0f4675f24f7
ms.sourcegitcommit: 003e73f8eea1e3e9df248d55c65348779c79b1d6
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/02/2020
ms.locfileid: "75613958"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Oktatóanyag: Alkalmazás üzembe helyezése a különálló Service Fabric-fürtön

Az önálló Service Fabric-fürtök lehetővé teszik, hogy kiválassza a saját környezetét, és hogy a Service Fabric „bármely operációs rendszer, bármilyen felhő” módszerével hozzon létre egy fürtöt. Ebben az oktatóanyag-sorozatban létrehozunk egy AWS-ben üzemeltetett önálló fürtöt, és üzembe helyezünk egy alkalmazást.

Ez az oktatóanyag egy sorozat harmadik része.  Service Fabric önálló fürtök lehetővé teszi, hogy kiválassza a saját környezetét, és hozzon létre egy fürtöt a "bármely operációs rendszer, bármilyen felhő" megközelítés részeként a Service Fabric használatával. Ez az oktatóanyag bemutatja, hogyan hozhatja létre az önálló fürt futtatásához szükséges AWS-infrastruktúrát.

A sorozat harmadik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A mintaalkalmazás letöltése
> * Üzembe helyezés a fürtön

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* [Telepítse a Visual Studio 2019](https://www.visualstudio.com/) alkalmazást, és telepítse az **Azure fejlesztési** és **ASP.net, valamint a webes fejlesztési** feladatokat.
* [A Service Fabric SDK telepítése](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>A mintául szolgáló szavazóalkalmazás letöltése

Egy parancssori ablakban futtassa a következő parancsot a mintaalkalmazás-adattár helyi számítógépre történő klónozásához.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Az alkalmazás üzembe helyezése a Service Fabric-fürtben

Az alkalmazás letöltése után telepítheti azt a fürtön, közvetlenül a Visual Studióból.

1. A Visual Studio megnyitása

2. Válassza a **Fájl** > **Megnyitás** elemet

3. Navigáljon ahhoz a mappához, amelybe a Git-adattárat klónozta, és válassza ki a Voting.sln fájlt

4. A Megoldáskezelőben kattintson a jobb gombbal a `Voting` alkalmazásra, és válassza a **Közzététel** lehetőséget.

5. Kattintson a **Kapcsolati végpont** legördülő menüjére, és írja be a fürtben lévő egyik csomópont DNS-nevét.  Például: `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. Vegye figyelembe, hogy az Azure-ban a teljes tartománynév (FQDN) nem lesz automatikusan megadva, de [a virtuális gép áttekintő oldalán egyszerűen beállítható.](https://docs.microsoft.com/azure/virtual-machines/linux/portal-create-fqdn)

6. Nyissa meg az előnyben részesített böngészőt, és adja meg a fürt címét (a kapcsolati végpontot; ez az alkalmazás a 8080-as porton van üzembe helyezve; például: ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![A fürttől érkező API-válasz](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>Következő lépések

A sorozat harmadik részében megismerte, hogyan helyezhet üzembe egy alkalmazást a fürtön:

> [!div class="checklist"]
> * A mintaalkalmazás letöltése
> * Üzembe helyezés a fürtön

A sorozat negyedik részében megtisztítja a fürtöt a felesleges erőforrásoktól.

> [!div class="nextstepaction"]
> [Az erőforrások törlése](service-fabric-tutorial-standalone-clean-up.md)