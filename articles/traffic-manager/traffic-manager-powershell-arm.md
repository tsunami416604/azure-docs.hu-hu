---
title: Az Azure Traffic Manager kezelése a PowerShell segítségével
description: A Traffic Manager esetében az Azure Resource Manager PowerShell használatával
services: traffic-manager
documentationcenter: na
author: kumudd
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: efaa9101fbe46e0db2f582fe5a208dd8b16f095f
ms.sourcegitcommit: e69fc381852ce8615ee318b5f77ae7c6123a744c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/11/2019
ms.locfileid: "56003584"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>Traffic Manager kezelése a PowerShell segítségével

Az Azure Resource Manager az Azure-szolgáltatások az előnyben részesített felügyeleti kezelőfelület. Az Azure Traffic Manager-profilok az Azure Resource Manager-alapú API-k és eszközök használatával kezelhetők.

## <a name="resource-model"></a>Erőforrásmodell

Az Azure Traffic Manager van beállítva, a Traffic Manager-profil nevű beállítások használatával. Ehhez a profilhoz DNS-beállítások, forgalom-útválasztási beállítások, végpont a figyelési beállításokat és az adatforgalmat, amelyhez végpontok listáját tartalmazza.

Minden egyes Traffic Manager-profil "TrafficManagerProfiles" típusú erőforrás képviseli. A REST API-szintjén az URI-t az egyes profilok a következőképpen történik:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Azure PowerShell telepítése

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Ezek az utasítások a Microsoft Azure PowerShell-lel. A következő cikk ismerteti az Azure PowerShell telepítése és konfigurálása.

* [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)

Ebben a cikkben szereplő példák feltételezik, hogy egy meglévő erőforráscsoportot. Létrehozhat egy erőforráscsoportot a következő paranccsal:

```powershell
New-AzResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Az Azure Resource Manager megköveteli, hogy minden erőforráscsoport helyét. Ezen a helyen az alapértelmezett szolgál az erőforráscsoport létrehozott erőforrásokat. Azonban mivel a Traffic Manager-profil erőforrások globális, nem pedig regionális, a kiválasztott erőforráscsoport helye nem befolyásolja az Azure Traffic Manager.

## <a name="create-a-traffic-manager-profile"></a>Traffic Manager-profil létrehozása

Traffic Manager-profil létrehozásához használja a `New-AzTrafficManagerProfile` parancsmagot:

```powershell
$profile = New-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

A következő táblázat ismerteti a paramétereket:

| Paraméter | Leírás |
| --- | --- |
| Name (Név) |A Traffic Manager-profil erőforrás erőforrás neve. A profilok ugyanabban az erőforráscsoportban egyedi névvel kell rendelkezniük. Ezt a nevet a DNS-név, DNS-lekérdezések használt elkülönül. |
| ResourceGroupName |A profil-erőforrást tartalmazó erőforráscsoport neve. |
| TrafficRoutingMethod |Adja meg a forgalom-útválasztási módszer segítségével meghatározhatja, melyik végponthoz válaszban visszaadott DNS-lekérdezést. Lehetséges értékek: "Teljesítmény", "Súlyozott" vagy "Priority". |
| RelativeDnsName |Adja meg a Traffic Manager-profil által biztosított DNS-név állomásnév részét. Ezt az értéket a profil teljesen minősített tartománynevét (FQDN) az Azure Traffic Manager által használt DNS-tartománynév együtt. Például "contoso" értékre állítja lesz "contoso.trafficmanager.net." |
| TTL |A DNS Time-to-Live élettartam (TTL) megadja a másodpercek alatt. A TTL tájékoztatja a helyi DNS-feloldók és a DNS-ügyfelek mennyi a gyorsítótár DNS-válaszok a Traffic Manager-profil. |
| MonitorProtocol |A végpont állapotának figyeléséhez használt protokollt adja. Lehetséges értékek: "HTTP" és "HTTPS". |
| MonitorPort |Adja meg a végpont állapotának figyeléséhez használt TCP-portot. |
| MonitorPath |A végpont számára, hogy megvizsgálja a végpontonkénti állapotot használt tartománynév képest relatív elérési útja. |

