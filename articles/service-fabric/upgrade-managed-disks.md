---
title: Fürtcsomópontok frissítése az Azure által felügyelt lemezek használatához
description: Az alábbiakban bemutatja, hogyan frissítheti a meglévő Service Fabric-fürtöt az Azure által felügyelt lemezek használatához, a fürt kevés vagy semmilyen állásidő nélkül.
ms.topic: how-to
ms.date: 3/01/2020
ms.openlocfilehash: 2bda5572eda5579bb31c3613b220885f27220d99
ms.sourcegitcommit: 441db70765ff9042db87c60f4aa3c51df2afae2d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2020
ms.locfileid: "80758052"
---
# <a name="upgrade-cluster-nodes-to-use-azure-managed-disks"></a>Fürtcsomópontok frissítése az Azure által felügyelt lemezek használatához

[Az Azure által felügyelt lemezek](../virtual-machines/windows/managed-disks-overview.md) az ajánlott lemeztárolási ajánlat az Azure virtuális gépekkel való használatra az adatok állandó tárolásához. Javíthatja a Service Fabric-számítási feladatok rugalmasságát a virtuálisgép-méretezési csoportok frissítésével, amelyek a csomóponttípusok a felügyelt lemezek használatára szolgálnak. Az alábbiakban bemutatja, hogyan frissítheti a meglévő Service Fabric-fürtöt az Azure által felügyelt lemezek használatához, a fürt kevés vagy semmilyen állásidő nélkül.

A Service Fabric-fürtcsomópont felügyelt lemezek használatára történő frissítésének általános stratégiája a következő:

