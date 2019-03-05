---
title: Rövid útmutató – Azure PowerShell-lel alkalmazások magas rendelkezésre álláshoz a Traffic Manager-profil létrehozása
description: Ez a rövid útmutató a cikk ismerteti a magas rendelkezésre állású webalkalmazás létrehozása a Traffic Manager-profil létrehozása.
services: traffic-manager
author: KumudD
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2019
ms.author: kumud
ms.openlocfilehash: 6ffecf973632911113608b7478d2af2aef036257
ms.sourcegitcommit: 8b41b86841456deea26b0941e8ae3fcdb2d5c1e1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/05/2019
ms.locfileid: "57344603"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Gyors útmutató: A Traffic Manager profil létrehozása az Azure PowerShell-lel magas rendelkezésre állású webalkalmazás

Ebben a rövid útmutató egy Traffic Manager-profilt, amely a webalkalmazás magas rendelkezésre állást nyújt.

Ebben a rövid útmutatóban egy webalkalmazás két példánya fog létrehozni. Azok a fut egy másik Azure-régióban. Traffic Manager-profil alapján fog létrehozni [végpontprioritás](traffic-manager-routing-methods.md#priority). A profil arra utasítja a felhasználói adatforgalmat a webalkalmazás fut az elsődleges hely. A TRAFFIC Manager folyamatosan figyeli a webalkalmazást. Ha az elsődleges hely nem érhető el, a helykiszolgáló biztonsági mentése automatikus feladatátvételt biztosít.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-powershell.md](../../includes/cloud-shell-powershell.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Hozzon létre egy erőforrás csoport [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup).

```azurepowershell-interactive


# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Hozzon létre egy Traffic Manager profil [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) , amely arra utasítja a felhasználói adatforgalmat a végpont prioritás alapján.

```azurepowershell-interactive

# Generates a random value
$Random=(New-Guid).ToString().Substring(0,8)
$mytrafficmanagerprofile="mytrafficmanagerprofile$Random"

New-AzTrafficManagerProfile `
-Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup `
-TrafficRoutingMethod Priority `
-MonitorPath '/' `
-MonitorProtocol "HTTP" `
-RelativeDnsName $mytrafficmanagerprofile `
-Ttl 30 `
-MonitorPort 80
```

## <a name="create-web-apps"></a>Create Web Apps

Ebben a rövid útmutatóban két különböző Azure-régióban üzembe helyezett webalkalmazás két példányát kell (*USA nyugati RÉGIÓJA* és *USA keleti Régiójában*). Minden egyes erre a célra elsődleges és feladatátvételi végpontok Traffic Manager esetében.

### <a name="create-web-app-service-plans"></a>Web App Service-csomagok létrehozása
Hozzon létre Web App service-csomagok használatával [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) a webalkalmazást, amely központilag telepíti, két különböző Azure-régiókban lévő két példánya esetén.

```azurepowershell-interactive

# Variables
$App1Name="AppServiceTM1$Random"
$App2Name="AppServiceTM2$Random"
$Location1="WestUS"
$Location2="EastUS"

# Create an App service plan
New-AzAppservicePlan -Name "$App1Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location1 -Tier Standard
New-AzAppservicePlan -Name "$App2Name-Plan" -ResourceGroupName MyResourceGroup -Location $Location2 -Tier Standard

```
### <a name="create-a-web-app-in-the-app-service-plan"></a>Egy webalkalmazás létrehozása az App Service-csomag
Hozzon létre két példányt, a webes alkalmazást a [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) az App Service-csomagok az a *USA nyugati RÉGIÓJA* és *USA keleti Régiójában* Azure-régióban.

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása
Adja hozzá a két Web Apps használatával Traffic Manager-végpontok [New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) a Traffic Manager profil a következőképpen:
- Adja hozzá a Web App található a *USA nyugati RÉGIÓJA* az elsődleges végpontra irányíthatja a felhasználói forgalom Azure-régióban. 
- Adja hozzá a Web App található a *USA keleti Régiójában* a feladatátvételi végpontot Azure-régióban. Az elsődleges végpont nem érhető el, ha forgalom automatikusan a feladatátvétel végpontra irányítja.

```azurepowershell-interactive
New-AzTrafficManagerEndpoint -Name "$App1Name-$Location1" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App1ResourceId `
-EndpointStatus "Enabled"

New-AzTrafficManagerEndpoint -Name "$App2Name-$Location2" `
-ResourceGroupName MyResourceGroup `
-ProfileName "$mytrafficmanagerprofile" `
-Type AzureEndpoints `
-TargetResourceId $App2ResourceId `
-EndpointStatus "Enabled"
```

## <a name="test-traffic-manager-profile"></a>Traffic Manager-profil tesztelése

Ebben a szakaszban be fogja a tartományneve a Traffic Manager-profil. Is konfigurálja az elsődleges végpont nem érhető el. Végül, kérje meg, hogy a webalkalmazás továbbra is elérhető. Mivel a Traffic Manager küldi el a forgalmat a feladatátvételi végpont van.

### <a name="determine-the-dns-name"></a>A DNS-név meghatározása

Határozza meg a DNS-nevével a Traffic Manager profil használatával [Get-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile).

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Másolás a **RelativeDnsName** értéket. A DNS-név, a Traffic Manager-profil *http://<* relativednsname *>. trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben
1. Egy webböngészőben, adja meg a Traffic Manager-profil DNS-nevét (*http://<* relativednsname *>. trafficmanager.net*) megtekintéséhez a webalkalmazás alapértelmezett webhelyet.

    > [!NOTE]
    > Ebben a rövid útmutató forgatókönyvben összes kérelem átirányítása az elsődleges végpontot. A változó értéke **prioritása 1**.
2. A Traffic Manager feladatátvételi megtekintése működés közben, tiltsa le az elsődleges hely használatával [Disable-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint).

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Másolja a Traffic Manager-profil DNS-nevét (*http://<* relativednsname *>. trafficmanager.net*) kattintva megtekintheti a webhelyét az új webes böngésző-munkamenetet.
4. Ellenőrizze, hogy a webalkalmazás továbbra is elérhető.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Amikor végzett, törölje az erőforráscsoportok, a webes alkalmazások és a használatával minden kapcsolódó erőforrás [Remove-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup).

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Traffic Manager-profilt, amely a magas rendelkezésre állást biztosít a webalkalmazás. Forgalom szabályozásával kapcsolatos további információkért folytassa a Traffic Manager oktatóanyagok.

> [!div class="nextstepaction"]
> [Traffic Manager-oktatóanyagok](tutorial-traffic-manager-improve-website-response.md)