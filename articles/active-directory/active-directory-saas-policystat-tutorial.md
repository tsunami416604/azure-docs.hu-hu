<properties 
    pageTitle="教學課程：Azure Active Directory 與 PolicyStat 整合 | Microsoft Azure" 
    description="了解如何使用 PolicyStat 搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 PolicyStat 整合
  
本教學課程的目的是要示範 Azure 與 PolicyStat 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   PolicyStat 租用戶
  
完成本教學課程之後, 您已指派給 PolicyStat 的 Azure AD 使用者將能夠登入您 PolicyStat 公司網站 (服務提供者起始登入)，在應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 PolicyStat 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-policystat-tutorial/IC808662.png "Scenario")
##啟用 PolicyStat 的應用程式整合
  
本節的目的是要說明如何啟用 PolicyStat 的應用程式整合。

###若要啟用 PolicyStat 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-policystat-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-policystat-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-policystat-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-policystat-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **PolicyStat**。

    ![應用程式庫](./media/active-directory-saas-policystat-tutorial/IC808627.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **PolicyStat**, ，然後按一下 [ **完成** 加入應用程式。

    ![PolicyStat](./media/active-directory-saas-policystat-tutorial/IC810430.png "PolicyStat")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 PolicyStat 中進行驗證。  
PolicyStat 應用程式需要特定的格式，會要求您新增自訂屬性對應到 SAML 判斷提示您 **saml 權杖屬性** 組態。  
以下螢幕擷取畫面顯示上述的範例。

![屬性](./media/active-directory-saas-policystat-tutorial/IC808628.png "Attributes")

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **PolicyStat** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-policystat-tutorial/IC808629.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 PolicyStat** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-policystat-tutorial/IC808630.png "Configure Single Sign-On")

3.  在 **設定應用程式設定** 頁面上，於 **登入 URL** 文字方塊中，輸入您的使用者用於登入 URL PolicyStat 應用程式使用的 URL (例如: *"https://demo-azure.policystat.com"*)，然後按一下 [ **下一步**。

    ![設定 App 設定](./media/active-directory-saas-policystat-tutorial/IC808631.png "Configure App Settings")

4.  在 **PolicyStat 在設定單一登入** 頁面上，按一下 **下載中繼資料**, ，然後儲存您的電腦上的中繼資料檔案。

    ![設定單一登入](./media/active-directory-saas-policystat-tutorial/IC808632.png "Configure Single Sign-On")

5.  在不同的網頁瀏覽器視窗中，以系統管理員身分登入您的 PolicyStat 公司網站。

6.  按一下 [ **管理員** 索引標籤，然後再按一下 **單一登入組態** 左的導覽窗格中。

    ![系統管理員功能表](./media/active-directory-saas-policystat-tutorial/IC808633.png "Administrator Menu")

7.  在 **安裝** 區段中，選取 **啟用單一登入整合**。

    ![單一登入設定](./media/active-directory-saas-policystat-tutorial/IC808634.png "Single Sign-On Configuration")

8.  按一下 [ **設定屬性**, ，然後在 **設定屬性** 區段中，執行下列步驟:

    ![單一登入設定](./media/active-directory-saas-policystat-tutorial/IC808635.png "Single Sign-On Configuration")

    1.  在 **Username 屬性** 文字方塊中，輸入 **uid**。
    2.  在 **First Name 屬性** 文字方塊中，輸入 **firstname**。
    3.  在 **最後一個名稱屬性** 文字方塊中，輸入 **lastname**。
    4.  在 **電子郵件屬性** 文字方塊中，輸入 **emailaddress**。
    5.  按一下 [ **儲存變更**。

9.  按一下 [ **您 IDP 中繼資料**, ，然後在 **您 IDP 中繼資料** 區段中，執行下列步驟:

    ![單一登入設定](./media/active-directory-saas-policystat-tutorial/IC808635.png "Single Sign-On Configuration")

    1.  開啟下載的中繼資料檔案，複製內容，然後將它貼入 **您的身分識別提供者中繼資料** 文字方塊
    2.  按一下 [ **儲存變更**。

10. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-policystat-tutorial/IC771723.png "Configure Single Sign-On")

11. 12. 在頂端功能表中，按一下 **屬性** 開啟 **SAML 權杖屬性** ] 對話方塊。

    ![屬性](./media/active-directory-saas-policystat-tutorial/IC795920.png "Attributes")

13. 若要加入必要的屬性對應，請執行下列步驟：

    ![屬性](./media/active-directory-saas-policystat-tutorial/IC804823.png "Attributes")

    1.  按一下 [ **新增使用者屬性**。
    2.  在 **屬性名稱** 文字方塊中，輸入 **uid**。
    3.  在 **屬性值** 文字方塊中，選取 **ExtractMailPrefix()**。
    4.  從 **郵件** 清單中，選取 **User.mail**。
    5.  按一下 [ **完整**。
##設定使用者佈建
  
為了讓 Azure AD 使用者能夠登入 PolicyStat，必須將他們佈建到 PolicyStat。  
PolicyStat 支援即時使用者佈建。 這表示您不需要手動將使用者新增至 PolicyStat。  
使用者第一次透過單一登入登入時就會被自動新增。

>[AZURE.NOTE]您可以使用任何其他 PolicyStat 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶提供 PolicyStat。

##指派使用者
  
若要測試您的設定，您需要指派使用者，授予存取權給您想要允許其使用您的應用程式存取設定的 Azure AD 使用者。

###若要指派使用者給 PolicyStat，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * PolicyStat * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-policystat-tutorial/IC808636.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-policystat-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