A parancsmag létrehoz egy Traffic Manager-profil az Azure-ban, és a egy megfelelő profil objektumot ad vissza, a PowerShell. Ezen a ponton a profil nem tartalmaz olyan végpontok, amelyek. Végpontok Traffic Manager-profil történő hozzáadásával kapcsolatos további információkért tekintse meg a Traffic Manager-végpontok hozzáadása.

## <a name="get-a-traffic-manager-profile"></a>Traffic Manager-profil beolvasása

Egy meglévő Traffic Manager-profil objektumot lekéréséhez használja a `Get-AzTrafficManagerProfle` parancsmagot:

```powershell
$profile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Ez a parancsmag egy Traffic Manager-profil objektumot ad vissza.

## <a name="update-a-traffic-manager-profile"></a>Traffic Manager-profil frissítése

Traffic Manager-profilok módosítása egy 3-lépés folyamatot követi:

1. A profil használatával lekérdezni `Get-AzTrafficManagerProfile` vagy használni a profil által visszaadott `New-AzTrafficManagerProfile`.
2. Módosítsa a profil. Adja hozzá, és távolítsa el a végpontok vagy endpoint vagy a profil paramétereinek módosítása. Végezze a rögzítést műveletek, amelyek ezeket a módosításokat. A helyi objektum, a memória, a profilt jelölő csak változnak.
3. Mentse a módosításokat, használja a `Set-AzTrafficManagerProfile` parancsmagot.

Az összes profil tulajdonságainak kivételével a profil RelativeDnsName módosítható. Ha módosítani szeretné a RelativeDnsName, törölnie kell a profil és a egy új nevet az új profilt.

Az alábbi példa bemutatja, hogyan módosításához a profil TTL:

```powershell
$profile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$profile.Ttl = 300
Set-AzTrafficManagerProfile -TrafficManagerProfile $profile
```

Traffic Manager-végpontok három típusa van:

1. **Azure-beli** olyan Azure-ban üzemeltetett szolgáltatások
2. **Külső végpontok** olyan Azure-on kívül üzemeltetett szolgáltatások
3. **A beágyazott végpontokat** beágyazott hierarchiák a Traffic Manager-profilok létrehozására szolgálnak. Beágyazott végpontokat forgalom-útválasztási speciális konfigurációk, az összetett alkalmazások engedélyezéséhez.

Három esetben végpontok hozzáadása is lehetséges a két módon:

1. Korábban leírt folyamat 3. lépés – használatával. Ez a módszer az az előnye, hogy több végpont módosíthatók egy frissítés.
2. A New-AzTrafficManagerEndpoint parancsmag használatával. Ez a parancsmag hozzáad egy végpontot egy meglévő Traffic Manager-profilt egy művelettel.

## <a name="adding-azure-endpoints"></a>Azure-végpontok hozzáadása

Azure-beli Azure-ban üzemeltetett szolgáltatások hivatkozhat. Kétféle típusú Azure-beli támogatottak:

1. Azure App Service
2. Az Azure PublicIpAddress erőforrásokat (amely lehet rendelni egy terheléselosztót vagy egy virtuális gép hálózati adapter). A nyilvános IP-címre rendelt használható a Traffic Manager DNS névvel kell rendelkeznie.

Minden esetben:

* A szolgáltatás van megadva, a "targetresourceid azonosítója" paraméter használatával `Add-AzTrafficManagerEndpointConfig` vagy `New-AzTrafficManagerEndpoint`.
* A "Cél" és "EndpointLocation" targetresourceid azonosítója által is beleértve.
* Késleltetve a "Weight" megadása nem kötelező. Súlyok csak akkor használja, ha a profilt a "Súlyozott" forgalom-útválasztási módszer használatára van konfigurálva. Ellenkező esetben figyelmen kívül hagyja őket. Ha meg van adva, az érték 1 és 1000 közötti számnak kell lennie. Az alapértelmezett értéke "1".
* Késleltetve a "prioritás" megadása nem kötelező. Prioritások csak akkor használja, ha a profilt a "Priority" forgalom-útválasztási módszer használatára van konfigurálva. Ellenkező esetben figyelmen kívül hagyja őket. Érvényes értékek 1 és 1000 az alacsonyabb magasabb prioritású jelző:. Ha meg van adva egy végpontot, azok minden végpontok adható meg. Ha nincs megadva, alapértelmezett értéket "1" kezdődően alkalmazza a rendszer az, hogy szerepel-e a végpontokon.

### <a name="example-1-adding-app-service-endpoints-using-add-aztrafficmanagerendpointconfig"></a>1. példa: App Service-végpont használatával hozzáadása `Add-AzTrafficManagerEndpointConfig`

Ebben a példában hozunk létre Traffic Manager-profil, és adjon hozzá két App Service-végpont használatával a `Add-AzTrafficManagerEndpointConfig` parancsmagot.

```powershell
$profile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzWebApp -Name webapp1
Add-AzTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzWebApp -Name webapp2
Add-AzTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $profile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-aztrafficmanagerendpoint"></a>2. példa Egy nyilvános IP-végpontot a hozzáadása `New-AzTrafficManagerEndpoint`

