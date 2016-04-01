<properties 
    pageTitle="管理 API 應用程式" 
    description="了解如何使用 Azure 入口網站和 Visual Studio 伺服器總管來管理 Azure App Service API 應用程式。" 
    services="app-service\api" 
    documentationCenter="" 
    authors="tdykstra" 
    manager="wpickett" 
    editor="jimbe"/>

<tags 
    ms.service="app-service-api" 
    ms.workload="web" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na"
    ms.topic="article" 
    ms.date="12/04/2015" 
    ms.author="tdykstra"/>

# 管理 Azure App Service 中的 API 應用程式

[AZURE.INCLUDE [app-service-api-v2-note](../../includes/app-service-api-v2-note.md)]

本文示範如何使用 [Azure 預覽入口網站](https://portal.azure.com/) 執行 API 應用程式管理和監視工作。 以下是一些您可以執行的工作：

- 設定驗證 
- 啟用自動調整
- 檢視記錄檔
- 請查看提出多少要求，以及查看 API 應用程式使用多少資料。
- 備份 API 應用程式以及建立警示
- 設定角色存取安全性

本文也會說明如何在 Visual Studio 的 [伺服器總管] 視窗中進行某些管理工作。

## Azure 預覽入口網站中的 API 應用程式和閘道器刀鋒視窗

在 Azure App Service API 應用程式是 [web 應用程式](../app-service-web/app-service-web-overview.md) 具有其他用於裝載 web 服務的功能。 在 Azure 入口網站中，沒有 **API 應用程式** 刀鋒視窗管理 API 特定功能，和 **API 應用程式主機** 管理基礎 web 應用程式的刀鋒視窗。 

每個含有至少一個 API 應用程式的資源群組也包含  *閘道*。 閘道器可做為 Proxy，處理驗證以及資源群組中所有 API 應用程式的其他管理功能。 如同 API 應用程式，閘道是 web 應用程式具備額外功能，因此也有兩個入口網站刀鋒視窗可供管理閘道 ︰ **閘道** 閘道特定功能的分頁和 **閘道器主機** 管理基礎 web 應用程式的刀鋒視窗。

### 您只能在 API 應用程式刀鋒視窗上執行的工作

您使用 **API 應用程式** 刀鋒視窗中執行下列工作 ︰

- 設定存取層級-按一下 **設定 > 應用程式設定**。 預設值是內部，這表示只有在相同資源群組中的 API 應用程式才可以呼叫 API 應用程式。 如需詳細資訊，請參閱 [保護 API 應用程式](app-service-api-dotnet-add-authentication.md)。   
- 設定更新原則-按一下 **設定 > 應用程式設定**。 預設值是 **上**。 這表示當新版的 API 應用程式發佈至 Marketplace 時，您的 API 應用程式將會自動更新為新版本 (如果這不是重大變更)。  
- 設定驗證的傳出呼叫 API 應用程式中-按一下 **設定 > 驗證**。  如果 API 應用程式呼叫需要驗證的外部服務，則會在此輸入必要的設定值。 例如，Dropbox 連接器需要用戶端識別碼和用戶端密碼來存取 Dropbox 服務。
- 設定 [RBAC](../role-based-access-control-configure.md) -按一下 **設定 > 使用者**。 您在此設定的使用者存取決定只有誰可以存取 API 應用程式特定功能。 若要設定 web 應用程式功能的 RBAC，使用 **API 應用程式主機** 刀鋒視窗。 您通常會想要讓 API 應用程式和 API 應用程式主機的 RBAC 設定保持同步。 如果您允許某人存取 API 應用程式，但不 API 應用程式主機，他們將無法使用的功能 **API 應用程式** 實際屬於 API 應用程式主機刀鋒視窗。
- 檢視 API 定義-按一下 **API 定義** 中 **摘要** 一節，以查看 API 應用程式所公開的方法清單。
- [安裝混合式連線管理員](../app-service-logic/app-service-logic-hybrid-connection-manager.md)。 Hybrid Connection Manager 可讓您連線至內部部署系統 (例如 SQL Server 或 SAP)。 此混合式連線使用 Azure 服務匯流排連線並控制 Azure 資源與內部部署資源之間的安全性。

### 您可以在 API 應用程式刀鋒視窗和 API 應用程式主機刀鋒視窗上執行的工作 

 **API 應用程式** 刀鋒視窗可讓您執行許多有關基礎 web 應用程式的工作。 例如，您可以執行下列工作：

* 停止、啟動和重新啟動裝載 API 應用程式的 Web 應用程式。  
- 選取 **要求和錯誤** ，加入包括已知 HTTP 錯誤代碼的不同效能度量，例如 200、 400 或 500 HTTP 狀態碼。
- 請查看回應時間、對 API 應用程式提出多少要求，以及有多少輸入資料和輸出資料。 
- 如果度量值超出您選擇的臨界值，請建立電子郵件警示。 
- 請參閱多少 **CPU** 是由使用 API 應用程式，檢閱目前 **使用量配額** MB，以及查看根據成本層的最大資料使用量。
- 請參閱 **預估的花費**  來判斷執行 API 應用程式的潛在成本。
- 檢視應用程式記錄檔和其他 IIS 記錄檔 (包括 Web 伺服器記錄檔和 FREB 記錄檔)。
- 選取 **處理程序** 開啟程序總管。 這會顯示 Web 執行個體和其屬性 (包括執行緒計數和記憶體使用量)。

也可以使用執行這些工作 **API 應用程式主機** 刀鋒視窗。  這就是為什麼兩個刀鋒視窗共用許多相同 UI 的緣故。 比方說， **停止**, ，**啟動**, ，和 **重新啟動** 按鈕 **API 應用程式** 分頁有完全相同的效果 **停止**, ，**啟動**, ，和 **重新啟動** 按鈕 **API 應用程式主機** 刀鋒視窗。 同樣地，提供的監視資訊 **API 應用程式** 刀鋒視窗會與相同 **API 應用程式主機** 刀鋒視窗中顯示。 

功能上提供 **API 應用程式** 刀鋒視窗不重複項目從 **API 應用程式主機** 刀鋒視窗會列在上一節。

### 您只能在 API 應用程式主機刀鋒視窗上執行的工作

您使用 **API 應用程式主機** 刀鋒視窗中的所有工作執行會執行任何 web 應用程式。

### 您只能在閘道器刀鋒視窗上執行的工作

您使用 **閘道** 刀鋒視窗中執行下列工作 ︰

- API 應用程式-按一下 [連入呼叫的驗證提供者設定 **設定 > 識別**。 如果閘道器需要先驗證使用者，才能允許使用者呼叫資源群組中的 API 應用程式，則會在此輸入必要的設定值。 如需詳細資訊，請參閱 [設定和測試 SaaS 連接器，在 Azure App Service 中](app-service-api-connnect-your-app-to-saas-connector.md)。 
- 設定 [RBAC](../role-based-access-control-configure.md) -按一下 **設定 > 使用者**。 您在此設定的使用者存取決定只有誰可以存取閘道器特定功能，而非與所有 Web 應用程式共用的功能。

### 您可以在閘道器刀鋒視窗和閘道器主機刀鋒視窗上執行的工作 

閘道器和閘道器主機刀鋒視窗與 API 應用程式和 API 應用程式主機刀鋒視窗共用相同的 UI。

### 您只能在閘道器主機刀鋒視窗上執行的工作

您使用 **閘道器主機** 刀鋒視窗中的所有工作執行會執行任何 web 應用程式。

## <a id="navigate"></a>如何巡覽至 API 應用程式和閘道器分頁 

其中一種方式取得 **API 應用程式** 分頁是透過 Azure 入口網站的瀏覽功能。  在入口網站的首頁上，按一下 [ **瀏覽 > API 應用程式** 來查看所有可管理 API 應用程式。 

![](./media/app-service-api-manage-in-portal/browse.png)

![](./media/app-service-api-manage-in-portal/apiappslist.png)

### 瀏覽至 API 應用程式刀鋒視窗

當您按一下 [使用中的資料列 **API 應用程式** 清單刀鋒視窗中，在入口網站會顯示 **API 應用程式** 刀鋒視窗。

![](./media/app-service-api-manage-in-portal/apiappblade.png)

### 瀏覽至 API 應用程式主機刀鋒視窗

若要取得 **API 應用程式主機** 刀鋒視窗中，按一下 [ **API 應用程式主機** 中 **API 應用程式** 刀鋒視窗。

![](./media/app-service-api-manage-in-portal/apiappbladetohost.png)

![](./media/app-service-api-manage-in-portal/apiapphostbladenocallouts.png)

### 瀏覽至閘道器刀鋒視窗

若要取得 **閘道** 刀鋒視窗中，按一下 [ **閘道** 中連結 **API 應用程式** 刀鋒視窗。
   
![](./media/app-service-api-manage-in-portal/apiappbladegotogateway.png)

![](./media/app-service-api-manage-in-portal/gatewaybladenocallout.png)

### 瀏覽至閘道器主機刀鋒視窗

若要取得 **閘道器主機** 刀鋒視窗中，按一下 [ **閘道器主機** 中連結 **閘道** 刀鋒視窗。
   
![](./media/app-service-api-manage-in-portal/gatewaybladetohost.png)

![](./media/app-service-api-manage-in-portal/gatewayhost.png)

## 在 Visual Studio 的伺服器總管中存取 API 應用程式

在 **伺服器總管** 在 Visual Studio 啟動遠端偵錯工作階段、 檢視串流記錄檔，然後按一下入口網站中開啟 API 應用程式刀鋒視窗的功能表項目。

若要在伺服器總管] 中的 API 應用程式，請按一下 [ **Azure > App Service > [資源群組名稱] > [API 應用程式名稱]**, ，如下圖所示。

![](./media/app-service-api-manage-in-portal/se.png)

## 後續步驟

本文已說明如何使用 Azure 入口網站執行 API 應用程式的管理工作。 您安裝從 API 應用程式組件庫的 API 應用程式，請參閱 [管理和監視內建 API 應用程式和連接器](../app-service-logic/app-service-logic-monitor-your-connectors.md)。

如需如何使用命令列管理 API 應用程式資訊，請參閱的文章 **自動化** 區段 （在寬瀏覽器視窗） 的左邊出現的功能表或上方的 （在窄瀏覽器視窗）。