1. Telepítsen egy egyébként duplikált, adott csomóponttípusú virtuálisgép-méretezési készletet, de a [managedDisk](https://docs.microsoft.com/azure/templates/microsoft.compute/2019-07-01/virtualmachinescalesets/virtualmachines#ManagedDiskParameters) objektumot a virtuálisgép méretezési csoport központi telepítési sablonjának `osDisk` szakaszához adva. Az új méretezési csoportnak ugyanahhoz a terheléselosztóhoz / IP-címhez kell kötődnie, mint az eredetihez, hogy az ügyfelek ne tapasztalhassanak szolgáltatáskimaradást az áttelepítés során.

2. Miután az eredeti és a frissített méretezési csoportok egymás mellett futnak, tiltsa le az eredeti csomópontpéldányokat egyenként, hogy a rendszerszolgáltatások (vagy az állapotalapú szolgáltatások replikái) áttérjenek az új méretezési csoportra.

3. Ellenőrizze, hogy a fürt és az új csomópontok kifogástalanállapotúak-e, majd távolítsa el a törölt csomópontok eredeti méretezési csoportját és csomópontállapotát.

Ez a cikk végigvezeti a példafürt elsődleges csomóponttípusának felügyelt lemezek használatára történő frissítésének lépésein, elkerülve a fürtállásokat (lásd alább). A példa tesztfürt kezdeti állapota egy csomóponttípusú [Ezüst tartósságból](service-fabric-cluster-capacity.md#the-durability-characteristics-of-the-cluster)áll, amelyet egyetlen méretezési csoport öt csomópontos támogat.

> [!CAUTION]
> Ezzel az eljárással csak akkor tapasztalhat kimaradást, ha függőségevan a fürt DNS-étől (például a [Service Fabric Explorer](service-fabric-visualizing-your-cluster.md)elérésekor). Az [előtér-szolgáltatások architekturális ajánlott eljárása,](https://docs.microsoft.com/azure/architecture/microservices/design/gateway) hogy valamilyen [terheléselosztó](https://docs.microsoft.com/azure/architecture/guide/technology-choices/load-balancing-overview) van a csomóponttípusok előtt, hogy a csomópont-csere lehetővé tegye kimaradás nélkül.

Az alábbiakban az Azure Resource Manager [sablonjait és parancsmagjait](https://github.com/erikadoyle/service-fabric-scripts-and-templates/tree/managed-disks/templates/nodetype-upgrade-no-outage) olvashatja, amelyeket a frissítési forgatókönyv befejezéséhez fogunk használni. A sablon módosításait az alábbi [elsődleges csomóponttípus frissített méretezési csoportjának telepítése](#deploy-an-upgraded-scale-set-for-the-primary-node-type) ismerteti.

## <a name="set-up-the-test-cluster"></a>A tesztfürt beállítása

Állítsuk be a kezdeti Service Fabric tesztfürt. Először [töltse le](https://github.com/erikadoyle/service-fabric-scripts-and-templates/tree/managed-disks/templates/nodetype-upgrade-no-outage) az Azure resource manager mintasablonokat, amelyeket a forgatókönyv végrehajtásához használunk.

Ezután jelentkezzen be az Azure-fiókjába.

```powershell
# Sign in to your Azure account
Login-AzAccount -SubscriptionId "<subscription ID>"
```

A következő parancsok végigvezetik egy új önaláírt tanúsítvány létrehozásán és a tesztfürt üzembe helyezésén. Ha már rendelkezik használni kívánt tanúsítvánnyal, ugorjon [a Fürt telepítéséhez meglévő tanúsítvány használata lehetőségre.](#use-an-existing-certificate-to-deploy-the-cluster)

### <a name="generate-a-self-signed-certificate-and-deploy-the-cluster"></a>Önaláírt tanúsítvány létrehozása és a fürt telepítése

Először rendelje hozzá a Service Fabric-fürt központi telepítéséhez szükséges változókat. Állítsa be `resourceGroupName`a `certSubjectName` `parameterFilePath`, `templateFilePath` , , és az adott fiók és környezet értékét:

```powershell
# Assign deployment variables
$resourceGroupName = "sftestupgradegroup"
$certOutputFolder = "c:\certificates"
$certPassword = "Password!1" | ConvertTo-SecureString -AsPlainText -Force
$certSubjectName = "sftestupgrade.southcentralus.cloudapp.azure.com"
$templateFilePath = "C:\Initial-1NodeType-UnmanagedDisks.json"
$parameterFilePath = "C:\Initial-1NodeType-UnmanagedDisks.parameters.json"
```

> [!NOTE]
> Győződjön `certOutputFolder` meg arról, hogy a hely létezik a helyi számítógépen, mielőtt a parancsot egy új Service Fabric-fürt üzembe helyezéséhez.

Ezután nyissa meg a [*Initial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) fájlt, és állítsa be a PowerShellben beállított dinamikus értékek `clusterName` et, és `dnsName` mentse a módosításokat.

Ezután telepítse a Service Fabric tesztfürtjét:

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

A telepítés befejezése után keresse meg a`$certPfx` *.pfx* fájlt ( ) a helyi számítógépen, és importálja a tanúsítványtárolóba:

```powershell
cd c:\certificates
$certPfx = ".\sftestupgradegroup20200312121003.pfx"

Import-PfxCertificate `
     -FilePath $certPfx `
     -CertStoreLocation Cert:\CurrentUser\My `
     -Password (ConvertTo-SecureString Password!1 -AsPlainText -Force)
```

A művelet visszaadja a tanúsítvány ujjlenyomatát, amelyet [az új fürthöz való csatlakozáshoz és állapotának ellenőrzéséhez](#connect-to-the-new-cluster-and-check-health-status) fog használni. (Hagyja ki a következő szakaszt, amely a fürt telepítésének alternatív megközelítése.)

### <a name="use-an-existing-certificate-to-deploy-the-cluster"></a>Meglévő tanúsítvány használata a fürt telepítéséhez

A tesztfürt üzembe helyezéséhez egy meglévő Azure Key Vault-tanúsítványt is használhat. Ehhez be kell [szereznie a Key Vaultra és a](#obtain-your-key-vault-references) tanúsítvány ujjlenyomatára mutató hivatkozásokat.

```powershell
# Key Vault variables
$certUrlValue = "https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
$sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
$thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
```

Nyissa meg a [*Initial-1NodeType-UnmanagedDisks.parameters.json*](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Initial-1NodeType-UnmanagedDisks.parameters.json) fájlt, és módosítsa az `clusterName` értékeket, és `dnsName` valami egyedi.

Végül jelöljön ki egy erőforráscsoport nevet `templateFilePath` `parameterFilePath` a fürthöz, és állítsa be a *Initial-1NodeType-UnmanagedDisks* fájlok helyét és helyét:

> [!NOTE]
> A kijelölt erőforráscsoportnak már léteznie kell, és ugyanabban a régióban kell lennie, mint a Key Vault.

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$resourceGroupName = "sftestupgradegroup"
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"
```

Végül futtassa a következő parancsot a kezdeti tesztfürt telepítéséhez:

```powershell
New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

### <a name="connect-to-the-new-cluster-and-check-health-status"></a>Csatlakozás az új fürthöz és állapotellenőrzés

Csatlakozzon a fürthöz, és győződjön meg arról, `clusterName` `thumb` hogy mind az öt csomópontja kifogástalan (a fürt változóinak és változóinak cseréje):

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

Ezzel készen állunk a frissítési eljárás megkezdésére.

## <a name="deploy-an-upgraded-scale-set-for-the-primary-node-type"></a>Frissített méretezési csoport telepítése az elsődleges csomóponttípushoz

A csomóponttípus frissítéséhez vagy *vertikális méretezéséhez*telepíteni kell az adott csomóponttípus virtuálisgép-méretezési készletének egy példányát, amely egyébként megegyezik `nodeTypeRef` `subnet`az `loadBalancerBackendAddressPools`eredeti méretezési készlettel (beleértve az azonos , és ) való hivatkozást, kivéve, hogy tartalmazza a kívánt frissítést/módosításokat, valamint a saját külön alhálózatát és bejövő NAT-címkészletét. Mivel egy elsődleges csomóponttípust frissítünk, az új méretezési`isPrimary: true`csoport az eredeti méretezési csoporthoz hasonlóan elsődleges ( ) lesz megjelölve. (Nem elsődleges csomóponttípus-frissítések esetén egyszerűen hagyja ki ezt.)

Az egyszerűség kedvéért a *frissítés-1NodeType-2ScaleSets-ManagedDisks* [sablonban](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.json) és paraméterfájlokban már elvégezte a szükséges [módosításokat.](https://github.com/erikadoyle/service-fabric-scripts-and-templates/blob/managed-disks/templates/nodetype-upgrade-no-outage/Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json)

A következő szakaszok részletesen ismertetik a sablon változásait. Ha szeretné, kihagyhatja a magyarázatot, és [folytathatja a frissítési eljárás következő lépésével.](#obtain-your-key-vault-references)

### <a name="update-the-cluster-template-with-the-upgraded-scale-set"></a>A fürtsablon frissítése a frissített méretezési csoportgal

Az alábbiakban az eredeti fürttelepítési sablon szakaszonkénti módosításait olvashatja az elsődleges csomóponttípus frissített méretezési csoportjának hozzáadásához.

#### <a name="parameters"></a>Paraméterek

Adja hozzá a példány nevét, számát és az új méretezési csoport méretét. Vegye `vmNodeType1Name` figyelembe, hogy az új méretezési csoport egyedi, míg a darabszám és a méret értékek megegyeznek az eredeti méretezési csoport.

**Sablonfájl**

```json
"vmNodeType1Name": {
    "type": "string",
    "defaultValue": "NTvm2",
    "maxLength": 9
},
"nt1InstanceCount": {
    "type": "int",
    "defaultValue": 5,
    "metadata": {
        "description": "Instance count for node type"
    }
},
"vmNodeType1Size": {
    "type": "string",
    "defaultValue": "Standard_D2_v2"
},
```

**Paraméterek fájl**

```json
"vmNodeType1Name": {
    "value": "NTvm2"
},
"nt1InstanceCount": {
    "value": 5
},
"vmNodeType1Size": {
    "value": "Standard_D2_v2"
}
```

### <a name="variables"></a>Változók

A központi `variables` telepítési sablon szakaszban adjon hozzá egy bejegyzést az új méretezési csoport bejövő NAT-címkészletéhez.

**Sablonfájl**

```json
"lbNatPoolID1": "[concat(variables('lbID0'),'/inboundNatPools/LoadBalancerBEAddressNatPool1')]", 
```

### <a name="resources"></a>További források

A központi telepítési sablon *erőforrásai csoportban* adja hozzá az új virtuálisgép-méretezési készletet, szem előtt tartva az alábbi dolgokat:

* Az új méretezési csoport ugyanarra a csomóponttípusra hivatkozik, mint az eredeti:

    ```json
    "nodeTypeRef": "[parameters('vmNodeType0Name')]",
    ```

* Az új méretezési készlet ugyanarra a terheléselosztó-háttércímre és alhálózatra hivatkozik (de más bejövő nat-készletet használ):

   ```json
    "loadBalancerBackendAddressPools": [
        {
            "id": "[variables('lbPoolID0')]"
        }
    ],
    "loadBalancerInboundNatPools": [
        {
            "id": "[variables('lbNatPoolID1')]"
        }
    ],
    "subnet": {
        "id": "[variables('subnet0Ref')]"
    }
   ```

* Az eredeti méretezési csoporthoz hasonlóan az új méretezési csoport is elsődleges csomóponttípusként van megjelölve. (Nem elsődleges csomóponttípusok frissítésekor hagyja ki ezt a módosítást.)

    ```json
    "isPrimary": true,
    ```

* Az eredeti méretezési csoporttól eltérően az új méretezési csoport felügyelt lemezek használatára frissül.

    ```json
    "managedDisk": {
        "storageAccountType": "[parameters('storageAccountType')]"
    }
    ```

Miután végrehajtotta a sablon- és paraméterfájlok összes módosítását, folytassa a következő szakaszban a Key Vault-hivatkozások beszerzéséhez és a frissítések fürtbe való telepítéséhez.

### <a name="obtain-your-key-vault-references"></a>A Key Vault-hivatkozások beszerzése

A frissített konfiguráció üzembe helyezéséhez először a Key Vaultban tárolt fürttanúsítványra mutató hivatkozásokat kell beszereznie. Ezek az értékek megkeresésének legegyszerűbb módja az Azure Portalon keresztül. A következők szükségesek:

* **A fürttanúsítvány Key Vault-URL-címe.** Az Azure Portalon lévő Key Vaultban válassza **a Tanúsítványok** > *a kívánt tanúsítványtitkos* > **azonosítót:**

    ```powershell
    $certUrlValue="https://sftestupgradegroup.vault.azure.net/secrets/sftestupgradegroup20200309235308/dac0e7b7f9d4414984ccaa72bfb2ea39"
    ```

* **A fürttanúsítvány ujjlenyomata.** (Valószínűleg már rendelkezik ezzel, ha [a kezdeti fürthöz csatlakozik az](#connect-to-the-new-cluster-and-check-health-status) állapotának ellenőrzéséhez.) Ugyanebből a tanúsítványpanelről **(Tanúsítványok** > *a kívánt tanúsítványból)* az Azure Portalon másolja az **X.509 SHA-1 ujjlenyomatot (hexaxában):**

    ```powershell
    $thumb = "BB796AA33BD9767E7DA27FE5182CF8FDEE714A70"
    ```

* **A key vault erőforrás-azonosítója.** Az Azure Portalon lévő Key Vaultban válassza **a Properties** > **Resource ID**lehetőséget:

    ```powershell
    $sourceVaultValue = "/subscriptions/########-####-####-####-############/resourceGroups/sftestupgradegroup/providers/Microsoft.KeyVault/vaults/sftestupgradegroup"
    ```

### <a name="deploy-the-updated-template"></a>A frissített sablon telepítése

Szükség `parameterFilePath` szerint `templateFilePath` módosítsa a és a következő parancsot:

```powershell
# Deploy the new scale set (upgraded to use managed disks) into the primary node type.
$templateFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.json"
$parameterFilePath = "C:\Upgrade-1NodeType-2ScaleSets-ManagedDisks.parameters.json"

New-AzResourceGroupDeployment `
    -ResourceGroupName $resourceGroupName `
    -TemplateFile $templateFilePath `
    -TemplateParameterFile $parameterFilePath `
    -CertificateThumbprint $thumb `
    -CertificateUrlValue $certUrlValue `
    -SourceVaultValue $sourceVaultValue `
    -Verbose
```

Amikor a központi telepítés befejeződik, ellenőrizze újra a fürt állapotát, és győződjön meg arról, hogy mind a tíz csomópont (öt az eredeti és öt az új méretezési csoportban) kifogástalan állapotban van.When the deployment completes, check the cluster health again and ensure all ten nodes (five on the original and five on the new scale set) are healthy.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="migrate-seed-nodes-to-the-new-scale-set"></a>Magcsomópontok áttelepítése az új méretezési készletbe

Most már készen állunk az eredeti méretezési halmaz csomópontjainak letiltására. Ezek a csomópontok letiltása, a rendszerszolgáltatások és a magcsomópontok áttelepülnek az új méretezési csoport virtuális gépeire, mert elsődleges csomóponttípusként is meg van jelölve.

```powershell
# Disable the nodes in the original scale set.
$nodeNames = @("_NTvm1_0","_NTvm1_1","_NTvm1_2","_NTvm1_3","_NTvm1_4")

Write-Host "Disabling nodes..."
foreach($name in $nodeNames){
    Disable-ServiceFabricNode -NodeName $name -Intent RemoveNode -Force
}
```

A Service Fabric Explorer segítségével figyelheti a magcsomópontok áttelepítését az új méretezési készletre és az eredeti méretezési skálán lévő csomópontok előrehaladását *a Letiltás letiltása* *állapotra.*

![A Szolgáltatásháló-kezelő a letiltott csomópontok állapotát mutatja](./media/upgrade-managed-disks/service-fabric-explorer-node-status.png)

> [!NOTE]
> Eltarthat egy ideig, amíg a letiltási művelet az eredeti méretezési készlet összes csomópontjára. Az adatok konzisztenciájának biztosítása érdekében egyszerre csak egy magcsomópont módosítható. Minden egyes magcsomópont-módosításhoz fürtfrissítésszükséges; így egy magcsomópont cseréje két fürtfrissítést igényel (egy-egy csomópont hozzáadása és eltávolítása). Ebben a mintaforgatókönyvben az öt magcsomópontok frissítése tíz fürtfrissítést eredményez.

## <a name="remove-the-original-scale-set"></a>Az eredeti méretezési készlet eltávolítása

A letiltási művelet befejezése után távolítsa el a méretezési készletet.

```powershell
# Remove the original scale set
$scaleSetName = "NTvm1"

Remove-AzVmss `
    -ResourceGroupName $resourceGroupName `
    -VMScaleSetName $scaleSetName `
    -Force

Write-Host "Removed scale set $scaleSetName"
```

A Service Fabric Explorer, az eltávolított csomópontok (és így a *fürt állapota)* most jelenik meg *hiba* állapotban.

![A Service Fabric Explorer hibás állapotban lévő letiltott csomópontokat jelenít meg](./media/upgrade-managed-disks/service-fabric-explorer-disabled-nodes-error-state.png)

Távolítsa el az elavult csomópontokat a Service Fabric-fürtből a fürt állapotának *OK*állapotának visszaállításához.

```powershell
# Remove node states for the deleted scale set
foreach($name in $nodeNames){
    Remove-ServiceFabricNodeState -NodeName $name -TimeoutSec 300 -Force
    Write-Host "Removed node state for node $name"
}
```

![Service Fabric Explorer hibás állapotban lévő lefelé jelző csomókkal](./media/upgrade-managed-disks/service-fabric-explorer-healthy-cluster.png)

## <a name="next-steps"></a>További lépések

Ebben a forgatókönyvben megtanulta, hogyan frissítheti a Service Fabric-fürt virtuálisgép-méretezési készleteit felügyelt lemezek használatára, miközben elkerüli a szolgáltatás kimaradásokat a folyamat során. A kapcsolódó témakörökről az alábbi forrásokban olvashat bővebben.

Az alábbiak végrehajtásának módját ismerheti meg:

* [Service Fabric-fürt elsődleges csomóponttípusának vertikális felskálázása](service-fabric-scale-up-node-type.md)

* [Méretezési csoport sablonjának konvertálása felügyelt lemezek használatára](../virtual-machine-scale-sets/virtual-machine-scale-sets-convert-template-to-md.md)

* [Service Fabric-csomóponttípus eltávolítása](service-fabric-how-to-remove-node-type.md)

Lásd még:

* [Minta: Fürtcsomópontok frissítése az Azure által kezelt lemezek használatához](https://github.com/erikadoyle/service-fabric-scripts-and-templates/tree/managed-disks/templates/nodetype-upgrade-no-outage)

* [Vertikális skálázási szempontok](service-fabric-best-practices-capacity-scaling.md#vertical-scaling-considerations)