Ebben a példában egy nyilvános IP-cím erőforrás hozzáadódik a Traffic Manager-profil. A nyilvános IP-cím rendelkeznie kell egy DNS-név konfigurálva, és a hálózati Adaptert egy virtuális gép vagy terheléselosztó köthető.

```powershell
$ip = Get-AzPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Külső végpontok hozzáadása

A TRAFFIC Manager külső végpontokat használ Azure-on kívül üzemeltetett szolgáltatások adatforgalmat. Mivel az Azure-végponttal rendelkező külső végpontokat is hozzáadhatók vagy segítségével `Add-AzTrafficManagerEndpointConfig` követ `Set-AzTrafficManagerProfile`, vagy `New-AzTrafficManagerEndpoint`.

Külső végpontok megadásakor:

* A végpont tartomány nevét a "Cél" paraméter használatával kell megadni
* Ha a "Teljesítmény" forgalom-útválasztási módszert használja, a "EndpointLocation" szükség. Ellenkező esetben ez nem kötelező. Az értéknek kell lennie egy [érvényes Azure-régió neve](https://azure.microsoft.com/regions/).
* A "Weight" és "Priority" megadása nem kötelező.

### <a name="example-1-adding-external-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>1. példa: Külső végpontok használata hozzáadása `Add-AzTrafficManagerEndpointConfig` és `Set-AzTrafficManagerProfile`

Ebben a példában azt egy Traffic Manager-profil létrehozása, adjon hozzá két külső végpontokat, és véglegesítse a módosításokat.

```powershell
$profile = New-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-external-endpoints-using-new-aztrafficmanagerendpoint"></a>2. példa Külső végpontok használata hozzáadása `New-AzTrafficManagerEndpoint`

Ebben a példában a külső végpont egy meglévő profilt hozzáadunk. A profil van megadva, a profil és az erőforráscsoport nevét használja.

