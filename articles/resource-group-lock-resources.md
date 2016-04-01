<properties 
    pageTitle="使用資源管理員鎖定資源 | Microsoft Azure" 
    description="透過套用限制到所有使用者和角色，防止使用者更新或刪除特定資源。" 
    services="azure-resource-manager" 
    documentationCenter="" 
    authors="tfitzmac" 
    manager="wpickett" 
    editor=""/>

<tags 
    ms.service="azure-resource-manager" 
    ms.workload="multiple" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="12/02/2015" 
    ms.author="tomfitz"/>

# 使用 Azure 資源管理員來鎖定資源

身為管理員，有下列情況下，您會想將鎖定放在訂閱、 資源群組或資源防止組織中的其他使用者寫入動作，或不小心刪除重要資源。 

「Azure 資源管理員」透過資源管理鎖，提供限制在資源上執行作業的能力。 鎖定是會在特定領域強制執行鎖定層級的原則。 範圍可以是訂用帳戶、資源群組或資源。 鎖定層級識別之類型的強制執行原則，目前有兩種值 – **CanNotDelete** 和 **ReadOnly**。 **CanNotDelete** 表示經過授權的使用者仍然可以讀取和修改資源，但無法刪除任何受限制的資源。 **ReadOnly** 表示授權使用者只能讀取資源，但無法修改或刪除任何受限制的資源。

鎖定不同於使用以角色為基礎的存取控制來指派使用者權限以執行特定動作。 若要深入了解設定權限的使用者和角色，請參閱 
[入口網站中的角色型存取控制](role-based-access-control-configure.md) 和 [管理和稽核資源存取權](resource-group-rbac.md)。 不同於角色型存取控制，您可以使用管理鎖定來對所有使用者和角色套用限制，而您一般僅在有限間套用限制鎖定。

## 常見案例

常見案例之一是，您有一個資源群組包含某些以頻繁開關模式運作的資源。  虛擬機器資源會定期開啟處理序資料給定的間隔 
時間，然後關閉。 在此案例中，您會想要啟用向下的關機的 vm，但請務必在 
無法刪除儲存體帳戶。 在此案例中，您會使用的資源鎖定的鎖定層級 **CanNotDelete** 上的儲存體帳戶。

在另一個案例中，您的企業可能有一段時間是不希望更新進入實際執行環境。  **ReadOnly** 鎖定層級會防止建立或更新。 如果您是零售公司，您可能不想在假期購物期間允許更新。  如果您是金融服務公司，您可能與部署期間相關的條件約束 
特定市場的小時。 資源鎖定能提供可適當鎖定資源的原則。 這可以只套用到特定資源或到整個資源群組。

## 誰可以建立或刪除您的組織中的鎖定

若要建立或刪除管理鎖定，您必須存取 **Microsoft.Authorization/\*** 或 **Microsoft.Authorization/locks/\*** 動作。 內建角色，僅 **擁有者** 和 **使用者存取系統管理員** 授與這些動作。 如需指派存取控制的詳細資訊，請參閱 [管理資源的存取權](resource-group-rbac.md)。

## 鎖定繼承

當您在父範圍套用鎖定時，所有子系資源都會都繼承相同的鎖定。

如果您將多個鎖定套用至資源，最嚴格的鎖定優先順序較高。 例如，如果您套用 **ReadOnly** （例如資源群組） 的父層級和 **CanNotDelete** 該群組內的資源，從父更嚴格的鎖定 （唯讀） 的優先順序。

## 在範本中建立鎖定

以下範例顯示的範本會在儲存體帳戶建立鎖定。 要套用鎖定的儲存體帳戶提供做為參數，並使用 
搭配 concat （） 函式。  結果是在此情況下附加 'Microsoft.Authorization'，然後是名稱的鎖定的資源名稱 **myLock**。

必須依資源型別提供型別。 針對儲存體，此型別為 "Microsoft.Storage/storageaccounts/providers/locks"。

使用設定的範圍層級 **層級** 資源屬性。 由於範例著重在協助避免意外刪除，層級設定為 **CannotDelete**。

    {
        "$schema": "https://schema.management.azure.com/schemas/2015-01-01/deploymentTemplate.json#",
        "contentVersion": "1.0.0.0",
        "parameters": {
            "lockedResource": {
                "type": "string"
            }
        },
        "resources": [
            {
                "name": "[concat(parameters('lockedResource'), '/Microsoft.Authorization/myLock')]",
                "type": "Microsoft.Storage/storageAccounts/providers/locks",
                "apiVersion": "2015-01-01",
                "properties": {
                    "level": "CannotDelete"
                }
            }
        ]
    }

## 使用 REST API 建立鎖定

您可以鎖定已部署的資源 [管理鎖定的 REST API](https://msdn.microsoft.com/library/azure/mt204563.aspx)。 REST API 可讓您建立和刪除鎖定，以及 
擷取現有鎖定的相關資訊。

若要建立鎖定，請執行：

    PUT https://management.azure.com/{scope}/providers/Microsoft.Authorization/locks/{lock-name}?api-version={api-version}

範圍可以是訂用帳戶、資源群組或資源。 lock-name 是您想要命名鎖定的任何名稱。 如需 api 版本，使用 **2015年-01-01**。

在要求中，包含指定鎖定屬性的 JSON 物件。

    {
        "properties": {
            "level": {lock-level},
            "notes": "Optional text notes."
        }
    } 

鎖定層級指定 **CanNotDelete** 或 **ReadOnly**。

如需範例，請參閱 [管理鎖定的 REST API](https://msdn.microsoft.com/library/azure/mt204563.aspx)。

## 使用 Azure PowerShell 建立鎖定

[AZURE.INCLUDE [powershell-preview-inline-include](../includes/powershell-preview-inline-include.md)]

您可以使用鎖定已部署的資源，使用 Azure PowerShell **新增 AzureRmResourceLock** ，如下所示。 透過 PowerShell，您只能設定 **LockLevel** 至 **CanNotDelete**。

    PS C:\> New-AzureRmResourceLock -LockLevel CanNotDelete -LockName LockSite -ResourceName examplesite -ResourceType Microsoft.Web/sites

Azure PowerShell 提供其他命令的使用中的鎖定，例如 **組 AzureRmResourceLock** 可以更新鎖定和 **移除 AzureRmResourceLock** 可以刪除鎖定。

## 後續步驟

- 如需使用資源鎖定的詳細資訊，請參閱 [鎖定下您 Azure 資源](http://blogs.msdn.com/b/cloud_solution_architect/archive/2015/06/18/lock-down-your-azure-resources.aspx)
- 若要深入了解以邏輯方式組織資源，請參閱 [使用標記來組織您的資源](resource-group-using-tags.md)
- 若要變更資源位於哪一個資源群組，請參閱 [將資源移動到新的資源群組](resource-group-move-resources.md)
- 您可以使用自訂原則，在訂用帳戶內套用限制和慣例。 如需詳細資訊，請參閱 [的使用原則來管理資源，並控制存取](resource-manager-policy.md)。


