<properties 
    pageTitle="教學課程：Azure Active Directory 與 Intacct 整合 | Microsoft Azure" 
    description="了解如何使用 Intacct 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Intacct 整合
  
本教學課程的目的是要示範 Azure 與 Intacct 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   Intacct 租用戶
  
完成本教學課程之後, 您已指派給 Intacct 的 Azure AD 使用者將能夠登入位於您 Intacct 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Intacct 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-intacct-tutorial/IC790030.png "Scenario")
##啟用 Intacct 的應用程式整合
  
本節的目的是要說明如何啟用 Intacct 的應用程式整合。

###若要啟用 Intacct 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-intacct-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-intacct-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-intacct-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-intacct-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **Intacct**。

    ![應用程式庫](./media/active-directory-saas-intacct-tutorial/IC790031.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Intacct**, ，然後按一下 [ **完成** 加入應用程式。

    ![Intacct](./media/active-directory-saas-intacct-tutorial/IC790032.png "Intacct")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶在 Intacct 中進行驗證。  
在此程序中，您必須建立 base-64 編碼的憑證檔案。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Intacct** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-intacct-tutorial/IC790033.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 Intacct** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-intacct-tutorial/IC790034.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **Intacct 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://Intacct.com/company*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-intacct-tutorial/IC790035.png "Configure App URL")

4.  在 **Intacct 在設定單一登入** 頁面上，按一下 **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-intacct-tutorial/IC790036.png "Configure Single Sign-On")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 Intacct 公司網站。

6.  按一下 [ **公司** 索引標籤，然後再按一下 **公司資訊**。

    ![公司](./media/active-directory-saas-intacct-tutorial/IC790037.png "Company")

7.  按一下 [ **安全性** 索引標籤，然後再按一下 **編輯**。

    ![安全性](./media/active-directory-saas-intacct-tutorial/IC790038.png "Security")

8.  在 **單一登入 (SSO)** 區段中，執行下列步驟:

    ![單一登入](./media/active-directory-saas-intacct-tutorial/IC790039.png "Single Sign On")

    1.  選取 **啟用單一登入**。
    2.  做為 **身分識別提供者類型**, ，請選取 **SAML 2.0**。
    3.  在 Azure 網站中，在 **Intacct 在設定單一登入** 對話方塊頁面中，複製 **簽發者 URL** 值，並接著將它貼入 **簽發者 URL** 文字方塊。
    4.  在 Azure 網站中，在 **Intacct 在設定單一登入** 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼入 **登入 URL** 文字方塊。
    5.  建立 **base 64 編碼** 檔案下載的憑證。
        
        >[AZURE.TIP]如需詳細資訊，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)

    6.  在記事本中開啟 base-64 編碼的憑證，將它的內容複製到剪貼簿，然後將它貼到 **憑證** 文字方塊
    7.  按一下 [ **儲存**。

9.  在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-intacct-tutorial/IC790040.png "Configure Single Sign-On")
##設定使用者佈建
  
若要讓 Azure AD 使用者能夠登入 Intacct，則必須將他們佈建到 Intacct。  
在 Intacct 的情況下，佈建是手動工作。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您 **Intacct** 租用戶。

2.  按一下 [ **公司** 索引標籤，然後再按一下 **使用者**。

    ![使用者](./media/active-directory-saas-intacct-tutorial/IC790041.png "Users")

3.  按一下 [ **新增** ] 索引標籤

    ![新增](./media/active-directory-saas-intacct-tutorial/IC790042.png "Add")

4.  在 **使用者資訊** 區段中，執行下列步驟:

    ![使用者資訊](./media/active-directory-saas-intacct-tutorial/IC790043.png "User Information")

    1.  型別 **使用者識別碼**, 、 **姓氏**, ，**名字**, 、 **電子郵件地址**, 、 **標題** 和 **電話** 的 Azure AD 帳戶，您想要佈建相關的文字方塊。
    2.  選取 **系統管理員權限** 您想要佈建的 Azure AD 帳戶。
    3.  按一下 [ **儲存**。
        
        >[AZURE.NOTE] AAD 帳戶持有者將收到的電子郵件，並依循連結來確認其帳戶，然後就會生效。

>[AZURE.NOTE] 您可以使用任何其他 Intacct 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶 Intacct 所提供。

##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 Intacct，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Intacct * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-intacct-tutorial/IC790044.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-intacct-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
