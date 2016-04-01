<properties
    pageTitle="Azure 資源管理員原則 | Microsoft Azure"
    description="描述如何使用 Azure 資源管理員原則以防止在不同的範圍，例如訂閱、 資源群組或個別資源的違規。"
    services="azure-resource-manager"
    documentationCenter="na"
    authors="ravbhatnagar"
    manager="ryjones"
    editor=""/>

<tags
    ms.service="azure-resource-manager"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="na"
    ms.date="11/10/2015"
    ms.author="gauravbh;tomfitz"/>

# 使用原則來管理資源和控制存取

Azure 資源管理員現在可讓您透過自訂控制存取
原則。 您可以透過原則來防止組織中的使用者違反管理組織資源所需的慣例。 

建立描述您想要明確拒絕之動作或資源的原則定義。 
指定所需的範圍，例如訂閱中，這些原則定義
資源群組或個別資源。 

在本文中，我們將說明此原則的基本結構
定義可用來建立原則的語言。 然後我們將
描述如何套用這些原則在不同範圍，
最後，我們將說明如何透過完成一些的範例
REST API。

原則目前以預覽版提供。

## 它和 RBAC 有什麼不同？

有幾個主要的差異，原則和以角色為基礎的存取控制，但首先要了解， 
原則和 RBAC 一起運作。 若要使用原則，使用者必須經由 RBAC 通過驗證。 不同於 RBAC，原則是 
預設允許與明確拒絕系統。 

RBAC 著重於動作 **使用者** 可以執行在不同的範圍。 
例如，特定的使用者會加入至所需的範圍、 資源群組的參與者角色讓使用者可以變更， 
資源群組。 

原則著重於 **資源** 在各種範圍的動作。 例如，您可以透過原則來 
控制項可以佈建或限制所在位置之資源類型 
資源可佈建。

## 常見案例

常見案例之一是為需要部門標記計費功能
用途。 組織可能想要允許的作業時，才
適當的成本中心是相關聯。否則，它們將會拒絕要求。
這會幫助他們收費適當的成本中心
執行作業。

另一個常見的案例是組織可能想要控制
建立資源的位置。 他們可能會想要控制或
藉由使用特定類型的資源存取的資源
佈建。

同樣地，組織可以控制服務類別目錄，或強制執行
資源所需的命名慣例。

使用原則可輕易地達成這些案例，如下所述。

## 原則定義結構

原則定義是使用 JSON 建立。 它包含一或多個
條件/邏輯運算子會定義動作和效果
這會告知符合條件時，會發生什麼事。

基本上，原則包含下列：

**條件/邏輯運算子 ︰** 它包含一組條件的
可以透過一組邏輯運算子的操作。

**效果 ︰** 條件時，效果功能將會說明
滿足 – 拒絕或稽核。 稽核效果會發出警告
事件服務記錄檔。 例如，系統管理員可以建立原則，如果有任何人建立大型 VM，然後稍後檢閱記錄檔，則此原則會引發稽核。

    {
      "if" : {
        <condition> | <logical operator>
      },
      "then" : {
        "effect" : "deny | audit"
      }
    }


## 邏輯運算子

以下列出支援的邏輯運算子以及語法：

| 運算子名稱     | 語法         |
| :------------- | :------------- |
| 否            | "not": {& l t; 條件或運算子 & gt;}             |
| 和           | "allOf" : [ {&lt;條件 1&gt;},{&lt;條件 2&gt;}] |
| 或                         | "anyOf" : [ {&lt;條件 1&gt;},{&lt;條件 2&gt;}] |

不支援巢狀的條件。

## 條件

條件評估是否 **欄位** 或 **來源** 符合特定準則。 以下列出支援的條件名稱及語法：

| 條件名稱 | 語法                |
| :------------- | :------------- |
| Equals             | "equals" : "&lt;值&gt;"               |
| Like                  | "like" : "&lt;值&gt;"                   |
| Contains          | "contains" : "&lt;值&gt;"|
| 在                        | "in" : [ "&lt;值 1&gt;","&lt;值 2&gt;" ]|
| ContainsKey    | "containsKey" : "&lt;機碼名稱&gt;" |


## 欄位和來源

條件是透過欄位和來源的使用所形成。 欄位代表資源要求裝載中的屬性 來源代表要求本身的特性。 

支援下列欄位和來源：

欄位 ︰ **名稱**, ，**種類**, ，**類型**, ，**位置**, ，**標記**, ，**標記。***.

來源 ︰ **動作**。 

若要取得有關動作的詳細資訊，請參閱 [RBAC-內建的角色] (active-directory/role-based-access-built-in-roles.md)。 

## 原則定義範例

現在讓我們看一下我們會定義原則，以達到的方式
以上所列的案例。

### 退款：要求部門標記

