<properties
    pageTitle="Azure Active Directory AD 角色型存取控制 | Microsoft Azure"
    description="本文說明 Azure 角色型存取控制"
    services="active-directory"
    documentationCenter=""
    authors="IHenkel"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.devlang="na"
    ms.topic="article"
    ms.tgt_pltfrm="na"
    ms.workload="identity"
    ms.date="12/14/2015"
    ms.author="inhenk"/>

# Azure 角色型存取控制

## 角色型存取控制
Azure 角色型存取控制 (RBAC) 可以對 Azure 進行更細緻的存取權管理。 您可以使用 RBAC 來區隔開發小組的職責，僅授與使用者作業所需的存取權。

### Azure 存取權管理的基礎
每一個 Azure 訂用帳戶都以 Azure Active Directory 為依歸。 只有來自該目錄的使用者、群組和應用程式可以獲得存取權，使用 Azure 入口網站、Azure 命令列工具和 Azure 管理 API 來管理 Azure 訂用帳戶中的資源。

在正確範圍將適當的 RBAC 角色指派給使用者、群組和應用程式，即可授與存取權。 若要將存取權授與整個訂用帳戶，請在訂用帳戶範圍指派角色。 若要將存取權授與訂用帳戶內的特定資源群組，請在資源群組範圍指派角色。 您可能也會在特定資源指派角色，例如網站、虛擬機器和子網路，只授與該資源的存取權。

![](./media/role-based-access-control-configure/overview.png)

您指派給使用者、群組和應用程式的 RBAC 角色，指定使用者 (或應用程式) 可以在該範圍內管理的資源。

### Azure RBAC 內建角色
Azure RBAC 有適用於所有資源類型的三個基本角色：擁有者、參與者和讀者。 擁有者具有所有資源的完整存取權，包括將存取權委派給其他人的權限。 參與者可以建立和管理所有類型的 Azure 資源，但是不能將存取權授與其他人。 讀者只能檢視現有的 Azure 資源。 Azure 中其餘的 RBAC 角色可以管理特定 Azure 資源。 例如，虛擬機器參與者角色可以建立和管理虛擬機器，但是無法管理虛擬網路或虛擬機器所連接的子網路。

[在角色中的 RBAC 建置](role-based-access-built-in-roles.md) 列出 Azure 中可用的內建 RBAC 角色。 它會針對每個角色指定內建角色獲得存取權的作業。

### Azure 資源階層和存取繼承
在 Azure 中的每個訂用帳戶只屬於一個目錄，每個資源群組只屬於一個訂用帳戶，每個資源只屬於一個資源群組。 您在父範圍授與的存取權會在子範圍繼承。 如果您在訂用帳戶範圍授與 Azure AD 群組讀者角色，該群組的成員可以檢視訂用帳戶中的每個資源群組和每個資源。 如果您在資源群組範圍授與應用程式參與者角色，它可以管理該資源群組中的所有類型資源，但是無法管理訂用帳戶中的其他資源群組。

