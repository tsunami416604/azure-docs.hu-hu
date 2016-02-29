<properties
    pageTitle="在 Azure RemoteApp 中發佈 App | Microsoft Azure"
    description="了解如何在 Azure RemoteApp 中發佈應用程式和資源。"
    services="remoteapp"
    documentationCenter=""
    authors="lizap"
    manager="mbaldwin" />

<tags
    ms.service="remoteapp"
    ms.workload="tbd"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/12/2015"
    ms.author="elizapo" />


# 如何在 RemoteApp 中發佈應用程式

建立 RemoteApp 收藏之後，您必須發佈想供使用者使用的應用程式或資源。 隨您的訂閱提供的範本映像預設只會發佈幾個應用程式，若要共用其他應用程式，您必須發佈它們。

> [AZURE.NOTE] 您需要更新應用程式嗎? 您將需要 [更新映像](remoteapp-update.md) 第一次。

在 **發佈** 在入口網站] 索引標籤上，按一下 **發行**。 您可以將應用程式從範本映像的 **啟動** 功能表提供的範本映像上安裝應用程式的路徑。 如果您選擇從新增 **啟動** ] 功能表上，選擇要從清單中發佈應用程式。 如果您選擇提供應用程式的路徑，請輸入應用程式的名稱和應用程式路徑。 請在路徑中使用變數，例如 "%systemdrive%" 而非 "c:\"。

> [AZURE.NOTE] 如果您想要加入您的應用程式從 **開始** ] 功能表上，您必須擁有 *該應用程式來新增 **啟動** 您範本映像上的功能表。*否則，RemoteApp 只會看到什麼 *是* 上 **啟動** ] 功能表上，而且您會覺得很困惑。 

>若要確定您的應用程式是以 **開始** ] 功能表上，將捷徑檔案- **.lnk** -%systemdrive%\ProgramData\Microsoft\Windows\Start Menu\Programs 資料夾內。

> 如果您忘記將應用程式以 **開始** ] 功能表中建立範本時選擇新增路徑到應用程式。 (或者重新建立您的範本映像，但是這會比較費工。)


 
