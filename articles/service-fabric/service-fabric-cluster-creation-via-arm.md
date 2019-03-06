---
title: Azure Service Fabric-fürt létrehozása |} A Microsoft Docs
description: Ismerje meg, hogyan állítható be a biztonságos Service Fabric-fürtön az Azure-ban Azure Resource Manager használatával.  A fürt alapértelmezett sablon használata, vagy a saját fürt sablon használatával hozhat létre.
services: service-fabric
documentationcenter: .net
author: aljo-microsoft
manager: timlt
editor: chackdan
ms.assetid: 15d0ab67-fc66-4108-8038-3584eeebabaa
ms.service: service-fabric
ms.devlang: dotnet
ms.topic: conceptual
ms.tgt_pltfrm: NA
ms.workload: NA
ms.date: 08/16/2018
ms.author: aljo
ms.openlocfilehash: 715b0f9c5a5a68bb1fc42ed4206ca81ad85b844f
ms.sourcegitcommit: 7e772d8802f1bc9b5eb20860ae2df96d31908a32
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/06/2019
ms.locfileid: "57450685"
---
# <a name="create-a-service-fabric-cluster-using-azure-resource-manager"></a>Az Azure Resource Manager egy Service Fabric-fürt létrehozása 
> [!div class="op_single_selector"]
> * [Azure Resource Manager](service-fabric-cluster-creation-via-arm.md)
> * [Azure Portal](service-fabric-cluster-creation-via-portal.md)
>
>

Egy [Azure Service Fabric-fürt](service-fabric-deploy-anywhere.md) mikroszolgáltatásokat helyezhet üzembe és felügyelhet, amelybe a virtuális gépek hálózaton keresztül csatlakozó készlete.  Azure-ban futó Service Fabric-fürt egy Azure-erőforrás, és ezt telepítjük, az Azure Resource Manager használatával. Ez a cikk ismerteti, hogyan helyezhet üzembe egy biztonságos Service Fabric-fürtön az Azure-ban a Resource Manager használatával. Fürt alapértelmezett sablon vagy egy egyéni sablont is használhat.  Ha még nem rendelkezik egy egyéni sablont, akkor az [megtudhatja, hogyan hozhat létre egyet](service-fabric-cluster-creation-create-template.md).

Fürtbiztonság van beállítva, amikor a fürt első telepítést, és később nem módosítható. A fürt beállítása előtt olvassa el a [Service Fabric-fürtök biztonsági forgatókönyveit][service-fabric-cluster-security]. Az Azure-ban a Service Fabric használ x509 tanúsítványt a biztonságos a fürt és a végpontok, az ügyfelek hitelesítése, és adatok titkosításához. Az Azure Active Directory emellett javasoljuk, hogy a biztonságos hozzáférés a felügyeleti végpontok. Az Azure AD-bérlőt, és a felhasználók a fürt létrehozása előtt létre kell hozni.  További információkért olvassa el [állítsa be az Azure AD-ügyfelek hitelesítésére](service-fabric-cluster-creation-setup-aad.md).

Ha éles számítási feladatok futtatásához egy éles fürtöt hoz létre, javasoljuk, hogy első olvasási keresztül a [éles készültségi ellenőrzőlista](service-fabric-production-readiness-checklist.md).

## <a name="prerequisites"></a>Előfeltételek 
Ebben a cikkben a Service Fabric-RM powershell vagy az Azure CLI-modulok telepítéséhez használja egy fürt:

* [Az Azure PowerShell 4.1 és újabb verziók][azure-powershell]
* [Az Azure CLI 2.0-s vagy újabb verzió][azure-CLI]

