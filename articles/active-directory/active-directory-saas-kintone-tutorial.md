<properties 
    pageTitle="教學課程：Azure Active Directory 與 Kintone 整合 | Microsoft Azure" 
    description="了解如何使用 Kintone 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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
    ms.date="10/20/2015" 
    ms.author="markvi" />

#教學課程：Azure Active Directory 與 Kintone 整合
  
本教學課程的目的是要示範 Azure 與 Kintone 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 Kintone 單一登入的訂用帳戶
  
完成本教學課程之後, 您已指派給 Kintone 的 Azure AD 使用者將能夠登入位於您 Kintone 公司網站 （服務提供者起始登入），應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Kintone 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-kintone-tutorial/IC785859.png "Scenario")
##啟用 Kintone 的應用程式整合
  
本節的目的是要說明如何啟用 Kintone 的應用程式整合。

###若要啟用 Kintone 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-kintone-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-kintone-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-kintone-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-kintone-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Kintone**。

    ![應用程式庫](./media/active-directory-saas-kintone-tutorial/IC785867.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Kintone**, ，然後按一下 [ **完成** 加入應用程式。

    ![Kintone](./media/active-directory-saas-kintone-tutorial/IC785871.png "Kintone")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 Kintone 中進行驗證。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Kintone** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-kintone-tutorial/IC785872.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 Kintone** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-kintone-tutorial/IC785873.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **Kintone 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://company.kintone.com*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-kintone-tutorial/IC785875.png "Configure App URL")

4.  在 **設定單一登入在 Kintone** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-kintone-tutorial/IC785878.png "Configure Single Sign-On")

5.  在不同的網頁瀏覽器視窗中，登入您 **Kintone** 系統管理員身分的公司網站。

6.  按一下 [ **設定**。

    ![設定](./media/active-directory-saas-kintone-tutorial/IC785879.png "Settings")

7.  按一下 [ **使用者和系統管理**。

    ![使用者與系統管理](./media/active-directory-saas-kintone-tutorial/IC785880.png "Users & System Administration")

8.  在 **系統管理 \ > 安全性** 按一下 **登入**。

    ![登入](./media/active-directory-saas-kintone-tutorial/IC785881.png "Login")

9.  按一下 [ **啟用 SAML 驗證**。

    ![SAML 驗證](./media/active-directory-saas-kintone-tutorial/IC785882.png "SAML Authentication")

10. 在 [SAML 驗證] 區段中，執行下列步驟：

    ![SAML 驗證](./media/active-directory-saas-kintone-tutorial/IC785883.png "SAML Authentication")

    1.  在 Azure 網站中，在 **Kintone 在設定單一登入** 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **登入 URL** 文字方塊。
    2.  在 Azure 網站中，在 **Kintone 在設定單一登入** 對話方塊頁面中，複製 **遠端登出 URL** 值，並接著將它貼入 **登出 URL** 文字方塊。
    3.  按一下 [ **瀏覽** 上傳您下載的憑證。
    4.  按一下 [ **儲存**。

11. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-kintone-tutorial/IC785884.png "Configure Single Sign-On")
##設定使用者佈建
  
若要讓 Azure AD 使用者能夠登入 Kintone，必須將他們佈建到 Kintone。  
Kintone 需以手動的方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您 **Kintone** 系統管理員身分的公司網站。

2.  按一下 [ **設定**。

    ![設定](./media/active-directory-saas-kintone-tutorial/IC785879.png "Settings")

3.  按一下 [ **使用者和系統管理**。

    ![使用者與系統管理](./media/active-directory-saas-kintone-tutorial/IC785880.png "User & System Administration")

4.  在 **使用者管理**, ，按一下 [ **部門和使用者**。

    ![部門與使用者](./media/active-directory-saas-kintone-tutorial/IC785888.png "Department & Users")

5.  按一下 [ **新使用者**。

    ![新增使用者](./media/active-directory-saas-kintone-tutorial/IC785889.png "New Users")

6.  在 **新使用者** 區段中，執行下列步驟 ︰

    ![新增使用者](./media/active-directory-saas-kintone-tutorial/IC785890.png "New Users")

    1.  型別 a **顯示名稱**, ，**登入名稱**, ，**新密碼**, ，**確認密碼**, ，**電子郵件地址** 和其他您想要佈建相關 texboxes 的有效 AAD 帳戶的詳細資料。
    2.  按一下 [ **儲存**。

>[AZURE.NOTE] 您可以使用任何其他 Kintone 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 Kintone。

##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 Kintone，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Kintone * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-kintone-tutorial/IC785891.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-kintone-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

