---
title: "Belső terheléselosztó létrehozása a PowerShell használatával a klasszikus üzembehelyezési modellben | Microsoft Docs"
description: "Ismerje meg, hogyan hozható létre belső terheléselosztó a PowerShell használatával a klasszikus üzembehelyezési modellben"
services: load-balancer
documentationcenter: na
author: kumudd
manager: timlt
editor: 
tags: azure-service-management
ms.assetid: 3be93168-3787-45a5-a194-9124fe386493
ms.service: load-balancer
ms.devlang: na
ms.topic: get-started-article
ms.tgt_pltfrm: na
ms.workload: infrastructure-services
ms.date: 02/09/2016
ms.author: kumud
translationtype: Human Translation
ms.sourcegitcommit: 8827793d771a2982a3dccb5d5d1674af0cd472ce
ms.openlocfilehash: 829ed8ac5f4770c7745923edf3c09b29f4ad3ed4

---

# <a name="get-started-creating-an-internal-load-balancer-classic-using-powershell"></a>Bevezetés a belső terheléselosztó (klasszikus) PowerShell használatával történő létrehozásába

> [!div class="op_single_selector"]
> * [PowerShell](../load-balancer/load-balancer-get-started-ilb-classic-ps.md)
> * [Azure CLI](../load-balancer/load-balancer-get-started-ilb-classic-cli.md)
> * [Felhőszolgáltatások](../load-balancer/load-balancer-get-started-ilb-classic-cloud.md)

[!INCLUDE [load-balancer-get-started-ilb-intro-include.md](../../includes/load-balancer-get-started-ilb-intro-include.md)]

> [!IMPORTANT]
> Az Azure két különböző üzembe helyezési modellel rendelkezik az erőforrások létrehozásához és használatához: [Resource Manager és klasszikus](../azure-resource-manager/resource-manager-deployment-model.md).  Ez a cikk a klasszikus üzembehelyezési modellt ismerteti. A Microsoft azt javasolja, hogy az új telepítések esetén a Resource Manager modellt használja. Ismerje meg, [hogyan hajthatja végre ezeket a lépéseket a Resource Manager-modell használatával](load-balancer-get-started-ilb-arm-ps.md).

[!INCLUDE [load-balancer-get-started-ilb-scenario-include.md](../../includes/load-balancer-get-started-ilb-scenario-include.md)]

[!INCLUDE [azure-ps-prerequisites-include.md](../../includes/azure-ps-prerequisites-include.md)]

## <a name="create-an-internal-load-balancer-set-for-virtual-machines"></a>Belső terheléselosztó készlet létrehozása a virtuális gépekhez

Egy belső terheléselosztó készletnek, illetve azoknak a kiszolgálóknak a létrehozásához, amelyek a forgalmukat küldik rá, a következőket kell tennie:

1. Hozzon létre egy belső terheléselosztási példányt, amely annak a bejövő forgalomnak a végpontja lesz, amelynek a terhelését el kell osztani az elosztott terhelésű készlet kiszolgálóin.
2. Adja hozzá a virtuális gépekhez rendelt végpontokat, amelyek a bejövő forgalmat fogják kapni.
3. Konfigurálja úgy a kiszolgálókat, amelyek a terheléselosztóra fogják küldeni a forgalmat, hogy azt a belső terheléselosztási példány virtuális IP-címére (VIP) küldjék.

### <a name="step-1-create-an-internal-load-balancing-instance"></a>1. lépés: Belső terheléselosztási példány létrehozása

Egy meglévő felhőalapú szolgáltatás vagy egy regionális virtuális hálózat alatt üzembe helyezett felhőalapú szolgáltatás esetében a következő Windows PowerShell-parancsokkal hozhat létre belső terheléselosztási példányt:

```powershell
$svc="<Cloud Service Name>"
$ilb="<Name of your ILB instance>"
$subnet="<Name of the subnet within your virtual network>"
$IP="<The IPv4 address to use on the subnet-optional>"

Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb –SubnetName $subnet –StaticVNetIPAddress $IP
```

