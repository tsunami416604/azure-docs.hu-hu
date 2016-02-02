<properties
    pageTitle="Azure 資源管理員原則 | Microsoft Azure"
    description="說明如何使用 Azure 資源管理員原則來防止在不同的範圍，例如訂閱、 資源群組或個別資源的違規。"
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

Azure 的資源管理員現在可讓您控制透過自訂的存取
原則。 您可以透過原則來防止組織中的使用者違反管理組織資源所需的慣例。

建立描述您想要明確拒絕之動作或資源的原則定義。 
您指定想要的範圍，例如在訂閱中，這些原則定義
資源群組或個別的資源。

在本文中我們將說明原則的基本結構
定義建立原則，您可以使用的語言。 然後我們會
描述如何將主版頁面套用這些原則，在不同範圍和
最後，我們會示範如何完成這透過一些範例
REST API。

原則目前以預覽版提供。

## 它和 RBAC 有什麼不同？

原則和以角色為基礎的存取控制，之間的一些關鍵差別，但首先要了解是 
原則和 RBAC 一起運作。 若要使用原則，使用者必須經由 RBAC 通過驗證。 不像 RBAC，原則是 
預設允許和明確拒絕系統。

RBAC 著重於**使用者**在不同範圍內可執行的動作。 
例如，特定的使用者會加至投稿者 」 角色，如資源群組，在您想要的範圍，因此使用者可以進行變更， 
資源群組。

原則著重於各種範圍的**資源**動作。 例如，透過原則，您可以 
控制可以佈建，或限制的位置，在其中的資源類型 
可以提供資源。

## 常見案例

一個常見的案例是需要 chargeback 部門的標籤
目的。 組織可能要允許作業時才
適當的成本中心 」 是相關聯。否則，它們會拒絕要求。
這會幫助他們收費適當的成本中心
執行的作業。

另一個常見案例是組織可能會想要控制
資源建立的所在位置。 它們也可能想要控制項
藉由允許特定類型的資源存取的資源
佈建。

同樣地，組織可以控制服務目錄，或強制執行
資源所需的命名慣例。

使用原則可輕易地達成這些案例，如下所述。

## 原則定義結構

原則定義是使用 JSON 建立。 它包含一或多個
定義動作及效果的條件/邏輯運算子
表示當條件符合時，會發生什麼事。

基本上，原則包含下列：

**條件/邏輯運算子:** 它包含一組條件其中
透過一組邏輯運算子，可以進行操作。

**效果:** 這描述了什麼效果會在條件為時
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

| 運算子名稱| 語法|
| :------------- | :------------- |
| Not| "not": {< 條件或運算子 >}|
| 和| "載 」: [{< 條件 1 >}，{< condition2 >}]|
| 或| "anyOf 」: [{< 條件 1 >}，{< condition2 >}]|

不支援巢狀的條件。

## 條件

條件會評估某個**欄位**或**來源**是否符合特定準則。 以下列出支援的條件名稱及語法：

| 條件名稱| 語法|
| :------------- | :------------- |
| Equals| 「 等於 」: < >"值"|
| Like| "like": < >"值"|
| Contains| "包含": < >"值"|
| 在| "在": ["< value1 >"、"< value2 > 」]|
| ContainsKey| "containsKey":"< keyName >"|


## 欄位和來源

條件是透過欄位和來源的使用所形成。 欄位代表資源要求裝載中的屬性 來源代表要求本身的特性。

支援下列欄位和來源：

Fields: **name**, **kind**, **type**, **location**, **tags**, **tags.***.

來源：**action**。

若要取得關於動作的詳細資訊，請參閱 [RBAC-內建的角色] (active-directory/role-based-access-built-in-roles.md)。

## 原則定義範例

現在讓我們看一下我們會定義原則以達到的方式
上面所列的案例。

### 退款：要求部門標記

下列原則會拒絕沒有標記，包含所有要求
"costCenter"的索引鍵。

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

下列範例示範如何使用來源。 它會顯示的動作只會在
服務的型別 Microsoft.Resources/\*, ，Microsoft.Compute/\*,，
Microsoft.Storage/\*, ，Microsoft.Network/\* 允許。 任何其他項目
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

下列範例示範如何使用條件支援的萬用字元
「 要 」。 條件陳述，如果名稱符合所述的模式 (namePrefix\ * nameSuffix) 然後拒絕
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

在不同的範圍，像是訂閱，資源可以套用原則
群組和個別的資源。 原則所繼承的所有子系
資源。 所以如果原則套用到資源群組，它將會
適用於該資源群組中的所有資源。

## 建立原則

本節提供如何原則可使用來建立其他詳細資料
API 撰寫。

### 使用 REST API 建立原則定義

您可以建立原則，以與 [的原則定義的其他 API](https://msdn.microsoft.com/library/azure/mt588471.aspx)。 REST API 可讓您建立和刪除原則定義，以及取得現有定義的相關資訊。

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
對於 api-version，請使用 *2015-10-01-preview*。 取得範例和詳細資料，
請參閱 [的原則定義的其他 API](https://msdn.microsoft.com/library/azure/mt588471.aspx)。

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

您可以套用原則定義在您想要的範圍內，透過 [原則的工作分派的其他 API](https://msdn.microsoft.com/library/azure/mt588466.aspx)。
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

如範例以及其他詳細資訊，請參閱 [原則的工作分派的其他 API](https://msdn.microsoft.com/library/azure/mt588466.aspx)。

### 使用 PowerShell 指派原則

您可以使用 New-AzureRmPolicyAssignment Cmdlet 將上面透過 PowerShell 建立的原則套用至所需的範圍，如下所示：

    New-AzureRmPolicyAssignment -Name regionPolicyAssignment -PolicyDefinition $policy -Scope    /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

以下 $policy 是由於執行 New-AzureRmPolicyDefinition Cmdlet 而傳回的原則物件，如下所示。 此處的範圍是您指定之資源群組的名稱。

如果想要移除上述原則指派，您可以執行如下動作：

    Remove-AzureRmPolicyAssignment -Name regionPolicyAssignment -Scope /subscriptions/########-####-####-####-############/resourceGroups/<resource-group-name>

您可以分別透過 Get-AzureRmPolicyDefinition、Set-AzureRmPolicyDefinition 和 Remove-AzureRmPolicyDefinition Cmdlet 取得、變更或移除原則定義。

同樣地，您可以分別透過 Get-AzureRmPolicyAssignment、Set-AzureRmPolicyAssignment 和 Remove-AzureRmPolicyAssignment 取得、變更或移除原則指派。





