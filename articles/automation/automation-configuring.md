<properties
   pageTitle="設定 Azure 自動化"
   description="描述設定 Azure 自動化以進行初次使用時必須執行的步驟。"
   services="automation"
   documentationCenter=""
   authors="SnehaGunda"
   manager="stevenka"
   editor="tysonn" />
<tags
   ms.service="automation"
   ms.devlang="na"
   ms.topic="get-started-article"
   ms.tgt_pltfrm="na"
   ms.workload="infrastructure-services"
   ms.date="11/10/2015"
   ms.author="bwren;sngun" />

# 設定 Azure 自動化

這篇文章描述最初開始使用 Azure 自動化時，您必須執行的動作。

## 自動化帳戶

當您第一次啟動 Azure 自動化時，您必須建立至少一個自動化帳戶。 自動化帳戶可讓您將您的自動化資源 (Runbook、資產、組態) 與其他自動化帳戶中包含的自動化資源中區隔。 您可以使用自動化帳戶，將自動化資源分成個別的邏輯環境。 例如，您可能會使用一個帳戶進行開發和另一個用於生產環境。

每個自動化帳戶的自動化資源都會與單一 Azure 區域相關聯，但自動化帳戶可管理任何區域中的 Azure 服務。 在不同區域建立自動化帳戶的主要原因會是，若您擁有需要區隔資料和資源到特定區域的原則時。

>[AZURE.NOTE] 自動化帳戶，以及建立的資源，使用 Azure preview 入口網站無法存取 Azure 入口網站中。 如果您想使用 Windows PowerShell 來管理這些帳戶或它們的資源，您必須使用 「Azure 資源管理員」模組。 
>
>使用 Azure 入口網站建立的自動化帳戶可以透過入口網站或 Cmdlet 集來管理。 帳戶一旦建立，您在帳戶中建立和管理資源的方式就沒有差別。 如果您打算繼續使用 Azure 入口網站，您應該使用它代替 Azure Preview 入口網站來建立任何的自動化帳戶。


如果您的 Azure 帳戶有問題 (例如逾期未付款)，可能會將自動化帳戶暫止。 在此情況下，您無法存取帳戶、將暫止任何執行中的工作，並將停用所有排程。 您可以檢視帳戶，但不會在其中看到任何資源。 一旦您更正問題並啟用自動化帳戶，您將必須啟用排程並重新啟動被暫止的任何 Runbook。


## 設定對 Azure 資源的驗證

