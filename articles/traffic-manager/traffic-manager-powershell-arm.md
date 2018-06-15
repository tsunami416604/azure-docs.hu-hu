---
title: Az Azure Traffic Manager kezelése a PowerShell használatával |} Microsoft Docs
description: A Traffic Manager az Azure Resource Manager eszközzel PowerShell használatával
services: traffic-manager
documentationcenter: na
author: kumudd
manager: timlt
ms.assetid: bc247448-1d2e-4104-ac03-42b59ebde065
ms.service: traffic-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 03/16/2017
ms.author: kumud
ms.openlocfilehash: 951e845e23a1ed0cbdc83fc24a97a545f00c52ad
ms.sourcegitcommit: 1362e3d6961bdeaebed7fb342c7b0b34f6f6417a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/18/2018
ms.locfileid: "31526641"
---
# <a name="using-powershell-to-manage-traffic-manager"></a>A Traffic Manager kezelése a PowerShell segítségével

Az Azure Resource Manager rendszer az előnyben részesített felügyeleti szolgáltatások az Azure-ban, Az Azure Traffic Manager-profilok használatával az Azure Resource Manager-alapú API-k és eszközök is felügyelhetők.

## <a name="resource-model"></a>Erőforrásmodell

Az Azure Traffic Manager a Traffic Manager-profil neve beállítások segítségével konfigurálható. Ehhez a profilhoz DNS-beállítások, forgalom útválasztásához tartozó beállításokat, végpontmonitoring beállításai, és, amelyhez továbbítódik végpontok listáját tartalmazza.

Minden egyes Traffic Manager-profil "TrafficManagerProfiles" típusú erőforrást jelképezi. A REST API-szintű URI-JÁNAK minden profil a következőképpen történik:

    https://management.azure.com/subscriptions/{subscription-id}/resourceGroups/{resource-group-name}/providers/Microsoft.Network/trafficManagerProfiles/{profile-name}?api-version={api-version}

## <a name="setting-up-azure-powershell"></a>Azure PowerShell telepítése

Ezek az utasítások a Microsoft Azure PowerShell használata. A következő cikk ismerteti az Azure PowerShell telepítése és konfigurálása.

* [Az Azure PowerShell telepítése és konfigurálása](/powershell/azure/overview)

Ebben a cikkben szereplő példák azt feltételezik, hogy rendelkezik-e egy meglévő erőforráscsoportot. Létrehozhat egy olyan erőforráscsoport, a következő parancsot:

```powershell
New-AzureRmResourceGroup -Name MyRG -Location "West US"
```

> [!NOTE]
> Az Azure Resource Manager megköveteli, hogy a hely összes erőforráscsoport. Ezen a helyen az alapértelmezett szolgál az erőforráscsoport létrejött erőforrásokat. Azonban mivel a Traffic Manager-profil erőforrások globális, nem pedig regionális, a választott erőforráscsoport helye nincs hatással van az Azure Traffic Manager.

## <a name="create-a-traffic-manager-profile"></a>A Traffic Manager-profil létrehozása

