<properties 
    pageTitle="我的 Cordova 專案 (Visual Studio 儲存體連接服務) 發生什麼狀況 | Microsoft Azure" 
    description="描述使用 Visual Studio 連接服務加入 Azure 行動服務之後，您的 Azure Cordova 專案發生什麼狀況 " 
    services="mobile-services" 
    documentationCenter="na" 
    authors="TomArcher" 
    manager="douge" 
    editor=""/>

<tags 
    ms.service="mobile-services" 
    ms.workload="mobile" 
    ms.tgt_pltfrm="na" 
    ms.devlang="multiple" 
    ms.topic="article" 
    ms.date="09/17/2015" 
    ms.author="tarcher"/>


# 使用 Visual Studio 連接服務加入 Azure 行動服務之後，我的 Azure Cordova 專案發生什麼狀況？

> [AZURE.SELECTOR]
> - 
> - 

## 加入參考

所有多重裝置混合應用程式隨附的 Azure 行動服務用戶端外掛程式已啟用。

## 行動服務的連接字串值

此檔案包含行動服務用戶端物件的初始化，類似於下列程式碼。

    var mobileServiceClient;
    document.addEventListener("deviceready", function() {
            mobileServiceClient = new WindowsAzure.MobileServiceClient(
            "<your mobile service name>.azure-mobile.net",
            "<insert your key>"
        );







