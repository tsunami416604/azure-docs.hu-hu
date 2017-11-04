---
title: "A Windows Service Fabric-fürt létrehozása az Azure-ban |} Microsoft Docs"
description: "Megtudhatja, hogyan telepítheti a Windows Service Fabric-fürtöt létrehozni egy meglévő Azure virtuális hálózatban, PowerShell használatával."
services: service-fabric
documentationcenter: .net
author: rwike77
manager: timlt
editor: 
ms.assetid: 
ms.service: service-fabric
ms.devlang: dotNet
ms.topic: tutorial
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 11/02/2017
ms.author: ryanwi
ms.openlocfilehash: 31e35432ecc10b06c7a6400a1e0904e7bc2cd8c9
ms.sourcegitcommit: 3df3fcec9ac9e56a3f5282f6c65e5a9bc1b5ba22
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 11/04/2017
---
# <a name="deploy-a-service-fabric-windows-cluster-into-an-azure-virtual-network"></a>Service Fabric Windows Azure virtuális hálózat a fürt központi telepítése
Ez az oktatóanyag egy sorozat része. Megtudhatja, hogyan telepítheti a Windows Service Fabric-fürt be egy meglévő Azure virtuális hálózatot (VNET), és részterv net a PowerShell használatával. Amikor végzett, hogy a fürt fut a felhőben, amely központilag telepíthető alkalmazások.  Azure parancssori felület használatával Linux-fürt létrehozásához lásd: [biztonságos Linux-fürt létrehozása az Azure](service-fabric-tutorial-create-vnet-and-linux-cluster.md).

Eben az oktatóanyagban az alábbiakkal fog megismerkedni:

> [!div class="checklist"]
> * VNET létrehozása az Azure PowerShell használatával
> * Hozzon létre egy kulcstartót és a tanúsítvány feltöltése
> * A biztonságos Service Fabric-fürt létrehozása az Azure PowerShell
> * A fürt egy X.509 tanúsítvánnyal biztonságos
> * Csatlakozás a fürthöz PowerShell használatával
> * A fürt eltávolítása

Az oktatóanyag adatsorozat elsajátíthatja, hogyan:
> [!div class="checklist"]
> * Biztonságos fürt létrehozása az Azure-on
> * [A Service Fabric az API Management központi telepítését](service-fabric-tutorial-deploy-api-management.md)

