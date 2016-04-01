<properties
    pageTitle="教學課程：Azure Active Directory 與 ABa Sainsburys Connect 整合 | Microsoft Azure" 
    description="了解如何使用 ABa Sainsburys Connect 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 ABa Sainsburys Connect 整合

本教學課程的目的是要示範 Azure 與 Aba Sainsburys Connect 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Aba Sainsburys Connect 單一登入的訂用帳戶

完成本教學課程之後, 您已指派給 Aba Sainsburys 連接的 Azure AD 使用者將能夠登入您 Aba Sainsburys 連接公司網站 （服務提供者起始登入），在應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Aba Sainsburys Connect 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807723.png "Scenario")
##啟用 Aba Sainsburys Connect 的應用程式整合

本節的目的是概述如何啟用 Aba Sainsburys Connect 的應用程式整合。

###若要啟用 Aba Sainsburys Connect 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Aba Sainsburys 連接**。

    ![Aba Sainsburys Connect](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807724.png "Aba Sainsburys Connect")

7.  在 [結果] 窗格中，選取 **Aba Sainsburys 連接**, ，然後按一下 [ **完成** 加入應用程式。

    ![Aba Sainsburys Connect](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807725.png "Aba Sainsburys Connect")
##設定單一登入

本節的目的是概述如何依據 SAML 通訊協定來使用同盟，讓使用者能夠以自己的 Azure AD 帳戶在 Aba Sainsburys Connect 中進行驗證。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Aba Sainsburys 連接** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807726.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 Aba Sainsburys 連接** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807727.png "Configure Single Sign-On")

3.  在 **設定應用程式設定** 頁面上，執行下列步驟 ︰

    ![設定 App 設定](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807728.png "Configure App Settings")

    1.  在 **登入 URL** 文字方塊中，輸入您的使用者用於登入您 Aba Sainsburys 連接的應用程式使用的 URL (例如 ︰ *https://myaba.co.uk/client-access/sainsburys/saml.php*)。
    2.  按一下 [ **下一步**

4.  在 **在 Aba Sainsburys 連接設定單一登入** ] 頁面上，若要下載中繼資料中，按一下 [ **下載中繼資料**, ，然後儲存您的電腦上的中繼資料檔案。

    ![設定單一登入](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807729.png "Configure Single Sign-On")

5.  將下載的中繼資料檔傳送給 Aba Sainsburys Connect 支援小組。

    >[AZURE.NOTE] Aba Sainsburys 連接支援小組必須執行實際的 SSO 設定。
    當您的訂用帳戶啟用 SSO 之後，您會收到通知。

6.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807730.png "Configure Single Sign-On")
##設定使用者佈建

若要讓 Azure AD 使用者能夠登入 Aba Sainsburys Connect，必須將他們佈建到 Aba Sainsburys Connect。  
Aba Sainsburys Connect 的使用者帳戶必須由 Aba Sainsburys Connect 支援小組建立。

>[AZURE.NOTE] 您可以使用任何其他 Aba Sainsburys 連接使用者帳戶建立工具或 Api 所提供 Aba Sainsburys 連接來佈建 Azure Active Directory 使用者帳戶。

##指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派到 Aba Sainsburys Connect，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Aba Sainsburys 連接 * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC807731.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-aba-sainsburys-connect-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。


