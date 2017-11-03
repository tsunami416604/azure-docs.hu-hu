---
title: "Alkalmazás üzembe helyezése Azure Service Fabric fél fürthöz |} Microsoft Docs"
description: "Megtudhatja, hogyan fél fürtre alkalmazás központi telepítése."
services: service-fabric
documentationcenter: .net
author: mikkelhegn
manager: msfussell
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/09/2017
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: 5766ef2097b0da295d42e7c5909efc524049f418
ms.sourcegitcommit: 6699c77dcbd5f8a1a2f21fba3d0a0005ac9ed6b7
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/11/2017
---
# <a name="deploy-an-application-to-a-party-cluster-in-azure"></a>Alkalmazás üzembe helyezése az Azure-ban fél fürtre
Ez az oktatóanyag két egy sor és bemutatja, hogyan fél fürthöz az Azure-ban az Azure Service Fabric-alkalmazás központi telepítéséről.

A második rész az oktatóanyag sorozatának, megismerheti, hogyan:
> [!div class="checklist"]
> * A távoli fürt Visual Studio alkalmazás központi telepítése
> * Az alkalmazás eltávolítása egy fürtről Service Fabric Explorerrel

Az oktatóanyag adatsorozat elsajátíthatja, hogyan:
> [!div class="checklist"]
> * [A .NET Service Fabric-alkalmazás létrehozása](service-fabric-tutorial-create-dotnet-app.md)
> * Telepítse központilag az alkalmazást egy távoli fürthöz
> * [Konfigurálja a CI/CD Visual Studio Team Services használatával](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Figyelés és diagnosztika az alkalmazás beállítása](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag elkezdéséhez:
- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- [Telepítse a Visual Studio 2017](https://www.visualstudio.com/) és telepítse a **Azure fejlesztési** és **ASP.NET és a webes fejlesztési** munkaterhelések.
- [A Service Fabric SDK telepítése](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>Töltse le a szavazási mintaalkalmazást
Ha Ön nem összeállítása a szavazási mintaalkalmazást [rész az oktatóanyag adatsorozat](service-fabric-tutorial-create-dotnet-app.md), tölthető le. Egy parancssorban a következő parancsot a helyi számítógépen, a minta app tárház klónozása.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="set-up-a-party-cluster"></a>Egy entitás fürt beállítása
A nyilvános fürtök ingyenes, korlátozott időtartamú Azure Service Fabric-fürtök, amelyek futtatását a Service Fabric csapata végzi, és amelyeken bárki üzembe helyezhet alkalmazásokat, és megismerkedhet a platform használatával. Az ingyenes!

Egy entitás fürt eléréséhez, keresse meg a hely: http://aka.ms/tryservicefabric és kövesse az utasításokat a fürt eléréséhez. Egy entitás fürt eléréséhez Facebook-on vagy a GitHub-fiók szükséges.

Használhatja a saját fürt fél fürt helyett, ha azt szeretné.  Az ASP.NET core webes előtér-a fordított proxy használ az állapotalapú szolgáltatási háttér-folytatott kommunikációhoz.  Entitás fürtök és a helyi fejlesztési fürtöt kell fordított proxy alapértelmezés szerint engedélyezett.  Ha a saját fürt Voting minta alkalmazást telepít központilag, akkor kell [engedélyezéséhez a fürt fordított proxy](service-fabric-reverseproxy.md#setup-and-configuration).

> [!NOTE]
> Entitás fürtök nem biztonságosak, így az alkalmazások és azok helyezett adatokat esetleg mások is láthatják másoknak. Ne telepítsen semmit nem szeretné, hogy mások is láthatják. Mindenképpen olvassa el a használati feltételek a részletekért keresztül.

## <a name="configure-the-listening-port"></a>A figyelő portja konfigurálása
A VotingWeb előtér-szolgáltatás létrehozásakor, a Visual Studio véletlenszerűen választ egy portot a figyelést a szolgáltatáshoz.  A VotingWeb szolgáltatás úgy működik, mint az előtér-alkalmazás, és elfogadja a külső forgalom, most, hogy a szolgáltatás kötése egy rögzített méretű és jól ismeri port. A Solution Explorerben nyissa meg a *VotingWeb/PackageRoot/ServiceManifest.xml*.  Keresés a **végpont** erőforrás a **erőforrások** szakaszt, és módosítsa a **Port** 80-as értéket.

```xml
<Resources>
    <Endpoints>
      <!-- This endpoint is used by the communication listener to obtain the port on which to 
           listen. Please note that if your service is partitioned, this port is shared with 
           replicas of different partitions that are placed in your code. -->
      <Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="80" />
    </Endpoints>
  </Resources>
```

Az alkalmazás URL-Címének tulajdonság értéke a szavazási projekt frissíteni, egy webböngészőben megnyílik a megfelelő porthoz hibakeresése "F5" használatával.  A Megoldáskezelőben, válassza ki a **Voting** projektet és a frissítés a **alkalmazás URL-Címének** tulajdonság.

![Alkalmazás URL-címe](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-url.png)

## <a name="deploy-the-app-to-the-azure"></a>Az alkalmazás telepítése az Azure-bA
Most, hogy az alkalmazás készen áll, ezután telepítheti azt a közvetlen fél fürthöz a Visual Studio eszközből.

1. Kattintson a jobb gombbal **Voting** a Megoldáskezelőben kattintson **közzététel**.

    ![Publish (Közzététel) párbeszédpanel](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

2. A csatlakozási végpont a fél fürt írja be a **csatlakozási végpont** mezőben, majd kattintson a **közzététel**.

    Miután befejezte a közzététel, kell kérelmet küld az alkalmazás böngésző használatával.

3. Nyissa meg előnyben részesített böngészőt, és írja be a fürt címe (a csatlakozási végpont a port adatok, például win1kw5649s.westus.cloudapp.azure.com nélkül).

    Ekkor megjelenik a ugyanazt az eredményt, ahogy azt az alkalmazást a helyi futtatás során.

    ![A fürt API-válaszból](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="remove-the-application-from-a-cluster-using-service-fabric-explorer"></a>Az alkalmazás eltávolítása egy fürtről Service Fabric Explorerrel
Service Fabric Explorer egy grafikus felhasználói felület vizsgálatát, és a Service Fabric-fürt alkalmazásokat kezeléséhez.

Az alkalmazás eltávolítása a fél fürt:

1. Tallózással keresse meg a Service Fabric Explorer által fél fürt feliratkozáshoz hivatkozás segítségével. Például http://win1kw5649s.westus.cloudapp.azure.com:19080/Explorer/index.html.

2. A Service Fabric Explorerben, navigáljon a **fabric://Voting** csomópontot a bal oldali fastruktúrában.

3. Kattintson a **művelet** jobb oldali gomb **Essentials** ablaktáblán, és válassza a **alkalmazás törlése**. Az alkalmazáspéldány, így a fürtben futó alkalmazás példánya nincs törlésének megerősítéséhez.

![A Service Fabric Explorerben alkalmazás törlése](./media/service-fabric-tutorial-deploy-app-to-party-cluster/delete-application.png)

## <a name="remove-the-application-type-from-a-cluster-using-service-fabric-explorer"></a>Az alkalmazástípus eltávolítása egy fürtről Service Fabric Explorerrel
A Service Fabric-fürt, amely lehetővé teszi több példány és az alkalmazás fut a fürtön belül alkalmazástípusok alkalmazások üzemelnek. Miután eltávolította az alkalmazás futó példányát, azt is eltávolíthatja a típusát, a tisztítás, a központi telepítés befejezéséhez.

A Service Fabric az alkalmazásmodell kapcsolatos további információkért lásd: [egy alkalmazás a Service Fabric modell](service-fabric-application-model.md).

1. Keresse meg a **VotingType** a TreeView vezérlő csomópont.

2. Kattintson a **művelet** jobb oldali gomb **Essentials** ablaktáblán, és válassza a **Unprovision típus**. Ellenőrizze, hogy az alkalmazás típus leépítése.

![A Service Fabric Explorerben alkalmazástípus leépíteni a következőt:](./media/service-fabric-tutorial-deploy-app-to-party-cluster/unprovision-type.png)

Ezzel befejezte az oktatóanyag.

## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * A távoli fürt Visual Studio alkalmazás központi telepítése
> * Az alkalmazás eltávolítása egy fürtről Service Fabric Explorerrel

Előzetes következő oktatóanyagot:
> [!div class="nextstepaction"]
> [Beállíthat folyamatos integrációt, a Visual Studio Team Services használatával](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)