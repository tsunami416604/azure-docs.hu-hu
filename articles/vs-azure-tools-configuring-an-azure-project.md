<properties
   pageTitle="使用 Visual Studio 設定 Azure 雲端服務專案 | Microsoft Azure"
   description="了解如何在 Visual Studio 中根據 Azure 雲端服務專案的需求來設定專案。"
   services="visual-studio-online"
   documentationCenter="na"
   authors="TomArcher"
   manager="douge"
   editor="" />
<tags
   ms.service="multiple"
   ms.devlang="dotnet"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="multiple"
   ms.date="09/29/2015"
   ms.author="tarcher" />

# 使用 Visual Studio 設定 Azure 雲端服務專案

您可以根據 Azure 雲端服務專案的需求來設定專案。 您可以設定下列類別的專案的屬性：

- **將雲端服務發佈至 Azure**

  您可以設定屬性以確保部署至 Azure 的現有雲端服務不會被意外刪除。

- **執行或偵錯本機電腦上的雲端服務**

  您可以選取一個要使用的服務組態，並指示是否要啟動 Azure 儲存體模擬器。

- **在建立雲端服務封裝時進行驗證**

  您可以決定將任何警告均視為錯誤，這樣便能確保雲端服務封裝部署時不會發生錯誤。 若您部署後發現有失敗情形發生，這會減少等候時間。

下圖顯示如何選取在本機執行或偵錯雲端服務時要使用的組態。 如圖所示，您可以從此視窗中設定您需要的任一專案屬性。

![設定 Microsoft Azure 專案](./media/vs-azure-tools-configuring-an-azure-project/IC713462.png)

## 設定 Azure 雲端服務專案

1. 若要設定雲端服務專案，從 **方案總管] 中**, ，開啟雲端服務專案的捷徑功能表，然後選擇 **屬性**。

  Visual Studio 編輯器中會顯示具有該雲端服務專案名稱的頁面。

1. 選擇 **開發** ] 索引標籤。

1. 若要確定您沒有不小心刪除的提示之前刪除現有的部署清單中的現有部署在 Azure 中，選擇 [ **True**。

1. 若要選取您想要使用當您執行或偵錯在本機，雲端服務的服務設定中 **服務組態** 清單中選擇服務組態。

  >[AZURE.NOTE] 如果您想要建立服務組態使用，請參閱 How to: 管理服務組態和設定檔。 如果您想要修改角色的服務組態，請參閱 [如何使用 Visual Studio 設定 Azure 雲端服務的角色](vs-azure-tools-configure-roles-for-cloud-service.md)。

1. 若要啟動 Azure 儲存體模擬器，當您執行或偵錯雲端服務在本機，在 **啟動 Azure 儲存體模擬器**, ，選擇 [ **True**。

1. 若要藉此確定如果有封裝驗證錯誤，無法發行 **將警告視為錯誤**, ，選擇 [ **True**。

1. 若要確定您的 web 角色使用相同連接埠每次啟動 IIS Express，在本機中 **使用 web 專案的連接埠**, ，選擇 [ **True**。 若要使用特定連接埠特定 web 專案，開啟 web 專案的捷徑功能表，選擇 [ **屬性** 索引標籤上，選擇 [ **Web** 索引標籤，然後變更連接埠號碼，在 **專案 Url** 中設定 **IIS Express** 一節。 例如，輸入 `http://localhost:14020` 做為專案 URL。

1. 若要儲存您對雲端服務專案的屬性的任何變更，請選擇 [ **儲存** 工具列上的按鈕。

## 後續步驟

若要深入了解如何在 Visual Studio 中設定 Azure 雲端服務專案，請參閱 [設定您的 Azure 專案中使用多個服務組態](vs-azure-tools-multiple-services-project-configurations.md)。

