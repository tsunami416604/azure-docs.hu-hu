---
title: A PowerShell használata a Traffic Manager kezeléséhez az Azure-ban
description: Ezzel a tanulási útvonallal első lépések az Azure PowerShell for Traffic Manager használatával.
services: traffic-manager
documentationcenter: na
author: rohinkoul
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: rohink
ms.openlocfilehash: 7886764a69eefa68be071a801bea65ae995fbdc3
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76938501"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>A PowerShell használata a Traffic Manager kezeléséhez

Az Azure Resource Manager az Azure-beli szolgáltatások előnyben részesített felügyeleti felülete. Az Azure Traffic Manager-profilok az Azure Resource Manager-alapú API-k és eszközök használatával kezelhetők.

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="resource-model"></a>Erőforrásmodell

Az Azure Traffic Manager traffic manager konfigurálva van a Traffic Manager-profil nevű beállítások gyűjteményével. Ez a profil tartalmazza a DNS-beállításokat, a forgalomútválasztási beállításait, a végpontfigyelési beállításokat és a szolgáltatás végpontjainak listáját, amelyekhez a forgalom továbbításra kerül.

Minden Traffic Manager-profilt "TrafficManagerProfiles" típusú erőforrás jelöl. A REST API szintjén az egyes profilok URI-ja a következő:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Az Azure PowerShell beállítása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ezek a utasítások a Microsoft Azure PowerShell t használják. Az alábbi cikk bemutatja, hogyan telepítheti és konfigurálhatja az Azure PowerShellt.

* [How to install and configure Azure PowerShell (Az Azure PowerShell telepítése és konfigurálása)](/powershell/azure/overview)

A cikkben szereplő példák feltételezik, hogy meglévő erőforráscsoporttal rendelkezik. Erőforráscsoportot a következő paranccsal hozhat létre:

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport rendelkezik egy hely. Ez a hely lesz az erőforráscsoportban létrehozott erőforrások alapértelmezett helye. Mivel azonban a Traffic Manager-profil erőforrásai globálisak, nem regionálisak, az erőforráscsoport helyének kiválasztása nincs hatással az Azure Traffic Managerre.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Traffic Manager-profil létrehozásához `New-AzTrafficManagerProfile` használja a parancsmast:

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

Az alábbi táblázat a paramétereket ismerteti:

| Paraméter | Leírás |
| --- | --- |
| Név |A Traffic Manager-profilerőforrás erőforrásneve. Az azonos erőforráscsoportban lévő profiloknak egyedi névvel kell rendelkezniük. Ez a név nem a DNS-lekérdezésekhez használt DNS-névtől független. |
| ResourceGroupName |A profilerőforrást tartalmazó erőforráscsoport neve. |
| TrafficRoutingMetódus |Megadja azt a forgalom-útválasztási módszert, amely meghatározza, hogy melyik végpontot adja vissza a VÁLASZban egy DNS-lekérdezés. A lehetséges értékek a következők: "Teljesítmény", "Súlyozott" vagy "Prioritás". |
| RelativeDnsName |Megadja a Traffic Manager-profil által megadott DNS-név állomásnév-részét. Ez az érték az Azure Traffic Manager által a profil teljesen minősített tartománynevének (FQDN) létrehozásához használt DNS-tartománynévvel van kombinálva. A "contoso" értékének beállítása például "contoso.trafficmanager.net" lesz. |
| Élettartam |A DNS -idő-életre (TTL) másodpercben megadott. Ez a TTL tájékoztatja a helyi DNS-feloldókat és a DNS-ügyfeleket arról, hogy mennyi ideig kell gyorsítótárazni a DNS-válaszokat ehhez a Traffic Manager-profilhoz. |
| MonitorProtokoll |Megadja a végpont állapotának figyelésére használandó protokollt. A lehetséges értékek a "HTTP" és a "HTTPS". |
| MonitorPort |Megadja a végpont állapotának figyelésére használt TCP-portot. |
| MonitorPath (MonitorPath) |Megadja a végpont tartománynevéhez viszonyított elérési utat, amelya végpont állapotának vizsgálatához használható. |

A parancsmag létrehoz egy Traffic Manager-profilt az Azure-ban, és egy megfelelő profilobjektumot ad vissza a PowerShellnek. Ezen a ponton a profil nem tartalmaz végpontokat. A Traffic Manager-profilvégpontok hozzáadásáról a Traffic Manager-végpontok hozzáadása című témakörben talál további információt.

## <a name="get-a-traffic-manager-profile"></a>Traffic Manager-profil beszereznie

