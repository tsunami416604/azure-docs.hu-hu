---
title: "Virtuális gép az Azure-veremben biztonságosan tárolt tanúsítvány telepítése |} Microsoft Docs"
description: "Útmutató: virtuális gép telepítése, és küldje le a tanúsítványt az Azure verem kulcstároló használatával"
services: azure-stack
documentationcenter: 
author: mattbriggs
manager: femila
editor: 
ms.assetid: 46590eb1-1746-4ecf-a9e5-41609fde8e89
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 08/03/2017
ms.author: mabrigg
ms.openlocfilehash: e319f5c6d27d3a223764b0a5593480f02864ddbe
ms.sourcegitcommit: a5f16c1e2e0573204581c072cf7d237745ff98dc
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/11/2017
---
# <a name="create-a-virtual-machine-and-include-certificate-retrieved-from-a-key-vault"></a>Virtuális gép létrehozása és kulcstároló lekért tanúsítványt

Ez a cikk segít a virtuális gép létrehozása Azure verem és a leküldéses tanúsítványok rá. 

## <a name="prerequisites"></a>Előfeltételek

* Az ajánlat, amely tartalmazza a Key Vault szolgáltatás elő kell fizetnie. 
* [Telepítse a PowerShell Azure verem.](azure-stack-powershell-install.md)  
* [Az Azure-verem felhasználói PowerShell környezet konfigurálása](azure-stack-powershell-configure-user.md)

Azure-készletben kulcstároló tanúsítványok tárolására szolgál. Tanúsítványok számos különböző forgatókönyvben hasznosak. Vegyük példaként a helyzetet, amelyben az Azure-készletben, hogy a tanúsítványt igénylő alkalmazás fut vannak egy virtuális gépet. Ez a tanúsítvány használható titkosítására, az Active Directory vagy az SSL-hez egy webhelyen. A tanúsítvány egy kulcstartót segítségével győződjön meg arról, hogy az informatikai, akkor biztonságos.

Ez a cikk azt végigvezetik Önt leküldéses tanúsítvány települ a Windows virtuális gépre az Azure-verem szükséges lépéseket. Ezeket a lépéseket az Azure verem szoftverfejlesztői készlet, vagy a Windows-alapú külső ügyfél is használhatja, ha a VPN-en keresztül kapcsolódik.

Az alábbi lépéseket a szükséges leküldéses tanúsítvány települ a virtuális gépre folyamatát írják le:

1. Titkos kulcs tároló létrehozása.
2. Frissítse a azuredeploy.parameters.json fájlt.
3. A sablon üzembe helyezése

## <a name="create-a-key-vault-secret"></a>Titkos kulcs tároló létrehozása

A következő parancsfájl létrehoz egy tanúsítványt a .pfx formátumú, hoz létre egy kulcstartót és tárolja a tanúsítványt a key vault egy titkos kulcsként. Kell használnia a `-EnabledForDeployment` paraméter a kulcstartó létrehozásakor. Ez a paraméter gondoskodik arról, hogy a key vault Azure Resource Manager-sablonok alapján lehet hivatkozni.

```powershell

# Create a certificate in the .pfx format
New-SelfSignedCertificate `
  -certstorelocation cert:\LocalMachine\My `
  -dnsname contoso.microsoft.com

$pwd = ConvertTo-SecureString `
  -String "<Password used to export the certificate>" `
  -Force `
  -AsPlainText

Export-PfxCertificate `
  -cert "cert:\localMachine\my\<Certificate Thumbprint that was created in the previous step>" `
  -FilePath "<Fully qualified path where the exported certificate can be stored>" `
  -Password $pwd

# Create a key vault and upload the certificate into the key vault as a secret
$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "servicecert"
$fileName = "<Fully qualified path where the exported certificate can be stored>"
$certPassword = "<Password used to export the certificate>"

$fileContentBytes = get-content $fileName `
  -Encoding Byte

$fileContentEncoded = [System.Convert]::ToBase64String($fileContentBytes)
$jsonObject = @"
{
"data": "$filecontentencoded",
"dataType" :"pfx",
"password": "$certPassword"
}
"@
$jsonObjectBytes = [System.Text.Encoding]::UTF8.GetBytes($jsonObject)
$jsonEncoded = [System.Convert]::ToBase64String($jsonObjectBytes)

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location `
  -sku standard `
  -EnabledForDeployment

