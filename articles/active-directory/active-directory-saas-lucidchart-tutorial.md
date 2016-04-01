<properties 
    pageTitle="教學課程：Azure Active Directory 與 Lucidchart 整合 | Microsoft Azure" 
    description="了解如何使用 Lucidchart 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Lucidchart 整合
  
本教學課程的目的是要示範 Azure 與 Lucidchart 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Lucidchart 單一登入的訂用帳戶
  
完成本教學課程之後, 您已指派給 Lucidchart 的 Azure AD 使用者將能夠登入位於您 Lucidchart 公司網站 （服務提供者起始登入），應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Lucidchart 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-lucidchart-tutorial/IC791183.png "Scenario")
##啟用 Lucidchart 的應用程式整合
  
本節的目的是要說明如何啟用 Lucidchart 的應用程式整合。

###若要啟用 Lucidchart 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-lucidchart-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-lucidchart-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-lucidchart-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-lucidchart-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Lucidchart**。

    ![應用程式庫](./media/active-directory-saas-lucidchart-tutorial/IC791184.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Lucidchart**, ，然後按一下 [ **完成** 加入應用程式。

    ![Lucidchart](./media/active-directory-saas-lucidchart-tutorial/IC791185.png "Lucidchart")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 Lucidchart 中進行驗證。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Lucidchart** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-lucidchart-tutorial/IC791186.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 Lucidchart** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-lucidchart-tutorial/IC791187.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **Lucidchart 登入 URL** 文字方塊中，輸入 URL，讓使用者中用來登入 Lucidchart 的應用程式 (例如: 「*https://chart2.office.lucidchart.com/saml/sso/azure*」)，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-lucidchart-tutorial/IC791188.png "Configure App URL")

4.  在 **Lucidchart 在設定單一登入** ] 頁面上，若要下載中繼資料中，按一下 [ **下載中繼資料**, ，然後將儲存在本機電腦上的資料檔案。

    ![設定單一登入](./media/active-directory-saas-lucidchart-tutorial/IC791189.png "Configure Single Sign-On")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Lucidchart 公司網站。

6.  在頂端功能表中，按一下 [ **小組**。

    ![小組](./media/active-directory-saas-lucidchart-tutorial/IC791190.png "Team")

7.  按一下 [ **應用程式 \ > 管理 SAML**。

    ![管理 SAML](./media/active-directory-saas-lucidchart-tutorial/IC791191.png "Manage SAML")

8.  在 **SAML 驗證設定** 對話方塊頁面上，執行下列步驟 ︰

    1.  選取 **啟用 SAML 驗證**, ，然後按一下 [ **選擇性**。
        ![SAML 驗證設定](./media/active-directory-saas-lucidchart-tutorial/IC791192.png "SAML Authentication Settings")
    2.  在 **網域** 文字方塊中，輸入您的網域，然後按一下 **變更憑證**。
        ![變更憑證](./media/active-directory-saas-lucidchart-tutorial/IC791193.png "Change Certificate")
    3.  開啟下載的中繼資料檔案，複製內容，然後將它貼入 **上傳中繼資料** 文字方塊。
        ![上傳中繼資料](./media/active-directory-saas-lucidchart-tutorial/IC791194.png "Upload Metadata")
    4.  選取 **自動新增使用者到團隊**, ，然後按一下 [ **儲存變更**。
        ![儲存變更](./media/active-directory-saas-lucidchart-tutorial/IC791195.png "Save Changes")

9.  選取單一登入設定確認，然後再按 **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-lucidchart-tutorial/IC791196.png "Configure Single Sign-On")
##設定使用者佈建
  
沒有動作項目可讓您設定 Lucidchart 使用者佈建。  
當指派的使用者嘗試使用存取面板登入 Lucidchart 時，Lucidchart 會檢查使用者是否存在。  
如果尚無可用的使用者帳戶，Lucidchart 會自動予以建立。
##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 Lucidchart，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Lucidchart * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-lucidchart-tutorial/IC791197.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-lucidchart-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

