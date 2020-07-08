---
title: Azure Service Fabric csomópont-típus vertikális felskálázása
description: Megtudhatja, hogyan méretezheti Service Fabric fürtöt egy virtuálisgép-méretezési csoport hozzáadásával.
ms.topic: article
ms.date: 02/13/2019
ms.openlocfilehash: 2d700367049e0bf9bf710aad110c850a78c26220
ms.sourcegitcommit: 877491bd46921c11dd478bd25fc718ceee2dcc08
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/02/2020
ms.locfileid: "85610693"
---
# <a name="scale-up-a-service-fabric-cluster-primary-node-type"></a>Service Fabric-fürt elsődleges csomóponttípusának vertikális felskálázása
Ez a cikk azt ismerteti, hogyan lehet a virtuális gépek erőforrásainak növelésével bővíteni egy Service Fabric-fürt elsődleges csomópontjának típusát. A Service Fabric-fürt olyan virtuális vagy fizikai gépek hálózathoz csatlakoztatott készlete, amelybe a rendszer üzembe helyezi és kezeli a szolgáltatásait. Egy fürt részét képező gépet vagy virtuális gépet csomópontnak nevezzük. A virtuálisgép-méretezési csoportok egy Azure-beli számítási erőforrás, amely készletként telepíti és felügyeli a virtuális gépek gyűjteményét. Az Azure-fürtben definiált összes csomópont-típus [külön méretezési csoportként van beállítva](service-fabric-cluster-nodetypes.md). Ezután mindegyik csomópont-típust külön lehet kezelni. Service Fabric-fürt létrehozása után függőlegesen méretezheti a fürt csomópontjának típusát (módosítsa a csomópontok erőforrásait), vagy frissítse a csomópont típusú virtuális gépek operációs rendszerét.  A fürtöt bármikor méretezheti, még akkor is, ha a munkaterhelések futnak a fürtön.  A fürt skálázása esetén az alkalmazások is automatikusan méretezhetők.

> [!WARNING]
> Ha a fürt állapota nem kifogástalan, ne próbálkozzon az elsődleges csomópont típusú vertikális Felskálázási eljárással, mivel ez a művelet csak a fürt destabilizálására szolgál.
>

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="process-to-upgrade-the-size-and-operating-system-of-the-primary-node-type-vms"></a>Az elsődleges csomópont típusú virtuális gépek méretének és operációs rendszerének frissítésére szolgáló folyamat
Az alábbi folyamat a virtuális gépek méretének és operációs rendszerének frissítését írja le.  A frissítés után az elsődleges csomópont típusú virtuális gépek szabványos D4_V2 és a Windows Server 2016 Datacenter tárolókkal futnak.

> [!WARNING]
> Az eljárás üzemi fürtön való megkísérlése előtt javasoljuk, hogy tanulmányozza a minta sablonokat, és ellenőrizze a folyamatot egy tesztelési fürtön. A fürt egy időre sem érhető el. Párhuzamosan nem lehet módosítani több VMSS, amelyek ugyanabban a NodeType vannak deklarálva; külön üzembe helyezési műveleteket kell végrehajtania, hogy az egyes NodeType-VMSS módosításokat alkalmazzon.

1. Telepítse a kezdeti fürtöt két csomópont-típussal és két méretezési csoporttal (egy csomópont típusú méretezési csoport) a minta [sablon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.json) és a [Paraméterek](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-2ScaleSets.parameters.json) fájljainak használatával.  Mindkét méretezési csoport mérete standard D2_V2, és a Windows Server 2012 R2 Datacenter fut.  Várjon, amíg a fürt befejezi az alapkonfiguráció frissítését.   
2. Opcionális – állapot-nyilvántartó minta üzembe helyezése a fürtön.
3. Miután eldöntötte, hogy frissítette az elsődleges csomópont típusú virtuális gépeket, adjon hozzá egy új méretezési csoportot az elsődleges csomópont-típushoz a minta [sablon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) és a [Parameters](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) fájlok használatával, hogy az elsődleges csomópont típusa most két méretezési csoporttal rendelkezik.  A rendszerszolgáltatások és a felhasználói alkalmazások áttelepíthetők a két különböző méretezési csoportba tartozó virtuális gépek között.  Az új méretezési csoport virtuális gépei standard szintű D4_V2, és Windows Server 2016 Datacenter-t futtatnak tárolókkal.  Az új méretezési csoporttal új terheléselosztó és nyilvános IP-cím is hozzá lesz adva.  
    A sablonban található új méretezési csoport megkereséséhez keresse meg a *vmNodeType2Name* paraméter által megnevezett "Microsoft. számítási/virtualMachineScaleSets" erőforrást.  Az új méretezési csoport hozzá lesz adva az elsődleges csomópont-típushoz a Properties->virtualMachineProfile->extensionProfile->Extensions->Properties->Settings->nodeTypeRef-beállítás használatával.
