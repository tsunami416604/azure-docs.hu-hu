<properties 
   pageTitle="使用 Visual Studio 建立 Azure 專案"
   description="使用 Visual Studio 建立 Azure 專案"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags 
   ms.service="multiple"
   ms.devlang="multiple"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="08/24/2015"
   ms.author="tarcher" />

# 使用 Visual Studio 建立 Azure 專案

Azure Tools for Visual Studio 提供可讓您建立 Azure 雲端服務的範本。 此工具也可協助您設定、偵錯雲端服務，以及將雲端服務部署至 Azure。

Azure 雲端服務解決方案包含下列專案類型：

- **Azure 專案** 
    
    Azure 專案與方案中的角色專案有關。 它還包含服務定義檔和服務組態檔。 服務定義檔可定義應用程式的執行階段設定，包括需要哪些角色、端點和虛擬機器大小。 服務組態檔可設定一個角色可以執行的執行個體數目，以及為角色定義的設定值。 如需有關這些設定的詳細資訊，請參閱 [How to ︰ 設定 Azure 雲端服務與 Visual Studio 的角色](https://msdn.microsoft.com/library/azure/hh369931.aspx)。

- **Web 角色專案**
 
    背景工作角色會執行背景處理。 背景工作角色可以與儲存體服務和其他網際網路服務進行通訊。 背景工作角色可以有任意數目的 HTTP、HTTPS 或 TCP 端點。

    - **ASP.NET Web 角色**, ，建置具有 web 前端的 ASP.NET 應用程式
    - **ASP.NET MVC5 Web 角色**
    - **ASP.NET MVC4 Web 角色**
    - **ASP.NET MVC3 Web 角色**
    - **WCF 服務 Web 角色**, ，用於建置 WCF 服務
    - **Silverlight 商務應用程式 Web 角色** （需要 Visual Studio 2012）

- **快取背景工作角色** 

    可提供應用程式專用快取的角色。

- **搭配服務匯流排佇列的背景工作角色** 

    服務匯流排佇列可提供與背景工作角色處理序進行通訊的訊息佇列功能。 如需詳細資訊，請參閱 [如何使用服務匯流排佇列](http://go.microsoft.com/fwlink/?LinkId=260560)。

## 在 Visual Studio 中建立 Azure 雲端服務專案

1. 以系統管理員身分啟動 Microsoft Visual Studio。

1. 在功能表列上選擇 [ **檔案**, ，**新增**, ，**專案**。

1. 在 **專案類型** ] 窗格中，選擇 [ **定域機組** 從 Visual C# 或 Visual Basic 專案範本節點。

1. 在 **範本** ] 窗格中，選擇 [  **Azure 雲端服務**。

1. 指定您想要用來開發專案的 .NET Framework 版本。

1. 輸入專案的名稱和位置以及方案的名稱。 選擇 **確定** ] 按鈕。

1. 在 **新的 Azure 專案** 對話方塊中，選擇您想要新增的角色，然後選擇向右箭號按鈕，將它們新增到您的方案。 您可以根據需求加入任意多個角色。

1. 若要重新命名已加入到專案的角色，將滑鼠游標停留在角色上 **新的 Azure 專案** 對話方塊方塊，然後選擇 **重新命名** 圖示右邊的角色。 您也可以為已加入方案的角色重新命名。




