---
title: Key Vault titkos Resource Manager-sablonnal |} Microsoft Docs
description: "Bemutatja, hogyan adhatók át a titkos kulcs kulcstároló paraméterként üzembe helyezése során."
services: azure-resource-manager,key-vault
documentationcenter: na
author: tfitzmac
manager: timlt
editor: tysonn
ms.assetid: c582c144-4760-49d3-b793-a3e1e89128e2
ms.service: azure-resource-manager
ms.devlang: na
ms.topic: article
ms.tgt_pltfrm: na
ms.workload: na
ms.date: 07/25/2017
ms.author: tomfitz
ms.openlocfilehash: 1ca72599e67e79d42a3d430dbb13e89ea7265334
ms.sourcegitcommit: 02e69c4a9d17645633357fe3d46677c2ff22c85a
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/03/2017
---
# <a name="use-key-vault-to-pass-secure-parameter-value-during-deployment"></a>Továbbítsa biztonságos paraméter értékét a telepítés során a Key Vault használatával

Adjon át egy biztonságos értéket (például jelszót) paraméterként központi telepítése során kell, ha az érték le egy [Azure Key Vault](../key-vault/key-vault-whatis.md). A kulcstartó és a paraméter-fájlban a titkos kulcs Vezérlőpultjának kikeresheti az értéket. Az érték sosem hagyja, mert csak hivatkoznak a kulcstartót azonosítóját. Nem kell manuálisan adja meg az értékét a titkos kulcsot minden alkalommal, amikor az erőforrások telepítése. A key vault létezhet, mint az erőforráscsoportot, hogy telepít egy másik előfizetésben. A key vault hivatkozik, akkor adja meg az előfizetés-azonosító.

A kulcstartó létrehozásakor állítsa be a *enabledForTemplateDeployment* tulajdonságot *igaz*. Ez az érték true értékre állításával lehetővé hozzáférési Resource Manager-sablonok alapján a telepítés során.  

## <a name="deploy-a-key-vault-and-secret"></a>Telepítsen egy kulcstartót és a titkos kulcs

A kulcstartó és a titkos kulcs létrehozásához használja az Azure parancssori felület vagy a PowerShell. Figyelje meg, hogy a key vault sablon-üzembehelyezés engedélyezve van. 

Azure CLI esetén használja az alábbi parancsot:

```azurecli
vaultname={your-unique-vault-name}
password={password-value}

az group create --name examplegroup --location 'South Central US'
az keyvault create --name $vaultname --resource-group examplegroup --location 'South Central US' --enabled-for-template-deployment true
az keyvault secret set --vault-name $vaultname --name examplesecret --value $password
```

PowerShell esetén használja az alábbi parancsot:

```powershell
$vaultname = "{your-unique-vault-name}"
$password = "{password-value}"

New-AzureRmResourceGroup -Name examplegroup -Location "South Central US"
New-AzureRmKeyVault -VaultName $vaultname -ResourceGroupName examplegroup -Location "South Central US" -EnabledForTemplateDeployment
$secretvalue = ConvertTo-SecureString $password -AsPlainText -Force
Set-AzureKeyVaultSecret -VaultName $vaultname -Name "examplesecret" -SecretValue $secretvalue
```

## <a name="enable-access-to-the-secret"></a>A titkos kulcs hozzáférésének engedélyezése