Meglévő Traffic Manager-profilobjektum lekéréséhez használja a `Get-AzTrafficManagerProfle` parancsmabot:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Ez a parancsmag egy Traffic Manager-profilobjektumot ad vissza.

## <a name="update-a-traffic-manager-profile"></a>Traffic Manager-profil frissítése

A Traffic Manager-profilok módosítása háromlépésből álló folyamatot követ:

1. A profil `Get-AzTrafficManagerProfile` lekérése vagy a `New-AzTrafficManagerProfile`rendszer által visszaadott profil használata.
2. Módosítsa a profilt. Hozzáadhat és eltávolíthat végpontokat, illetve módosíthatja a végpont- vagy profilparamétereket. Ezek a módosítások nem üzemen kívüli műveletek. Csak a profilt reprezentativita magát képviselő helyi objektumot módosítja a memóriában.
3. A módosítások véglegesítése a `Set-AzTrafficManagerProfile` parancsmag használatával.

A profil összes tulajdonsága módosítható, kivéve a profil RelativeDnsName.All profile properties can be changed except the profile's RelativeDnsName. A RelativeDnsName módosításához törölnie kell a profilt és egy új nevű új profilt.

A következő példa bemutatja, hogyan módosíthatja a profil TTL-t:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$TmProfile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

A Traffic Manager végpontjainak három típusa van:

1. **Az Azure-végpontok** az Azure-ban üzemeltetett szolgáltatások
2. **A külső végpontok** az Azure-on kívül üzemeltetett szolgáltatások
3. **A beágyazott végpontok** a Traffic Manager-profilok beágyazott hierarchiáinak létrehozásához használatosak. A beágyazott végpontok speciális forgalom-útválasztási konfigurációkat tesznek lehetővé összetett alkalmazásokhoz.

Mindhárom esetben a végpontok kétféleképpen adhatók hozzá:

1. A korábban leírt háromlépéses folyamat használata. Ennek a módszernek az az előnye, hogy egyetlen frissítésben több végpontmódosítás is elvégezhető.
2. A New-AzTrafficManagerEndpoint parancsmag használata. Ez a parancsmag egy végpontot ad hozzá egy meglévő Traffic Manager-profilhoz egyetlen műveletben.

## <a name="adding-azure-endpoints"></a>Azure-végpontok hozzáadása

Az Azure-végpontok referenciaszolgáltatások az Azure-ban üzemeltetett. Az Azure-végpontok két típusa támogatott:

1. Azure App Service
2. Azure PublicIpAddress erőforrások (amely egy terheléselosztóhoz vagy egy virtuális gép hálózati adapteréhez csatolható). A PublicIpAddress címnek rendelkeznie kell a Traffic Managerben használandó DNS-névvel.

Minden esetben:

* A szolgáltatás a vagya "targetResourceId" `Add-AzTrafficManagerEndpointConfig` `New-AzTrafficManagerEndpoint`paraméterével van megadva.
* A "Cél" és az "EndpointLocation" a TargetResourceId hallgatólagos.
* A "Súly" megadása nem kötelező. A súlyok csak akkor használatosak, ha a profil a "Súlyozott" forgalom-útválasztási módszer használatára van konfigurálva. Ellenkező esetben a rendszer figyelmen kívül hagyja őket. Ha meg van adva, az értéknek 1 és 1000 közötti számnak kell lennie. Az alapértelmezett érték "1".
* A "Prioritás" megadása nem kötelező. A prioritások csak akkor használatosak, ha a profil a "Prioritás" forgalom-útválasztási módszer használatára van konfigurálva. Ellenkező esetben a rendszer figyelmen kívül hagyja őket. Az érvényes értékek 1 és 1000 között vannak, az alacsonyabb értékek magasabb prioritást jeleznek. Ha egy végponthoz van megadva, azokat minden végponthoz meg kell adni. Ha nincs megadva, az "1" értéktől kezdődő alapértelmezett értékeket a program a végpontok felsorolásának sorrendjében alkalmazza.

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>1. példa: App Service-végpontok hozzáadása`Add-AzTrafficManagerEndpointConfig`

Ebben a példában létrehozunk egy Traffic Manager-profilt, `Add-AzTrafficManagerEndpointConfig` és két App Service-végpontot adunk hozzá a parancsmag használatával.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $TmProfile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>2. példa: PublicIpAddress végpont hozzáadása`New-AzTrafficManagerEndpoint`

