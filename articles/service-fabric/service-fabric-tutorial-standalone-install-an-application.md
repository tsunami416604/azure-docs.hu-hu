---
title: Alkalmazás telepítése önálló fürtre
description: Ebben az oktatóanyagban megtudhatja, hogyan telepíthet egy alkalmazást az önálló Service Fabric-fürtbe.
author: dkkapur
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 124fc6035c1d0ad504a34b7db227f0f4675f24f7
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75613958"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Oktatóanyag: Alkalmazás üzembe helyezése a különálló Service Fabric-fürtön

Az önálló Service Fabric-fürtök lehetővé teszik, hogy kiválassza a saját környezetét, és hogy a Service Fabric „bármely operációs rendszer, bármilyen felhő” módszerével hozzon létre egy fürtöt. Ebben az oktatóanyag-sorozatban hozzon létre egy önálló fürtöt az AWS-en, és telepítsen egy alkalmazást.

Ez az oktatóanyag egy sorozat harmadik része.  A Service Fabric önálló fürtök lehetőséget kínálnak a saját környezet kiválasztására, és hozzon létre egy fürtet a Service Fabric "bármely operációs rendszer, bármilyen felhő" megközelítés részeként. Ez az oktatóanyag bemutatja, hogyan hozhatja létre az önálló fürt futtatásához szükséges AWS-infrastruktúrát.

A sorozat harmadik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A mintaalkalmazás letöltése
> * Üzembe helyezés a fürtön

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* [Telepítse a Visual Studio 2019-et,](https://www.visualstudio.com/) és telepítse az **Azure fejlesztési** és **ASP.NET és webfejlesztési** munkaterheléseit.
* [A Service Fabric SDK telepítése](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>A mintául szolgáló szavazóalkalmazás letöltése

Egy parancssori ablakban futtassa a következő parancsot a mintaalkalmazás-adattár helyi számítógépre történő klónozásához.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Az alkalmazás üzembe helyezése a Service Fabric-fürtben

Az alkalmazás letöltése után telepítheti azt a fürtön, közvetlenül a Visual Studióból.

1. A Visual Studio megnyitása

2. **Fájl** > **megnyitása**

3. Navigáljon ahhoz a mappához, amelybe a Git-adattárat klónozta, és válassza ki a Voting.sln fájlt

4. A Megoldáskezelőben kattintson a jobb gombbal a `Voting` alkalmazásra, és válassza a **Közzététel** lehetőséget.

5. Kattintson a **Kapcsolati végpont** legördülő menüjére, és írja be a fürtben lévő egyik csomópont DNS-nevét.  Például: `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`. Vegye figyelembe, hogy az Azure-ban egy teljesen minősített tartománynév (FQDN) nem adja meg automatikusan, de könnyen [beállítható a virtuális gép áttekintése lapon.](https://docs.microsoft.com/azure/virtual-machines/linux/portal-create-fqdn)

6. Nyissa meg az előnyben részesített böngészőt, és adja meg a fürt címét (a kapcsolati végpontot; ez az alkalmazás a 8080-as porton van üzembe helyezve; például: ec2-34-215-183-77.us-west-2.compute.amazonaws.com:8080).

    ![A fürttől érkező API-válasz](./media/service-fabric-tutorial-standalone-cluster/deployed-app.png)

## <a name="next-steps"></a>További lépések

A sorozat harmadik részében megismerte, hogyan helyezhet üzembe egy alkalmazást a fürtön:

> [!div class="checklist"]
> * A mintaalkalmazás letöltése
> * Üzembe helyezés a fürtön

A sorozat negyedik részében megtisztítja a fürtöt a felesleges erőforrásoktól.

> [!div class="nextstepaction"]
> [Az erőforrások törlése](service-fabric-tutorial-standalone-clean-up.md)