Vegye figyelembe, hogy az [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx) Windows PowerShell-parancsmag ilyen használata a DefaultProbe paraméterkészletet használja. A további paraméterkészletekkel kapcsolatos további információkért lásd: [Add-AzureEndpoint](https://msdn.microsoft.com/library/dn495300.aspx).

### <a name="step-2-add-endpoints-to-the-internal-load-balancing-instance"></a>2. lépés: Végpontok hozzáadása a belső terheléselosztási példányhoz

Például:

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
$lbsetname="lbset"
$prot="tcp"
$locport=1433
$pubport=1433
$ilb="ilbset"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -Lbset $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

### <a name="step-3-configure-your-servers-to-send-their-traffic-to-the-new-internal-load-balancing-endpoint"></a>3. lépés: A kiszolgálók konfigurálása, hogy a forgalmat az új belső terheléselosztási végpontra küldjék

Azokat a kiszolgálókat, amelyeknek a forgalmán terheléselosztás történik, úgy kell konfigurálni, hogy a belső terheléselosztási példány új IP-címét (a VIP-címet) használják. Ez az a cím, amelyet a belső terheléselosztási példány figyel. A legtöbb esetben csak hozzá kell adnia egy DNS-rekordot a belső terheléselosztási példány VIP-címéhez, vagy módosítania kell a DNS-rekordot.

Ha a belső terheléselosztási példány létrehozása során adta meg az IP-címet, már rendelkezik a VIP-címmel. Ellenkező esetben a VIP-cím a következő parancsokkal tekinthető meg:

```powershell
$svc="<Cloud Service Name>"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

A parancsok használatához töltse ki az értékeket, és távolítsa el a < és a > karaktereket. Például:

```powershell
$svc="mytestcloud"
Get-AzureService -ServiceName $svc | Get-AzureInternalLoadBalancer
```

A Get-AzureInternalLoadBalancer parancs kimenetéből írja fel az IP-címet, és végezze el a megfelelő változtatásokat a kiszolgálókon vagy a DNS-rekordokon annak a biztosítására, hogy a rendszer a VIP-címre küldje a forgalmat.

> [!NOTE]
> A Microsoft Azure platform egy statikus, nyilvánosan irányítható IPv4-címet használ számos különböző felügyeleti forgatókönyvhöz. Az IP-cím a 168.63.129.16. Ezt az IP-címet nem blokkolhatja egy tűzfal sem, mert ez nem várt viselkedést okozhat.
> Az Azure belső terheléselosztás esetében ezt az IP-címet használják a terheléselosztó figyelési mintavételezői az elosztott terhelésű készlet virtuális gépeinek állapotmeghatározásához. Ha egy belső elosztott terhelésű készletben hálózati biztonsági csoportot használnak az Azure virtuális gépekre irányuló forgalom korlátozásához, vagy egy virtuális alhálózaton alkalmazzák, győződjön meg arról, hogy a 168.63.129.16 címről érkező adatforgalom engedélyezéséhez felvettek egy hálózati biztonsági szabályt.

## <a name="example-of-internal-load-balancing"></a>Példa a belső terheléselosztásra

A következő szakaszok lépésenként mutatják be egy elosztott terhelésű készlet létrehozásának teljes folyamatát két példakonfigurációhoz.

### <a name="an-internet-facing-multi-tier-application"></a>Az internetről elérhető, több rétegből álló alkalmazás

Elosztott terhelésű adatbázis-szolgáltatást szeretne biztosítani internetről elérhető webkiszolgálók készlete számára. Mindkét kiszolgálókészletet ugyanaz az Azure felhőszolgáltatás üzemelteti. Az 1433-as TCP-portra irányuló webkiszolgálói forgalmat két virtuális gép között kell elosztani az adatbázisszinten. Az 1. ábra a konfigurációt mutatja.

![Belső elosztott terhelésű készlet az adatbázisszint számára](./media/load-balancer-internal-getstarted/IC736321.png)

A konfiguráció a következőket tartalmazza:

* A meglévő felhőszolgáltatás neve, amely a virtuális gépeket üzemelteti, a mytestcloud.
* A két meglévő adatbázis-kiszolgáló neve DB1 és DB2.
* A webszint webkiszolgálói az adatbázisszint adatbázis-kiszolgálóihoz magánhálózati IP-címek használatával csatlakoznak. Egy másik megoldás saját DNS használata a virtuális hálózathoz, és A-rekord manuális regisztrálása a belső terheléselosztó készlethez.

A következő parancsok **ILBset** néven egy új belső terheléselosztási példányt konfigurálnak, és a két adatbázis-kiszolgálóra vonatkozóan végpontokat adnak hozzá a virtuális gépekhez:

```powershell
$svc="mytestcloud"
$ilb="ilbset"
Add-AzureInternalLoadBalancer -ServiceName $svc -InternalLoadBalancerName $ilb
$prot="tcp"
$locport=1433
$pubport=1433
$epname="TCP-1433-1433"
$lbsetname="lbset"
$vmname="DB1"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM

$epname="TCP-1433-1433-2"
$vmname="DB2"
Get-AzureVM –ServiceName $svc –Name $vmname | Add-AzureEndpoint -Name $epname -LbSetName $lbsetname -Protocol $prot -LocalPort $locport -PublicPort $pubport –DefaultProbe -InternalLoadBalancerName $ilb | Update-AzureVM
```

## <a name="remove-an-internal-load-balancing-configuration"></a>Belső terheléselosztási konfiguráció eltávolítása

Használja a következő parancsokat a virtuális gép végpontként történő eltávolítására a belső terheléselosztási példányról:

```powershell
$svc="<Cloud service name>"
$vmname="<Name of the VM>"
$epname="<Name of the endpoint>"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

A parancsok használatához töltse ki az értékeket, a < és a > karaktert eltávolítva.

Például:

```powershell
$svc="mytestcloud"
$vmname="DB1"
$epname="TCP-1433-1433"
Get-AzureVM -ServiceName $svc -Name $vmname | Remove-AzureEndpoint -Name $epname | Update-AzureVM
```

Belső terheléselosztási példány eltávolításához egy felhőszolgáltatásból használja a következő parancsokat:

```powershell
$svc="<Cloud service name>"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

A parancsok használatához töltse ki az értékeket, és távolítsa el a < és a > karaktereket.

Például:

```powershell
$svc="mytestcloud"
Remove-AzureInternalLoadBalancer -ServiceName $svc
```

## <a name="additional-information-about-internal-load-balancer-cmdlets"></a>További információ a belső terheléselosztási parancsmagokról

A belső terheléselosztási parancsmagokkal kapcsolatos további információkért futtassa a Windows PowerShell parancssorában a következő parancsokat:

```powershell
Get-Help New-AzureInternalLoadBalancerConfig -full
Get-Help Add-AzureInternalLoadBalancer -full
Get-Help Get-AzureInternalLoadbalancer -full
Get-Help Remove-AzureInternalLoadBalancer -full
```

## <a name="next-steps"></a>Következő lépések

[A terheléselosztó elosztási módjának konfigurálása forrás IP-affinitás használatával](load-balancer-distribution-mode.md)

[A terheléselosztó üresjárati TCP-időtúllépési beállításainak konfigurálása](load-balancer-tcp-idle-timeout.md)




<!--HONumber=Nov16_HO5-->


