---
title: Rövid útmutató:Profil létrehozása az alkalmazások magas rendelkezésre állásához - Azure PowerShell - Azure Traffic Manager
description: Ez a rövid útmutató ismerteti, hogyan hozhat létre egy Traffic Manager-profilt egy magas rendelkezésre állású webalkalmazás létrehozásához.
services: traffic-manager
author: rohinkoul
mnager: twooley
Customer intent: As an IT admin, I want to direct user traffic to ensure high availability of web applications.
ms.service: traffic-manager
ms.devlang: na
ms.topic: quickstart
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/04/2019
ms.author: rohink
ms.openlocfilehash: 0ab7392b4fa6e248d51392706fedaed156344a99
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "76934821"
---
# <a name="quickstart-create-a-traffic-manager-profile-for-a-highly-available-web-application-using-azure-powershell"></a>Rövid útmutató: Traffic Manager-profil létrehozása egy magas rendelkezésre állású webalkalmazáshoz az Azure PowerShell használatával

Ez a rövid útmutató ismerteti, hogyan hozhat létre egy Traffic Manager-profilt, amely magas rendelkezésre állást biztosít a webalkalmazás számára.

Ebben a rövid útmutatóban egy webalkalmazás két példányát hozza létre. Mindegyik egy másik Azure-régióban fut. A [végpontprioritás](traffic-manager-routing-methods.md#priority-traffic-routing-method)alapján hozzon létre egy Traffic Manager-profilt. A profil a felhasználói forgalmat a webalkalmazást futtató elsődleges helyre irányítja. A Traffic Manager folyamatosan figyeli a webalkalmazást. Ha az elsődleges hely nem érhető el, automatikus feladatátvételt biztosít a biztonsági mentési helynek.

Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F).

[!INCLUDE [cloud-shell-try-it.md](../../includes/cloud-shell-try-it.md)]

Ha a PowerShell helyi telepítése és használata mellett dönt, ehhez a cikkhez az Azure PowerShell-modul 5.4.1-es vagy újabb verziójára lesz szükség. A telepített verzió azonosításához futtassa a következőt: `Get-Module -ListAvailable Az`. Ha frissíteni szeretne, olvassa el [az Azure PowerShell-modul telepítését](/powershell/azure/install-Az-ps) ismertető cikket. Ha helyileg futtatja a PowerShellt, akkor emellett a `Connect-AzAccount` futtatásával kapcsolatot kell teremtenie az Azure-ral.

## <a name="create-a-resource-group"></a>Erőforráscsoport létrehozása
Erőforráscsoport létrehozása a [New-AzResourceGroup](/powershell/module/az.resources/new-azresourcegroup)segítségével.

```azurepowershell-interactive


# Variables
$Location1="WestUS"

# Create a Resource Group
New-AzResourceGroup -Name MyResourceGroup -Location $Location1
```

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Hozzon létre egy Traffic Manager-profilt a [New-AzTrafficManagerProfile](/powershell/module/az.trafficmanager/new-aztrafficmanagerprofile) használatával, amely a felhasználói forgalmat végpontprioritás alapján irányítja.

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

## <a name="create-web-apps"></a>Webalkalmazások létrehozása

Ehhez a rövid útmutatóhoz két példányra van szükség egy webalkalmazás két különböző Azure-régióban *(USA nyugati régióban* és *USA keleti régiójában).* Mindegyik elsődleges és feladatátvételi végpontként fog szolgálni a Traffic Manager számára.

### <a name="create-web-app-service-plans"></a>WebApp-szolgáltatáscsomagok létrehozása
Hozzon létre Web App service-csomagok at [New-AzAppServicePlan](/powershell/module/az.websites/new-azappserviceplan) a két példány a webalkalmazás, amely két különböző Azure-régiókban telepíti.

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
### <a name="create-a-web-app-in-the-app-service-plan"></a>Webalkalmazás létrehozása az App Service-csomagban
Hozzon létre két példányt a webalkalmazás használatával [New-AzWebApp](/powershell/module/az.websites/new-azwebapp) az App Service-csomagok az *USA nyugati régiójában* és az USA keleti régiójában az *Azure-régiókban.*

