<properties 
    pageTitle="教學課程：Azure Active Directory 與 OfficeSpace Software 整合 | Microsoft Azure" 
    description="了解如何使用 OfficeSpace Software 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 OfficeSpace Software 整合
  
本教學課程的目的是要示範 Azure 與 OfficeSpace Software 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 OfficeSpace Software 單一登入的訂閱
  
完成本教學課程之後, 您已指派給 OfficeSpace Software 的 Azure AD 使用者將能夠登入位於您 OfficeSpace Software 公司網站 （服務提供者起始登入），應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 OfficeSpace Software 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-officespace-software-tutorial/IC777764.png "Scenario")
##啟用 OfficeSpace Software 的應用程式整合
  
本節的目的是要說明如何啟用 OfficeSpace Software 的應用程式整合。

###若要啟用 OfficeSpace Software 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-officespace-software-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-officespace-software-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-officespace-software-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-officespace-software-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **OfficeSpace Software**。

    ![應用程式資源庫](./media/active-directory-saas-officespace-software-tutorial/IC777765.png "Application gallery")

7.  在 [結果] 窗格中，選取 **OfficeSpace Software**, ，然後按一下 [ **完成** 加入應用程式。

    ![OfficeSpace Software](./media/active-directory-saas-officespace-software-tutorial/IC781007.png "OfficeSpace Software")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 OfficeSpace Software 中進行驗證。  
設定 OfficeSpace Software 的單一登入需要您從憑證抓取指紋值。  
如果您不熟悉此程序，請參閱 [如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **OfficeSpace Software** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-officespace-software-tutorial/IC777766.png "Configure single sign=on")

2.  在 **您希望使用者如何登入 OfficeSpace Software** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-officespace-software-tutorial/IC777767.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，於 **OfficeSpace 軟體登入 URL** 文字方塊中，輸入 URL，讓使用者中用來登入 OfficeSpace Software 的應用程式 (例如: 「*https://company.officespacesoftware.com*」)，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-officespace-software-tutorial/IC775556.png "Configure App URL")

4.  在 **在 OfficeSpace Software 設定單一登入** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後將儲存在本機電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-officespace-software-tutorial/IC793769.png "Configure single sign-on")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 OfficeSpace Software 公司網站。

6.  移至 **Admin \ > 連接器**。

    ![Admin](./media/active-directory-saas-officespace-software-tutorial/IC777769.png "Admin")

7.  按一下 [ **SAML 授權**。

    ![連接器](./media/active-directory-saas-officespace-software-tutorial/IC777770.png "Connectors")

8.  在 **SAML 授權** 區段中，執行下列步驟 ︰

    ![SAM 組態](./media/active-directory-saas-officespace-software-tutorial/IC777771.png "SAML configuration")

    1.  在 Azure 網站中，在 **在 OfficeSpace Software 設定單一登入** ] 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **登出提供者 url** 文字方塊。
    2.  在 Azure 網站中，在 **在 OfficeSpace Software 設定單一登入** ] 對話方塊頁面中，複製 **遠端登出 URL** 值，並接著將它貼入 **用戶端 idp 目標 url** 文字方塊。
    3.  複製 **指紋** 值匯出的憑證，並接著將它貼入 **idp 用戶端憑證指紋** 文字方塊。  

        >[AZURE.TIP]
        如需詳細資訊，請參閱 [如何擷取憑證的指紋值](http://youtu.be/YKQF266SAxI)

    4.  按一下 [ **儲存設定**。

9.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-officespace-software-tutorial/IC777772.png "Configure single sign-on")
##設定使用者佈建
  
為了讓 Azure AD 使用者能夠登入 OfficeSpace Software，必須將他們佈建到 OfficeSpace Software 中。 在 OfficeSpace Software 的情況下，佈建是自動化工作。  
沒有您需要使用的動作項目。  
在第一次單一登入嘗試時會自動建立使用者 (如有必要)。

>[AZURE.NOTE]您可以使用任何其他 OfficeSpace Software 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶 OfficeSpace Software 所提供。

##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要將使用者指派給 OfficeSpace Software，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * OfficeSpace Software * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-officespace-software-tutorial/IC777773.png "Assign users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-officespace-software-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

