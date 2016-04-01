<properties
   pageTitle="透過資源管理員範本使用金鑰保存庫密碼 | Microsoft Azure"
   description="示範如何在部署期間從金鑰保存庫中傳遞密碼做為參數。"
   services="azure-resource-manager,key-vault"
   documentationCenter="na"
   authors="tfitzmac"
   manager="wpickett"
   editor=""/>

<tags
   ms.service="azure-resource-manager"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/15/2015"
   ms.author="tomfitz"/>

# 在部署期間傳遞安全值

當您需要在部署期間，做為參數傳遞的安全值 （例如密碼） 時，您可以將該值儲存為中密碼 [Azure 金鑰保存庫](./key-vault/key-vault-whatis.md) 和參考的其他資源管理員範本中的值。 包含密碼的參考 
在範本中永遠不會公開機密，因此您不需要手動輸入值的密碼每次您部署的資源。 您指定哪些使用者或 
服務主體可以存取密碼。  

> [AZURE.NOTE] 目前，只有 Azure CLI 支援參考金鑰保存庫密碼的能力。 Azure PowerShell 將儘速加入此功能。 

## 部署金鑰保存庫和密碼

若要建立可以從其他資源管理員範本中參考的金鑰保存庫，您必須設定 **enabledForTemplateDeployment** 屬性 **true**, ，並向使用者必須授與存取或 
將執行的部署會參考密碼的服務主體。

若要深入了解部署金鑰保存庫和密碼，請參閱 
[金鑰保存庫的結構描述](resource-manager-template-keyvault.md) 和 [金鑰保存庫密碼結構描述](resource-manager-template-keyvault-secret.md)。

## 參考密碼

您會從將值傳遞至範本的參數檔案中參考密碼。 您可以藉由傳遞金鑰保存庫的資源識別碼和密碼的名稱來參考密碼。

    "parameters": {
        "adminPassword": {
            "reference": {
                "keyVault": {
                    "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
                }, 
                "secretName": "sqlAdminPassword" 
            } 
        }
    }

整個參數檔案可能看起來如下：

    {
        "$schema": "http://schema.management.azure.com/schemas/2015-01-01/deploymentParameters.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "value": ""
            },
            "sqlsvrAdminLoginPassword": {
                "reference": {
                    "keyVault": {
                        "id": "/subscriptions/{guid}/resourceGroups/{group-name}/providers/Microsoft.KeyVault/vaults/{vault-name}"
                    },
                    "secretName": "adminPassword"
                }
            }
        }
    }

接受密碼的參數應該是 **securestring**。 下列範例顯示範本的相關區段，該範本會部署需要系統管理員密碼的 SQL Server。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "sqlsvrAdminLogin": {
                "type": "string",
                "minLength": 4
            },
            "sqlsvrAdminLoginPassword": {
                "type": "securestring"
            },
            ...
        },
        "resources": [
        {
            "name": "[variables('sqlsvrName')]",
            "type": "Microsoft.Sql/servers",
            "location": "[resourceGroup().location]",
            "apiVersion": "2014-04-01-preview",
            "properties": {
                "administratorLogin": "[parameters('sqlsvrAdminLogin')]",
                "administratorLoginPassword": "[parameters('sqlsvrAdminLoginPassword')]"
            },
            ...
        }],
        "variables": {
            "sqlsvrName": "[concat('sqlsvr', uniqueString(resourceGroup().id))]"
        },
        "outputs": { }
    }




## 後續步驟

- 如需金鑰保存庫的一般資訊，請參閱 [開始使用 Azure 金鑰保存庫](./key-vault/key-vault-get-started.md)。
- 如需部署範本的詳細資訊，請參閱 [應用程式使用 Azure 資源管理員範本部署](resource-group-template-deploy.md)。
- 參考索引鍵的機密資料的完整範例，請參閱 [金鑰保存庫範例](https://github.com/rjmax/ArmExamples/tree/master/keyvaultexamples)。