Traffic Manager-profil létrehozásához használja a `New-AzureRmTrafficManagerProfile` parancsmagot:

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName contoso -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
```

A következő táblázat a paramétereket:

| Paraméter | Leírás |
| --- | --- |
| Name (Név) |A Traffic Manager-profil erőforrás erőforrás neve. Profilok ugyanabban az erőforráscsoportban egyedi névvel kell rendelkezniük. Ez a név nem azonos a DNS-lekérdezések használt DNS-név. |
| ResourceGroupName |A profil erőforrást tartalmazó erőforráscsoport neve. |
| TrafficRoutingMethod |Megadja a forgalom-útválasztási módszert határozza meg, melyik végponthoz válaszként visszaadott a DNS-lekérdezés. Lehetséges értékek: "Teljesítmény", "Weighted" vagy "Priority". |
| RelativeDnsName |Adja meg a Traffic Manager-profil által biztosított DNS-nevét hostname része. Ezt az értéket a DNS-tartománynevét, Azure Traffic Manager által használt a teljes tartománynevét (FQDN) a profil együtt. Például "contoso" értékre állítja lesz "contoso.trafficmanager.net." |
| TTL |Adja meg a DNS idő élettartamát (TTL). Az élettartam tájékoztatja a helyi DNS-feloldókat és a DNS-ügyfelek mennyi a gyorsítótár DNS-válaszok a Traffic Manager-profil. |
| MonitorProtocol |Adja meg a végpont állapotának figyeléséhez használni kívánt protokollt. Lehetséges értékek: "HTTP" és "HTTPS". |
| MonitorPort |Adja meg a végpont állapotának figyeléséhez használt TCP-portot. |
| MonitorPath |A végpont az végpont health mintavételi használt tartománynév relatív elérési út megadása |

A parancsmag egy Traffic Manager-profil létrehozása az Azure-ban, és egy megfelelő profil objektumot ad vissza a PowerShell. Ezen a ponton a profil nem tartalmaz végpontok. Végpontok Traffic Manager-profil történő hozzáadásával kapcsolatos további információkért lásd: [Traffic Manager-végpontok hozzáadása](#adding-traffic-manager-endpoints).

## <a name="get-a-traffic-manager-profile"></a>Traffic Manager-profil beolvasása

Egy meglévő Traffic Manager-profil objektummal lekéréséhez használja a `Get-AzureRmTrafficManagerProfle` parancsmagot:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
```

Ez a parancsmag egy Traffic Manager-profil objektumot ad vissza.

## <a name="update-a-traffic-manager-profile"></a>Traffic Manager-profil frissítése

Traffic Manager-profilok módosítása a 3. lépés folyamatot követi:

1. Le a profil `Get-AzureRmTrafficManagerProfile` , vagy használja a profil által visszaadott `New-AzureRmTrafficManagerProfile`.
2. Módosítsa a profilt. Adja hozzá, és távolítsa el a végpontok, vagy módosítsa a végpont vagy profil paraméterek. A kapcsolat nélküli műveletek, amelyek ezeket a módosításokat. Csak módosítja a helyi objektum a memóriában, amely a profil jelöli.
3. A módosítások végrehajtásához használja a `Set-AzureRmTrafficManagerProfile` parancsmag.

Az összes profil tulajdonságai kivételével a profil RelativeDnsName módosíthatók. Ha módosítani szeretné a RelativeDnsName, törölnie kell a profil és egy új nevet az új profil.

A következő példa bemutatja, hogyan módosíthatja a profil élettartam:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
$profile.Ttl = 300
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

A Traffic Manager-végpontok három típusa van:

1. **Azure-végpontok** Azure-ban üzemeltetett szolgáltatások
2. **Külső végpontok száma** Azure-on kívüli üzemeltetett szolgáltatások
3. **A beágyazott végpontok** beágyazott hierarchiák Traffic Manager-profilok létrehozásához használt. Beágyazott végpontok összetett alkalmazások speciális forgalom-útválasztási konfigurációi engedélyezése.

A három esetben végpontok hozzáadása is lehetséges két módon:

1. A fentiekben ismertetett 3. lépés folyamatok használata. Ez a módszer előnye, hogy több végpont módosítás egy frissítés.
2. A New-AzureRmTrafficManagerEndpoint parancsmag használatával. Ez a parancsmag egy olyan végpont hozzáadása egy meglévő Traffic Manager-profil egy művelettel.

## <a name="adding-azure-endpoints"></a>Azure-végpontok hozzáadása

Azure-végpontok Azure-ban üzemeltetett szolgáltatások hivatkozik. Azure-végpontok két típusú támogatottak:

1. Azure Web Apps
2. Az Azure nyilvános erőforrások (csatolható terheléselosztó vagy egy virtuális gép hálózati adapter). A nyilvános rendelt használható a Traffic Manager DNS névvel kell rendelkeznie.

