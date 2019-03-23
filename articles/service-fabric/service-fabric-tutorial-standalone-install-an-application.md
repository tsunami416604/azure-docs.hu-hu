---
title: 'Oktatóanyag: Alkalmazás telepítése a különálló Service Fabric-fürtre – Azure Service Fabric | Microsoft Docs'
description: Ebből az oktatóanyagból megismerheti, hogyan telepíthet egy alkalmazást a különálló Service Fabric-fürtön.
services: service-fabric
documentationcenter: .net
author: dkkapur
manager: timlt
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 05/11/2018
ms.author: dekapur
ms.custom: mvc
ms.openlocfilehash: 392fcdd9b9df3eb37c8e105c846a6bddfddeb4d1
ms.sourcegitcommit: 49c8204824c4f7b067cd35dbd0d44352f7e1f95e
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/22/2019
ms.locfileid: "58370431"
---
# <a name="tutorial-deploy-an-application-on-your-service-fabric-standalone-cluster"></a>Oktatóanyag: Önálló Service Fabric-fürtben az alkalmazás üzembe helyezése

Az önálló Service Fabric-fürtök lehetővé teszik, hogy kiválassza a saját környezetét, és hogy a Service Fabric „bármely operációs rendszer, bármilyen felhő” módszerével hozzon létre egy fürtöt. Ebben az oktatóanyag-sorozatban AWS-en futtatott különálló fürtöt hoz létre, és egy alkalmazást helyez üzembe azon.

Ez az oktatóanyag egy sorozat harmadik része.  A különálló Service Fabric-fürtök lehetővé teszik, hogy kiválassza a saját környezetét, és hogy a Service Fabric használatával a „bármely operációs rendszeren vagy felhőben” megközelítésünk részeként hozzon létre egy fürtöt. Ez az oktatóanyag bemutatja, hogyan hozhatja létre az önálló fürt futtatásához szükséges AWS-infrastruktúrát.

A sorozat harmadik részében az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * A mintaalkalmazás letöltése
> * Üzembe helyezés a fürtön

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* [Telepítse a Visual Studio 2017-et](https://www.visualstudio.com/) az **Azure-fejlesztési**, valamint az **ASP.NET- és webfejlesztési** számítási feladatokkal.
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

5. Kattintson a **Kapcsolati végpont** legördülő menüjére, és írja be a fürtben lévő egyik csomópont DNS-nevét.  Például: `ec2-34-215-183-77.us-west-2.compute.amazonaws.com:19000`

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