以下原則拒絕沒有一個標記，其中包含的所有要求
「 costCenter 」 索引鍵。

    {
      "if": {
        "not" : {
          "field" : "tags",
          "containsKey" : "costCenter"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }


### 地理區域法規遵循：確保資源位置

下列範例顯示的原則將會拒絕位置不是北歐或西歐的所有要求。

    {
      "if" : {
        "not" : {
          "field" : "location",
          "in" : ["northeurope" , "westeurope"]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### 服務策展：選取服務目錄

下列範例示範如何使用來源。 它會顯示動作只能在
服務的型別 Microsoft.Resources/\*，Microsoft.Compute/\*，
允許 Microsoft.Network/\* Microsoft.Storage/\*。 任何其他項目
將被拒絕。

    {
      "if" : {
        "not" : {
          "anyOf" : [
            {
              "source" : "action",
              "like" : "Microsoft.Resources/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Compute/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Storage/*"
            },
            {
              "source" : "action",
              "like" : "Microsoft.Network/*"
            }
          ]
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

### 命名慣例

下列範例將示範如何使用萬用字元所支援的條件
「 類似 」。 條件陳述，如果名稱不符合所述的模式 (namePrefix\ * nameSuffix) 然後拒絕
要求。

    {
      "if" : {
        "not" : {
          "field" : "name",
          "like" : "namePrefix*nameSuffix"
        }
      },
      "then" : {
        "effect" : "deny"
      }
    }

## 原則指派

原則可以套用不同的範圍，例如訂閱、 資源
群組和個別資源。 所有子系會繼承原則
資源。 因此，如果原則會套用至資源群組，它會是
適用於該資源群組中的所有資源。

## 建立原則

本節提供有關如何原則可以建立使用 REST 的詳細資料
API 撰寫。

### 使用 REST API 建立原則定義

您可以建立與原則 [REST API 的原則定義](https://msdn.microsoft.com/library/azure/mt588471.aspx)。 REST API 可讓您建立和刪除原則定義，以及取得現有定義的相關資訊。

若要建立新原則，請執行：

    PUT https://management.azure.com/subscriptions/{subscription-id}/providers/Microsoft.authorization/policydefinitions/{policyDefinitionName}?api-version={api-version}

使用如下的要求內文：

    {
      "properties":{
        "policyType":"Custom",
        "description":"Test Policy",
        "policyRule":{
          "if" : {
            "not" : {
              "field" : "tags",
              "containsKey" : "costCenter"
            }
          },
          "then" : {
            "effect" : "deny"
          }
        }
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
      "type":"Microsoft.Authorization/policyDefinitions",
      "name":"testdefinition"
    }


原則定義可以定義為如上所示的其中一個範例。
Api 版本使用 *2015年-10-01-預覽*。 如需範例和詳細資訊，
請參閱 [REST API 的原則定義](https://msdn.microsoft.com/library/azure/mt588471.aspx)。

### 使用 PowerShell 建立原則定義

您可以使用 New-AzureRmPolicyDefinition Cmdlet 建立新的原則定義，如下所示。 下面範例會建立一個原則，只允許北歐和西歐中的資源。

    $policy = New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation onlyin certain regions" -Policy '{   "if" : {
                            "not" : {
                                "field" : "location",
                                    "in" : ["northeurope" , "westeurope"]
                                }
                              },
                                "then" : {
                                    "effect" : "deny"
                                    }
                            }'          

執行的輸出會儲存 $policy 物件中，以便稍後可在指派原則期間使用它。 針對原則參數，也可以提供包含原則之.json 檔案的路徑，而不是指定內嵌原則，如下所示。

    New-AzureRmPolicyDefinition -Name regionPolicyDefinition -Description "Policy to allow resource creation only in certain    regions" -Policy "path-to-policy-json-on-disk"


## 套用原則

### 使用 REST API 的原則指派

您可以套用到所需範圍的原則定義 [REST API 的原則指派](https://msdn.microsoft.com/library/azure/mt588466.aspx)。
REST API 可讓您建立和刪除原則指派，以及取得現有指派的相關資訊。

若要建立新的原則指派，請執行：

    PUT https://management.azure.com /subscriptions/{subscription-id}/providers/Microsoft.authorization/policyassignments/{policyAssignmentName}?api-version={api-version}

{policy-assignment} 是原則指派的名稱。 如需
api 版本使用 *2015年-10-01-預覽*。 

使用如下的要求內文：

    {
      "properties":{
        "displayName":"VM_Policy_Assignment",
        "policyDefinitionId":"/subscriptions/########/providers/Microsoft.Authorization/policyDefinitions/testdefinition",
        "scope":"/subscriptions/########-####-####-####-############"
      },
      "id":"/subscriptions/########-####-####-####-############/providers/Microsoft.Authorization/policyAssignments/VMPolicyAssignment",
      "type":"Microsoft.Authorization/policyAssignments",
      "name":"VMPolicyAssignment"
    }

如需範例和詳細資訊，請參閱 [REST API 的原則指派](https://msdn.microsoft.com/library/azure/mt588466.aspx)。

### 使用 PowerShell 指派原則

您可以使用 New-AzureRmPolicyAssignment Cmdlet 將上面透過 PowerShell 建立的原則套用至所需的範圍，如下所示：

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>
        
以下 $policy 是由於執行 New-AzureRmPolicyDefinition Cmdlet 而傳回的原則物件，如下所示。 此處的範圍是您指定之資源群組的名稱。

如果想要移除上述原則指派，您可以執行如下動作：

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

您可以分別透過 Get-AzureRmPolicyDefinition、Set-AzureRmPolicyDefinition 和 Remove-AzureRmPolicyDefinition Cmdlet 取得、變更或移除原則定義。

同樣地，您可以分別透過 Get-AzureRmPolicyAssignment、Set-AzureRmPolicyAssignment 和 Remove-AzureRmPolicyAssignment 取得、變更或移除原則指派。


