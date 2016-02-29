<properties 
    pageTitle="教學課程：Azure Active Directory 與 Jive 整合 | Microsoft Azure" 
    description="了解如何使用 Jive 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" 
    manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#教學課程：Azure Active Directory 與 Jive 整合

  
本教學課程的目的是要示範 Azure 與 Jive 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Jive 中的租用戶
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Jive 的應用程式整合
2.  設定使用者佈建

##啟用 Jive 的應用程式整合
  
本節的目的是要說明如何啟用 Jive 的應用程式整合。

###若要啟用 Jive 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-jive-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-jive-tutorial/IC700994.png "Applications")

4.  若要開啟 **應用程式庫**, ，按一下 [ **新增應用程式**, ，然後按一下 [ **加入我的組織使用的應用程式**。

    ![欲執行動作](./media/active-directory-saas-jive-tutorial/IC700995.png "What do you want to do?")

5.  在 **搜尋方塊**, ，型別 **Jive**。

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701001.png "Jive")

6.  在 [結果] 窗格中，選取 **Jive**, ，然後按一下 [ **完成** 加入應用程式。

    ![Jive](./media/active-directory-saas-jive-tutorial/IC701005.png "Jive")
##設定使用者佈建
  
本節的目的是要說明如何對 Jive 啟用 Active Directory 使用者帳戶的使用者佈建。  
在此程序中，您必須提供從 Jive.com 要求所需的使用者安全性權杖。
  
下列螢幕擷取畫面顯示 Azure AD 中相關的對話方塊範例：

![設定使用者佈建](./media/active-directory-saas-jive-tutorial/IC698794.png "Configure User Provisioning")

###若要設定使用者佈建，請執行下列步驟：

1.  在 Azure 管理入口網站中，在 **Jive** 應用程式整合頁面上，按一下 [ **設定使用者佈建** 開啟 **設定使用者佈建** ] 對話方塊。

2.  在 **輸入您的 Jive 認證，才能啟用自動使用者佈建** 頁面上，提供下列組態設定:

    1.  在 **Jive 管理使用者名稱** 文字方塊中，輸入 Jive 帳戶名稱具有 **系統管理員** Jive.com 指派中的設定檔。

    2.  在 **Jive 管理員密碼** 文字方塊中，輸入此帳戶的密碼。

    3.  在 **Jive 租用戶 URL** 文字方塊中，輸入 Jive 租用戶 URL。

        >[AZURE.NOTE] Jive 租用戶 URL 是您的組織所用來登入 Jive 的 URL。  
        一般而言，URL 具有下列格式: **www.\<organization\>.jive.com**。

    4.  按一下 [ **驗證** 來驗證您的組態。

    5.  按一下 [ **下一步** 按鈕來開啟 **確認** 頁面。

3.  在 **確認** 頁面上，按一下核取記號以儲存您的設定。
  
您現在可以建立測試帳戶，等待 10 分鐘，然後確認帳戶已同步至 Jive.com。


