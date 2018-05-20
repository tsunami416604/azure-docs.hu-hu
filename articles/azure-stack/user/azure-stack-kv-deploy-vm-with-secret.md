---
title: Egy Azure veremben biztonságosan tárolt jelszóval rendelkező virtuális gép üzembe helyezése |} Microsoft Docs
description: Egy Azure verem Key Vault tárolt jelszót használó virtuális gépek telepítése
services: azure-stack
documentationcenter: ''
author: mattbriggs
manager: femila
editor: ''
ms.assetid: 23322a49-fb7e-4dc2-8d0e-43de8cd41f80
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: get-started-article
ms.date: 05/07/2018
ms.author: mabrigg
ms.openlocfilehash: 4239eb31afd4abc8b3555f0ee353f5d96716d623
ms.sourcegitcommit: c52123364e2ba086722bc860f2972642115316ef
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 05/11/2018
---
# <a name="create-a-virtual-machine-using-a-secure-password-stored-in-azure-stack-key-vault"></a>Hozzon létre egy virtuális gépet az Azure verem Key Vault tárolja biztonságos jelszó

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Ez a cikk lépéseket üzembe helyezhet egy Windows Server virtuális gép Azure verem Key Vault tárolt jelszó használatával. Az kulcstároló jelszó használata biztonságosabb, mint egy egyszerű szöveges jelszó átadásakor.

## <a name="overview"></a>Áttekintés (klasszikus)

Egy Azure verem-tárolóban egy titkos kulcsként értékeket, például a jelszó is tárolhatja. Titkos kulcs létrehozása után hivatkozhasson rá az Azure Resource Manager sablonokban. Titkos kulcsok használata a Resource Manager a következő előnyöket biztosítja:

* Ne kelljen manuálisan adja meg titkos kulcsot minden alkalommal, amikor telepít egy erőforrást.
* Megadhatja, hogy mely felhasználók vagy szolgáltatásnevekről férhetnek hozzá a titkos kulcsot.

## <a name="prerequisites"></a>Előfeltételek

* Az ajánlat, amely tartalmazza a Key Vault szolgáltatás elő kell fizetnie.
* [Telepítse a PowerShell Azure verem.](azure-stack-powershell-install.md)
* [Konfigurálja az Azure-verem felhasználói PowerShell környezetet.](azure-stack-powershell-configure-user.md)

A folyamat által a kulcstároló tárolt jelszó lekérése a virtuális gép létrehozásához szükséges a következő lépésekből áll:

1. Titkos kulcs tároló létrehozása.
2. Frissítse a azuredeploy.parameters.json fájlt.
3. A sablon telepítéséhez.

>[MEGJEGYZÉS] Ezeket a lépéseket az Azure verem szoftverfejlesztői készlet, vagy egy külső ügyfél is használhatja, ha a VPN-en keresztül kapcsolódik.

## <a name="create-a-key-vault-secret"></a>Titkos kulcs tároló létrehozása

Az alábbi parancsfájlt hoz létre a kulcstároló, és a kulcstároló, mint a titkos kulcs jelszót tárolja. Használja a `-EnabledForDeployment` paraméter a kulcstartó létrehozásakor. Ez a paraméter gondoskodik arról, hogy a key vault Azure Resource Manager-sablonok alapján lehet hivatkozni.

```powershell

$vaultName = "contosovault"
$resourceGroup = "contosovaultrg"
$location = "local"
$secretName = "MySecret"

New-AzureRmResourceGroup `
  -Name $resourceGroup `
  -Location $location

New-AzureRmKeyVault `
  -VaultName $vaultName `
  -ResourceGroupName $resourceGroup `
  -Location $location
  -EnabledForTemplateDeployment

$secretValue = ConvertTo-SecureString -String '<Password for your virtual machine>' -AsPlainText -Force

Set-AzureKeyVaultSecret `
  -VaultName $vaultName `
  -Name $secretName `
  -SecretValue $secretValue

```

Az előző parancsfájl futtatásakor a kimenet tartalmazza a titkos URI. Jegyezze fel ezt az URI. Hivatkozni rá az, hogy a [központi telepítése Windows rendszerű virtuális gép kulcstároló sablon jelszóval rendelkező](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv). Töltse le a [101-vm--jelszó](https://github.com/Azure/AzureStack-QuickStart-Templates/tree/master/101-vm-windows-create-passwordfromkv) a fejlesztési számítógépen mappát. Ez a mappa tartalmaz a `azuredeploy.json` és `azuredeploy.parameters.json` fájlokat, amelyek a következő lépésben szüksége lesz.

Módosítsa a `azuredeploy.parameters.json` fájlt a környezeti értékek alapján. A érdeklik paraméterei a tároló neve, a tároló-csoport és a titkos kulcs URI (például az előző parancsfájl által létrehozott). A következő fájl egy paraméterfájl példája:

## <a name="update-the-azuredeployparametersjson-file"></a>A azuredeploy.parameters.json fájl frissítése

Frissítse a azuredeploy.parameters.json fájlt a KeyVault URI, secretName, adminUsername a virtuális gép értékek szerint a környezetben. A következő JSON-fájlt a sablonfájl paraméterek példáját mutatja be:

```json
{
    "$schema":  "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion":  "1.0.0.0",
    "parameters":  {
       "adminUsername":  {
         "value":  "demouser"
          },
         "adminPassword":  {
           "reference":  {
              "keyVault":  {
                "id":  "/subscriptions/xxxxxx/resourceGroups/RgKvPwd/providers/Microsoft.KeyVault/vaults/KvPwd"
                },
              "secretName":  "MySecret"
           }
         },
       "dnsLabelPrefix":  {
          "value":  "mydns123456"
        },
        "windowsOSVersion":  {
          "value":  "2016-Datacenter"
        }
    }
}

```

## <a name="template-deployment"></a>Sablon telepítése

Most már a sablon telepítéséhez a következő PowerShell-parancsfájl használatával:

```powershell
New-AzureRmResourceGroupDeployment `
  -Name KVPwdDeployment `
  -ResourceGroupName $resourceGroup `
  -TemplateFile "<Fully qualified path to the azuredeploy.json file>" `
  -TemplateParameterFile "<Fully qualified path to the azuredeploy.parameters.json file>"
```

Ha a sablon sikeresen telepítve lett, a következő kimenetet eredményezi:

![Központi telepítés kimeneti](media/azure-stack-kv-deploy-vm-with-secret/deployment-output.png)

## <a name="next-steps"></a>További lépések

[A Key Vault mintaalkalmazás telepítése](azure-stack-kv-sample-app.md)

[A Key Vault tanúsítvánnyal egy virtuális gép üzembe helyezése](azure-stack-kv-push-secret-into-vm.md)