```azurepowershell-interactive
$App1ResourceId=(New-AzWebApp -Name $App1Name -ResourceGroupName MyResourceGroup -Location $Location1 -AppServicePlan "$App1Name-Plan").Id
$App2ResourceId=(New-AzWebApp -Name $App2Name -ResourceGroupName MyResourceGroup -Location $Location2 -AppServicePlan "$App2Name-Plan").Id

```

## <a name="add-traffic-manager-endpoints"></a>Traffic Manager-végpontok hozzáadása
Adja hozzá a két webalkalmazást Traffic [Manager-végpontként a New-AzTrafficManagerEndpoint](/powershell/module/az.trafficmanager/new-aztrafficmanagerendpoint) használatával a Traffic Manager-profilhoz az alábbiak szerint:
- Adja hozzá a webalkalmazás található, az *USA nyugati részén* az Azure régióban, mint az elsődleges végpont a felhasználói forgalom irányításához. 
- Adja hozzá a webalkalmazás található az *USA keleti régiójában* Azure régióban, mint a feladatátvételi végpont. Ha az elsődleges végpont nem érhető el, a forgalom automatikusan a feladatátvételi végpontra vezet.

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

Ebben a szakaszban ellenőrizni fogja a Traffic Manager-profil tartománynevét. Azt is konfigurálja, hogy az elsődleges végpont nem érhető el. Végül láthatja, hogy a webalkalmazás továbbra is elérhető. Ennek oka, hogy a Traffic Manager elküldi a forgalmat a feladatátvételi végpontra.

### <a name="determine-the-dns-name"></a>A DNS-név meghatározása

Határozza meg a Traffic Manager-profil DNS-nevét a [Get-AzTrafficManagerProfile segítségével.](/powershell/module/az.trafficmanager/get-aztrafficmanagerprofile)

```azurepowershell-interactive
Get-AzTrafficManagerProfile -Name $mytrafficmanagerprofile `
-ResourceGroupName MyResourceGroup
```

Másolja a **RelativeDnsName** értéket. A Traffic Manager-profil DNS-neve *http://<* relativednsname *>.trafficmanager.net*. 

### <a name="view-traffic-manager-in-action"></a>A Traffic Manager megtekintése működés közben
1. A webböngészőben adja meg a Traffic Manager-profil DNS-nevét (*http://<* relativednsname *>.trafficmanager.net*) a Web App alapértelmezett webhelyének megtekintéséhez.

    > [!NOTE]
    > Ebben a rövid útmutatóban az összes kérelem az elsődleges végpontra irányít. **1-es prioritásra**van állítva.
2. A Traffic Manager feladatátvételének működés közbeni megtekintéséhez tiltsa le az elsődleges helyet az [Disable-AzTrafficManagerEndpoint használatával.](/powershell/module/az.trafficmanager/disable-aztrafficmanagerendpoint)

   ```azurepowershell-interactive
    Disable-AzTrafficManagerEndpoint -Name $App1Name-$Location1 `
    -Type AzureEndpoints `
    -ProfileName $mytrafficmanagerprofile `
    -ResourceGroupName MyResourceGroup `
    -Force
   ```
3. Másolja a Traffic Manager-profil DNS-nevét (*http://<* relativednsname *>.trafficmanager.net*) a webhely új webböngésző-munkamenetben való megtekintéséhez.
4. Ellenőrizze, hogy a webalkalmazás továbbra is elérhető-e.

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása

Ha elkészült, törölje az erőforráscsoportokat, a webalkalmazásokat és az összes kapcsolódó erőforrást az [Eltávolítás-AzResourceGroup](/powershell/module/az.resources/remove-azresourcegroup)csoport használatával.

```azurepowershell-interactive
Remove-AzResourceGroup -Name MyResourceGroup
```

## <a name="next-steps"></a>További lépések

Ebben a rövid útmutatóban létrehozott egy Traffic Manager-profilt, amely magas rendelkezésre állást biztosít a webalkalmazás számára. Ha többet szeretne megtudni a forgalom útválasztásáról, folytassa a Traffic Manager oktatóanyagokkal.

> [!div class="nextstepaction"]
> [Traffic Manager-oktatóanyagok](tutorial-traffic-manager-improve-website-response.md)
