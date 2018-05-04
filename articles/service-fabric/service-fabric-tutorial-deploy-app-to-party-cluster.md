---
title: Azure Service Fabric-alkalmazás üzembe helyezése egy fürtön a Visual Studióból | Microsoft Docs
description: Ismerje meg, hogyan helyezhető üzembe egy alkalmazás egy fürtön a Visual Studióból
services: service-fabric
documentationcenter: .net
-author: mikkelhegn
-manager: msfussell
editor: ''
ms.assetid: ''
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 02/23/2018
ms.author: mikhegn
ms.custom: mvc
ms.openlocfilehash: 4f0d41dbc2438217cb4f382da7c44833379b9637
ms.sourcegitcommit: fa493b66552af11260db48d89e3ddfcdcb5e3152
ms.translationtype: HT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2018
---
# <a name="tutorial-deploy-an-application-to-a-service-fabric-cluster-in-azure"></a>Oktatóanyag: Alkalmazás üzembe helyezése egy Service Fabric-fürtön az Azure-ban
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
- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).
- [Telepítse a Visual Studio 2017-et](https://www.visualstudio.com/) az **Azure-fejlesztési**, valamint az **ASP.NET- és webfejlesztési** számítási feladatokkal.
- [A Service Fabric SDK telepítése](service-fabric-get-started.md)

## <a name="download-the-voting-sample-application"></a>A mintául szolgáló szavazóalkalmazás letöltése
Ha nem hozta létre a mintául szolgáló szavazóalkalmazást [az oktatóanyag-sorozat első részében](service-fabric-tutorial-create-dotnet-app.md), akkor le is töltheti. Egy parancssori ablakban futtassa a következő parancsot a mintaalkalmazás-adattár helyi számítógépre történő klónozásához.

```
git clone https://github.com/Azure-Samples/service-fabric-dotnet-quickstart
```

## <a name="deploy-the-sample-application"></a>A mintaalkalmazás üzembe helyezése

### <a name="select-a-service-fabric-cluster-to-which-to-publish"></a>A közzétételhez használni kívánt Service Fabric-fürt kiválasztása
Az alkalmazást a létrehozása után telepítheti a fürtben, közvetlenül a Visual Studióból.

Az üzembe helyezéshez két lehetősége van:
- Egy fürt létrehozása a Visual Studióból. Ez lehetővé teszi, hogy közvetlenül a Visual Studióból hozzon létre egy biztonságos fürtöt a választott konfigurációval. Ez a fürttípus megfelelő az olyan tesztelési helyzetekhez, amelyekben létrehozhatja a fürtöt, és a Visual Studióban közvetlenül erre a fürtre tehet közzé tartalmakat.
- Közzététel az előfizetés egyik meglévő fürtjére.

Ez az oktatóanyag lépésenként bemutatja, hogyan hozhat létre egy fürtöt a Visual Studióban. A többi lehetőséghez másolhatja és beillesztheti a kapcsolati végpontot, vagy kiválaszthatja az előfizetésből is.
> [!NOTE]
> Számos szolgáltatás fordított proxyt használ az egymással folytatott kommunikációhoz. A Visual Studióból létrehozott fürtök és a nyilvános fürtök esetében a fordított proxy alapértelmezés szerint engedélyezve van.  Ha meglévő fürtöt használ, [engedélyeznie kell a fürtben a fordított proxyt](service-fabric-reverseproxy.md#setup-and-configuration).

### <a name="deploy-the-app-to-the-service-fabric-cluster"></a>Az alkalmazás üzembe helyezése a Service Fabric-fürtben
1. A Megoldáskezelőben kattintson a jobb gombbal az alkalmazásra, és válassza a **Közzététel** lehetőséget.

2. Jelentkezzen be az Azure-fiókjával, hogy hozzáférjen az előfizetéséhez vagy előfizetéseihez. Ez a lépés nem kötelező, ha nyilvános fürtöt használ.

3. Kattintson a **Kapcsolati végpont** legördülő menüjére és válassza az <Create New Cluster...> lehetőséget.
    
    ![Publish (Közzététel) párbeszédpanel](./media/service-fabric-tutorial-deploy-app-to-party-cluster/publish-app.png)
    
4. A Fürt létrehozása párbeszédpanelen módosítsa az alábbi beállításokat:

    1. Adja meg a fürtje nevét a Fürt neve mezőben, valamint a használni kívánt előfizetést és helyet.
    2. Választható lehetőségként módosíthatja a csomópontok számát. Alapértelmezés szerint három csomópont van. Ez a Service Fabric-forgatókönyvek teszteléséhez szükséges minimális érték.
    3. Válassza a Tanúsítvány lapot. Ezen a lapon adjon meg egy jelszót a fürt tanúsítványának védelméhez. Ez a tanúsítvány segít a fürt biztonságossá tételében. Annak a helynek az elérési útját is módosíthatja, ahová menteni kívánja a tanúsítványt. A Visual Studióval is importálhatja a tanúsítványt, mert erre a lépésre szükség van az alkalmazás fürtön való közzétételéhez.
    4. Válassza ki a Virtuális gép részletei lapot. Adja meg a jelszót, amelyet a fürtöt alkotó virtuális gépekhez kíván használni. A felhasználónév és jelszó használatával távolról csatlakozhat a virtuális gépekhez. A virtuális gép méretét is ki kell választania és ha szükséges, módosíthatja a virtuális gép rendszerképét is.
    5. Nem kötelező: A Speciális lapon módosíthatja azoknak a portoknak a listáját, amelyeket a fürttel együtt létrejövő terheléselosztón szeretne megnyitni. Hozzáadhat egy meglévő Application Insights-kulcsot is, amelyet az alkalmazás naplófájljainak útválasztásához használhat.
    6. Ha végzett a beállítások módosításával, kattintson a Létrehozás gombra. A létrehozás eltarthat néhány percig, és a kimeneti ablak jelzi, ha a fürt teljesen elkészült.
    
    ![Fürt létrehozása párbeszédpanel](./media/service-fabric-tutorial-deploy-app-to-party-cluster/create-cluster.png)

4. Ha a használni kívánt fürt készen áll, kattintson a jobb gombbal az alkalmazásprojektre, és válassza a **Közzététel** lehetőséget.

    Miután a közzététel befejeződött, egy böngészőből kérést küldhet az alkalmazáshoz.

5. Nyissa meg a kívánt böngészőt, és adja meg a fürt címét (a kapcsolati végpontot a portra vonatkozó információk nélkül, például win1kw5649s.westus.cloudapp.azure.com).

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
