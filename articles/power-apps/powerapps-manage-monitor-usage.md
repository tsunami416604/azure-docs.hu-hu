<properties
    pageTitle="在 PowerApps Enterprise 中檢查您的 app 使用情況 | Microsoft Azure"
    description="在 Azure 入口網站中查看所有 app、API、使用者、app 要求，並更新權限"
    services=""
    suite="powerapps"
    documentationCenter="" 
    authors="MandiOhlinger"
    manager="dwrede"
    editor=""/>

<tags
   ms.service="powerapps"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na" 
   ms.date="11/25/2015"
   ms.author="litran"/>



# 管理及保護您的 PowerApps

您建立 app 服務環境，然後新增 API 及其連線。 現在您組織中的使用者便可以開始取用這些 API 和連線。 您也可以管理在您組織中建立的所有 app。 這些選項包含︰

- 查看您 app 服務環境環境內的各種不同 app，包括 PowerApps、Web app、邏輯 app、行動 app 等。
- 查看特定 app 使用的所有 API。
- 檢視及管理使用者對 app 服務環境內 app 的存取權。
- 檢視及管理使用者對 API 及其連線的存取權。

請記住，您的 app 服務環境是您要新增其他 app (包括 Web app 與邏輯 app) 的環境。 接著，您可以開啟 PowerApps Enterprise 來查看及管理這些 app。


## 新增 PowerApps 系統管理員

PowerApps Enterprise 已啟用且可供使用，您可以新增系統管理員，以及監視您 app 服務環境內的其他 app。

