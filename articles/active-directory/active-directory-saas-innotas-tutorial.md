<properties 
    pageTitle="教學課程：Azure Active Directory 與 Innotas 整合 | Microsoft Azure"
    description="了解如何使用 Innotas 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Innotas 整合
  
本教學課程的目的是要示範 Azure 與 Innotas 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Innotas 租用戶
  
完成本教學課程之後, 您已指派給 Innotas 的 Azure AD 使用者將能夠登入位於您 Innotas 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Innotas 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-innotas-tutorial/IC777331.png "Scenario")
##啟用 Innotas 的應用程式整合
  
本節的目的是要說明如何啟用 Innotas 的應用程式整合。

###若要啟用 Innotas 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-innotas-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-innotas-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-innotas-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-innotas-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Innotas**。

    ![應用程式庫](./media/active-directory-saas-innotas-tutorial/IC777332.png "Application gallery")

7.  在 [結果] 窗格中，選取 **Innotas**, ，然後按一下 [ **完成** 加入應用程式。

    ![Innotas](./media/active-directory-saas-innotas-tutorial/IC777333.png "Innotas")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 Innotas 中進行驗證。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Innotas** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-innotas-tutorial/IC777334.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 Innotas** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-innotas-tutorial/IC777335.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，於 **Innotas 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://\<tenant-name\>。Innotas.com*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-innotas-tutorial/IC777336.png "Configure app URL")

4.  在 **Innotas 在設定單一登入** ] 頁面上，若要下載中繼資料中，按一下 [ **下載中繼資料**, ，並且再將資料檔案在本機上做為 **c:\\InnotasMetaData.xml**。

    ![設定單一登入](./media/active-directory-saas-innotas-tutorial/IC777337.png "Configure single sign-on")

5.  將該中繼資料檔案轉寄給 Innotas 支援小組。 支援小組需要為您設定單一登入。

6.  選取單一登入設定確認，然後再按 **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-innotas-tutorial/IC777338.png "Configure single sign-on")
##設定使用者佈建
  
沒有動作項目可讓您設定 Innotas 使用者佈建。  
當已指派的使用者嘗試使用存取面板登入 Innotas 時，Innotas 會檢查使用者是否存在。  
如果尚無可用的使用者帳戶，Innotas 會自動予以建立。
##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要將使用者指派給 Innotas，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Innotas * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-innotas-tutorial/IC777339.png "Assign users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-innotas-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