```powershell
New-AzTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>"Beágyazott" végpontok hozzáadása

Minden egyes Traffic Manager-profil megadja egy forgalom-útválasztási módszert. Vannak azonban olyan kifinomultabb forgalom-útválasztást egyetlen Traffic Manager-profil által kínált útválasztását esetekben. Traffic Manager-profilok úgy, hogy egynél több forgalom-útválasztási módszer előnyei ágyazhatja be. Beágyazott profilok segítségével felülírhatja az alapértelmezett a Traffic Manager viselkedés támogatási nagyobb és összetettebb alkalmazások központi telepítésének engedélyezése. További részletes példák: [beágyazott Traffic Manager-profilok](traffic-manager-nested-profiles.md).

Beágyazott végpontokat vannak beállítva, a szülő profil egy adott típusú végpont, "NestedEndpoints". Beágyazott végpontokat megadásakor:

* A végpont a "targetresourceid azonosítója" paraméter használatával kell megadni
* Ha a "Teljesítmény" forgalom-útválasztási módszert használja, a "EndpointLocation" szükség. Ellenkező esetben ez nem kötelező. Az értéknek kell lennie egy [érvényes Azure-régió neve](https://azure.microsoft.com/regions/).
* A "Weight" és "Priority" megadása nem kötelező, mint az Azure-beli.
* A minchildendpoints "tulajdonság" paramétert nem kötelező megadni. Az alapértelmezett értéke "1". Elérhető végpontok száma a küszöbérték alá csökken, ha a szülő profilt úgy ítéli meg, a gyermek profil "csökkentett teljesítményű", és a szülő-profilban a többi végpontokra érkező forgalom hozunk.

### <a name="example-1-adding-nested-endpoints-using-add-aztrafficmanagerendpointconfig-and-set-aztrafficmanagerprofile"></a>1. példa: Használatával beágyazott végpontok hozzáadása `Add-AzTrafficManagerEndpointConfig` és `Set-AzTrafficManagerProfile`

Ebben a példában azt új Traffic Manager gyermek és szülő-profilok létrehozása a gyermek hozzáadása egy beágyazott végpont a szülő és a módosítások véglegesítéséhez.

```powershell
$child = New-AzTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzTrafficManagerProfile -TrafficManagerProfile $profile
```

Ebben a példában kivonatosan azt tette nem adható hozzá bármilyen más végpontok a gyermek vagy szülő profilok.

### <a name="example-2-adding-nested-endpoints-using-new-aztrafficmanagerendpoint"></a>2. példa Használatával beágyazott végpontok hozzáadása `New-AzTrafficManagerEndpoint`

Ebben a példában hozzáadunk egy meglévő gyermek-profilt egy beágyazott végpontként egy meglévő szülő profilt. A profil van megadva, a profil és az erőforráscsoport nevét használja.

```powershell
$child = Get-AzTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Végpontok hozzáadása egy másik előfizetésből

A TRAFFIC Manager képes együttműködni különböző előfizetésekről csatlakoztat végpontok. Váltson át az előfizetést az a végpont beolvasni a szükséges bemeneti átirányítása a Traffic Managerhez hozzáadni kívánt kell. Majd váltson át a Traffic Manager-profilt az előfizetéseket, és hozzá tud adni a encpoint kell. Az alábbi példa bemutatja, hogyan ehhez a nyilvános IP-címmel.

```powershell
Set-AzContext -SubscriptionId $EndpointSubscription
$ip = Get-AzPublicIpAddress -Name $IpAddresName -ResourceGroupName $EndpointRG

Set-AzContext -SubscriptionId $trafficmanagerSubscription
New-AzTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>Traffic Manager végpont frissítése

Egy meglévő Traffic Manager-végpont frissítése két módja van:

1. A Traffic Manager profil használatával lekérése `Get-AzTrafficManagerProfile`, a profilon belül a végpont tulajdonságainak frissítése, és hajtsa végre a módosításokat `Set-AzTrafficManagerProfile`. Ez a módszer az előnye, hogy egyetlen művelettel több végpont frissítése.
2. A Traffic Manager-végpontot a GET `Get-AzTrafficManagerEndpoint`, a végpont tulajdonságainak frissítése, és hajtsa végre a módosításokat `Set-AzTrafficManagerEndpoint`. Ez a módszer azért egyszerűbb, mert nem igényel az indexelés a profil végpontjainak tömbbe.

### <a name="example-1-updating-endpoints-using-get-aztrafficmanagerprofile-and-set-aztrafficmanagerprofile"></a>1. példa: A végpontok segítségével frissítése `Get-AzTrafficManagerProfile` és `Set-AzTrafficManagerProfile`

Ebben a példában két végpontot egy meglévő profilon belül a prioritásának módosítjuk.

```powershell
$profile = Get-AzTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$profile.Endpoints[0].Priority = 2
$profile.Endpoints[1].Priority = 1
Set-AzTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-updating-an-endpoint-using-get-aztrafficmanagerendpoint-and-set-aztrafficmanagerendpoint"></a>2. példa Frissíti egy végpontot a `Get-AzTrafficManagerEndpoint` és `Set-AzTrafficManagerEndpoint`

