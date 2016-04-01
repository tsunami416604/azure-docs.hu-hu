<properties 
    pageTitle="教學課程：Azure Active Directory 與 Jitbit Helpdesk 整合 | Microsoft Azure" 
    description="了解如何使用 Jitbit Helpdesk 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
    services="active-directory" 
    authors="MarkusVi"  
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

#教學課程：Azure Active Directory 與 Jitbit Helpdesk 整合
  
本教學課程的目的是要示範 Azure 與 Jitbit Helpdesk 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Jitbit Helpdesk 租用戶
  
完成本教學課程之後, 您已指派給 Jitbit Helpdesk 的 Azure AD 使用者將能夠登入位於您 Jitbit Helpdesk 公司網站 （服務提供者起始登入），應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Jitbit Helpdesk 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777676.png "Scenario")
##啟用 Jitbit Helpdesk 的應用程式整合
  
本節的目的是要說明如何啟用 Jitbit Helpdesk 的應用程式整合。

###若要啟用 Jitbit Helpdesk 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Jitbit Helpdesk**。

    ![應用程式庫](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777677.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Jitbit Helpdesk**, ，然後按一下 [ **完成** 加入應用程式。

    ![JitBit](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC781008.png "JitBit")
##設定單一登入
  
本節的目標在於概述如何讓使用者透過他們的帳戶驗證至 Jitbit Helpdesk SAML 通訊協定為基礎的同盟，使用 Azure AD 中。 .  
在此程序中，您必須建立 base-64 編碼的憑證檔案。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

>[AZURE.IMPORTANT] 若要能在 Jitbit Helpdesk 租用戶上設定單一登入，您必須先連絡 Jitbit Helpdesk 技術支援部門將此功能啟用。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Jitbit Helpdesk** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777678.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 Jitbit Helpdesk** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777679.png "Configure single sign-on")

3.  在 **設定應用程式 URL** 頁面上，於 **Jitbit Helpdesk 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://\<tenant-name\>。Jitbit.com*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777528.png "Configure app URL")

4.  在 **設定單一登入在 Jitbit Helpdesk** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後將儲存在本機憑證檔當做 **c:\\Jitbit Helpdesk.cer**。

    ![設定單一登入](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777680.png "Configure single sign-on")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Jitbit Helpdesk 公司網站。

6.  在頂端工具列中，按一下 [ **管理**。

    ![系統管理](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")

7.  按一下 [ **一般設定**。

    ![使用者、公司和權限](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Users, companies and permissions")

8.  在 **驗證設定** 組態區段中，執行下列步驟 ︰

    ![驗證設定](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777683.png "Authentication settings")

    1.  選取 **單一的 [啟用 SAML 2.0 登入** 登入使用單一登入 (SSO) 與 **OneLogin**。
    2.  在 Azure 網站中，在 **Jitbit Helpdesk 在設定單一登入** ] 對話方塊頁面中，複製 **服務提供者 (SP) 起始端點** 值，並接著將它貼入 **端點 URL** 文字方塊。
    3.  建立 **base 64 編碼** 檔案下載的憑證。
        
        >[AZURE.TIP]如需詳細資訊，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)

    4.  開啟 base-64 編碼的憑證，將它的內容複製到剪貼簿，然後將它貼到 **X.509 憑證** 文字方塊
    5.  按一下 [ **儲存變更**。

9.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777684.png "Configure single sign-on")
##設定使用者佈建
  
為了讓 Azure AD 使用者登入 Jitbit Helpdesk，他們必須佈建到 Jitbit Helpdesk 中。  
在 Jitbit Helpdesk 的情況下，佈建是手動工作。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您 **Jitbit Helpdesk** 租用戶。

2.  在頂端功能表中，按一下 [ **管理**。

    ![系統管理](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777681.png "Administration")

3.  按一下 [ **使用者、 公司和權限**。

    ![使用者、公司和權限](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777682.png "Users, companies and permissions")

4.  按一下 [ **加入使用者**。

    ![新增使用者](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777685.png "Add user")

5.  在 [建立] 區段中，輸入您想要在下列文字方塊的佈建的 Azure AD 帳戶資料 ︰ **Username**, ，**電子郵件**, ，**名字**, ，**姓氏**

    ![建立](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777686.png "Create")

6.  按一下 [ **建立**。

>[AZURE.NOTE] 您可以使用任何其他 Jitbit Helpdesk 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶 Jitbit Helpdesk 所提供。

##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 Jitbit Helpdesk，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Jitbit Helpdesk * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC777687.png "Assign users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-jitbit-helpdesk-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