Minden esetben:

* A szolgáltatás adott meg a "targetresourceid azonosítója" paraméterének `Add-AzureRmTrafficManagerEndpointConfig` vagy `New-AzureRmTrafficManagerEndpoint`.
* A "Target" és "EndpointLocation" targetresourceid azonosítója által azt jelentette.
* Adja meg a "súly" megadása nem kötelező. Súlyozás csak használják, ha a profil használatára van konfigurálva, a "Weighted" forgalom-útválasztási módszer használatát. Ellenkező esetben nem veszi őket figyelembe. Ha meg van adva, az érték 1 és 1000 közötti számnak kell lennie. Az alapértelmezett érték: "1".
* Adja meg a "Priority" megadása nem kötelező. Prioritások csak akkor használatosak, ha a profil használatára van konfigurálva, a "Priority" forgalom-útválasztási módszer használatát. Ellenkező esetben nem veszi őket figyelembe. Érvényes értékek az alacsonyabb magasabb prioritású jelző: 1-1000. Ha meg van adva egy végpontot, azok összes végpontok adható meg. Ha nincs megadva, "1"-től kezdődő alapértelmezett értékeket, hogy a végpontok felsorolt sorrendben alkalmazza.

### <a name="example-1-adding-web-app-endpoints-using-add-azurermtrafficmanagerendpointconfig"></a>1. példa: A webalkalmazás végpontjaitól hozzáadása `Add-AzureRmTrafficManagerEndpointConfig`

Ebben a példában azt a Traffic Manager-profil létrehozása, és adja hozzá a két Web App végpontjaitól az `Add-AzureRmTrafficManagerEndpointConfig` parancsmag.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$webapp1 = Get-AzureRMWebApp -Name webapp1
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp1ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp1.Id -EndpointStatus Enabled
$webapp2 = Get-AzureRMWebApp -Name webapp2
Add-AzureRmTrafficManagerEndpointConfig -EndpointName webapp2ep -TrafficManagerProfile $profile -Type AzureEndpoints -TargetResourceId $webapp2.Id -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```
### <a name="example-2-adding-a-publicipaddress-endpoint-using-new-azurermtrafficmanagerendpoint"></a>2. példa: Hozzáadása egy nyilvános végpontot a `New-AzureRmTrafficManagerEndpoint`

Ebben a példában egy nyilvános IP-cím erőforrás a Traffic Manager-profilhoz van adva. A nyilvános IP-cím rendelkeznie kell egy DNS-név, és a hálózati Adaptert egy virtuális gép vagy egy adott terheléselosztóhoz köthető.

```powershell
$ip = Get-AzureRmPublicIpAddress -Name MyPublicIP -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name MyIpEndpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="adding-external-endpoints"></a>Külső végpontok hozzáadása

A TRAFFIC Manager külső végpontok segítségével Azure-on kívüli tárolt szolgáltatások forgalmat irányítani. Mivel az Azure-végpontok, külső végpontok hozzáadása is lehetséges vagy használatával `Add-AzureRmTrafficManagerEndpointConfig` követ `Set-AzureRmTrafficManagerProfile`, vagy `New-AzureRMTrafficManagerEndpoint`.

Külső végpontok száma megadásakor:

