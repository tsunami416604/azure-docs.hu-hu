---
title: Azure Service Fabric-fürt létrehozása
description: Ismerje meg, hogyan állíthat be egy biztonságos Service Fabric-fürtöt az Azure-ban az Azure Resource Manager használatával.  Fürtöt alapértelmezett sablon vagy saját fürtsablon használatával hozhat létre.
ms.topic: conceptual
ms.date: 08/16/2018
ms.openlocfilehash: 24bc80a7ab3ce61b79466cdd943c60efd367ce01
ms.sourcegitcommit: b55d7c87dc645d8e5eb1e8f05f5afa38d7574846
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/16/2020
ms.locfileid: "81458300"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Service Fabric-fürt létrehozása az Azure Resource Manager használatával 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

Az [Azure Service Fabric-fürt](service-fabric-deploy-anywhere.md) olyan virtuális gépek hálózathoz csatlakoztatott készlete, amelybe a mikroszolgáltatásokat üzembe helyeziésés kezeli.  Az Azure-ban futó Service Fabric-fürt egy Azure-erőforrás, és az Azure Resource Manager használatával van telepítve. Ez a cikk ismerteti, hogyan telepíthet egy biztonságos Service Fabric-fürt az Azure-ban az Erőforrás-kezelő használatával. Használhat alapértelmezett fürtsablont vagy egyéni sablont.  Ha még nem rendelkezik egyéni sablonnal, [megtudhatja, hogyan hozhat létre egyet.](service-fabric-cluster-creation-create-template.md)

A fürt védelmére kiválasztott biztonsági típust (azaz Windows-identitás, X509 stb.) meg kell adni a fürt kezdeti létrehozásához, és azt követően nem módosítható. Fürt beállítása előtt olvassa el a [Service Fabric-fürt biztonsági forgatókönyveit.][service-fabric-cluster-security] Az Azure-ban a Service Fabric x509-es tanúsítványt használ a fürt és a végpontok védelmére, az ügyfelek hitelesítésére és az adatok titkosítására. Az Azure Active Directory is ajánlott a felügyeleti végpontokhoz való biztonságos hozzáférés. További információért olvassa el [az Azure AD beállítása az ügyfelek hitelesítéséhez](service-fabric-cluster-creation-setup-aad.md)című olvassa el.

Ha éles erőforrások futtatásához éles fürtöt hoz létre, javasoljuk, hogy először olvassa el az [éles készenléti ellenőrzőlistát.](service-fabric-production-readiness-checklist.md)