A dokumentációja a Service Fabric-modulok itt találja:
* [AzureRM.ServiceFabric](https://docs.microsoft.com/powershell/module/azurerm.servicefabric)
* [az SF CLI modul](https://docs.microsoft.com/cli/azure/sf?view=azure-cli-latest)

### <a name="sign-in-to-azure"></a>Bejelentkezés az Azure-ba

Ebben a cikkben a parancsok egyikét futtatja, előtt először jelentkezzen be az Azure-bA.

```powershell
Connect-AzureRmAccount
Set-AzureRmContext -SubscriptionId <subscriptionId>
```

```azurecli
az login
az account set --subscription $subscriptionId
```

## <a name="create-a-new-cluster-using-a-system-generated-self-signed-certificate"></a>Hozzon létre egy új fürtöt, egy rendszer által létrehozott önaláírt tanúsítvány használatával

A következő parancsok használatával hozzon létre egy fürtöt, a rendszer által létrehozott önaláírt tanúsítvánnyal védett. Ez a parancs állít be egy elsődleges fürttanúsítvány fürtbiztonsági célra és a rendszergazdai hozzáférés beállításához használt tanúsítvánnyal, hogy felügyeleti műveleteket.  Önaláírt tanúsítványok a tesztfürtök biztonságos hasznosak.  Éles fürtök titkosítani kell egy tanúsítványt egy hitelesítésszolgáltatótól (CA).

### <a name="use-the-default-cluster-template-that-ships-in-the-module"></a>A modul részét képező alapértelmezett fürt sablon használatához

A következő parancs használatával adja meg a minimális paramétereket, az alapértelmezett sablon használatával gyorsan, hozzon létre egy fürtöt.

A használt sablon érhető el a [Azure Service Fabric-sablonminták: windows-sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) és [Ubuntu-sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

A következő paranccsal hozhat létre bármelyik Windows vagy Linux-fürtön, adja meg az operációs rendszer ennek megfelelően kell. A PowerShell és CLI-parancsok is a tanúsítványt a megadott kimeneti *CertificateOutputFolder*, azonban győződjön meg arról, hogy tanúsítványmappa már létrehozott. A parancs a többi paramétert, valamint a VM-Termékváltozatok például vesz igénybe.

> [!NOTE]
> A következő PowerShell-parancs csak működik az Azure Resource Manager PowerShell-lel verzió > 6.1-es verzióját. Az Azure Resource Manager PowerShell-verzió aktuális verziójának ellenőrzéséhez futtassa a következő PowerShell-parancsot: "Get-Module AzureRM". Hajtsa végre a [ezt a hivatkozást](/powershell/azure/azurerm/install-azurerm-ps?view=azurermps-6.3.0) frissítése az Azure Resource Manager PowerShell-verzió. 
>
>

A fürt PowerShell-lel üzembe helyezéséhez:

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

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -OS $os -VmPassword $vmpassword -VmUserName $vmuser
```

A fürt Azure CLI-vel üzembe helyezéséhez:

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

### <a name="use-your-own-custom-template"></a>A saját egyéni sablon használata

Ha hozzon létre egy egyéni sablont, hogy illeszkedjen az igényeihez van szüksége, azt javasoljuk, hogy az egyik elérhető sablont megkezdése a [Azure Service Fabric-sablonminták](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Ismerje meg, hogyan [fürtsablonhoz testreszabása][customize-your-cluster-template].

Ha már rendelkezik egy egyéni sablont, és ellenőrizze, hogy a három tanúsítvány kapcsolatos-e a paramétereket a sablon és a alkalmazásparaméter-fájlt a következő nevesített és értékek a következők null értékű:

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

A fürt PowerShell-lel üzembe helyezéséhez:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mycluster"
$CertSubjectName="mycluster.westus.cloudapp.azure.com"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$certOutputFolder="c:\certificates"

$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -CertificateOutputFolder $certOutputFolder -CertificatePassword $certpassword -CertificateSubjectName $CertSubjectName -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

A fürt Azure CLI-vel üzembe helyezéséhez:

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

## <a name="create-a-new-cluster-using-your-own-x509-certificate"></a>Hozzon létre egy új fürtöt a saját X.509-tanúsítvány

A következő paranccsal hozhat létre fürtöt, ha a fürt védelmére használni kívánt tanúsítványt.

Ha ez egy hitelesítésszolgáltató által aláírt tanúsítvány, amely, megszűnik más célra, majd javasoljuk, hogy kimondottan a key vault adjon meg egy egyedi erőforráscsoport. Azt javasoljuk, hogy a key vault mindössze a saját erőforráscsoport. Ez a művelet lehetővé teszi a számítási és tárolási erőforráscsoport eltávolítására, beleértve az erőforráscsoport, amely a Service Fabric-fürt tartalmazza a kulcsok és titkok elvesztése nélkül. **Az erőforráscsoport, amely tartalmazza a kulcstartó *ugyanabban a régióban kell lennie* a fürt által használt.**

### <a name="use-the-default-five-node-one-node-type-template-that-ships-in-the-module"></a>Az alapértelmezett öt csomópont, a modul részét képező egy csomópont típusa sablon használata
A használt sablon megtalálható a [Azure-minták: Windows-sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Windows-1-NodeTypes-Secure-NSG) és [Ubuntu-sablon](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master/5-VM-Ubuntu-1-NodeTypes-Secure)

A fürt PowerShell-lel üzembe helyezéséhez:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$vmpassword=("Password!4321" | ConvertTo-SecureString -AsPlainText -Force) 
$vmuser="myadmin"
$os="WindowsServer2016DatacenterwithContainers"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile C:\MyCertificates\chackocertificate3.pfx -CertificatePassword $certPassword -OS $os -VmPassword $vmpassword -VmUserName $vmuser 
```

A fürt Azure CLI-vel üzembe helyezéséhez:

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

### <a name="use-your-own-custom-cluster-template"></a>A saját egyéni fürterőforrás-sablonnal
Ha hozzon létre egy egyéni sablont, hogy illeszkedjen az igényeihez van szüksége, azt javasoljuk, hogy az egyik elérhető sablont megkezdése a [Azure Service Fabric-sablonminták](https://github.com/Azure-Samples/service-fabric-cluster-templates/tree/master). Ismerje meg, hogyan [fürtsablonhoz testreszabása][customize-your-cluster-template].

Ha már rendelkezik egy egyéni sablont, majd győződjön meg arról, hogy ellenőrizze, hogy a három tanúsítvány kapcsolatos paramétereket a sablon és a alkalmazásparaméter-fájlt a következő nevesített és értékek a következők null értékű.

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

A fürt PowerShell-lel üzembe helyezéséhez:

```powershell
$resourceGroupLocation="westus"
$resourceGroupName="mylinux"
$vaultName="myvault"
$vaultResourceGroupName="myvaultrg"
$certPassword="Password!1" | ConvertTo-SecureString -AsPlainText -Force 
$os="WindowsServer2016DatacenterwithContainers"
$parameterFilePath="c:\mytemplates\mytemplateparm.json"
$templateFilePath="c:\mytemplates\mytemplate.json"
$certificateFile="C:\MyCertificates\chackonewcertificate3.pem"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -Location $resourceGroupLocation -TemplateFile $templateFilePath -ParameterFile $parameterFilePath -KeyVaultResourceGroupName $vaultResourceGroupName -KeyVaultName $vaultName -CertificateFile $certificateFile -CertificatePassword $certPassword
```

A fürt Azure CLI-vel üzembe helyezéséhez:

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

### <a name="use-a-pointer-to-a-secret-uploaded-into-a-key-vault"></a>Titkos kulcs mutató feltölti a key vault használata

Egy meglévő key vault használata esetén a key vaultban kell [engedélyezve van a központi telepítés](../key-vault/key-vault-manage-with-cli2.md#bkmk_KVperCLI) , hogy a számítási erőforrás-szolgáltató tanúsítványok lekérése, és telepítse a fürtcsomópontok számára.

A fürt PowerShell-lel üzembe helyezéséhez:

```powershell
Set-AzureRmKeyVaultAccessPolicy -VaultName 'ContosoKeyVault' -EnabledForDeployment

$parameterFilePath="c:\mytemplates\mytemplate.json"
$templateFilePath="c:\mytemplates\mytemplateparm.json"
$secretID="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

New-AzureRmServiceFabricCluster -ResourceGroupName $resourceGroupName -SecretIdentifier $secretId -TemplateFile $templateFilePath -ParameterFile $parameterFilePath 
```

A fürt Azure CLI-vel üzembe helyezéséhez:

```azurecli
declare $resourceGroupName = "testRG"
declare $parameterFilePath="c:\mytemplates\mytemplate.json"
declare $templateFilePath="c:\mytemplates\mytemplateparm.json"
declare $secertId="https://test1.vault.azure.net:443/secrets/testcertificate4/55ec7c4dc61a462bbc645ffc9b4b225f"

az sf cluster create --resource-group $resourceGroupName --location $resourceGroupLocation  \
    --secret-identifier az $secretID  \
    --template-file $templateFilePath --parameter-file $parametersFilePath 
```

## <a name="next-steps"></a>További lépések
Ezen a ponton rendelkezik egy Azure-ban futó biztonságos fürt. Ezután [csatlakozni a fürthöz](service-fabric-connect-to-secure-cluster.md) , és ismerje meg, hogyan [titkos alkalmazáskulcsok kezelése](service-fabric-application-secret-management.md).

A JSON-szintaxist és egy sablon tulajdonságait: [Microsoft.ServiceFabric/clusters sablonreferenciája](/azure/templates/microsoft.servicefabric/clusters).

<!-- Links -->
[azure-powershell]:https://docs.microsoft.com/powershell/azure/azurerm/install-azurerm-ps
[azure-CLI]:https://docs.microsoft.com/cli/azure/get-started-with-azure-cli?view=azure-cli-latest
[service-fabric-cluster-security]: service-fabric-cluster-security.md
[customize-your-cluster-template]: service-fabric-cluster-creation-create-template.md
