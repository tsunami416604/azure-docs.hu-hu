---
title: Service Fabric-alkalmazás üzembe helyezése fürtre az Azure-ban
description: Ismerje meg, hogyan telepíthet egy meglévő alkalmazást egy újonnan létrehozott Azure Service Fabric-fürtre a Visual Studióból.
author: athinanthny
ms.topic: tutorial
ms.date: 07/22/2019
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: 9951610732cbb1c5884a7b7e830033f427db0ab1
ms.sourcegitcommit: 0947111b263015136bca0e6ec5a8c570b3f700ff
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/24/2020
ms.locfileid: "75646007"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Oktatóanyag: Service Fabric-alkalmazás üzembe helyezése egy fürtön az Azure-ban

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
> * [Állítsa be az alkalmazás figyelését és diagnosztikát.](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot.](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
* [Telepítse a Visual Studio 2019-et](https://www.visualstudio.com/), és telepítse az **Azure fejlesztési** és **ASP.NET és webfejlesztési** számítási feladatait.
* [Telepítse a Service Fabric SDK-t.](service-fabric-get-started.md)

> [!NOTE]
> Előfordulhat, hogy egy ingyenes fiók nem felel meg a virtuális gépek létrehozásához támasztott követelményeknek. Ez megakadályozza az oktatóanyag befejezését. Emellett egy nem munkahelyi vagy nem iskolai fiók engedélyproblémákkal szembesülhet a fürthöz társított keyvault tanúsítványának létrehozása közben. Ha a tanúsítványkészítéssel kapcsolatos hibát tapasztal, használja a portált a fürt létrehozásához. 

## <a name="download-the-voting-sample-application"></a>A mintául szolgáló szavazóalkalmazás letöltése

Ha nem hozta létre a mintául szolgáló szavazóalkalmazást [az oktatóanyag-sorozat első részében](service-fabric-tutorial-create-dotnet-app.md), akkor le is töltheti. A parancsablakban futtassa a következő kódot a mintaalkalmazás-tárház helyi számítógépre történő klónozásához.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart 
```

Nyissa meg az alkalmazást a Visual Studióban, amely rendszergazdaként fut, és létrehozza az alkalmazást.

## <a name="create-a-cluster"></a>Fürt létrehozása

Most, hogy az alkalmazás készen áll, hozzon létre egy Service Fabric-fürtöt, majd telepítse az alkalmazást a fürtre. A [Service Fabric-fürt](https://docs.microsoft.com/azure/service-fabric/service-fabric-deploy-anywhere) olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a mikroszolgáltatások üzembe helyezése és kezelése történik.

Ebben az oktatóanyagban egy új három csomópontteszt-fürtöt hoz létre a Visual Studio IDE-ben, majd közzéteszi az alkalmazást a fürtben. Az éles fürt [létrehozásáról](service-fabric-tutorial-create-vnet-and-windows-cluster.md) a Fürt létrehozása és kezelése című témakörben talál információt. Az alkalmazást egy meglévő fürtre is telepítheti, amelyet korábban az [Azure Portalon](https://portal.azure.com)keresztül hozott [létre, PowerShell-](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) vagy [Azure CLI-parancsfájlok](./scripts/cli-create-cluster.md) használatával, vagy egy Azure [Resource Manager-sablonból.](service-fabric-tutorial-create-vnet-and-windows-cluster.md)

> [!NOTE]
> A szavazási alkalmazás, és sok más alkalmazások, használja a Service Fabric fordított proxy szolgáltatások közötti kommunikációhoz. A Visual Studio programból létrehozott fürtök alapértelmezés szerint engedélyezve vannak a fordított proxyval. Ha egy meglévő fürtre telepít, engedélyeznie kell [a fordított proxyt a fürtben](service-fabric-reverseproxy-setup.md) a szavazási alkalmazás működéséhez.


### <a name="find-the-votingweb-service-endpoint"></a>A VotingWeb szolgáltatásvégpontjának megkeresése

A szavazási alkalmazás előtér-webszolgáltatása egy adott porton hallgat (8080, ha követte az [oktatóanyag-sorozat első részének](service-fabric-tutorial-create-dotnet-app.md)lépéseit. Az alkalmazások Azure-beli fürtön való üzembe helyezésekor a fürt és az alkalmazás is Azure-terheléselosztó mögött fut. Az alkalmazásportot meg kell nyitni az Azure terheléselosztóban egy szabály használatával. A szabály bejövő forgalmat küld a terheléselosztón keresztül a webszolgáltatásnak. Ezt a portot a **VotingWeb/PackageRoot/ServiceManifest.xml** fájlban találhatja meg az **Endpoint** elemben. 

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

Vegye figyelembe a szolgáltatás végpontját, amelyre egy későbbi lépésben van szükség.  Ha egy meglévő fürtre telepít, nyissa meg ezt a portot egy terheléselosztási szabály létrehozásával, és az Azure terheléselosztóban egy [PowerShell-parancsfájl](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) vagy a fürt terheléselosztóján keresztül az [Azure Portalon.](https://portal.azure.com)

### <a name="create-a-test-cluster-in-azure"></a>Tesztfürt létrehozása az Azure-ban
A Megoldáskezelőben kattintson a jobb gombbal a **Szavazás** elemre, majd válassza a **Közzététel** lehetőséget.

A **Kapcsolatvégpont csoportban**válassza **az Új fürt létrehozása**lehetőséget.  Ha egy meglévő fürtre telepít, válassza ki a fürtvégpontot a listából.  Megnyílik a Szolgáltatásháló-fürt létrehozása párbeszédpanel.

A **Fürt** lapon adja meg a **fürt nevét** (például "mytestcluster"), válassza ki az előfizetést, válasszon ki egy régiót a fürthöz (például USA déli középső régiója), adja meg a fürtcsomópontok számát (három csomópontot ajánlunk egy tesztfürthöz), és adjon meg egy erőforráscsoportot (például "mytestclustergroup"). Kattintson a **Tovább** gombra.

![Fürt létrehozása](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

A **Tanúsítvány** lapon adja meg a fürttanúsítvány jelszavát és kimeneti elérési útját. Az önaláírt tanúsítvány PFX-fájlként jön létre, és a megadott kimeneti elérési útra kerül.  A tanúsítvány a csomópont-csomópont és az ügyfél-csomópont biztonsághoz is használható.  Ne használjon önaláírt tanúsítványt éles fürtökhöz.  Ezt a tanúsítványt a Visual Studio a fürttel való hitelesítésre és az alkalmazások központi telepítésére használja. Válassza a **Tanúsítvány importálása** lehetőséget a számítógép CurrentUser\Saját tanúsítványtárolóban történő telepítéséhez.  Kattintson a **Tovább** gombra.

![Fürt létrehozása](./media/service-fabric-tutorial-deploy-app-to-party-cluster/certificate.png)

A **Virtuálisgép részletei** lapon adja meg a fürt felügyeleti fiókjának **felhasználónevét** és **jelszavát.**  Válassza ki a **fürtcsomópontok virtuálisgép-lemezképét** és az egyes fürtcsomópontok **virtuálisgép-méretét.**  Kattintson a **Speciális** fülre.

![Fürt létrehozása](./media/service-fabric-tutorial-deploy-app-to-party-cluster/vm-detail.png)

A **Portok mezőbe**írja be az előző lépés (például 8080) votingweb szolgáltatás végpontját.  A fürt létrehozásakor ezek az alkalmazásportok megnyílnak az Azure terheléselosztóban a forgalom fürtre történő továbbításához.  Kattintson a **Létrehozás** gombra a fürt létrehozásához, amely több percet vesz igénybe.

![Fürt létrehozása](./media/service-fabric-tutorial-deploy-app-to-party-cluster/advanced.png)

## <a name="publish-the-application-to-the-cluster"></a>Az alkalmazás közzététele a fürtben

Amikor az új fürt készen áll, telepítheti a szavazási alkalmazást közvetlenül a Visual Studióból.

A Megoldáskezelőben kattintson a jobb gombbal a **Szavazás** elemre, majd válassza a **Közzététel** lehetőséget. Megjelenik a **Publish** (Közzététel) párbeszédpanel.

A **Kapcsolatvégpont ban**jelölje ki az előző lépésben létrehozott fürt végpontját.  Például "mytestcluster.southcentral.cloudapp.azure.com:19000". Ha a **Speciális kapcsolati paraméterek lehetőséget választja,** a tanúsítványadatokat automatikusan ki kell tölteni.  
![Service Fabric-alkalmazás közzététele](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

Kattintson a **Publish** (Közzététel) elemre.

Az alkalmazás telepítése után nyisson meg egy böngészőt, és adja meg a fürt címét, majd **a :8080**értéket. Vagy adja meg egy másik konfigurált port számát. Például: `http://mytestcluster.southcentral.cloudapp.azure.com:8080`. Ezután megjelenik a fürtön futó alkalmazás az Azure-ban. A szavazás weboldalán próbáljon hozzáadni és törölni szavazási lehetőségeket, valamint szavazni ezek közül egyre vagy többre.

![Service Fabric szavazási minta](./media/service-fabric-tutorial-deploy-app-to-party-cluster/application-screenshot-new-azure.png)


## <a name="next-steps"></a>További lépések
Az oktatóanyag jelen részében megismerkedhetett a következőkkel:

> [!div class="checklist"]
> * Hozzon létre egy fürtöt.
> * Alkalmazás üzembe helyezése egy távoli fürtön Visual Studio használatával.

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [HTTPS engedélyezése](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
