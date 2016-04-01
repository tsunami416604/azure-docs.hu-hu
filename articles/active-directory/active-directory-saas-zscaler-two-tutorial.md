<properties 
    pageTitle="教學課程：Azure Active Directory 與 Zscaler Two 整合 | Microsoft Azure" 
    description="了解如何使用 Zscaler Two 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Zscaler Two 整合

本教學課程的目的是要示範 Azure 與 ZScaler Two 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   啟用 ZScaler Two 單一登入的訂用帳戶
  
完成本教學課程之後, 您已指派給 ZScaler Two 的 Azure AD 使用者將能夠登入 ZScaler Two 公司網站 （服務提供者起始登入），在應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 ZScaler Two 的應用程式整合
2.  設定單一登入
3.  進行 Proxy 設定
4.  設定使用者佈建
5.  指派使用者

![設定單一登入](./media/active-directory-saas-zscaler-two-tutorial/IC800199.png "Configure Single Sign-On")

##啟用 ZScaler Two 的應用程式整合
  
本節的目的是要說明如何啟用 ZScaler Two 的應用程式整合。

###若要啟用 ZScaler Two 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-zscaler-two-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-zscaler-two-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-zscaler-two-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-zscaler-two-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **ZScaler Two**。

    ![應用程式庫](./media/active-directory-saas-zscaler-two-tutorial/IC800200.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **ZScaler Two**, ，然後按一下 [ **完成** 加入應用程式。

    ![ZScaler Two](./media/active-directory-saas-zscaler-two-tutorial/IC800201.png "ZScaler Two")

##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 ZScaler Two 中進行驗證。  
在此程序中，您需要上傳 base-64 編碼憑證到您的 ZScaler Two 租用戶。  
如果您不熟悉此程序，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **ZScaler Two** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-zscaler-two-tutorial/IC800202.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 ZScaler Two** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-zscaler-two-tutorial/IC800203.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **ZScaler 兩個登入 URL** 文字方塊中，輸入您的使用者登入 ZScaler Two 的應用程式所使用的 URL，然後按一下 **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-zscaler-two-tutorial/IC800204.png "Configure App URL")

    >[AZURE.NOTE] 如果您需要可以向 ZScaler Two 支援小組取得您環境的實際值。

4.  在 **設定單一登入在 ZScaler Two** ] 頁面上，下載您的憑證，按一下 [ **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-zscaler-two-tutorial/IC800205.png "Configure Single Sign-On")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 ZScaler Two 公司網站。

6.  在頂端功能表中，按一下 [ **管理**。

    ![系統管理](./media/active-directory-saas-zscaler-two-tutorial/IC800206.png "Administration")

7.  在 **管理管理員及角色**, ，按一下 [ **管理使用者及授權**。

    ![管理使用者和驗證](./media/active-directory-saas-zscaler-two-tutorial/IC800207.png "Manage Users & Authentication")

8.  在 **選擇您的組織的驗證選項** 區段中，執行下列步驟 ︰

    ![驗證](./media/active-directory-saas-zscaler-two-tutorial/IC800208.png "Authentication")

    1.  選取 **使用 SAML 單一登入驗證**。
    2.  按一下 [ **設定 SAML 單一登入參數**。

9.  在 **設定 SAML 單一登入參數** 對話方塊頁面上，執行下列步驟，以及 [ **完成**:

    ![單一登入](./media/active-directory-saas-zscaler-two-tutorial/IC800209.png "Single Sign-On")

    1.  在 Azure 網站中上, **在兩個 ZScaler 設定單一登入** 對話方塊頁面中，複製 **驗證要求 URL** 值，並接著將它貼入 **SAML 入口網站進行驗證的使用者會傳送至此 URL** 文字方塊。
    2.  在 **屬性包含登入名稱** 文字方塊中，輸入 **NameID**。
    3.  若要上傳您下載的憑證，請按一下 [ **Zscaler pem**。
    4.  選取 **啟用 SAML 自動佈建**。

10. 在 **設定使用者驗證** 對話方塊頁面上，執行下列步驟 ︰

    ![系統管理](./media/active-directory-saas-zscaler-two-tutorial/IC800210.png "Administration")

    1.  按一下 [ **儲存**。
    2.  按一下 [ **立即啟用**。

11. 在 Azure 網站中，在 **設定單一登入在 ZScaler Two** 對話方塊頁面上，選取 [單一登入設定確認，然後再按 **完成**。

    ![設定單一登入](./media/active-directory-saas-zscaler-two-tutorial/IC800211.png "Configure Single Sign-On")

##進行 Proxy 設定

###在 Internet Explorer 中進行 Proxy 設定

1.  啟動 **Internet Explorer**。

2.  選取 **網際網路選項** 從 **工具** 功能表開啟 **網際網路選項** ] 對話方塊。

    ![，](./media/active-directory-saas-zscaler-two-tutorial/IC769492.png "Internet Options")

3.  按一下 [ **連線** ] 索引標籤。

    ![連線](./media/active-directory-saas-zscaler-two-tutorial/IC769493.png "Connections")

4.  按一下 [ **LAN 設定** 開啟 **LAN 設定** ] 對話方塊。

5.  在 [Proxy 伺服器] 區段中，執行下列步驟：

    ![Proxy 伺服器](./media/active-directory-saas-zscaler-two-tutorial/IC769494.png "Proxy server")

    1.  選取 [在您的區域網路使用 Proxy 伺服器]。
    2.  在 [位址] 文字方塊中輸入 **gateway.zscalerone.net**。
    3.  在連接埠] 文字方塊中，輸入 **80**。
    4.  選取 **近端網址不使用 proxy 伺服器**。
    5.  按一下 [ **確定** 關閉 **區域網路 (LAN) 設定** ] 對話方塊。

6.  按一下 [ **確定** 關閉 **網際網路選項** ] 對話方塊。

##設定使用者佈建
  
為了讓 Azure AD 使用者登入 ZScaler Two，他們必須佈建到 ZScaler Two。  
ZScaler Two 需以手動的方式佈建。

###若要設定使用者佈建，請執行下列步驟：

1.  登入您 **Zscaler** 租用戶。

2.  按一下 [ **管理**。

    ![系統管理](./media/active-directory-saas-zscaler-two-tutorial/IC781035.png "Administration")

3.  按一下 [ **使用者管理**。

    ![新增](./media/active-directory-saas-zscaler-two-tutorial/IC781037.png "Add")

4.  在 **使用者** 索引標籤上，按一下 [ **新增**。

    ![新增](./media/active-directory-saas-zscaler-two-tutorial/IC781037.png "Add")

5.  在 [新增使用者] 區段中，執行下列步驟：

    ![新增使用者](./media/active-directory-saas-zscaler-two-tutorial/IC781038.png "Add User")

    1.  型別 **UserID**, ，**使用者顯示名稱**, ，**密碼**, ，**確認密碼**, ，然後選取 **群組** 和 **部門** 您想要佈建之有效 AAD 帳戶。
    2.  按一下 [ **儲存**。

>[AZURE.NOTE] 您可以使用任何其他 ZScaler Two 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供的 ZScaler Two。

##指派使用者
  
若要測試您的組態，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 ZScaler Two，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **ZScaler Two** 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-zscaler-two-tutorial/IC800212.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-zscaler-two-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