[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

## <a name="prerequisites"></a>Előfeltételek 
Ebben a cikkben használja a Service Fabric RM powershell vagy az Azure CLI-modulok fürt üzembe helyezéséhez:

* [Azure PowerShell 4.1-es és újabb][azure-powershell]
* [Az Azure CLI 2.0-s és újabb verziója][azure-CLI]

A Service Fabric-modulok referenciadokumentációját itt találja:
* [Az.ServiceFabric](https://docs.microsoft.com/powershell/module/az.servicefabric)
* [az SF CLI modul](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

A cikkben szereplő parancsok futtatása előtt először jelentkezzen be az Azure-ba.

```powershell
Connect-AzAccount
Set-AzContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Új fürt létrehozása rendszer által létrehozott önaláírt tanúsítvánnyal

A következő parancsokkal hozzon létre egy fürtet, amely et önaláírt tanúsítvánnyal védett. Ez a parancs beállít egy elsődleges fürttanúsítványt, amely a fürt biztonsága és a rendszergazdai hozzáférés beállítása a felügyeleti műveletek végrehajtására a tanúsítvány használatával.  Az önaláírt tanúsítványok a tesztfürtök biztonságossá tétele érdekében hasznosak.  A termelési fürtöket egy hitelesítésszolgáltatótól (CA) származó tanúsítvánnyal kell biztosítani.

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>A modulban lévő alapértelmezett fürtsablon használata

A következő paranccsal gyorsan hozhat létre fürtöt minimális paraméterek megadásával az alapértelmezett sablon használatával.

A használt sablon elérhető az [Azure Service Fabric sablonminták : windows sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) és Ubuntu [sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

A következő parancs windowsos vagy Linux-fürtöket hozhat létre, ennek megfelelően meg kell adnia az operációs rendszert. A PowerShell/CLI parancsok a tanúsítványt a megadott *CertificateOutputFolder mappában*is kivezetik; ellenőrizze azonban, hogy a tanúsítványmappa már létrejött-e. A parancs más paramétereket is figyelembe vesz, például a virtuális gép termékváltozatát is.

> [!NOTE]
> A következő PowerShell-parancs csak az `Az` Azure PowerShell-modullal működik. Az Azure Resource Manager PowerShell-verzió aktuális verziójának ellenőrzéséhez futtassa a következő PowerShell-parancsot: "Get-Module Az". Ezen [a hivatkozáson](/powershell/azure/install-Az-ps) keresztül frissítheti az Azure Resource Manager PowerShell-verzióját. 
>
>

Telepítse a fürtöt a PowerShell használatával:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password123!@#" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword="Password4321!@#" | ConvertTo-SecureString -AsPlainText -Force
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"
$certOutputFolder="c:\certificates"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

Telepítse a fürtöt az Azure CLI használatával:

```azurecli
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare CertSubjectName="mylinux.westus.cloudapp.azure.com"
declare vmpassword="Password!1"
declare certpassword="Password!4321"
declare vmuser="myadmin"
declare vmOs="UbuntuServer1604"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certpassword  \
    --vault-name $vaultName --vault-resource-group $resourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath --vm-os $vmOs  \
    --vm-password $vmpassword --vm-user-name $vmuser
```

### <a name="use-your-own-custom-template"></a>Saját egyéni sablon használata

Ha az igényeinek megfelelően egyéni sablont kell készítette, erősen ajánlott az [Azure Service Fabric-sablonmintákban](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)elérhető sablonok egyikével kezdeni. További információ [a fürtsablon testreszabásáról.][customize-your-cluster-template]

Ha már rendelkezik egyéni sablonnal, ellenőrizze, hogy a sablonban és a paraméterfájlban szereplő mindhárom tanúsítványhoz kapcsolódó paraméter a következőképpen van-e elnevezve, és az értékek a következők:

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Telepítse a fürtöt a PowerShell használatával:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Telepítse a fürtöt az Azure CLI használatával:

```azurecli
declare certPassword=""
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare certSubjectName="mylinuxsecure.westus.cloudapp.azure.com"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"
declare certOutputFolder="c:\certificates"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-output-folder $certOutputFolder --certificate-password $certPassword  \
    --certificate-subject-name $certSubjectName \
    --template-file $templateFilePath --parameter-file $parametersFilePath
```

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Új fürt létrehozása saját X.509-es tanúsítvánnyal

A következő paranccsal hozzon létre fürtöt, ha rendelkezik olyan tanúsítvánnyal, amelyet a fürt védelmére szeretne használni.

Ha ez egy hitelesítésszolgáltató által aláírt tanúsítvány, amelyet más célokra is használni fog, akkor ajánlott külön erőforráscsoportot biztosítani kifejezetten a key vaultszámára. Azt javasoljuk, hogy a key vault saját erőforráscsoportba. Ez a művelet lehetővé teszi, hogy távolítsa el a számítási és tárolási erőforráscsoportok, beleértve a Service Fabric-fürtöt tartalmazó erőforráscsoport, a kulcsok és a titkos kulcsok elvesztése nélkül. **A key vaultot tartalmazó *erőforráscsoportnak ugyanabban a régióban kell lennie,* mint az azt használó fürtnek.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Használja az alapértelmezett öt csomópont, egy csomópont típusú sablont, amely a modulban van
A használt sablon elérhető az [Azure-mintákon : Windows-sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) és [Ubuntu sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

Telepítse a fürtöt a PowerShell használatával:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

Telepítse a fürtöt az Azure CLI használatával:

```azurecli
declare vmPassword="Password!1"
declare certPassword="Password!1"
declare vmUser="myadmin"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare certificate-file="c:\certificates\mycert.pem"
declare vmOs="UbuntuServer1604"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $certPassword  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --vm-os vmOs \
    --vm-password $vmPassword --vm-user-name $vmUser
```

### <a name="use-your-own-custom-cluster-template"></a>Saját egyéni fürtsablon használata
Ha az igényeinek megfelelően egyéni sablont kell készítette, erősen ajánlott az [Azure Service Fabric-sablonmintákban](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master)elérhető sablonok egyikével kezdeni. További információ [a fürtsablon testreszabásáról.][customize-your-cluster-template]

Ha már rendelkezik egyéni sablonnal, ellenőrizze, hogy a sablonban és a paraméterfájlban lévő mindhárom tanúsítványhoz kapcsolódó paraméter a következőképpen van-e elnevezve, és az értékek az alábbiak szerint nullértékűek.If you already have a custom template, then make sure to double check that all the all the the certificate related parameters in the template and the parameter file are named s, and values are null as follows.

```json
   "certificateThumbprint": {
      "value": ""
    },
    "sourceVaultValue": {
      "value": ""
    },
    "certificateUrlValue": {
      "value": ""
    },
```

Telepítse a fürtöt a PowerShell használatával:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

Telepítse a fürtöt az Azure CLI használatával:

```azurecli
declare certPassword="Password!1"
declare resourceGroupLocation="westus"
declare resourceGroupName="mylinux"
declare vaultResourceGroupName="myvaultrg"
declare vaultName="myvault"
declare parameterFilePath="c:\mytemplates\linuxtemplateparm.json"
declare templateFilePath="c:\mytemplates\linuxtemplate.json"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --certificate-file $certificate-file --certificate-password $password  \
    --vault-name $vaultName --vault-resource-group $vaultResourceGroupName  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>A key vaultba feltöltött titkos kulcsra mutató mutató használata

Meglévő kulcstartó használatához engedélyezni kell a key vaultot a [telepítéshez,](../key-vault/general/manage-with-cli2.md#bkmk_KVperCLI) hogy a számítási erőforrás-szolgáltató tanúsítványokat kapjon belőle, és telepítse fürtcsomópontokra.

Telepítse a fürtöt a PowerShell használatával:

```powershell
Set-AzKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretID -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

Telepítse a fürtöt az Azure CLI használatával:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier $secretID  \
    --template-file $templateFilePath --parameter-file $parameterFilePath 
```

## <a name="next-steps"></a>További lépések
Ezen a ponton egy biztonságos fürt az Azure-ban fut. Ezután [csatlakozzon a fürthöz,](service-fabric-connect-to-secure-cluster.md) és ismerje meg, hogyan [kezelheti az alkalmazástitok.](service-fabric-application-secret-management.md)

A JSON szintaxisát és tulajdonságait sablon használatára vonatkozóan olvassa el a [Microsoft.ServiceFabric/clusters sablon hivatkozása](/azure/templates/microsoft.servicefabric/clusters).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/install-Az-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