* A végpont tartomány nevét kötelező megadni a "Target" paraméter használatával
* Ha a "Teljesítmény" forgalom-útválasztási módszert használ, a "EndpointLocation" megadása kötelező. Ellenkező esetben nem kötelező. Az értéknek kell lennie egy [érvényes Azure-régiót neve](https://azure.microsoft.com/regions/).
* A "Súly" és "Priority" megadása nem kötelező.

### <a name="example-1-adding-external-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>1. példa: Használó külső végpontok hozzáadása `Add-AzureRmTrafficManagerEndpointConfig` és `Set-AzureRmTrafficManagerProfile`

Ebben a példában azt Traffic Manager-profil létrehozása, adja hozzá a két külső végpont és a változtatások véglegesítése a határidő.

```powershell
$profile = New-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName myapp -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName eu-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointLocation "North Europe" -EndpointStatus Enabled
Add-AzureRmTrafficManagerEndpointConfig -EndpointName us-endpoint -TrafficManagerProfile $profile -Type ExternalEndpoints -Target app-us.contoso.com -EndpointLocation "Central US" -EndpointStatus Enabled
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-adding-external-endpoints-using-new-azurermtrafficmanagerendpoint"></a>2. példa: Hozzáadása használata külső végpontok száma `New-AzureRmTrafficManagerEndpoint`

Ebben a példában jelenleg egy olyan külső végpont a egy meglévő profilt kell felvenni. A profil van megadva, a profil és az erőforrás nevének használatával.

```powershell
New-AzureRmTrafficManagerEndpoint -Name eu-endpoint -ProfileName MyProfile -ResourceGroupName MyRG -Type ExternalEndpoints -Target app-eu.contoso.com -EndpointStatus Enabled
```

## <a name="adding-nested-endpoints"></a>"Nested" végpontok hozzáadása

Minden egyes Traffic Manager-profil megadja egy forgalom-útválasztási módszert. Vannak azonban forgatókönyv esetén van szükség, kifinomultabb a forgalom útválasztásához, mint egyetlen Traffic Manager-profil által biztosított útválasztását. Traffic Manager-profilok egynél több forgalom-útválasztási módszer előnyei egyesítése ágyazhatja be. Beágyazott profilok lehetővé teszik a támogatási nagyobb és összetettebb alkalmazások központi telepítésének alapértelmezett Traffic Manager működés felülbírálásához. További részletes példák: [beágyazott Traffic Manager-profilok](traffic-manager-nested-profiles.md).

Beágyazott végpontok úgy vannak konfigurálva, a szülő profilnál, egy adott típusú végpont, "NestedEndpoints" használatával. Beágyazott végpontok megadásakor:

* A végpont a "targetresourceid azonosítója" paraméter használatával kell megadni
* Ha a "Teljesítmény" forgalom-útválasztási módszert használ, a "EndpointLocation" megadása kötelező. Ellenkező esetben nem kötelező. Az értéknek kell lennie egy [érvényes Azure-régiót neve](http://azure.microsoft.com/regions/).
* A "Súly" és "Priority" megadása nem kötelező, mint az Azure-végpontok.
* A "MinChildEndpoints" paraméter nem kötelező. Az alapértelmezett érték: "1". A rendelkezésre álló végpontok száma a küszöbérték alá csökken, ha a szülő profil úgy ítéli meg, a gyermek profil "csökkentett teljesítményű" és a szülő profil végpontja felé irányuló forgalom hozunk.

### <a name="example-1-adding-nested-endpoints-using-add-azurermtrafficmanagerendpointconfig-and-set-azurermtrafficmanagerprofile"></a>1. példa: Hozzáadása beágyazott végpontjaitól `Add-AzureRmTrafficManagerEndpointConfig` és `Set-AzureRmTrafficManagerProfile`

Ebben a példában azt új Traffic Manager gyermek és szülő profilok létrehozása, a gyermek hozzáadása egy beágyazott végpontként a szülő és a változtatások véglegesítése a határidő.

```powershell
$child = New-AzureRmTrafficManagerProfile -Name child -ResourceGroupName MyRG -TrafficRoutingMethod Priority -RelativeDnsName child -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
$parent = New-AzureRmTrafficManagerProfile -Name parent -ResourceGroupName MyRG -TrafficRoutingMethod Performance -RelativeDnsName parent -Ttl 30 -MonitorProtocol HTTP -MonitorPort 80 -MonitorPath "/"
Add-AzureRmTrafficManagerEndpointConfig -EndpointName child-endpoint -TrafficManagerProfile $parent -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

Ebben a példában kivonatosan mutatja azt adta hozzá, más végpontok a gyermek vagy szülő profilok.

### <a name="example-2-adding-nested-endpoints-using-new-azurermtrafficmanagerendpoint"></a>2. példa: Használó beágyazott végpontok hozzáadása `New-AzureRmTrafficManagerEndpoint`

Ebben a példában azt egy meglévő gyermek profil hozzáadása egy beágyazott végpontként egy meglévő szülő profilt. A profil van megadva, a profil és az erőforrás nevének használatával.

```powershell
$child = Get-AzureRmTrafficManagerEndpoint -Name child -ResourceGroupName MyRG
New-AzureRmTrafficManagerEndpoint -Name child-endpoint -ProfileName parent -ResourceGroupName MyRG -Type NestedEndpoints -TargetResourceId $child.Id -EndpointStatus Enabled -EndpointLocation "North Europe" -MinChildEndpoints 2
```

## <a name="adding-endpoints-from-another-subscription"></a>Egy másik előfizetésből végpontok hozzáadása

A TRAFFIC Manager képes együttműködni a végpontok különböző előfizetésekhez. Váltson át a végponttal hozzá szeretne adni a szükséges bemeneti adatok felvétele a Traffic Manager beolvasni az előfizetés szükséges. Majd kell váltani az előfizetéssel, valamint a Traffic Manager-profilt, és a encpoint felvétele. Az alábbi példa bemutatja, hogyan ehhez a nyilvános IP-címmel.

```powershell
Set-AzureRmContext -SubscriptionId $EndpointSubscription
$ip = Get-AzureRmPublicIpAddress -Name $IpAddresName -ResourceGroupName $EndpointRG

Set-AzureRmContext -SubscriptionId $trafficmanagerSubscription
New-AzureRmTrafficManagerEndpoint -Name $EndpointName -ProfileName $ProfileName -ResourceGroupName $TrafficManagerRG -Type AzureEndpoints -TargetResourceId $ip.Id -EndpointStatus Enabled
```

## <a name="update-a-traffic-manager-endpoint"></a>A Traffic Manager-végpont frissítése

Két módon lehet egy meglévő Traffic Manager-végpont frissítése:

1. Használatával a Traffic Manager-profil beolvasása `Get-AzureRmTrafficManagerProfile`, frissítse a profilon belül a végpont tulajdonságait, és véglegesítse a módosításokat `Set-AzureRmTrafficManagerProfile`. Ez a módszer azzal az előnnyel jár, amely nem tudja frissíteni a egy művelettel több végpont.
2. A Traffic Manager-végpontot a GET `Get-AzureRmTrafficManagerEndpoint`, frissítse a végpont tulajdonságait, és véglegesítse a módosításokat `Set-AzureRmTrafficManagerEndpoint`. Ez a módszer is egyszerűbb, mivel a profil a végpontok tömbbe indexelő nincs szükség.

### <a name="example-1-updating-endpoints-using-get-azurermtrafficmanagerprofile-and-set-azurermtrafficmanagerprofile"></a>1. példa: Frissítése végpontjaitól `Get-AzureRmTrafficManagerProfile` és `Set-AzureRmTrafficManagerProfile`

Ebben a példában két végpontot egy meglévő profilon belül a prioritásának módosítására azt.

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name myprofile -ResourceGroupName MyRG
$profile.Endpoints[0].Priority = 2
$profile.Endpoints[1].Priority = 1
Set-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile
```

### <a name="example-2-updating-an-endpoint-using-get-azurermtrafficmanagerendpoint-and-set-azurermtrafficmanagerendpoint"></a>2. példa: Frissítése egy végpontot a `Get-AzureRmTrafficManagerEndpoint` és `Set-AzureRmTrafficManagerEndpoint`

Ebben a példában egy meglévő profil egyetlen végpont súlyának módosíthatja azt.

```powershell
$endpoint = Get-AzureRmTrafficManagerEndpoint -Name myendpoint -ProfileName myprofile -ResourceGroupName MyRG -Type ExternalEndpoints
$endpoint.Weight = 20
Set-AzureRmTrafficManagerEndpoint -TrafficManagerEndpoint $endpoint
```

## <a name="enabling-and-disabling-endpoints-and-profiles"></a>Engedélyezése és letiltása a végpontok és profilok

A TRAFFIC Manager lehetővé teszi, hogy engedélyezve van, és le van tiltva, valamint engedélyezése és letiltása teljes profilok lehetővé egyedi végpontok.
Ezeket a módosításokat a végpont vagy profil erőforrások első/frissítése/beállítás módosítható. Ezek a gyakori műveletek egyszerűsítésére, támogatottak továbbá akkor dedikált parancsmagok segítségével.

### <a name="example-1-enabling-and-disabling-a-traffic-manager-profile"></a>1. példa: Engedélyezése és letiltása Traffic Manager-profil

Engedélyezze a Traffic Manager-profil az `Enable-AzureRmTrafficManagerProfile`. A profilt a profil objektum adható meg. A profil objektum használatával vagy keresztül a feldolgozási sor adhatók át a "-TrafficManagerProfile" paraméter. Ebben a példában azt adja meg a profilhoz a profil és az erőforrás neve.

```powershell
Enable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

Traffic Manager-profil letiltása:

```powershell
Disable-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyResourceGroup
```

A Disable-AzureRmTrafficManagerProfile parancsmag megerősítés kéri. Ez a kérdés is letiltja, használja a "-Force" paraméter.

### <a name="example-2-enabling-and-disabling-a-traffic-manager-endpoint"></a>2. példa: Engedélyezése és letiltása Traffic Manager-végpontot

A Traffic Manager-végpont engedélyezéséhez az `Enable-AzureRmTrafficManagerEndpoint`. Adja meg a végpont két módja van

1. Egy keresztül a feldolgozási sor átadott TrafficManagerEndpoint objektum használatával vagy az a "-TrafficManagerEndpoint" paraméter
2. A végpont neve, a típusú végpont, a profil neve és az erőforráscsoport-név használatával:

```powershell
Enable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Hasonlóképpen a Traffic Manager-végpont letiltása:

```powershell
Disable-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG -Force
```

A `Disable-AzureRmTrafficManagerProfile`, a `Disable-AzureRmTrafficManagerEndpoint` parancsmag jóváhagyást kér. Ez a kérdés is letiltja, használja a "-Force" paraméter.

## <a name="delete-a-traffic-manager-endpoint"></a>A Traffic Manager-végpont törlése

Az egyes végpontok eltávolításához használja a `Remove-AzureRmTrafficManagerEndpoint` parancsmagot:

```powershell
Remove-AzureRmTrafficManagerEndpoint -Name MyEndpoint -Type AzureEndpoints -ProfileName MyProfile -ResourceGroupName MyRG
```

Ez a parancsmag felszólítja megerősítést kér. Ez a kérdés is letiltja, használja a "-Force" paraméter.

## <a name="delete-a-traffic-manager-profile"></a>Traffic Manager-profil törlése

Traffic Manager-profil törléséhez használja a `Remove-AzureRmTrafficManagerProfile` parancsmag, a profil és az erőforrás nevének megadása:

```powershell
Remove-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG [-Force]
```

Ez a parancsmag felszólítja megerősítést kér. Ez a kérdés is letiltja, használja a "-Force" paraméter.

Törli a profilt a profil objektum használatával is megadható:

```powershell
$profile = Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG
Remove-AzureRmTrafficManagerProfile -TrafficManagerProfile $profile [-Force]
```

Ebben a sorozatban is átirányítható:

```powershell
Get-AzureRmTrafficManagerProfile -Name MyProfile -ResourceGroupName MyRG | Remove-AzureRmTrafficManagerProfile [-Force]
```

## <a name="next-steps"></a>További lépések

[A TRAFFIC Manager figyelése](traffic-manager-monitoring.md)

[A Traffic Manager teljesítményével kapcsolatos megfontolások](traffic-manager-performance-considerations.md)