4. Ellenőrizze a fürt állapotát, és ellenőrizze, hogy az összes csomópont kifogástalan állapotú-e.
5. Tiltsa le a csomópontokat az elsődleges csomópont típusának régi méretezési csoportjában a csomópont eltávolítására szolgáló szándékkal. Egyszerre letilthatja az összes műveletet, és a rendszer várólistára helyezi a műveleteket. Várjon, amíg a csomópontok le vannak tiltva, ami hosszabb időt is igénybe vehet.  Mivel a csomópont-típus régebbi csomópontjai le vannak tiltva, a rendszerszolgáltatások és a magok csomópontjai az elsődleges csomópont típusában lévő új méretezési csoport virtuális gépei felé lesznek áttelepítve.
6. Távolítsa el a régebbi méretezési készletet az elsődleges csomópont típusától. (Miután a csomópontok le vannak tiltva az 5. lépésben, a Azure Portal virtuálisgép-méretezési csoport paneljén, a csomópontok felszabadítása a régi csomópont típusától eggyel.)
7. Távolítsa el a régi méretezési csoporthoz társított terheléselosztó. A fürt nem érhető el, amíg az új nyilvános IP-cím és terheléselosztó konfigurálva van az új méretezési csoportra.  
8. Tárolja a régi elsődleges csomópont típusú méretezési csoporthoz társított nyilvános IP-cím DNS-beállításait egy változóban, és távolítsa el a nyilvános IP-címet.
9. Cserélje le az új elsődleges csomópont típusú méretezési csoporthoz társított nyilvános IP-cím DNS-beállításait a törölt nyilvános IP-cím DNS-beállításaival.  A fürt most már elérhető.
10. Távolítsa el a csomópontok csomópontjának állapotát a fürtből.  Ha a régi méretezési csoport tartóssági szintje ezüst vagy arany volt, ezt a lépést a rendszer automatikusan végrehajtja.
11. Ha egy előző lépésben telepítette az állapot-nyilvántartó alkalmazást, ellenőrizze, hogy az alkalmazás működőképes-e.

## <a name="set-up-the-test-cluster"></a>A tesztelési fürt beállítása

Először töltse le az oktatóanyaghoz szükséges két készletet, a [sablont]() és a [paramétereket]() , valamint a [sablon]() és [Paraméterek]()után.

Ezután jelentkezzen be az Azure-fiókjába.

```powershell
# sign in to your Azure account and select your subscription
Login-AzAccount -SubscriptionId "<your subscription ID>"
```

