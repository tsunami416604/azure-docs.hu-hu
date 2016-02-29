<properties 
   pageTitle="使用 Visual Studio 的已連接服務加入行動服務 | Microsoft Azure"
   description="使用 Visual Studio 的加入已連接服務對話方塊加入行動服務"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="tlee" />
<tags 
   ms.service="visual-studio-online"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="mobile"
   ms.date="08/12/2015"
   ms.author="tarcher" />

# 使用 Visual Studio 已連接服務加入行動服務

使用 Visual Studio 2015，您可以連接到 Azure 行動服務使用 **加入已連接服務** ] 對話方塊。 您可以從任何 C# 用戶端應用程式、任何 JavaScript 應用程式或跨平台 Cordova 應用程式連接。 連接後，您可以建立和存取資料、建立自訂 API 和排程的工作，或加入推播通知的支援。  已連接服務作業會加入所有適當的參考和連接程式碼。 您也可以利用包含各種熱門身分識別配置 (例如 Azure AD、Facebook、Twitter 和 Microsoft 帳戶) 的內建驗證支援。

## 支援的專案類型

>[AZURE.NOTE] 在 Visual Studio 2015 中，不支援使用 [新增連接的服務] 對話方塊中，將 Azure 行動服務加入至 Windows 通用 (Windows 10) 的專案。 使用 NuGet 封裝管理員為您的專案安裝適當的封裝，即可加入 Azure 行動服務。

您可以使用 [已連接服務] 對話方塊來連接到 Azure Mobile Services 中的下列專案類型。

- .NET Windows 8.1 市集、電話和通用應用程式專案

- JavaScript Windows 8.1 市集、電話和通用應用程式專案

- 使用 Visual Studio Tools for Apache Cordova 建立的專案


## 使用 [加入已連接服務] 對話方塊連接到 Azure 行動服務

1. 確定您具有 Azure 帳戶。 如果您沒有 Azure 帳戶，您可以註冊 [免費試用版](http://go.microsoft.com/fwlink/?LinkId=518146)。

1. 開啟 **新增連接的服務** 對話方塊。
 - .NET 應用程式，Visual Studio 中開啟您的專案中，開啟內容功能表 **參考** 在 [方案總管] 節點，然後選擇 [ **加入已連接服務**
 
        ![Connecting to Azure Mobile Service](./media/vs-azure-tools-connected-services-add-mobile-services/IC797635.png)

 - Apache Cordova 應用程式專案中，開啟您的專案在 Visual Studio 方案總管] 中開啟專案節點的內容功能表，然後選擇 [ **加入已連接服務**。

1. 在 **加入已連接服務** 對話方塊方塊中，選擇 [ **Azure 行動服務**, ，然後選擇 [ **設定** ] 按鈕。 如果您尚未登入 Azure，系統可能會提示您這麼做。

    ![加入 Azure 行動服務](./media/vs-azure-tools-connected-services-add-mobile-services/IC797636.png)

1. 在 **Azure 行動服務** 對話方塊方塊中，選擇現有的行動服務，如果您有一個。 如果您需要建立新的 Azure 行動服務，請遵循下面的程序。 否則請跳到下一步。

    若要建立新的行動服務帳戶:
    1. 選擇 [* * 建立服務 * * 對話方塊底部的連結。
        ![加入新的已連接行動服務](./media/vs-azure-tools-connected-services-add-mobile-services/IC797637.png)




    2. On the **Create Mobile Service** dialog box, you can choose a JavaScript backend mobile service, or a .NET backend mobile service from the **Runtime** drop-down list. 
  
        ![Creating a mobile service](./media/vs-azure-tools-connected-services-add-mobile-services/IC797638.png)

        A JavaScript backend service is simple and powerful. If you create a JavaScript backend mobile service, the server-side JavaScript code is stored in the cloud, but you can edit server scripts by using Server Explorer, or the Azure management portal. 

        A .NET backend mobile service gives you the full power and flexibility of Web API and Entity Framework. If you create a .NET backend mobile service, a project is created for you and added to your solution. 

    1. Choose the **Region** where you want the mobile service, and then enter a user name and password for the server.
 
    1. After you've entered all the required information, choose the **Create** button to create the mobile service.
    2. The new mobile service should appear in the service list on the **Azure Mobile Services** dialog box. Choose the new mobile service in the list and then choose the **Add** button to add the service to your project.
    

1. 檢閱所顯示的 [開始使用] 頁面並了解您的專案修改方式。 每當您加入已連接服務時，您的瀏覽器就會出現 [開始使用] 頁面。 您可以檢閱建議的後續步驟和程式碼範例，或切換到 [發生什麼情形] 頁面以查看哪些參考已加入至您的專案，以及您的程式碼和組態檔的修改方式。

1. 使用程式碼範例做為指南，開始撰寫程式碼以存取您的行動服務！

## 您的專案修改方式

Visual Studio 修改您的專案的方式視專案類型而定。 C# 用戶端應用程式，請參閱 [發生了什麼事 – C# 專案](http://go.microsoft.com/fwlink/p/?LinkId=513119)。 JavaScript 用戶端應用程式，請參閱 [發生了什麼事 – JavaScript 專案](http://go.microsoft.com/fwlink/p/?LinkId=513120)。 Cordova 應用程式，請參閱 [發生了什麼事 – Cordova 專案](http://go.microsoft.com/fwlink/p/?LinkId=513116)。


##後續步驟

提出問題並取得協助︰ 

 - [MSDN 論壇：Azure 行動服務](https://social.msdn.microsoft.com/forums/azure/home?forum=azuremobile)

 - [Microsoft Azure 團隊部落格上的 Azure 行動服務](http://azure.microsoft.com/blog/topics/mobile/)

 - [azure.microsoft.com 上的 Azure 行動服務](http://azure.microsoft.com/services/mobile-services/)

 - [azure.microsoft.com 上的 Azure 行動服務文件](http://azure.microsoft.com/documentation/services/mobile-services/)