Függetlenül attól, hogy egy új kulcstartó vagy egy meglévő, győződjön meg arról, hogy a sablon telepítése a felhasználó hozzáférhet-e a titkos kulcsot. A felhasználónak, a titkos kulcs hivatkozó sablonok telepítésével kell rendelkeznie a `Microsoft.KeyVault/vaults/deploy/action` a key vault engedély. A [tulajdonos](../active-directory/role-based-access-built-in-roles.md#owner) és [közreműködő](../active-directory/role-based-access-built-in-roles.md#contributor) szerepkörök is engedélyezik a hozzáférést. Létrehozhat egy [egyéni szerepkör](../active-directory/role-based-access-control-custom-roles.md) , amely engedélyt ad a, és adja hozzá a felhasználót a szerepkörhöz. A felhasználó egy szerepkörhöz történő hozzáadásával kapcsolatos további információkért lásd: [felhasználó hozzárendelése az Azure Active Directory rendszergazdai szerepkörök](../active-directory/active-directory-users-assign-role-azure-portal.md).


## <a name="reference-a-secret-with-static-id"></a>Hivatkozás statikus azonosítójú titkos kulcs

A sablont, amely megkapja a kulcstartót titkos kulcs olyan, mint a többi sablont. Mivel ez **a paraméter fájlban, nem a sablon a key vault hivatkozik.** Például a következő sablon telepít egy SQL-adatbázis, amely egy rendszergazdai jelszót tartalmaz. A password paraméter értéke egy biztonságos karakterláncot kell megadnia. De a sablon nem adja meg, ha ezt az értéket származik.

```json
{
    "$schema": "http://schema.management.azure.com/schemas/2014-04-01-preview/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "administratorLogin": {
            "type": "string"
        },
        "administratorLoginPassword": {
            "type": "securestring"
        },
        "collation": {
            "type": "string"
        },
        "databaseName": {
            "type": "string"
        },
        "edition": {
            "type": "string"
        },
        "requestedServiceObjectiveId": {
            "type": "string"
        },
        "location": {
            "type": "string"
        },
        "maxSizeBytes": {
            "type": "string"
        },
        "serverName": {
            "type": "string"
        },
        "sampleName": {
            "type": "string",
            "defaultValue": ""
        }
    },
    "resources": [
        {
            "apiVersion": "2015-05-01-preview",
            "location": "[parameters('location')]",
            "name": "[parameters('serverName')]",
            "properties": {
                "administratorLogin": "[parameters('administratorLogin')]",
                "administratorLoginPassword": "[parameters('administratorLoginPassword')]",
                "version": "12.0"
            },
            "resources": [
                {
                    "apiVersion": "2014-04-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                    ],
                    "location": "[parameters('location')]",
                    "name": "[parameters('databaseName')]",
                    "properties": {
                        "collation": "[parameters('collation')]",
                        "edition": "[parameters('edition')]",
                        "maxSizeBytes": "[parameters('maxSizeBytes')]",
                        "requestedServiceObjectiveId": "[parameters('requestedServiceObjectiveId')]",
                        "sampleName": "[parameters('sampleName')]"
                    },
                    "type": "databases"
                },
                {
                    "apiVersion": "2014-04-01-preview",
                    "dependsOn": [
                        "[concat('Microsoft.Sql/servers/', parameters('serverName'))]"
                    ],
                    "location": "[parameters('location')]",
                    "name": "AllowAllWindowsAzureIps",
                    "properties": {
                        "endIpAddress": "0.0.0.0",
                        "startIpAddress": "0.0.0.0"
                    },
                    "type": "firewallrules"
                }
            ],
            "type": "Microsoft.Sql/servers"
        }
    ]
}
```

Most a fenti sablon paraméter fájl létrehozása. A paraméterfájl adja meg a sablon a paraméter neve megegyezik-e paraméter. A paraméterérték használatával hivatkozik a key vault a titkos kulcsot. A titkos kulcsot úgy, hogy a key vault erőforrás-azonosítója és a titkos kulcs neve hivatkozik. A következő példában a kulcstartót titkos kulcs már léteznie kell, és adjon meg statikus értéket az erőforrás-azonosítóhoz.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
        "administratorLogin": {
            "value": "exampleadmin"
        },
        "administratorLoginPassword": {
            "reference": {
              "keyVault": {
                "id": "/subscriptions/{guid}/resourceGroups/examplegroup/providers/Microsoft.KeyVault/vaults/{vault-name}"
              },
              "secretName": "examplesecret"
            }
        },
        "collation": {
            "value": "SQL_Latin1_General_CP1_CI_AS"
        },
        "databaseName": {
            "value": "exampledb"
        },
        "edition": {
            "value": "Standard"
        },
        "location": {
            "value": "southcentralus"
        },
        "maxSizeBytes": {
            "value": "268435456000"
        },
        "requestedServiceObjectiveId": {
            "value": "455330e1-00cd-488b-b5fa-177c226f28b7"
        },
        "sampleName": {
            "value": ""
        },
        "serverName": {
            "value": "exampleserver"
        }
    }
}
```

## <a name="reference-a-secret-with-dynamic-id"></a>Dinamikus azonosítójú titkos kulcs hivatkozik

Az előző szakaszban bemutatta, hogyan felelt meg a kulcstartót titkos kulcsot egy statikus erőforrás azonosítója. Azonban bizonyos esetekben kell kulcstároló titkos kulcs eltérő az aktuális telepítés alapján hivatkozik. Ebben az esetben nem lehet szigorú kódot az erőforrás-azonosítója a paraméterfájlban. Sajnos nem lehet dinamikusan létrehozhat az erőforrás-azonosítója a paraméterfájlban mert sablon kifejezések nem megengedettek a paraméterfájlban.

Lehet dinamikusan létrehozni a kulcstartó titkos kulcs az erőforrás-azonosító, át kell helyezni az erőforrás, amelyet a titkos kulcsot egy beágyazott sablonba. A fő sablonban a beágyazott sablon hozzáadása, és adjon át egy paraméter, amely tartalmazza a dinamikusan generált erőforrás-azonosító.

```json
{
    "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
    "contentVersion": "1.0.0.0",
    "parameters": {
      "vaultName": {
        "type": "string"
      },
      "secretName": {
        "type": "string"
      }
    },
    "resources": [
    {
      "apiVersion": "2015-01-01",
      "name": "nestedTemplate",
      "type": "Microsoft.Resources/deployments",
      "properties": {
        "mode": "incremental",
        "templateLink": {
          "uri": "https://www.contoso.com/AzureTemplates/newVM.json",
          "contentVersion": "1.0.0.0"
        },
        "parameters": {
          "adminPassword": {
            "reference": {
              "keyVault": {
                "id": "[concat(resourceGroup().id, '/providers/Microsoft.KeyVault/vaults/', parameters('vaultName'))]"
              },
              "secretName": "[parameters('secretName')]"
            }
          }
        }
      }
    }],
    "outputs": {}
}
```

## <a name="next-steps"></a>Következő lépések
* Kulcstároló kapcsolatos általános információkért lásd: [Ismerkedés az Azure Key Vault](../key-vault/key-vault-get-started.md).
* A titkos kulcs hivatkozó teljes példákért lásd [Key Vault példák](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples).