Ez az oktatóanyag végigvezeti az önaláírt tanúsítványok létrehozásának forgatókönyvén. Ha Azure Key Vault meglévő tanúsítványt szeretne használni, ugorja át az alábbi lépést, és [egy meglévő tanúsítvány használatával hajtsa végre a fürt üzembe helyezéséhez](https://docs.microsoft.com/azure/service-fabric/upgrade-managed-disks#use-an-existing-certificate-to-deploy-the-cluster)szükséges lépéseket.

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Önaláírt tanúsítvány létrehozása és a fürt üzembe helyezése

Először rendelje hozzá az Service Fabric-fürt üzembe helyezéséhez szükséges változókat. Módosítsa a, a, a és a értékét az `resourceGroupName` `certSubjectName` `parameterFilePath` `templateFilePath` adott fiókhoz és környezethez:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-2ScaleSets.parameters.json"
```

> [!NOTE]
> A parancs futtatása előtt győződjön meg arról, hogy a `certOutputFolder` hely létezik a helyi gépen, mielőtt új Service Fabric fürtöt szeretne üzembe helyezni.

Ezután nyissa meg a *Deploy-2NodeTypes-2ScaleSets.parameters.js* fájlt, és módosítsa a és a értékét, `clusterName` hogy azok `dnsName` megfeleljenek a PowerShellben megadott dinamikus értékeknek, és mentse a módosításokat.

Ezután telepítse a Service Fabric test-fürtöt:

```powershell
# Deploy the initial test cluster
New-AzServiceFabricCluster `
    -ResourceGroupName $resourceGroupName `
    -CertificateOutputFolder $certOutputFolder `
    -CertificatePassword $certPassword `
    -CertificateSubjectName $certSubjectName `
    -TemplateFile $templateFilePath `
    -ParameterFile $parameterFilePath
```

Miután az üzembe helyezés befejeződött, keresse meg a *. pfx* -fájlt a `$certPfx` helyi gépen, és importálja a tanúsítványtárolóba:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

A művelet visszaküldi a tanúsítvány ujjlenyomatát, amelyet az új fürthöz való kapcsolódáshoz fog használni, és megtekintheti annak állapotát.

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Kapcsolódás az új fürthöz és az állapot állapotának ellenõrzése

Kapcsolódjon a fürthöz, és ellenőrizze, hogy minden csomópontja kifogástalan állapotú-e (cserélje `clusterName` `thumb` le a fürt változóit):

```powershell
# Connect to the cluster
$clusterName = "sftestupgrade.southcentralus.cloudapp.azure.com:19000"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"

Connect-ServiceFabricCluster `
    -ConnectionEndpoint $clusterName `
    -KeepAliveIntervalInSec 10 `
    -X509Credential `
    -ServerCertThumbprint $thumb  `
    -FindType FindByThumbprint `
    -FindValue $thumb `
    -StoreLocation CurrentUser `
    -StoreName My

# Check cluster health
Get-ServiceFabricClusterHealth
```

Készen áll a frissítési eljárás megkezdésére.

## <a name="upgrade-the-primary-node-type-vms"></a>Az elsődleges csomópont típusú virtuális gépek frissítése

Miután eldöntötte, hogy frissítette az elsődleges csomópont típusú virtuális gépeket, vegyen fel egy új méretezési csoportot az elsődleges csomópont típusára úgy, hogy az elsődleges csomópont típusa most két méretezési csoporttal rendelkezik. A szükséges módosítások megjelenítéséhez minta- [sablon](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.json) és [Parameters](https://github.com/Azure/service-fabric-scripts-and-templates/blob/master/templates/nodetype-upgrade/Deploy-2NodeTypes-3ScaleSets.parameters.json) fájlok vannak megadva. Az új méretezési csoport virtuális gépei a standard szintű D4_V2 és a Windows Server 2016 Datacenter és a tárolók futtatása. Az új méretezési csoporttal új terheléselosztó és nyilvános IP-cím is hozzá lesz adva. 

A sablonban található új méretezési csoport megkereséséhez keresse meg a vmNodeType2Name paraméter által megnevezett "Microsoft. számítási/virtualMachineScaleSets" erőforrást. Az új méretezési csoport hozzá lesz adva az elsődleges csomópont-típushoz a Properties->virtualMachineProfile->extensionProfile->Extensions->Properties->Settings->nodeTypeRef-beállítás használatával.

### <a name="deploy-the-updated-template"></a>A frissített sablon üzembe helyezése

Módosítsa a `parameterFilePath` és `templateFilePath` szükség szerint, majd futtassa a következő parancsot:

```powershell
# Deploy the new scale set into the primary node type along with a new load balancer and public IP
$templateFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.json"
$parameterFilePath = "C:\Deploy-2NodeTypes-3ScaleSets.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Az üzembe helyezés befejeztével Ellenőrizze újra a fürt állapotát, és győződjön meg arról, hogy az összes csomópont (az eredeti és az új méretezési csoporton) kifogástalan állapotú.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-nodes-to-the-new-scale-set"></a>Csomópontok migrálása az új méretezési csoportba

Most már készen áll az eredeti méretezési csoport csomópontjainak letiltására. Mivel ezek a csomópontok le lesznek tiltva, a rendszerszolgáltatások és a vetőmag-csomópontok átkerülnek az új méretezési csoport virtuális gépei felé, mert az elsődleges csomópont-típusként is meg van jelölve.

A nem elsődleges csomópontok típusának méretezéséhez ebben a lépésben módosítania kell a szolgáltatás elhelyezésére vonatkozó korlátozást, hogy tartalmazza az új virtuálisgép-méretezési csoport/csomópont típusát, majd csökkentse a régi virtuálisgép-méretezési csoport példányainak darabszámát nulla értékre, egyszerre egy csomópontot (a csomópont-eltávolítás nem befolyásolja a fürt megbízhatóságát).

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

A Service Fabric Explorer segítségével figyelheti a magok áttelepítését az új méretezési csoportba, és az eredeti méretezési csoport csomópontjainak a *letiltott* állapotba való *letiltásának* folyamatát.

> [!NOTE]
> Eltarthat egy ideig, amíg az eredeti méretezési csoport összes csomópontján el nem végzi a letiltási műveletet. Az adatkonzisztencia garantálása érdekében egyszerre csak egy mag-csomópont módosítható. Minden egyes mag-csomópont változásához szükség van egy fürt frissítésére; így a magok csomópontjának cseréje két fürt frissítését igényli (egyet a csomópontok hozzáadásához és eltávolításához). A példában szereplő öt mag-csomópont frissítése tíz fürt frissítését eredményezi.

## <a name="remove-the-original-scale-set"></a>Az eredeti méretezési csoport eltávolítása

A letiltási művelet befejezése után távolítsa el a méretezési csoportját.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

Service Fabric Explorer az eltávolított csomópontok (és így a *fürt állapota*) mostantól *hibás* állapotban jelennek meg.

## <a name="remove-the-old-load-balancer-and-update-dns-settings"></a>A régi Load Balancer eltávolítása és a DNS-beállítások frissítése

Most már eltávolíthatja a régi elsődleges csomópont-típushoz kapcsolódó erőforrásokat, a terheléselosztó és a régi nyilvános IP-címektől kezdve. 

```powershell
$lbname="LB-sfupgradetest-NTvm1"
$oldPublicIpName="PublicIP-LB-FE-0"
$newPublicIpName="PublicIP-LB-FE-2"

# Store DNS settings of public IP address related to old Primary NodeType into variable 
$oldprimaryPublicIP = Get-AzPublicIpAddress -Name $oldPublicIpName  -ResourceGroupName $groupname

$primaryDNSName = $oldprimaryPublicIP.DnsSettings.DomainNameLabel

$primaryDNSFqdn = $oldprimaryPublicIP.DnsSettings.Fqdn

# Remove Load Balancer related to old Primary NodeType. This will cause a brief period of downtime for the cluster
Remove-AzLoadBalancer -Name $lbname -ResourceGroupName $groupname -Force

# Remove the old public IP
Remove-AzPublicIpAddress -Name $oldPublicIpName -ResourceGroupName $groupname -Force
```

Ezután frissítjük az új nyilvános IP-cím DNS-beállításait a régi elsődleges csomópont típusú nyilvános IP-címek beállításainak tükrözéséhez.

```powershell
# Replace DNS settings of Public IP address related to new Primary Node Type with DNS settings of Public IP address related to old Primary Node Type
$PublicIP = Get-AzPublicIpAddress -Name $newPublicIpName  -ResourceGroupName $groupname
$PublicIP.DnsSettings.DomainNameLabel = $primaryDNSName
$PublicIP.DnsSettings.Fqdn = $primaryDNSFqdn
Set-AzPublicIpAddress -PublicIpAddress $PublicIP
```

További információ a fürt állapotáról

```powershell
# Check the cluster health
Get-ServiceFabricClusterHealth
```

Végül távolítsa el az egyes kapcsolódó csomópontok csomópont-állapotát. Ha a régi méretezési csoport tartóssági szintje ezüst vagy arany, akkor ez automatikusan megtörténik.

```powershell
# Remove node state for the deleted nodes.
foreach($name in $nodeNames){
    # Remove the node from the cluster
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

A fürt elsődleges csomópontjának típusa már frissítve lett. Ellenőrizze, hogy a telepített alkalmazások megfelelően működnek-e, és hogy a fürt állapota rendben van-e.

## <a name="next-steps"></a>További lépések
* Megtudhatja, hogyan [adhat hozzá csomópont-típust fürthöz](virtual-machine-scale-set-scale-node-type-scale-out.md)
* Az [alkalmazások méretezhetőségének](service-fabric-concepts-scalability.md)megismerése.
* [Azure-fürt méretezése vagy](service-fabric-tutorial-scale-cluster.md)kibontása.
* Az [Azure-fürtöket programozott módon méretezheti](service-fabric-cluster-programmatic-scaling.md) a Fluent Azure számítási SDK használatával.
* [Önálló fürt méretezése vagy](service-fabric-cluster-windows-server-add-remove-nodes.md)kibontása.