當您存取 Azure 資源使用 [Azure cmdlet](http://msdn.microsoft.com/library/azure/jj554330.aspx), ，您必須提供驗證新增至您的 Azure 訂閱。 在 Azure 自動化中，這是由您設定為訂用帳戶的管理員的 Azure Active Directory 中組織帳戶來完成。 然後您可以建立 [認證](http://msdn.microsoft.com/library/dn940015.aspx) 這個使用者帳戶，並將它與 [Add-azureaccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) 在 runbook 中。

>[AZURE.NOTE] Microsoft 帳戶，前身為 Liveid，不能使用 Azure 自動化。

## 建立新 Azure Active Directory 使用者來管理 Azure 訂用帳戶

1. 以您想要管理的 Azure 訂用帳戶的服務系統管理員身分登入 Azure 入口網站。
2. 選取 **Active Directory**
3. 選取與您的 Azure 訂用帳戶相關聯的目錄名稱。 如果有需要，您可以變更此關聯從 **設定 > 訂用帳戶 > 編輯目錄**。
4. [建立新的 Active Directory 使用者](http://msdn.microsoft.com/library/azure/hh967632.aspx)。  選取 **貴組織中的新使用者** 的 **使用者類型** ，則沒有 **啟用多因素驗證**。
5. 請記下使用者的完整名稱和暫時密碼。
7. 選取 **設定 > 系統管理員 > 新增**。
8. 輸入您所建立之使用者的完整使用者名稱。
9. 選取您想讓使用者管理的訂用帳戶。
10. 登出 Azure，然後使用您剛才建立的帳戶登入。 將提示您變更使用者的密碼。
11. 建立新 [Azure 自動化認證資產](http://msdn.microsoft.com/library/dn940015.aspx) 如您所建立的使用者帳戶。  **認證類型** 應該 **Windows PowerShell 認證**。

## 建立自動化帳戶

自動化帳戶是 Azure 自動化資源的容器。 可供您劃分您的環境或進一步組織您的工作流程。 如果您已經建立自動化帳戶，則可以略過此步驟。

1. 登入 [Azure 預覽入口網站](https://portal.azure.com/)。

2. 在 Azure 預覽入口網站中，按一下 [ **新增** > **管理** > **自動化帳戶**

3. 在 **加入自動化帳戶** 刀鋒視窗中，設定您的自動化帳戶的詳細資料。 

>[AZURE.NOTE] 使用 Azure 預覽入口網站來建立自動化帳戶時，帳戶和與其相關聯的所有資源都不帶回至傳統的管理入口網站。 

以下是要設定的參數清單：

|參數            |說明 |
|:---|:---|
| 名稱 | 您的自動化帳戶的名稱；它必須是唯一的值。 |
| 資源群組 | 資源群組可簡單地查看和管理相關的 Azure 資源。 在 Azure Preview 入口網站中，您可以選擇現有的資源群組或為自動化帳戶建立新群組，而 Azure 管理入口網站中，所有的自動化帳戶會放在預設資源群組中。 |
| 訂閱 | 從可用的訂用帳戶清單中選擇訂用帳戶。 |
| 區域 | 此區域指定帳戶中的自動化資源將儲存在哪裡。 您可以從清單選擇任何區域，這不會影響帳戶的功能，但如果帳戶區域靠近其他 Azure 資源的儲存位置，則 Runbook 可能會執行得更快。 |
| 帳戶選項 | 此選項可讓您選擇何種資源就會建立在新的自動化帳戶。選取 **是** 將建立教學課程的 runbook。 |

![Create Account](media/automation-configuration/automation-01-create-automation-account.png)

>[AZURE.NOTE] 使用傳統的管理入口網站中建立自動化帳戶時 [移至不同的資源群組](../resource-group-move-resources.md) 使用 Azure 預覽入口網站，自動化帳戶將不再可用，在 Azure 傳統入口網站，在傳統的管理入口網站中不支援 Azure 資源管理員帳戶。



## 在 Runbook 中使用認證

您可以在 runbook 中使用的認證來擷取 [Get-automationpscredential](http://msdn.microsoft.com/library/dn940015.aspx) 活動，然後將它搭配 [Add-azureaccount](http://msdn.microsoft.com/library/azure/dn722528.aspx) 連接到您的 Azure 訂閱。 如果認證是系統管理員的多個 Azure 訂用帳戶，則您也應該使用 [Select-azuresubscription](http://msdn.microsoft.com/library/dn495203.aspx) 來指定正確的一個。 這會在以下的 Windows PowerShell 範例中顯示，通常會出現在大部分 Azure 自動化 Runbook 的頂端。

    $cred = Get-AutomationPSCredential –Name "myuseraccount.onmicrosoft.com"
    Add-AzureAccount –Credential $cred
    Select-AzureSubscription –SubscriptionName "My Subscription"

您應該在任何後重複這幾行 [檢查點](http://technet.microsoft.com/library/dn469257.aspx#bk_Checkpoints) 在 runbook 中。 如果 Runbook 暫止然後在另一個背景工作上繼續執行，則它必須重新執行驗證。

## 相關文章
- [Azure 自動化：使用 Azure Active Directory 對 Azure 進行驗證](http://azure.microsoft.com/blog/2014/08/27/azure-automation-authenticating-to-azure-using-azure-active-directory/)
 