Ebben a példában egy nyilvános IP-cím erőforrás takar a Traffic Manager-profil. A nyilvános IP-címnek konfigurált DNS-névvel kell rendelkeznie, és a virtuális gép hálózati adapteréhez vagy egy terheléselosztóhoz kötve.

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Külső végpontok hozzáadása

A Traffic Manager külső végpontok segítségével irányítja a forgalmat az Azure-on kívül üzemeltetett szolgáltatásokra. Az Azure-végpontokhoz is külső végpontok `Add-AzTrafficManagerEndpointConfig` adhatók `Set-AzTrafficManagerProfile`hozzá `New-AzTrafficManagerEndpoint`a használatával, a használatával vagy a használatával.

Külső végpontok megadásakor:

* A végpont tartománynevét a "Cél" paraméterrel kell megadni.
* Ha a "Teljesítmény" forgalom-útválasztási módszert használja, az "EndpointLocation" szükséges. Ellenkező esetben nem kötelező. Az értéknek [érvényes Azure-régiónévnek](https://azure.microsoft.com/regions/)kell lennie.
* A "Súly" és a "Prioritás" nem kötelező.

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>1. példa: Külső `Add-AzTrafficManagerEndpointConfig` végpontok hozzáadása a`Set-AzTrafficManagerProfile`

Ebben a példában létrehozunk egy Traffic Manager-profilt, két külső végpontot adunk hozzá, és véglegesítjük a módosításokat.

```powershell
$TmProfile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $TmProfile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>2. példa: Külső végpontok hozzáadása`New-AzTrafficManagerEndpoint`

Ebben a példában egy külső végpontot adunk egy meglévő profilhoz. A profil a profil és az erőforráscsoport neveivel van megadva.

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>"Beágyazott" végpontok hozzáadása

Minden Traffic Manager-profil egyetlen forgalom-útválasztási módszert határoz meg. Vannak azonban olyan forgatókönyvek, amelyek kifinomultabb forgalom-útválasztást igényelnek, mint az egyetlen Traffic Manager-profil által biztosított útválasztás. A Traffic Manager-profilok egymásba ágyazhatók egynél több forgalom-útválasztási módszer előnyeinek kombinálásával. A beágyazott profilok lehetővé teszik az alapértelmezett Traffic Manager-viselkedés felülbírálását a nagyobb és összetettebb alkalmazástelepítések támogatása érdekében. További részletes példákat a [Beágyazott forgalomkezelő-profilok című témakörben talál.](traffic-manager-nested-profiles.md)

A beágyazott végpontok a szülőprofilban vannak konfigurálva, egy adott végponttípus, a "NestedEndpoints" használatával. Egymásba ágyazott végpontok megadásakor:

* A végpontot a "targetResourceId" paraméterrel kell megadni.
* Ha a "Teljesítmény" forgalom-útválasztási módszert használja, az "EndpointLocation" szükséges. Ellenkező esetben nem kötelező. Az értéknek [érvényes Azure-régiónévnek](https://azure.microsoft.com/regions/)kell lennie.
* A "Súly" és a "Prioritás" nem kötelező, mint az Azure-végpontok.
* A "MinChildEndpoints" paraméter megadása nem kötelező. Az alapértelmezett érték "1". Ha a rendelkezésre álló végpontok száma e küszöbérték alá esik, a szülőprofil a gyermekprofilt "leromlottnak" tekinti, és a forgalmat a szülőprofil többi végpontjára irányítja át.

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>1. példa: Egymásba `Add-AzTrafficManagerEndpointConfig` ágyazott végpontok hozzáadása és`Set-AzTrafficManagerProfile`

Ebben a példában új Traffic Manager gyermek- és szülőprofilokat hozunk létre, a gyermeket beágyazott végpontként adjuk hozzá a szülőhöz, és véglegesítjük a módosításokat.

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $parent
```

A példában szereplő rövidség érdekében nem adtunk hozzá más végpontokat a gyermek- vagy szülőprofilokhoz.

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>2. példa: Egymásba ágyazott végpontok hozzáadása`New-AzTrafficManagerEndpoint`

Ebben a példában egy meglévő gyermekprofilt adunk hozzá beágyazott végpontként egy meglévő szülőprofilhoz. A profil a profil és az erőforráscsoport neveivel van megadva.

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Végpontok hozzáadása másik előfizetésből

A Traffic Manager különböző előfizetésekből származó végpontokkal dolgozhat. Át kell váltania az előfizetésre azzal a végponttal, amelyet hozzá szeretne adni a Traffic Manager szükséges bemenetének lekéréséhez. Ezután át kell váltania az előfizetések a Traffic Manager-profillal, és hozzá kell adnia a végpontot. Az alábbi példa bemutatja, hogyan kell ezt egy nyilvános IP-címet.

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddressName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Traffic Manager-végpont frissítése

Egy meglévő Traffic Manager-végpont ot kétféleképpen frissítheti:

1. A Traffic Manager-profil beszereznie a használatával, `Get-AzTrafficManagerProfile`frissítse a `Set-AzTrafficManagerProfile`végpont tulajdonságait a profilon belül, és véglegesítse a módosításokat a használatával. Ez a módszer azzal az előnnyel jár, hogy egyetlen műveletben egynél több végpontot frissít.
2. A Traffic Manager-végpont `Get-AzTrafficManagerEndpoint`bekéselése a használatával, `Set-AzTrafficManagerEndpoint`frissítse a végpont tulajdonságait, és véglegesítse a módosításokat a használatával. Ez a módszer egyszerűbb, mivel nem igényel indexelést a végpontok tömbbe a profilban.

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>1. példa: Végpontok `Get-AzTrafficManagerProfile` frissítése a`Set-AzTrafficManagerProfile`

Ebben a példában módosítjuk a prioritást egy meglévő profil két végpontján.

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$TmProfile.Endpoints[0].Priority = 2
$TmProfile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>2. példa: Végpont frissítése `Get-AzTrafficManagerEndpoint` a`Set-AzTrafficManagerEndpoint`

Ebben a példában módosítjuk egy meglévő profil egyetlen végpontjának súlyát.

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Végpontok és profilok engedélyezése és letiltása

A Traffic Manager lehetővé teszi az egyes végpontok engedélyezését és letiltását, valamint lehetővé teszi a teljes profilok engedélyezését és letiltását.
Ezek a módosítások a végpont- vagy profilerőforrások beszerzésével/frissítésével/beállításával hajthatók végre. Ezek a gyakori műveletek egyszerűsítése érdekében dedikált parancsmagok is támogatottak.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>1. példa: Traffic Manager-profil engedélyezése és letiltása

A Traffic Manager-profil `Enable-AzTrafficManagerProfile`engedélyezéséhez használja a használatát. A profil profilobjektum mal adható meg. A profilobjektum átadható a folyamaton keresztül vagy a "-TrafficManagerProfile" paraméter használatával. Ebben a példában a profilt a profil és az erőforráscsoport neve alapján adjuk meg.

```powershell
Enable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Traffic Manager-profil letiltása:

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Az Disable-AzTrafficManagerProfile parancsmag megerősítést kér. Ez a kérdés a '-Force' paraméterrel letiltható.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>2. példa: Traffic Manager-végpont engedélyezése és letiltása

A Traffic Manager-végpont engedélyezéséhez használja a használatát. `Enable-AzTrafficManagerEndpoint` A végpont kétféleképpen adható meg

1. TrafficManagerEndpoint objektum használata a folyamaton keresztül vagy a "-TrafficManagerEndpoint" paraméter használatával
2. A végpont neve, végponttípusa, profilneve és erőforráscsoport-neve:

```powershell
Enable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Hasonlóképpen a Traffic Manager-végpont letiltásához:

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

A `Disable-AzTrafficManagerProfile`parancsmag `Disable-AzTrafficManagerEndpoint` is megerősítést kér. Ez a kérdés a '-Force' paraméterrel letiltható.

## <a name="delete-a-traffic-manager-endpoint"></a>Traffic Manager-végpont törlése

Az egyes végpontok eltávolításához használja a `Remove-AzTrafficManagerEndpoint` parancsmabot:

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Ez a parancsmag megerősítést kér. Ez a kérdés a '-Force' paraméterrel letiltható.

## <a name="delete-a-traffic-manager-profile"></a>Traffic Manager-profil törlése

Traffic Manager-profil törléséhez `Remove-AzTrafficManagerProfile` használja a parancsmamot, adja meg a profil- és erőforráscsoportneveket:

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Ez a parancsmag megerősítést kér. Ez a kérdés a '-Force' paraméterrel letiltható.

A törölendő profil profil profilobjektummal is megadható:

```powershell
$TmProfile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $TmProfile [-Force]
```

Ez a sorozat is vezetékes:

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>További lépések

[Traffic Manager figyelése](traffic-manager-monitoring.md)

[A Traffic Manager teljesítményével kapcsolatos megfontolások](traffic-manager-performance-considerations.md)
