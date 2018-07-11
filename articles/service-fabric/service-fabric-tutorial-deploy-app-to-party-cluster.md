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
ms.date: 06/28/2018
ms.author: ryanwi,mikhegn
ms.custom: mvc
ms.openlocfilehash: f83ebcce68a7abe53d7b8eaeff5913a907e3df9a
ms.sourcegitcommit: 756f866be058a8223332d91c86139eb7edea80cc
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2018
ms.locfileid: "37344189"
---
# <a name="tutorial-deploy-a-service-fabric-application-to-a-cluster-in-azure"></a>Oktatóanyag: Service Fabric-alkalmazás üzembe helyezése egy fürtön az Azure-ban

Ez az oktatóanyag a sorozat második része, amely azt mutatja be, hogyan helyezhetők üzembe az Azure Service Fabric-alkalmazások egy új fürtön az Azure-ban, közvetlenül a Visual Studióból.

Ezen oktatóanyag segítségével megtanulhatja a következőket:
> [!div class="checklist"]
> * Fürt létrehozása a Visual Studióból
> * Alkalmazás üzembe helyezése egy távoli fürtön Visual Studio használatával

Ebben az oktatóanyag-sorozatban az alábbiakkal ismerkedhet meg:
> [!div class="checklist"]
> * [.NET Service Fabric-alkalmazás létrehozása](service-fabric-tutorial-create-dotnet-app.md)
> * Az alkalmazás üzembe helyezése egy távoli fürtön
> * [HTTPS-végpont hozzáadása ASP.NET Core kezelőfelületi szolgáltatáshoz](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
> * [A CI/CD konfigurálása a Visual Studio Team Services használatával](service-fabric-tutorial-deploy-app-with-cicd-vsts.md)
> * [Figyelés és diagnosztika beállítása az alkalmazáshoz](service-fabric-tutorial-monitoring-aspnet.md)

## <a name="prerequisites"></a>Előfeltételek

Az oktatóanyag elkezdése előtt:

* Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
* [Telepítse a Visual Studio 2017-et](https://www.visualstudio.com/) az **Azure-fejlesztési**, valamint az **ASP.NET- és webfejlesztési** számítási feladatokkal.
* [A Service Fabric SDK telepítése](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>A mintául szolgáló szavazóalkalmazás letöltése

Ha nem hozta létre a mintául szolgáló szavazóalkalmazást [az oktatóanyag-sorozat első részében](service-fabric-tutorial-create-dotnet-app.md), akkor le is töltheti. Egy parancssori ablakban futtassa a következő parancsot a mintaalkalmazás-adattár helyi számítógépre történő klónozásához.

```git
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="create-a-service-fabric-cluster"></a>Service Fabric-fürt létrehozása

Az alkalmazást a létrehozása után telepítheti a fürtben, közvetlenül a Visual Studióból. A [Service Fabric-fürt](/service-fabric/service-fabric-deploy-anywhere.md) virtuális és fizikai gépek hálózaton keresztül csatlakozó készlete, amelyen mikroszolgáltatásokat helyezhet üzembe és felügyelhet

A Visual Studióból való üzembe helyezéshez két lehetősége van:

* Egy fürt létrehozása az Azure-ban a Visual Studióból. Ez lehetővé teszi, hogy közvetlenül a Visual Studióból hozzon létre egy biztonságos fürtöt a választott konfigurációval. Ez a fürttípus megfelelő az olyan tesztelési helyzetekhez, amelyekben létrehozhatja a fürtöt, és a Visual Studióban közvetlenül erre a fürtre tehet közzé tartalmakat.
* Közzététel az előfizetés egyik meglévő fürtjére.  Service Fabric-fürtöket hozhat létre az [Azure Portal](https://portal.azure.com), a [PowerShell](./scripts/service-fabric-powershell-create-secure-cluster-cert.md) és [Azure CLI](./scripts/cli-create-cluster.md)-szkriptek segítségével, illetve akár [Azure Resource Manager-sablonból](service-fabric-tutorial-create-vnet-and-windows-cluster.md) is.

Ez az oktatóanyag a fürt Visual Studióból való létrehozását ismerteti. Ha már rendelkezik üzembe helyezett fürttel, másolhatja és beillesztheti a kapcsolati végpontot, vagy kiválaszthatja az előfizetésből.
> [!NOTE]
> Számos szolgáltatás fordított proxyt használ az egymással folytatott kommunikációhoz. A Visual Studióból létrehozott fürtök és a nyilvános fürtök esetében a fordított proxy alapértelmezés szerint engedélyezve van.  Ha meglévő fürtöt használ, [engedélyeznie kell a fürtben a fordított proxyt](service-fabric-reverseproxy.md#setup-and-configuration).

### <a name="find-the-votingweb-service-endpoint"></a>A VotingWeb szolgáltatásvégpontjának megkeresése

Első lépésként keresse meg az előtér-webszolgáltatás végpontját.  Az előtér-webszolgáltatás egy adott porton figyeli a kapcsolati kérelmeket.  Az alkalmazások Azure-beli fürtön való üzembe helyezésekor a fürt és az alkalmazás is Azure-terheléselosztó mögött fut.  Az alkalmazásportnak nyitva kell lennie az Azure terheléselosztóján, hogy a bejövő forgalom elérhesse a webszolgáltatást.  Ezt a portot (például 8080) a *VotingWeb/PackageRoot/ServiceManifest.xml* fájlban találhatja meg a **Endpoint** elemben:

```xml
<Endpoint Protocol="http" Name="ServiceEndpoint" Type="Input" Port="8080" />
```

A következő lépésben adja meg ezt a portot a **Fürt létrehozása** párbeszédpanel **Speciális** lapján.  Ha az alkalmazást egy meglévő fürtbe telepíti, megnyithatja ezt a portot az Azure terheléselosztóján egy [PowerShell-szkripttel](./scripts/service-fabric-powershell-open-port-in-load-balancer.md) vagy az [Azure Portal](https://portal.azure.com) segítségével.

### <a name="create-a-cluster-in-azure-through-visual-studio"></a>Fürt létrehozása az Azure-ban a Visual Studióval

A Megoldáskezelőben kattintson a jobb gombbal az alkalmazásra, és válassza a **Közzététel** lehetőséget.

Jelentkezzen be az Azure-fiókjával, hogy hozzáférjen az előfizetéséhez vagy előfizetéseihez. Ez a lépés nem kötelező, ha nyilvános fürtöt használ.

Kattintson a **Kapcsolati végpont** legördülő menüjére, és válassza az **<Create New Cluster...>** lehetőséget.

![Publish (Közzététel) párbeszédpanel](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)

A **Fürt létrehozása** párbeszédpanelen módosítsa az alábbi beállításokat:

1. Adja meg a fürtje nevét a **Fürt neve** mezőben, valamint a használni kívánt előfizetést és helyet.
2. Választható lehetőségként módosíthatja a csomópontok számát. Alapértelmezés szerint három csomópont van. Ez a Service Fabric-forgatókönyvek teszteléséhez szükséges minimális érték.
3. Válassza a **Tanúsítvány** lapot. Ezen a lapon adjon meg egy jelszót a fürt tanúsítványának védelméhez. Ez a tanúsítvány segít a fürt biztonságossá tételében. Annak a helynek az elérési útját is módosíthatja, ahová menteni kívánja a tanúsítványt. A Visual Studióval is importálhatja a tanúsítványt, mert erre a lépésre szükség van az alkalmazás fürtön való közzétételéhez.
4. Válassza a **Virtuális gép részletei** lapot. Adja meg a jelszót, amelyet a fürtöt alkotó virtuális gépekhez kíván használni. A felhasználónév és jelszó használatával távolról csatlakozhat a virtuális gépekhez. A virtuális gép méretét is ki kell választania és ha szükséges, módosíthatja a virtuális gép rendszerképét is.
5. A **Speciális** lapon módosíthatja azoknak a portoknak a listáját, amelyeket szeretne megnyitni a fürttel együtt létrejövő Azure-terheléselosztón.  Adja hozzá a VotingWeb előző lépésben felfedezett szolgáltatásvégpontját. Hozzáadhat egy meglévő Application Insights-kulcsot is, amelyet az alkalmazás naplófájljainak útválasztásához használhat.
6. Ha végzett a beállítások módosításával, kattintson a **Létrehozás** gombra. A létrehozás eltarthat néhány percig, és a kimeneti ablak jelzi, ha a fürt teljesen elkészült.

![Fürt létrehozása párbeszédpanel](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

## <a name="deploy-the-sample-application"></a>A mintaalkalmazás üzembe helyezése

Ha a használni kívánt fürt készen áll, kattintson a jobb gombbal az alkalmazásprojektre, és válassza a **Közzététel** lehetőséget.

Miután a közzététel befejeződött, egy böngészőből kérést küldhet az alkalmazáshoz.

Nyissa meg a kívánt böngészőt, és adja meg a fürt címét (a kapcsolati végpontot a portra vonatkozó információk nélkül, például win1kw5649s.westus.cloudapp.azure.com).

Ekkor ugyanannak az eredménynek kell megjelennie, mint az alkalmazás helyi futtatásakor.

![A fürttől érkező API-válasz](./media/service-fabric-tutorial-deploy-app-to-party-cluster/response-from-cluster.png)

## <a name="next-steps"></a>További lépések

Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * Fürt létrehozása a Visual Studióból
> * Alkalmazás üzembe helyezése egy távoli fürtön Visual Studio használatával

Folytassa a következő oktatóanyaggal:
> [!div class="nextstepaction"]
> [HTTPS engedélyezése](service-fabric-tutorial-dotnet-app-enable-https-endpoint.md)