$secret = ConvertTo-SecureString `
  -String $jsonEncoded `
  -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
   -SecretValue $secret

```

Az előző parancsfájl futtatásakor a kimenet tartalmazza a titkos URI. Jegyezze fel ezt az URI. Hivatkozni rá az, hogy a [Windows Resource Manager-sablon leküldéses tanúsítvány](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate). Töltse le a [vm leküldéses-tanúsítvány-windows-sablon](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/201-vm-windows-pushcertificate) a fejlesztési számítógépen mappát. Ez a mappa tartalmaz a `azuredeploy.json` és `azuredeploy.parameters.json` fájlokat, amelyek a következő lépésben szüksége lesz.

Módosítsa a `azuredeploy.parameters.json` fájlt a környezeti értékek alapján. A érdeklik paraméterei a tároló neve, a tároló-csoport és a titkos kulcs URI (például az előző parancsfájl által létrehozott). A következő fájl egy paraméterfájl példája:

## <a name="update-the-azuredeployparametersjson-file"></a>A azuredeploy.parameters.json fájl frissítése

Frissítse a azuredeploy.parameters.json fájlt a vaultName, titkos URI, VmName és egyéb értékek szerint a környezetben. A következő JSON-fájlt a sablonfájl paraméterek példáját mutatja be: 

```json
{
  "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
  "contentVersion": "1.0.0.0",
  "parameters": {
    "newStorageAccountName": {
      "value": "kvstorage01"
    },
    "vmName": {
      "value": "VM1"
    },
    "vmSize": {
      "value": "Standard_D1_v2"
    },
    "adminUserName": {
      "value": "demouser"
    },
    "adminPassword": {
      "value": "demouser@123"
    },
    "vaultName": {
      "value": "contosovault"
    },
    "vaultResourceGroup": {
      "value": "contosovaultrg"
    },
    "secretUrlWithVersion": {
      "value": "https://testkv001.vault.local.azurestack.external/secrets/testcert002/82afeeb84f4442329ce06593502e7840"
    }
  }
}
```

## <a name="deploy-the-template"></a>A sablon üzembe helyezése

Most már a sablon telepítéséhez a következő PowerShell-parancsfájl használatával:

```powershell
# Deploy a Resource Manager template to create a VM and push the secret onto it
New-AzureRmResourceGroupDeployment `
  -Name KVDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Ha a sablon sikeresen telepítve lett, a következő kimenetet eredményezi:

![Központi telepítés kimeneti](media/azure-stack-kv-push-secret-into-vm/deployment-output.png)

Ha a virtuális gép van telepítve, a Azure verem leküldéses értesítések a tanúsítvány települ a virtuális gépre. A Windows rendszerben a LocalMachine tanúsítvány helyre, a tanúsítványtárból, amelyet a felhasználó által megadott hozzáadta a tanúsítványt. A Linux, a tanúsítványt a /var/lib/waagent könyvtárba, a fájl neve alá &lt;UppercaseThumbprint&gt;.crt a X509 a tanúsítványfájl és &lt;UppercaseThumbprint&gt;.prv a titkos kulcshoz .

## <a name="retire-certificates"></a>Tanúsítványok visszavonása

Az előző szakaszban leírt azt bemutatta, hogyan leküldéses egy új tanúsítvány települ a virtuális gépre. A régi tanúsítvány továbbra is a virtuális gépen, és nem lehet eltávolítani. Azonban bármikor letilthatja a régebbi verzióját a titkos kulcs használatával a `Set-AzureKeyVaultSecretAttribute` parancsmag. Egy példa használati parancsmag a következő: Győződjön meg arról, hogy a tároló neve, titkos neve és verziója értékek alapján a környezetben:

```powershell
Set-AzureKeyVaultSecretAttribute -VaultName contosovault -Name servicecert -Version e3391a126b65414f93f6f9806743a1f7 -Enable 0
```

## <a name="next-steps"></a>Következő lépések

* [Virtuális gép üzembe helyezése Key Vault-jelszóval](azure-stack-kv-deploy-vm-with-secret.md)
* [Hogy az alkalmazás kulcstároló eléréséhez](azure-stack-kv-sample-app.md)


