<properties 
    pageTitle="教學課程：Azure Active Directory 與 Jobscience 整合 | Microsoft Azure" 
    description="了解如何使用 Jobscience 搭配 Azure Active Directory 來啟用單一登入、自動化佈建及更多功能！" 
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

#教學課程：Azure Active Directory 與 Jobscience 整合
  
本教學課程的目的是要示範 Azure 與 Jobscience 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Jobscience 單一登入的訂用帳戶
  
完成本教學課程之後, 您已指派給 Jobscience 的 Azure AD 使用者將能夠登入位於您 Jobscience 公司網站 （服務提供者起始登入），應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Jobscience 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-jobscience-tutorial/IC784341.png "Scenario")
##啟用 Jobscience 的應用程式整合
  
本節的目的是要說明如何啟用 Jobscience 的應用程式整合。

###若要啟用 Jobscience 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-jobscience-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-jobscience-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-jobscience-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-jobscience-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **jobscience**。

    ![應用程式庫](./media/active-directory-saas-jobscience-tutorial/IC784342.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Jobscience**, ，然後按一下 [ **完成** 加入應用程式。

    ![Jobscience](./media/active-directory-saas-jobscience-tutorial/IC784357.png "Jobscience")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 Jobscience 中進行驗證。  
設定 Jobscience 的單一登入需要您從憑證抓取指紋值。  
如果您不熟悉此程序，請參閱 [如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

###若要設定單一登入，請執行下列步驟：

1.  以系統管理員身分登入您的 Jobscience 公司網站。

2.  移至 **安裝**。

    ![設定](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Setup")

3.  在左的導覽窗格中，在 **管理** 區段中，按一下 **定義域管理** 以展開相關的區段，然後按一下 [ **我的網域** 開啟 **我的網域** 頁面。 

    ![我的網域](./media/active-directory-saas-jobscience-tutorial/IC767825.png "My Domain")

4.  若要確認您的網域已設定正確，請確定它是在 「**步驟 4 部署至使用者**」，並檢閱您 「**我的網域設定**」。

    ![已部署到使用者的網域](./media/active-directory-saas-jobscience-tutorial/IC784377.png "Doman Deployed to User")

5.  在不同的 Web 瀏覽器視窗中登入您的 Azure 入口網站。

6.  在 **Jobscience** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-jobscience-tutorial/IC784360.png "Configure Single Sign-On")

7.  在 **您希望使用者如何登入 Jobscience** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-jobscience-tutorial/IC784361.png "Configure Single Sign-On")

8.  在 **設定應用程式 URL** 頁面上，於 **Jobscience 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*http://company.my.salesforce.com*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-jobscience-tutorial/IC784362.png "Configure App URL")

9.  在 **設定單一登入在 Jobscience** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後將儲存在本機電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-jobscience-tutorial/IC784363.png "Configure Single Sign-On")

10. 在 Jobscience 公司網站中，按一下 [ **安全性控制**, ，然後按一下 [ **單一登入設定**。

    ![安全性控制項](./media/active-directory-saas-jobscience-tutorial/IC784364.png "Security Controls")

11. 在 **單一登入設定** 區段中，執行下列步驟 ︰

    ![單一登入設定](./media/active-directory-saas-jobscience-tutorial/IC781026.png "Single Sign-On Settings")

    1.  選取 **已啟用 SAML**。
    2.  按一下 [ **新**。

12. 在 **SAML 單一登入設定編輯** ] 對話方塊中，執行下列步驟 ︰

    ![SAML 單一登入設定](./media/active-directory-saas-jobscience-tutorial/IC784365.png "SAML Single Sign-On Setting")

    1.  在 **名稱** 文字方塊中，輸入您的組態名稱。
    2.  在 Azure 網站中，在 **Jobscience 在設定單一登入** ] 對話方塊頁面中，複製 **簽發者 URL** 值，並接著將它貼到 **簽發者** 文字方塊
    3.  在 **實體識別碼** 文字方塊中，輸入 **https://salesforce-jobscience.com**
    4.  按一下 [ **瀏覽** 上傳您的 Azure AD 憑證。
    5.  做為 **SAML 身分識別類型**, ，請選取 **判斷提示包含使用者物件的同盟 ID**。
    6.  做為 **SAML 身分識別位置**, ，請選取 **身分識別位於 Subject 陳述式 NameIdentfier 元素**。
    7.  在 Azure 網站中，在 **Jobscience 在設定單一登入** ] 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼到 **身分識別提供者登入 URL** 文字方塊
    8.  在 Azure 網站中，在 **Jobscience 在設定單一登入** ] 對話方塊頁面中，複製 **遠端登出 URL** 值，並接著將它貼到 **身分識別提供者登出 URL** 文字方塊
    9.  按一下 [ **儲存**。

13. 在左的導覽窗格中，在 **管理** 區段中，按一下 **定義域管理** 以展開相關的區段，然後按一下 [ **我的網域** 開啟 **我的網域** 頁面。 

    ![我的網域](./media/active-directory-saas-jobscience-tutorial/IC767825.png "My Domain")

14. 在 **我網域** 頁面上，於 **登入頁面商標** 區段中，按一下 **編輯**。

    ![登入頁面商標](./media/active-directory-saas-jobscience-tutorial/IC767826.png "Login Page Branding")

15. 在 **登入頁面商標** 頁面上，於 **驗證服務** 區段的名稱您 **SAML SSO 設定** 隨即出現。 並加以選取，然後按一下 [ **儲存**。

    ![登入頁面商標](./media/active-directory-saas-jobscience-tutorial/IC784366.png "Login Page Branding")

16. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-jobscience-tutorial/IC784367.png "Configure Single Sign-On")
  
若要取得預存程序啟動單一登上登入 URL 按一下 **單一登入設定** 中 **安全性控制** 功能表一節。

![安全性控制項](./media/active-directory-saas-jobscience-tutorial/IC784368.png "Security Controls")
  
按一下您已經在上述步驟中建立的 SSO 設定檔。  
此頁面會顯示單一登入 URL 貴公司 (例如 *https://companyname.my.salesforce.com?so=companyid*)。
##設定使用者佈建
  
若要讓 Azure AD 使用者能夠登入 Jobscience，必須將他們佈建到 Jobscience。  
Jobscience 需以手動的方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  登入您 **Jobscience** 系統管理員身分的公司網站。

2.  移到 [設定]

    ![設定](./media/active-directory-saas-jobscience-tutorial/IC784358.png "Setup")

3.  移至 **管理使用者 \ > 使用者**。

    ![使用者](./media/active-directory-saas-jobscience-tutorial/IC784369.png "Users")

4.  按一下 [ **新使用者**。

    ![所有使用者](./media/active-directory-saas-jobscience-tutorial/IC784370.png "All Users")

5.  在 **編輯使用者** ] 對話方塊中，執行下列步驟 ︰

    ![使用者編輯](./media/active-directory-saas-jobscience-tutorial/IC784371.png "User Edit")

    1.  在相關的文字方塊中輸入您想要佈建之 Azure AD 使用者的名字、姓氏、別名、電子郵件、使用者名稱與暱稱屬性。
    2.  按一下 [ **儲存**。

    >[AZURE.NOTE] Azure AD 帳戶持有者將會收到電子郵件，其中包含的連結來啟動它先確認其帳戶。

>[AZURE.NOTE] 您可以使用任何其他 Jobscience 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 Jobscience。

##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 Jobscience，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Jobscience * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-jobscience-tutorial/IC784372.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-jobscience-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

