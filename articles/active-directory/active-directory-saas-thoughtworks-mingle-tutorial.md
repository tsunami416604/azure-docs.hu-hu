<properties 
    pageTitle="教學課程：Azure Active Directory 與 Thoughtworks Mingle 整合 | Microsoft Azure" 
    description="了解如何使用 Thoughtworks Mingle 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
    services="active-directory" 
    authors="markusvi"  
    documentationCenter="na" manager="stevenpo"/>
<tags 
    ms.service="active-directory" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.tgt_pltfrm="na" 
    ms.workload="identity" 
    ms.date="10/22/2015" 
    ms.author="markvi" />

#教學課程：Azure Active Directory 與 Thoughtworks Mingle 整合
  
本教學課程的目的是要示範 Azure 與 Thoughtworks Mingle 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Thoughtworks Mingle 租用戶
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Thoughtworks Mingle 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785150.png "Scenario")

##啟用 Thoughtworks Mingle 的應用程式整合
  
本節的目的是要說明如何啟用 Thoughtworks Mingle 的應用程式整合。

###若要啟用 Thoughtworks Mingle 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **thoughtworks mingle**。

    ![應用程式庫](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785151.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Thoughtworks Mingle**, ，然後按一下 [ **完成** 加入應用程式。

    ![Thoughtworks Mingle](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785152.png "Thoughtworks Mingle")

##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶驗證至 Thoughtworks Mingle。  
您必須將憑證上傳至 Thoughtworks Mingle，這是程序的一部分。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站中，在 * * Thoughtworks Mingle * * 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785153.png "Configure single sign-on")

2.  在 **您希望使用者如何登入至 Thoughtworks Mingle** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785154.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，於 **Thoughtworks Mingle 租用戶 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*http://company.mingle.thoughtworks.com*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785155.png "Configure App URL")

4.  在 **在 Thoughtworks Mingle 設定單一登入** 頁面上，按一下 [下載中繼資料]，然後將它儲存在電腦上。

    ![設定單一登入](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785156.png "Configure single sign-on")

5.  登入您 **Thoughtworks Mingle** 系統管理員身分的公司網站。

6.  按一下 [ **管理員** 索引標籤，然後按 [下 **SSO 組態**。

    ![SSO 組態](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785157.png "SSO Config")

7.  在 **SSO 組態** 區段中，執行下列步驟:

    ![SSO 組態](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785158.png "SSO Config")

    1.  若要上傳中繼資料檔案，請按一下 [ **選擇檔案**。
    2.  按一下 [ **儲存變更**。

8.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785159.png "Configure single sign-on")

##設定使用者佈建
  
若要讓 AAD 使用者能夠登入，必須使用他們的 Azure Active Directory 使用者名稱將他們佈建到 Thoughtworks Mingle 應用程式。  
Thoughtworks Mingle 需以手動的方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  以系統管理員身分登入您的 Thoughtworks Mingle 公司網站。

2.  按一下 [ **設定檔**。

    ![第一個專案](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785160.png "Your First Project")

3.  按一下 [ **管理員** 索引標籤，然後再按一下 **使用者**。

    ![使用者](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785161.png "Users")

4.  按一下 [ **新使用者**。

    ![新使用者](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785162.png "New User")

5.  在 **新使用者** 對話方塊頁面上，執行下列步驟:

    ![新使用者](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785163.png "New User")

    1.  型別 **登入名稱**, ，**顯示名稱**, ，**選擇密碼**, ，**確認密碼** 您想要佈建到相關文字方塊之有效 AAD 帳戶。
    2.  做為 **使用者類型**, ，請選取 **完整使用者**。
    3.  按一下 [ **建立此設定檔**。

>[AZURE.NOTE] 您可以使用任何其他 Thoughtworks Mingle 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶 Thoughtworks Mingle 所提供。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要指派使用者給 Thoughtworks Mingle，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **Thoughtworks Mingle** 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC785164.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-thoughtworks-mingle-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