### Azure RBAC 與傳統訂用帳戶系統管理員和共同管理員
傳統訂用帳戶系統管理員和共同管理員具有 Azure 訂用帳戶的完整存取權。 他們可以管理使用 Azure 傳統入口網站 (https://manage.windowsazure.com)，以及 Azure 服務管理員 Api，以及 Azure 入口網站 (https://portal.azure.com) 和新的 Azure 資源管理員 Api 的資源。 在 RBAC 模型中，傳統系統管理員會獲指派訂用帳戶範圍的擁有者角色。

只能由 Azure 入口網站 (https://portal.azure.com) 與 Azure 資源管理員 Api 支援更精細的授權模型 (Azure RBAC)。 使用者和應用程式 (在訂用帳戶/資源/資源群組的範圍) 的 RBAC 角色指派，無法使用傳統的管理入口網站 (http://manage.windowsazure.com) 和 Azure 服務管理 Api。

### 管理授權與資料作業
更細緻的授權模型 (Azure RBAC) 僅支援對 Azure 入口網站的 Azure 資源與 Azure 資源管理員 API 進行管理作業。 並非所有 Azure 資源的資料作業都可以透過 RBAC 授權。 舉例來說，儲存體帳戶的建立/讀取/更新/刪除作業可以透過 RBAC 控制，但是儲存體帳戶內的 Blob 或資料表的建立/讀取/更新/刪除作業，卻還無法透過 RBAC 來控制。 同理，SQL DB 的建立/讀取/更新/刪除作業可以透過 RBAC 來控制，但是 DB 內的 SQL 資料表之建立/讀取/更新/刪除作業卻還無法透過 RBAC 來控制。

## 使用 Azure 入口網站管理存取
### 檢視存取權
在資源群組刀鋒視窗的基本功能區段，選取存取權設定。  **使用者** ] 刀鋒視窗中列出的所有使用者、 群組和應用程式，授與存取權的資源群組。 存取權是在資源群組上指派或繼承自父訂用帳戶指派。

![](./media/role-based-access-control-configure/view-access.png)

> [AZURE.NOTE] 傳統的訂用帳戶管理員與共同管理員實際上是全新的 RBAC 模型中之訂閱的擁有者。

### 新增存取權
1. 按一下 [ **新增** 圖示 **使用者** 刀鋒視窗。 ![](./media/role-based-access-control-configure/grant-access1.png)
2. 選取您想要指派的角色。
3. 搜尋並選取您想要授與存取權的使用者或群組或應用程式。
4. 使用顯示名稱、電子郵件地址和物件識別碼，搜尋使用者、群組和應用程式的目錄。![](./media/role-based-access-control-configure/grant-access2.png)

### 移除存取權
1. 在 **使用者** 刀鋒視窗中，選取您想要移除的角色指派。
2. 按一下 [ **移除** 指派詳細資料] 分頁中的圖示。
3. 按一下 [ **是** 以確認移除。

![](./media/role-based-access-control-configure/remove-access1.png)


> [AZURE.NOTE] 繼承的指派不會從子領域。 瀏覽至父範圍然後移除這類指派。


![](./media/role-based-access-control-configure/remove-access2.png)

## 使用 Azure PowerShell 管理存取權
可以使用 Azure PowerShell 工具中的 Azure RBAC 命令管理存取權。

-   使用 `Get-AzureRmRoleDefinition` 以列出可以指派的 RBAC 角色，以及檢查它們獲得存取權的作業。

-   使用 `Get-AzureRmRoleAssignment` 以列出在指定的訂用帳戶或資源群組或資源有效的 RBAC 存取權指派。 使用 `ExpandPrincipalGroups` 參數以列出指定使用者和使用者為其成員之群組的存取權指派。 使用 `IncludeClassicAdministrators` 參數同時列出傳統訂用帳戶系統管理員和共同管理員。

-   使用 `New-AzureRmRoleAssignment` 以將存取權授與使用者、群組和應用程式。

-   使用 `Remove-AzureRmRoleAssignment` 以移除存取權。

請參閱 [使用 Azure PowerShell 管理存取](role-based-access-control-manage-access-powershell.md) 如需詳細範例，使用 Azure PowerShell 的存取權的管理。

## 使用 Azure 命令列介面管理存取權
可以使用 Azure 命令列介面中的 Azure RBAC 命令管理存取權。

-   使用 `azure role list` 以列出可以指派的 RBAC 角色。 使用 Azure 角色顯示以檢查它們獲得存取權的作業。

-   使用 `azure role assignment list` 以列出在指定的訂用帳戶或資源群組或資源有效的 RBAC 存取權指派。 使用 `expandPrincipalGroups` 選項以列出指定使用者和使用者為其成員之群組的存取權指派。 使用  `includeClassicAdministrators` 參數也會列出傳統訂用帳戶管理員和共同管理員。

-   使用 `azure role assignment create` 以將存取權授與使用者、群組和應用程式。

-   使用 `azure role assignment delete` 以移除存取權。

請參閱 [管理使用 Azure CLI 存取](role-based-access-control-manage-access-azure-cli.md) 如需詳細範例，使用 Azure CLI 的存取權的管理。

## 使用存取權變更歷程記錄報告
您的 Azure 訂用帳戶中發生的所有存取權變更都會記錄在 Azure 事件。

### 使用 Azure PowerShell 建立報告
若要建立報告，以指出誰在您的 Azure 訂用帳戶中於哪個範圍對誰授與/撤銷何種存取權，請使用下列 PowerShell 命令：

    `Get-AzureAuthorizationChangeLog`

### 使用 Azure CLI 建立報告
若要建立誰在您的 Azure 訂用帳戶中於哪個範圍對誰授與/撤銷何種存取權的報告，請使用下列 Azure 命令列介面 (CLI) 命令：

    `azure authorization changelog`

> [AZURE.NOTE] 您可以查詢過去 90 天 (在 15 天批次) 存取變更。

以下列出過去 7 天訂用帳戶中的所有存取權變更。

![](./media/role-based-access-control-configure/access-change-history.png)

### 將存取權變更匯出至試算表
將存取權變更匯出至試算表進行檢閱很方便。

![](./media/role-based-access-control-configure/change-history-spreadsheet.png)

## 在 Azure RBAC 中自訂角色
如果內建角色都不符合您的特定存取需求，請在 Azure RBAC 中建立自訂角色。 可以使用 Azure PowerShell 中的 RBAC 命令列工具和 Azure 命令列介面，來建立自訂角色。 就像內建角色一樣，可以將自訂角色指派給訂用帳戶、資源群組和資源範圍的使用者、群組和應用程式。

以下是可以監視和重新啟動虛擬機器的範例自訂角色定義：

```
{
  "Name": "Virtual Machine Operator",
  "Id": "cadb4a5a-4e7a-47be-84db-05cad13b6769",
  "IsCustom": true,
  "Description": "Can monitor and restart virtual machines.",
  "Actions": [
    "Microsoft.Storage/*/read",
    "Microsoft.Network/*/read",
    "Microsoft.Compute/*/read",
    "Microsoft.Compute/virtualMachines/start/action",
    "Microsoft.Compute/virtualMachines/restart/action",
    "Microsoft.Authorization/*/read",
    "Microsoft.Resources/subscriptions/resourceGroups/read",
    "Microsoft.Insights/alertRules/*",
    "Microsoft.Insights/diagnosticSettings/*",
    "Microsoft.Support/*"
  ],
  "NotActions": [

  ],
  "AssignableScopes": [
    "/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e",
    "/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624",
    "/subscriptions/34370e90-ac4a-4bf9-821f-85eeedeae1a2"
  ]
}
```
### 動作
自訂角色的動作屬性會指定角色授與存取權的 Azure 作業。 它是識別 Azure 資源提供者的安全性實體作業的作業字串集合。 作業包含萬用字元的字串 (\ *) 授與存取權進入的比對作業字串的所有作業。 例如：

-   `*/read` 授與所有 Azure 資源提供者的所有資源類型的讀取作業的存取權。
-   `Microsoft.Network/*/read` 授與 Azure 的 Microsoft.Network 資源提供者的所有資源類型的讀取作業的存取權。
-   `Microsoft.Compute/virtualMachines/*` 授與虛擬機器和其子系資源類型的所有作業的存取權。
-   `Microsoft.Web/sites/restart/Action` 授與重新啟動網站的存取權。

使用 `Get-AzureRmProviderOperation` 或 `azure provider operations show` 命令以列出 Azure 資源提供者的作業。 您也可以使用這些命令以確認作業字串有效，以及展開萬用字元作業字串。

![](./media/role-based-access-control-configure/1-get-azurermprovideroperation-1.png)

![](./media/role-based-access-control-configure/1-azure-provider-operations-show.png)

### 非動作
如果您想要允許的作業集合輕鬆地表示排除特定的作業，而不是比然後使用您想要排除的項目，包括以外的所有作業作業 **NotActions** 自訂角色的內容。 有效的存取權授與自訂的角色會計算排除 **NotActions** 動作作業的作業。

請注意，如果使用者指派的角色中的作業中排除， **NotActions** 並指派存取權授與相同的作業 – 允許使用者執行這項操作的第二個角色。 **NotActions** 不拒絕規則 – 只是方便時要排除的特定作業需要建立一組允許的作業。

### AssignableScopes
 **AssignableScopes** 自訂角色的屬性會指定自訂的角色是指派給使用者、 群組和應用程式的範圍 (訂用帳戶，或資源群組或資源)。 使用 **AssignableScopes** 您可以提供自訂的角色指派只訂用帳戶或資源群組中需要它，並不會干擾其餘的訂用帳戶或資源群組的使用者經驗。 **AssignableScopes** 的自訂角色也控制可以檢視、 更新和刪除角色。 以下是一些有效的可指派範圍：

-   “/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e”, “/subscriptions/e91d47c4-76f3-4271-a796-21b4ecfe3624”：讓角色可用於兩個訂用帳戶中的指派。
-   “/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e”：讓角色可用於單一訂用帳戶中的指派。
-   “/subscriptions/c276fc76-9cd4-44c9-99a7-4fd71546436e/resourceGroups/Network”：讓角色僅可用於網路資源群組中的指派。

### 自訂角色存取控制
 **AssignableScopes** 自訂角色的內容指定誰可以檢視、 修改和刪除角色。

**誰可以建立自訂角色?**
若要建立自訂的角色，所有指定的允許使用者建立角色時，才會成功建立自訂角色 **AssignableScopes**。 建立角色的使用者可以執行時，才會成功建立自訂角色 `Microsoft.Authorization/roleDefinition/write operation` 所有 **AssignableScopes** 的角色。 因此，訂用帳戶、資源群組和資源的擁有者 (和使用者存取管理員) 可以建立自訂角色以在這些範圍中使用。

**誰可以修改自訂的角色?**
更新所有的自訂角色允許使用者 **AssignableScopes** 的角色可以修改該自訂角色。 使用者可以執行 `Microsoft.Authorization/roleDefinition/write` 上所有作業 **AssignableScopes** 的自訂角色可以修改該自訂角色。 比方說，如果自訂的角色指派兩個 Azure 訂用帳戶中 (也就是它有兩個訂閱其 **AssignableScopes** 屬性) 的使用者必須是擁有者 (或使用者存取系統管理員) 的兩個訂閱，無法修改自訂的角色。

**誰可以檢視指派範圍內的可用的自訂角色?**
可以在範圍中執行 `Microsoft.Authorization/roleDefinition/read` 作業的使用者，可以檢視可用於在該範圍中指派的 RBAC 角色。 Azure RBAC 中的所有內建角色允許檢視可用於指派的角色。