## <a name="prerequisites"></a>Előfeltételek
Ez az oktatóanyag elkezdéséhez:
- Ha nem rendelkezik Azure-előfizetéssel, hozzon létre egy [ingyenes fiókot](https://azure.microsoft.com/free/?WT.mc_id=A261C142F)
- Telepítse a [Service Fabric SDK és a PowerShell modul](service-fabric-get-started.md)
- Telepítse a [Azure Powershell 4.1-es vagy újabb verziója](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)

Az alábbi eljárások öt csomópontból Service Fabric-fürtök létrehozása. Használja az Azure Service Fabric-fürt futtatásával felmerülő költség kiszámításához a [Azure Díjkalkulátor](https://azure.microsoft.com/pricing/calculator/).

## <a name="sign-in-to-azure-and-select-your-subscription"></a>Bejelentkezés az Azure-ba, és jelölje ki az előfizetését
Ez az útmutató az Azure PowerShell használja. Amikor egy új PowerShell-munkamenetet indít el, jelentkezzen be az Azure-fiókjával, és jelölje ki az előfizetését, Azure parancsok végrehajtása előtt.
 
Futtassa az alábbi parancsfájlt az Azure-fiókjával bejelentkezhet jelölje ki az előfizetését:

```powershell
Login-AzureRmAccount
Get-AzureRmSubscription
Set-AzureRmContext -SubscriptionId <guid>
```

## <a name="create-a-resource-group"></a>Hozzon létre egy erőforráscsoportot
Hozzon létre egy új erőforráscsoportot, a telepítéshez, és adjon neki egy nevet és egy helyet.

```powershell
$groupname = "sfclustertutorialgroup"
$clusterloc="southcentralus"
New-AzureRmResourceGroup -Name $groupname -Location $clusterloc
```

## <a name="deploy-the-network-topology"></a>A hálózati topológia központi telepítéséhez
Következő lépésként állítsa be a hálózati topológia, amely az API Management és a Service Fabric-fürt telepítése. A [network.json] [ network-arm] Resource Manager-sablon létrehozására van beállítva a virtuális hálózathoz (VNET), és egy alhálózat és a hálózati biztonsági csoport (NSG) a Service Fabric és alhálózati és NSG-t az API Management . Ismerje meg a Vneteket, alhálózatok, és tájékozódhat az NSG-k [Itt](../virtual-network/virtual-networks-overview.md).

A [network.parameters.json] [ network-parameters-arm] paraméterfájl NSG-ket, hogy a Service Fabric és az API Management és az alhálózatok nevét tartalmazza.  Az API Management telepítve van a [oktatóanyag következő](service-fabric-tutorial-deploy-api-management.md). Ez az útmutató a paraméterértékek nem kell módosítani. A Service Fabric Resource Manager-sablonok használja ezeket az értékeket.  Ha itt módosítja az az értékeket, módosítania kell azokat a jelen oktatóanyagban használt többi Resource Manager sablon és a [központi telepítése az API Management oktatóanyag](service-fabric-tutorial-deploy-api-management.md). 

Töltse le a következő Resource Manager sablon és a Paraméterek:
- [Network.JSON][network-arm]
- [Network.Parameters.JSON][network-parameters-arm]

A következő PowerShell-parancs segítségével a hálózat beállítása a Resource Manager sablonnal és paraméterfájlokkal fájlok telepítése:

```powershell
New-AzureRmResourceGroupDeployment -ResourceGroupName $groupname -TemplateFile C:\winclustertutorial\network.json -TemplateParameterFile C:\winclustertutorial\network.parameters.json -Verbose
```

<a id="createvaultandcert" name="createvaultandcert_anchor"></a>
## <a name="deploy-the-service-fabric-cluster"></a>A Service Fabric-fürt központi telepítése
Ha a hálózati erőforrások végzett központi telepítése, a következő lépés a Service Fabric-fürt központi telepítése a virtuális hálózat, az alhálózat és a Service Fabric-fürt számára kijelölt NSG. A Resource Manager-sablon egy meglévő VNET és alhálózat (korábban ebben a cikkben telepített) egy fürt telepítése szükséges.  Az oktatóanyag adatsorozathoz a sablon nevét a virtuális Hálózatot, alhálózatot és az előző lépésben beállított NSG használandó előre konfigurálva.  

Töltse le a következő Resource Manager sablon és a Paraméterek:
- [cluster.JSON][cluster-arm]
- [cluster.Parameters.JSON][cluster-parameters-arm]

A sablon használatához a biztonságos fürtök létrehozásához.  A fürt tanúsítvány csomópontok kommunikáció biztosításához és a fürt felügyeleti végpontok egy felügyeleti ügyfél hitelesítésére használt X.509 tanúsítvány.  A fürt tanúsítvány is lehetővé teszi az SSL protokoll a HTTPS-szolgáltatásfelügyeleti API és a Service Fabric Explorer HTTPS-KAPCSOLATON keresztül. Az Azure Key Vault segítségével kezelheti az Azure Service Fabric-fürtök tanúsítványait.  A fürt telepítésekor az Azure-ban, a Service Fabric-fürtök létrehozásáért felelős az Azure erőforrás-szolgáltató kéri le a tanúsítványokat a Key Vault, és telepíti azokat a virtuális gépek fürtön. 

Egy hitelesítésszolgáltatótól (CA) származó tanúsítványt használjon a fürt-tanúsítványt, vagy a tesztelési célokra, hozzon létre egy önaláírt tanúsítványt. A fürt tanúsítványt kell:

- tartalmazza a titkos kulcsot.
- a kulcscseréhez használt, amely exportálható egy személyes információcsere (.pfx) fájl hozható létre.
- a tulajdonos nevét, amely megfelel a tartományt, amelyikhez a Service Fabric-fürt eléréséhez használt rendelkezik. A megfelelő szükség SSL a fürt HTTPS felügyeleti végpontokat és a Service Fabric Explorerben talál. Az SSL-tanúsítványt egy hitelesítésszolgáltatótól (CA) származó nem szerezze be a. cloudapp.azure.com tartomány. Be kell szereznie egy egyéni tartománynevet a fürt számára. A hitelesítésszolgáltató tanúsítvány kérése, amikor a tanúsítvány tulajdonosának nevét meg kell egyeznie az egyéni tartománynevet, amelyekkel a fürt számára.

Töltse ki a üres paraméterek a *cluster.parameters.json* fájl az adott környezethez:

|Paraméter|Érték|
|---|---|
|adminPassword|Jelszó #1234|
|adminUserName|vmadmin|
|Fürtnév|mysfcluster|
|location|southcentralus|

Hagyja a *certificateThumbprint*, *certificateUrlValue*, és *sourceVaultValue* paraméter üres, létrehozhat egy önaláírt tanúsítványt.  Ha egy meglévő kulcstároló korábban feltöltött tanúsítványt használni kívánt, töltse ki azokat a paraméterértékek.

Az alábbi parancsfájl használ a [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) parancsmag és a sablon telepítése egy új fürtöt az Azure-ban. A parancsmag is létrehoz egy új kulcstartó az Azure-ban, a key vault ad hozzá egy új önaláírt tanúsítványt, és letölti a fájlt helyileg. A többi paraméter használatával adhat meg egy meglévő tanúsítvány és/vagy a kulcstartót a [New-AzureRmServiceFabricCluster](/powershell/module/azurerm.servicefabric/New-AzureRmServiceFabricCluster) parancsmag.

```powershell
# Variables.
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
$certfolder="c:\mycertificates\"
$clustername = "mysfcluster"
$vaultname = "clusterkeyvault111"
$vaultgroupname="clusterkeyvaultgroup111"
$subname="$clustername.$clusterloc.cloudapp.azure.com"

# Create the Service Fabric cluster.
New-AzureRmServiceFabricCluster  -ResourceGroupName $groupname -TemplateFile 'C:\winclustertutorial\cluster.json' `
-ParameterFile 'C:\winclustertutorial\cluster.parameters.json' -CertificatePassword $certpwd `
-CertificateOutputFolder $certfolder -KeyVaultName $vaultname -KeyVaultResouceGroupName $vaultgroupname -CertificateSubjectName $subname
```

## <a name="connect-to-the-secure-cluster"></a>Csatlakozás a biztonságos fürthöz
Csatlakozzon a fürthöz, a Service Fabric PowerShell-modullal, telepített Service Fabric SDK-t.  Először telepítse a tanúsítványt az aktuális felhasználó a számítógépen (a) személyes tárolóba.  Futtassa a következő PowerShell-parancsot:

```powershell
$certpwd="q6D7nN%6ck@6" | ConvertTo-SecureString -AsPlainText -Force
Import-PfxCertificate -Exportable -CertStoreLocation Cert:\CurrentUser\My `
        -FilePath C:\mycertificates\mysfcluster20170531104310.pfx `
        -Password $certpwd
```

Most már készen áll a biztonságos fürthöz való csatlakozásra.

A **Service Fabric** PowerShell modul sok-parancsmagokat kínál a Service Fabric fürt, alkalmazások és szolgáltatások kezelése.  Használja a [Connect-ServiceFabricCluster](/powershell/module/servicefabric/connect-servicefabriccluster) parancsmag kapcsolódjon a biztonságos fürthöz. A tanúsítvány ujjlenyomata és kapcsolat végpont adatait az előző lépésben a kimeneti találhatók.

```powershell
Connect-ServiceFabricCluster -ConnectionEndpoint mysfcluster.southcentralus.cloudapp.azure.com:19000 `
          -KeepAliveIntervalInSec 10 `
          -X509Credential -ServerCertThumbprint C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -FindType FindByThumbprint -FindValue C4C1E541AD512B8065280292A8BA6079C3F26F10 `
          -StoreLocation CurrentUser -StoreName My
```

Ellenőrizze, hogy kapcsolódik-e, és a fürt állapota kifogástalan használatával a [Get-ServiceFabricClusterHealth](/powershell/module/servicefabric/get-servicefabricclusterhealth) parancsmag.

```powershell
Get-ServiceFabricClusterHealth
```

## <a name="clean-up-resources"></a>Az erőforrások eltávolítása
Az oktatóanyag adatsorozat további cikkeit az újonnan létrehozott fürt használja. Ha még nem azonnal áthelyezése a következő cikk be, előfordulhat, hogy törölni kívánja a fürt és a kulcstároló ezzel járó költségek elkerülése érdekében. A fürt és az összes általa használt erőforrás törlésének legegyszerűbb módja az erőforráscsoport törlése.

Törölje az erőforráscsoportot és használó fürt erőforrásait a [Remove-AzureRMResourceGroup parancsmag](/en-us/powershell/module/azurerm.resources/remove-azurermresourcegroup).  A key vault tartalmazó erőforráscsoportot is törli.

```powershell
Remove-AzureRmResourceGroup -Name $groupname -Force
Remove-AzureRmResourceGroup -Name $vaultgroupname -Force
```

## <a name="next-steps"></a>Következő lépések
Ez az oktatóanyag bemutatta, hogyan végezheti el az alábbi műveleteket:

> [!div class="checklist"]
> * VNET létrehozása az Azure PowerShell használatával
> * Hozzon létre egy kulcstartót és a tanúsítvány feltöltése
> * A biztonságos Service Fabric-fürt létrehozása az Azure PowerShell használatával
> * A fürt egy X.509 tanúsítvánnyal biztonságos
> * Csatlakozás a fürthöz PowerShell használatával
> * A fürt eltávolítása

A következő előzetes az alábbi oktatóanyag áttekintésével megismerheti, hogyan méretezni a fürtön.
> [!div class="nextstepaction"]
> [A fürt méretezése](service-fabric-tutorial-scale-cluster.md)


[network-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.json
[network-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/network.parameters.json

[cluster-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.json
[cluster-parameters-arm]:https://github.com/Azure-Samples/service-fabric-api-management/blob/master/cluster.parameters.json
