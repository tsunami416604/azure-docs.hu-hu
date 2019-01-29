---
title: Service Fabric-alkalmazás üzembe helyezése egy fürtön az Azure-ban | Microsoft Docs
description: Ismerje meg, hogyan helyezhető üzembe egy alkalmazás egy fürtön a Visual Studióból.
services: service-fabric
documentationcenter: .net
author: rwike77
manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 01/14/2019
ms.author: ryanwi,mikhegn
ms.custom: mvc
ms.openlocfilehash: 2ddc0376e256a977d7d14ea10b610fcd5861e7c8
ms.sourcegitcommit: eecd816953c55df1671ffcf716cf975ba1b12e6b
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/28/2019
ms.locfileid: "55100563"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Oktatóanyag: Egy fürtöt az Azure Service Fabric-alkalmazás üzembe helyezése

Ez az oktatóanyag egy sorozat második része. Azt mutatja be, hogy hogyan helyezhetők üzembe az Azure Service Fabric-alkalmazások egy új fürtön az Azure-ban.

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:
> [!div class="checklist"]
> * Hozzon létre egy fürtöt.
> * Alkalmazás üzembe helyezése egy távoli fürtön Visual Studio használatával.

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [.NET Service Fabric-alkalmazás létrehozása](service-fabric-tutorial-create-dotnet-app.md).
> * Az alkalmazás üzembe helyezése egy távoli fürtön.
> * [HTTPS-végpont hozzáadása ASP.NET Core kezelőfelületi szolgáltatáshoz](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md).
> * [A CI/CD konfigurálása az Azure Pipelines használatával](service-fabric-tutorial-deploy-app-with-cicd-vsts.md).
> * [Figyelés és diagnosztika beállítása az alkalmazáshoz](service-fabric-tutorial-monitoring-aspnet.md).

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Telepítse a Visual Studio 2017-et](https://www.visualstudio.com/) az **Azure-fejlesztési**, valamint az **ASP.NET- és webfejlesztési** számítási feladatokkal.
* [A Service Fabric SDK telepítése](service-fabric-get-started.md).

## <a name="download-the-voting-sample-application"></a>A mintául szolgáló szavazóalkalmazás letöltése

Ha nem hozta létre a mintául szolgáló szavazóalkalmazást [az oktatóanyag-sorozat első részében](service-fabric-tutorial-create-dotnet-app.md), akkor le is töltheti. Egy parancssori ablakban futtassa a következő kódot a klónozza a mintatárházat alkalmazás a helyi gépen.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

Nyissa meg az alkalmazás a Visual Studióban, a rendszergazda futtató, és hozza létre az alkalmazást.

## <a name="create-a-cluster"></a>Fürt létrehozása

Most, hogy az alkalmazás készen áll, Service Fabric-fürt létrehozása, és telepítheti az alkalmazást a fürtön. A [Service Fabric-fürt](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-anywhere) virtuális és fizikai gépek hálózaton keresztül csatlakozó készlete, amelyen mikroszolgáltatásokat helyezhet üzembe és felügyelhet.

Ebben az oktatóanyagban egy új háromcsomópont tesztfürt létrehozása a Visual Studio ide, és tegye közzé az alkalmazást a fürtön. Tekintse meg a [létrehozása és kezelése a fürtöket bemutató oktatóanyaggal](service-fabric-tutorial-create-vnet-and-windows-cluster.md) egy fürt létrehozásával kapcsolatos információkat. Az alkalmazás egy meglévő fürthöz korábban létrehozott keresztül is telepíthet a [az Azure portal](https://portal.azure.com), használatával [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) vagy [Azure CLI-vel](./scripts/cli-create-cluster.md) parancsfájlok, vagy egy [Azure Resource Manager-sablon](service-fabric-tutorial-create-vnet-and-windows-cluster.md).

> [!NOTE]
> A szavazóalkalmazást, és számos egyéb alkalmazás szolgáltatások közötti kommunikáció a Service Fabric fordított proxy használatával. A Visual Studióból létrehozott fürtök a fordított proxy alapértelmezés szerint engedélyezve van. Ha meglévő fürtöt telepít, meg kell [a fürtben a fordított proxy engedélyezése](service-fabric-reverseproxy-setup.md) a Voting alkalmazás működéséhez.


### <a name="find-the-votingweb-service-endpoint"></a>A VotingWeb szolgáltatásvégpontjának megkeresése

Egy adott porton figyeli a szavazóalkalmazás webes előtér-szolgáltatás (Ha követte a lépéseket a 8080-as [oktatóanyag-sorozat része](service-fabric-tutorial-create-dotnet-app.md). Az alkalmazások Azure-beli fürtön való üzembe helyezésekor a fürt és az alkalmazás is Azure-terheléselosztó mögött fut. Az alkalmazásport kell megnyitnia az Azure load balancerben szabály segítségével. A szabály a web Service a terheléselosztó bejövő forgalmat küld. Ezt a portot a **VotingWeb/PackageRoot/ServiceManifest.xml** fájlban találhatja meg az **Endpoint** elemben. 

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Jegyezze fel a szolgáltatási végpont, amelyet egy későbbi lépésben szükség lesz.  Helyez üzembe egy meglévő fürthöz, nyissa meg ezt a portot a terheléselosztási szabályok és mintavétel létrehozása az Azure load balancer használatával egy [PowerShell-parancsprogram](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) vagy a fürt számára a terheléselosztó a [Azure Portalon ](https://portal.azure.com).

### <a name="create-a-test-cluster-in-azure"></a>Egy tesztfürt létrehozása az Azure-ban
A Megoldáskezelőben kattintson a jobb gombbal a **Szavazás** elemre, majd válassza a **Közzététel** lehetőséget.

A **kapcsolati végpont**válassza **új fürt létrehozása**.  Helyez üzembe egy meglévő fürthöz, a listából válassza ki a fürt azon végpontján.  A Service Fabric-fürt létrehozása párbeszédpanel nyílik meg.

Az a **fürt** lapra, adja meg a **fürtnév** (például "mytestcluster"), válassza ki az előfizetését, válassza ki a régiót, a fürt (például az USA déli középső Régiója), adja meg a fürt csomópontjai (a Microsoft számát három csomópont esetén ajánlott), és adjon meg egy erőforráscsoportot (például "mytestclustergroup"). Kattintson a **tovább**.

![Fürt létrehozása](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

Az a **tanúsítvány** lapra, adja meg a jelszót és a kimeneti elérési utat a fürttanúsítvány. Egy önaláírt tanúsítványt, a PFX-fájlok létrehozása és mentése a megadott kimeneti elérési utat.  A tanúsítvány-csomópontok és az ügyfél és a csomópont közötti biztonsághoz használható.  Önaláírt tanúsítványok nem használandó éles fürtök esetén.  Ezt a tanúsítványt használják a Visual Studio a fürtön a hitelesítéshez és az alkalmazás üzembe helyezését. Válassza ki **tanúsítvány importálása** , telepítse a PFX a CurrentUser\My a számítógép tanúsítványtárolójában.  Kattintson a **tovább**.

![Fürt létrehozása](./media/service-fabric-tutorial-deploy-app-to-party-cluster/certificate.png)

Az a **virtuális gép részletei** lapra, adja meg a **felhasználónév** és **jelszó** a fürt rendszergazdai fiók számára.  Válassza ki a **virtuálisgép-lemezkép** a fürtcsomópontok és a **virtuálisgép-méret** a fürt minden csomópontján.  Kattintson a **speciális** fülre.

![Fürt létrehozása](./media/service-fabric-tutorial-deploy-app-to-party-cluster/vm-detail.png)

A **portok**, adja meg a VotingWeb szolgáltatás végpont az előző lépésben (például a 8080-as).  A fürt létrehozásakor ezek alkalmazás portokat a az Azure load balancer továbbítja a forgalmat a fürthöz.  Kattintson a **létrehozás** hozhat létre a fürt, amely több percig tart.

![Fürt létrehozása](./media/service-fabric-tutorial-deploy-app-to-party-cluster/advanced.png)

## <a name="publish-the-application-to-the-cluster"></a>Közzé az alkalmazást a fürtön

Amikor készen áll az új fürtre, telepíthet a szavazóalkalmazás közvetlenül a Visual Studióból.

A Megoldáskezelőben kattintson a jobb gombbal a **Szavazás** elemre, majd válassza a **Közzététel** lehetőséget. Megjelenik a **Publish** (Közzététel) párbeszédpanel.

A **kapcsolati végpont**, válassza ki az előző lépésben létrehozott fürt végpontját.  Például "mytestcluster.southcentral.cloudapp.azure.com:19000." Ha **speciális kapcsolati paraméterek**, a tanúsítvány adatait az automatikusan kitöltött kell lennie.  
![Service Fabric-alkalmazás közzététele](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

Kattintson a **Publish** (Közzététel) elemre.

Az alkalmazás üzembe helyezése után nyisson meg egy böngészőt, és adja meg a fürt címét, majd **: 8080-as**. Vagy adja meg egy másik konfigurált port számát. Például: `http://mytestcluster.southcentral.cloudapp.azure.com:8080`. Ezután megjelenik a fürtön futó alkalmazás az Azure-ban. A szavazás weboldalán próbáljon hozzáadni és törölni szavazási lehetőségeket, valamint szavazni ezek közül egyre vagy többre.

![Service Fabric szavazási minta](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-screenshot-new-azure.png)


## <a name="next-steps"></a>További lépések
Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * Hozzon létre egy fürtöt.
> * Alkalmazás üzembe helyezése egy távoli fürtön Visual Studio használatával.

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [HTTPS engedélyezése](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
