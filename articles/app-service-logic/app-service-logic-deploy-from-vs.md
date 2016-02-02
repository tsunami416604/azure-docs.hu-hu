<properties 
    pageTitle="從 Visual Studio 部署" 
    description="在 Visual Studio 中建立專案來管理邏輯應用程式。" 
    authors="stepsic-microsoft-com" 
    manager="dwrede" 
    editor="" 
    services="app-service\logic" 
    documentationCenter=""/>

<tags
    ms.service="app-service-logic"
    ms.workload="integration"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/29/2015"
    ms.author="stepsic"/> 


# 從 Visual Studio 部署

雖然 [Azure 入口網站](https://portal.azure.com) 提供您絕佳設計和管理邏輯應用程式，您可能也想要改為部署邏輯應用程式從 Visual Studio。 這帶來一些重要的功能：

- 將邏輯應用程式連同其他資產一起儲存在方案中，因此可含括應用程式的所有層面。
- 保持邏輯應用程式簽入原始檔控制中，讓您利用 TFS 或 Git 來追蹤它的修訂

您必須安裝 Azure SDK 2.7 或更新版本才能執行下列步驟。 尋找 [最新的 SDK for VS](http://azure.microsoft.com/downloads/) 這裡。

## 建立專案

1. 移至 **檔案** 功能表，然後選取 **新增** >  **專案** (或者，您可以移至 **新增** ，然後選取 **新的專案** 將它加入至現有的方案)
    ![[檔案] 功能表](./media/app-service-logic-deploy-from-vs/filemenu.png)

2. 在對話方塊中，尋找 [**雲端**]，然後選取 [**Azure 資源群組**]。 型別 a **名稱** 然後按一下 [ **確定**。
    ![加入新的專案](./media/app-service-logic-deploy-from-vs/addnewproject.png)

3. 您現在必須選取 [邏輯應用程式]**** 或 [邏輯應用程式 + API 應用程式]****。 選取 [邏輯應用程式]**** 需要指向現有的 API。 如果您選取 **邏輯應用程式 + API 應用程式** 則您也可以建立新的空白 API 應用程式在相同的時間。
    ![選取 Azure 範本](./media/app-service-logic-deploy-from-vs/selectazuretemplate.png)

4. 選取 [**範本**] 之後，按一下 [**確定**]。

邏輯應用程式現在會加入至方案。 您應該會看到 [方案總管] 中的部署:
![部署](./media/app-service-logic-deploy-from-vs/deployment.png)

## 設定邏輯應用程式

建立專案後，您可以在 Visual Studio 內編輯邏輯應用程式的定義。 在方案總管中，按一下 JSON 檔案。 您會看到預留位置定義，您可以在其中填入應用程式的邏輯。

建議在整個定義中都使用**參數**。 當您想同時部署至開發與生產環境時，這樣會很有用。 在此情況下，您應該將環境專用的所有組態放入 `指示詞` 檔案，而不是實際字串參數。

目前，Visual Studio 沒有內建的 JSON 設計工具，如果想要使用圖形化介面 (而不是撰寫 JSON)，您將需要使用 Azure 入口網站。

如果您先前建立的邏輯應用程式在 Azure 入口網站，而現在想要加入原始檔控制簽入，有三種方法可以完成這項作業:
- 移至 **程式碼檢視** 在入口網站和複製定義。
- 使用邏輯應用程式 [REST API](https://msdn.microsoft.com/library/azure/dn948510.aspx) 以取得定義。
- 使用 [Azure 資源管理員 powershell](../powershell-azure-resource-manager.md),  ，尤其是 [`Get-azureresource` 命令] (https://msdn.microsoft.com/library/dn654579.aspx) 下載定義。

## 部署邏輯應用程式

最後，設定應用程式之後，只要幾個步驟，就能從 Visual Studio 直接部署。

1. 以滑鼠右鍵按一下 [方案總管] 中的部署，並移至 **部署** > **新增部署...**
    ![新增部署](./media/app-service-logic-deploy-from-vs/newdeployment.png)

2. 系統會提示您登入 Azure 訂用帳戶。

3. 現在您需要選擇您想要部署邏輯應用程式的資源群組的詳細資料。 
    ![部署到資源群組](./media/app-service-logic-deploy-from-vs/deploytoresourcegroup.png)

    務必為資源群組選取正確的範本和參數檔 (例如，如果是部署至生產環境，請選擇生產參數檔)。

4. 部署的狀態會出現在 **輸出** 視窗 (您可能需要選擇 **Azure 佈建**。 
    ![輸出](./media/app-service-logic-deploy-from-vs/output.png)

未來，您可以在原始檔控制中修改邏輯應用程式，並利用 Visual Studio 來部署新的版本。 請注意，如果您直接在 Azure 入口網站中修改定義，則下次從 Visual Studio 部署時會覆寫那些變更。

若不想使用 Visual Studio，但仍想要使用工具來部署應用程式邏輯，從原始檔控制，您可以使用 [API](https://msdn.microsoft.com/library/azure/dn948510.aspx) 或 [Powershell](../powershell-azure-resource-manager.md) 直接來自動化部署。