1. 在 [Azure 入口網站](https://portal.azure.com/), ，開啟 **PowerApps**。
2. 選取 [Settings] (設定)****。
3. 在 **設定**, ，請選取 **Admin**:  
![][1]
4. 在 [Users] (使用者)**** 中，選取 [Add] (新增)****。
5. 選取 **擁有者** 角色:  
![][2]
    > [AZURE.IMPORTANT] 如果您要指派某個使用者做為「PowerApps 系統管理員」，請務必選取 [Owner] (擁有者)**** 角色。 其他列出的使用者並無法賦予使用者可管理 PowerApps 的完整存取權。 

6. 選取您的使用者或群組。
7. 選取 [OK] (確定)**** 以完成步驟。

當您將「系統管理員」新增到 PowerApps Enterprise 時，您新增為系統管理員的使用者和群組可以：

- 新增其他使用者做為 PowerApps 系統管理員。
- 管理所有 app 及其使用者存取權。
- 無法變更計費。

> [AZURE.IMPORTANT] 「PowerApps 系統管理員」必須在獲得 app 服務環境之資源群組上的「擁有者」角色之後，才能對「App Service 環境」進行變更。 若要這樣做，請參閱 [這篇文章](powerapps-get-started-azure-portal.md)。

獲得 app 服務環境之資源群組上的「擁有者」角色之後，PowerApps 系統管理員也可以：

- 建立和設定 API 及其連線。
- 對 PowerApps 設定 (包括 app 服務環境) 進行變更。
- 新增其他使用者和群組，並使其能夠角色和權限的 Api，其連線及 app service 環境。


## 管理您的 PowerApps 及其他類型的 app

啟用 PowerApps 和您的 app 服務環境之後，您可以將其他 app (例如 Web app 與邏輯 app) 新增到相同的 app 服務環境。 執行這項操作之後，這些 app 就會與在 PowerApps 中建立的 app 一起列在 [All apps] (所有 app)** 底下。 您可以按一下每一種類型的 app 來瀏覽 app。

### 檢視及管理您的 PowerApps

1. 在 [Azure 入口網站](https://portal.azure.com/), ，開啟 **PowerApps**。
2. 從 **所有應用程式** 磚中，選取 **PowerApps**:  
![][3]
3. 選取一個 app 來檢視該 app 的詳細資料，包括：
    - App 使用的 API
    - 具有 app 存取權的使用者和群組
    - App 的分析 (即將推出)

#### 新增 app

您可以透過 Azure 入口網站新增 app。 目前，請移至 [PowerApps 入口網站](http://go.microsoft.com/fwlink/p/?LinkId=715583)。

#### 刪除在 PowerApps 中建立的 app

身為「PowerApps 系統管理員」，您可以刪除任何 app，包括在 PowerApps 中建立的 app 及您 app 服務環境中其他類型的 app。 若要刪除您的應用程式，請選取 **所有應用程式** 磚，選取您的應用程式，然後選取 **刪除**:    
![][4]


#### 授與使用者或群組可使用 app 的存取權

身為 PowerApps 系統管理員，您可以對 PowerApps 新增或移除使用者和群組。

1. 在 [Azure 入口網站](https://portal.azure.com/), ，開啟 **PowerApps**。
2. 在 **所有應用程式** 磚中，選取 **PowerApps**:  
![][3]
3. 選取 app，例如 [Service Desk] (服務台)****。
4. 在 **設定**, ，請選取 **應用程式的使用者存取**:  
![][5]
5. 選取 [Add] (新增)**** 來新增使用者或群組。
6. 選取角色：
    - Can Edit (可編輯)
    - Can View (可檢視)
7. 選取使用者或群組。
8. 選取 [OK] (確定)**** 以完成步驟。

### 檢視及管理您的邏輯 app

1. 在 [Azure 入口網站](https://portal.azure.com/), ，開啟 **PowerApps**。
2. 在 **所有應用程式** 磚中，選取 **邏輯應用程式**:  
![][8]
3. 選取一個邏輯 app 來檢視該 app 的詳細資料。 請確定您選取 PowerApps 列出正確的邏輯應用程式的修正訂用帳戶:  
![][7]
    > [AZURE.IMPORTANT] 在公用預覽中，您可能會看到瀏覽刀鋒視窗中的邏輯 app 計數與主要 PowerApps 刀鋒視窗上顯示的計數不一致。 這是預期行為。 入口網站顯示的是所有虛擬主機方案的所有邏輯 app，並未篩選針對 PowerApps 部署的 app 服務環境下的邏輯 app。 在未來的更新中將會修正這個行為。

    * * 若要深入了解邏輯應用程式，以及如何管理它們，請參閱 [這些指示](https://azure.microsoft.com/documentation/services/app-service/logic/)。 * *

### 檢視及管理您的 Web app

1. 在 [Azure 入口網站](https://portal.azure.com/), ，開啟 **PowerApps**。
2. 在 **所有應用程式** 磚中，選取 **Web 應用程式**:  
![][9]

    * * 若要深入了解 web 應用程式，以及如何管理它們，請參閱 [這些指示](https://azure.microsoft.com/documentation/services/app-service/web/)。 * *

### 檢視及管理您的行動 app

1. 在 [Azure 入口網站](https://portal.azure.com/), ，開啟 **PowerApps**。
2. 在 **所有應用程式** 磚中，選取 **行動應用程式**:  
![][10]

    * * 行動應用程式與管理方式的詳細資訊，請參閱 [這些指示](https://azure.microsoft.com/documentation/services/app-service/mobile/)。 * *


## 檢閱安全性選項

視您要執行的動作而定，會使用不同的安全性方法。 以下是您需要知道的事項：

- **Subscription administrator** (訂用帳戶管理員)：這些管理員控制計費，並負責幫您的公司註冊 PowerApps Enterprise。 只有「訂用帳戶管理員」可以要求在您公司的 Azure 訂用帳戶內啟用 PowerApps。

- **Runtime user access** (執行階段使用者存取權)：有三種不同類型的執行階段使用者存取權：
    - **App user access** (App 使用者存取權)：此權限控制 app 的使用者是否「可編輯」**app 或「可檢視」** app。
    - **API user access** (API 使用者存取權)：此權限控制執行階段存取權。 如果使用者具有此權限，就可以在其 app 中使用 API。 使用者要不就是具有權限在執行階段使用 API，要不就是沒有權限。
    - **Connection user access** (連線使用者存取權)：*Can view* (可檢視) 和 *Can edit* (可編輯) 是可供連線使用的執行階段使用者權限。 當您新增 API (或連線設定檔)，並建立它的連線時，您授與使用者和群組這些特定的權限:  
        ![][6]

        For example, you can give the Sales group within your company *Can edit* permission to a connection of a SQL connector API. User with *Can edit* permission will be able to use the connection in their apps as well as edit the connection configuration. User with *Can view* permission will be able to use the connection in their apps but can't modify the connection configuration such as connection string. 


- **角色型存取控制** (RBAC)：許多 Azure 方案都使用角色型存取控制來決定哪個使用者可以執行什麼動作。 在 PowerApps 中，RBAC 用於幾個地方：
    - 當您第一次進入 PowerApps 入口網站時，您可以新增及管理應該是 PowerApps 系統管理員的使用者。
    - 當您建立 app 服務環境時，您可以將使用者或群組新增到 PowerApps，也可以從 PowerApps 移除使用者或群組。 例如，您可以將公司內特定的「系統管理員」群組新增到 [Owners] (擁有者)** 角色；這可讓他們建立 API 和連線。 接著，這些 API 和連線會新增到在 PowerApps 中建立的 app。
    - 將使用者新增到 app (例如 Web app、邏輯 app 或行動 app) 時， 您可以為這些使用者選擇角色。

        Adding users and assigning roles is just like using [Role-based access control](../role-based-access-control-configure.md) within Azure. Some roles include:   

     角色| 說明
    --- | ---
     參與者| 可管理所有事項，但將存取權授予使用者除外。
     讀取者| 可以檢視所有事項，但無法進行任何變更。
     擁有者| 可以管理所有事項及將存取權授與使用者。


藉由使用這些角色，您便可以將 Twitter 日常 app 的 **Can View** (可檢視) 權限授與 userA，以及將 ShuttleBus app 的 **Can Edit** (可編輯) 權限授與 userB。 您可以將所有 API 的存取權授與 userB。 您實際上可以再細分這些權限，或是為每個使用者新增一個特定的角色。 這實際上取決於您的業務需求。


## 摘要和後續步驟

在本主題中，您已閱讀有關可管理您 PowerApps 的各種不同選項，以及在 PowerApps 內實作的安全性方法。

現在您既已設定好您的 Azure 入口網站體驗，讓我們開始建立您的 app。 以下是一些適合的入門資源：

- [從 PowerApps 中的範本建立應用程式](http://go.microsoft.com/fwlink/p/?LinkId=715536)
- [建立應用程式從 PowerApps 中的資料](http://go.microsoft.com/fwlink/?LinkId=715539)
- [在 PowerApps 從頭開始建立應用程式](http://go.microsoft.com/fwlink/p/?LinkId=715538)



[1]: ./media/powerapps-manage-monitor-usage/addadmin.png 
[2]: ./media/powerapps-manage-monitor-usage/selectrole.png 
[3]: ./media/powerapps-manage-monitor-usage/PowerApps.png 
[4]: ./media/powerapps-manage-monitor-usage/deleteapp.png 
[5]: ./media/powerapps-manage-monitor-usage/appuseraccess.png 
[6]: ./media/powerapps-manage-monitor-usage/selectpermission.png 
[7]: ./media/powerapps-manage-monitor-usage/alllogicapps.png 
[8]: ./media/powerapps-manage-monitor-usage/logicapps.png 
[9]: ./media/powerapps-manage-monitor-usage/webapps.png 
[10]: ./media/powerapps-manage-monitor-usage/mobileapps.png 