Ebben a példában egy meglévő profilt az egyetlen végpont súlyának módosítjuk.

```powershell
$endpoint = Get-AzTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Engedélyezése és letiltása a végpontok és profilok

A TRAFFIC Manager lehetővé teszi, hogy engedélyezve van, és le van tiltva, valamint engedélyezése és letiltása a teljes profilok lehetővé teszik az egyes végpontokat.
Ezeket a módosításokat a végpontot, illetve profil erőforrások beolvasása/frissítése/beállítás szerint módosíthatók. Egyszerűsíthetők a gyakori műveleteket, hogy támogatottak továbbá akkor dedikált parancsmagjai.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>1. példa: Engedélyezése és letiltása Traffic Manager-profil

Engedélyezze a Traffic Manager-profil az `Enable-AzureRmTrafficManagerProfile`. A profilt a profil objektummal adható meg. A profil objektum használatával vagy a folyamat keresztül át lehet adni a "-TrafficManagerProfile" paraméter. Ebben a példában azt adja meg a profilhoz a profil- és erőforrás-csoport nevét.

```powershell
Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Traffic Manager-profil letiltása:

```powershell
Disable-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

A Disable-AzTrafficManagerProfile parancsmag megerősítést kér. Ez a parancssor használatával letilthatók a "-Force" paraméter.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>2. példa Engedélyezése és letiltása Traffic Manager végpont

Engedélyezze a Traffic Manager végpont az `Enable-AzureRmTrafficManagerEndpoint`. Adja meg a végpont kétféleképpen

1. Egy TrafficManagerEndpoint objektum átadása a folyamat, vagy pedig a "-TrafficManagerEndpoint" paraméter
2. A végpont neve, a végpont típusa, a profil nevét és a erőforráscsoport-név használatával:

```powershell
Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Hasonlóképpen egy Traffic Manager végpont letiltása:

```powershell
Disable-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

A `Disable-AzTrafficManagerProfile`, a `Disable-AzTrafficManagerEndpoint` parancsmag megerősítést kér. Ez a parancssor használatával letilthatók a "-Force" paraméter.

## <a name="delete-a-traffic-manager-endpoint"></a>A Traffic Manager-végpont törlése

Egyéni végpontok eltávolításához használja a `Remove-AzTrafficManagerEndpoint` parancsmagot:

```powershell
Remove-AzTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Ez a parancsmag megerősítést kérni fogja. Ez a parancssor használatával letilthatók a "-Force" paraméter.

## <a name="delete-a-traffic-manager-profile"></a>Traffic Manager-profil törlése

Traffic Manager-profil törléséhez használja a `Remove-AzTrafficManagerProfile` parancsmagot, adja meg a profil és az erőforráscsoport nevét:

```powershell
Remove-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Ez a parancsmag megerősítést kérni fogja. Ez a parancssor használatával letilthatók a "-Force" paraméter.

A profil törölni is egy profil objektummal adható meg:

```powershell
$profile = Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

Ez a sorozat is átadható olyan parancsoknak:

```powershell
Get-AzTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>További lépések

[Traffic Manager figyelése](traffic-manager-monitoring.md)

[A Traffic Manager teljesítményével kapcsolatos megfontolások](traffic-manager-performance-considerations.md)
