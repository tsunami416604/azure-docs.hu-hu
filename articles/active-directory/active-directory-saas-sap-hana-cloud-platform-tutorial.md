<properties 
    pageTitle="教學課程：Azure Active Directory 與 SAP HANA 雲端平台整合 | Microsoft Azure" 
    description="了解如何使用 SAP HANA 雲端平台搭配 Azure Active Directory 來啟用單一登入、自動佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 SAP HANA 雲端平台整合
  
本教學課程的目的是要示範 Azure 與 SAP HANA 雲端平台的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   SAP HANA 雲端平台帳戶
  
完成本教學課程之後, 您已指派給 SAP HANA Cloud Platform 的 Azure AD 使用者將能夠登入應用程式使用的單一登入 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。

>[AZURE.IMPORTANT]您要部署您自己的應用程式，或訂閱來測試單一登入 SAP HANA Cloud Platform 帳戶上的應用程式。 在本教學課程中，帳戶中已部署一個應用程式。
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 SAP HANA 雲端平台的應用程式整合
2.  設定單一登入
3.  將角色指派給使用者
4.  指派使用者

![案例](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "Scenario")
##啟用 SAP HANA 雲端平台的應用程式整合
  
本節的目的是要說明如何啟用 SAP HANA 雲端平台的應用程式整合。

###若要啟用 SAP HANA 雲端平台的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **SAP HANA Cloud Platform**。

    ![應用程式庫](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **SAP HANA Cloud Platform**, ，然後按一下 [ **完成** 加入應用程式。

    ![SAP Hana](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "SAP Hana")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 SAP HANA 雲端平台中進行驗證。  
在此程序中，您需要將 base-64 編碼憑證上傳到您的 SAP HANA 雲端平台租用戶。  
如果您不熟悉此程序，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **SAP HANA Cloud Platform** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "Configure single sign-on")

2.  在 **您希望使用者如何登入 SAP HANA Cloud Platform** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "Configure Single Sign-On")

3.  在不同的網頁瀏覽器視窗中，登入 SAP HANA Cloud Platform Cockpit，在 https://account.\<landscape host\ >.ondemand.com/cockpit (例如: *https://account.hanatrial.ondemand.com/cockpit*)。

4.  按一下 [ **信任** ] 索引標籤。

    ![信任](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "Trust")

5.  在 [信任管理] 區段中，執行下列步驟：

    ![取得中繼資料](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "Get Metadata")

    1.  按一下 [ **本機服務提供者** ] 索引標籤。
    2.  若要下載 SAP HANA Cloud Platform 中繼資料檔案，請按一下 [ **取得中繼資料**。

6.  在 Azure Active Directory 網站中，在 **設定應用程式 URL** 頁面上，執行下列步驟，然後按 **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "Configure App URL")

    1.  在 **登入 URL** 文字方塊中，輸入 URL，讓使用者中用來登入您 **SAP HANA Cloud Platform** 應用程式。 這是您的 SAP HANA 雲端平台應用程式中受保護資源的帳戶特定 URL。 URL 根據下列模式: *https://\<applicationName\>\<accountName\>.\<landscape 主機 >.ondemand.com/\ < path\_to\_protected\_resource\ >* (例如: *https://xleavep1941203872trial.hanatrial.ondemand.com/xleave*)

        >[AZURE.NOTE]這是您需要使用者驗證的 SAP HANA Cloud Platform 應用程式中的 URL。

    2.  開啟下載的 SAP HANA Cloud Platform 中繼資料檔案，然後尋找 **ns3:AssertionConsumerService** 標記。
    3.  複製的值 **位置** 屬性，然後再將它貼入 **SAP HANA 雲端平台回覆 URL** 文字方塊。

7.  在 **設定單一登入，在 SAP HANA Cloud Platform** ] 頁面上，若要下載中繼資料中，按一下 [ **下載中繼資料**, ，然後儲存您的電腦上的檔案。

    ![設定單一登入](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "Configure Single Sign-On")

8.  在 SAP HANA Cloud Platform Cockpit 中 **本機服務提供者** 區段中，執行下列步驟:

    ![信任管理](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "Trust Management")

    1.  按一下 [ **編輯**。
    2.  做為 **組態類型**, ，請選取 **自訂**。
    3.  做為 **本機提供者名稱**, ，保留預設值。
    4.  若要產生 **簽署金鑰** 和 **簽署憑證** 金鑰組，請按一下 [ **產生金鑰組**。
    5.  做為 **主體傳播**, ，請選取 **已停用**。
    6.  做為 **強制驗證**, ，請選取 **已停用**。
    7.  按一下 [ **儲存**。

9.  按一下 [ **受信任的身分識別提供者** 索引標籤，然後再按一下 **新增受信任的身分識別提供者**。

    ![信任管理](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "Trust Management")

    >[AZURE.NOTE]若要管理的受信任的身分識別提供者清單，您需要在本機服務提供者區段中選擇了自訂組態類型。 針對 [預設組態類型]，您對 SAP ID 服務有不可編輯且隱含的信任。 針對 [無]，您不具任何信任設定。

10. 按一下 [ **一般** 索引標籤，然後再按一下 **瀏覽** 下載的中繼資料檔案上傳。

    ![信任管理](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "Trust Management")

    >[AZURE.NOTE] 上傳中繼資料檔的值之後 **單一登入 URL**, ，**單一登出 URL** 和 **簽署憑證** 自動填入。

11. 按一下 [ **屬性** ] 索引標籤。

12. 在 **屬性** 索引標籤上，執行下列步驟:

    ![屬性](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "Attributes")

    1.  依序按一下 **新增屬性**, ，加入下列判斷提示型屬性:

        |判斷提示屬性| 主體屬性|
        |-------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/identity/claims/givenname|   firstname|--------------------|--------------------|
        |http://schemas.xmlsoap.org/ws/2005/05/identity/claims/surname|        lastname|-----------|
        |http://schemas.xmlsoap.org/ws/2005/05/identity/claims/emailaddress|電子郵件|

    >[AZURE.NOTE]屬性的組態取決於如何在 HCP 上的應用程式開發，也就是哪些屬性，預期在 SAML 回應中，而且在哪個名稱 (主體屬性)，他們存取這個程式碼中的屬性。
    >  
    >a.   **預設屬性** 螢幕擷取畫面是只供說明之用。 其並不是案例進行所必須的要素。  
    >
    >b.  名稱和值 **主體屬性** 螢幕擷取畫面所示的應用程式的開發方式而定。 您的應用程式很可能需要不同的對應。

13. 在 Azure 網站中，在 **設定單一登入，在 SAP HANA Cloud Platform** 對話方塊頁面上，選取單一登入設定確認，然後按 **完成**。

    ![設定單一登入](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "Configure Single Sign-On")
  
在選擇性的步驟中，您可以為 Azure Active Directory 識別提供者設定判斷提示式群組

>[AZURE.NOTE]在 SAP HANA Cloud Platform 使用群組，可讓您以動態方式將一或多個使用者指派給 SAP HANA Cloud Platform 應用程式，取決於 SAML 2.0 判斷提示中的屬性值中的一個或多個角色。 例如，如果判斷提示包含屬性"*合約 = 暫存*」，您可能會想要新增到群組的所有受影響的使用者 」*暫存*」。 「 群組 」*暫存*」 可能包含一個或多個角色部署 SAP HANA Cloud Platform 帳戶中的一個或多個應用程式。
>  
>如果您想要大量指派許多使用者給 SAP HANA 雲端平台帳戶中之應用程式的一個或多個角色，請使用判斷提示式的群組。 如果您只要將單一或小許多使用者指派給 (a) 的特定角色建議指派它們直接在 「**授權**「 SAP HANA Cloud Platform cockpit] 索引標籤。

##將角色指派給使用者
  
若要讓 Azure AD 使用者能夠登入 SAP HANA 雲端平台，您必須將 SAP HANA 雲端平台中的角色指派給他們。

###若要指派角色給使用者，請執行下列步驟：

1.  登入您 **SAP HANA Cloud Platform** cockpit。

2.  執行下列步驟：

    ![授權](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "Authorizations")

    1.  按一下 [ **授權**。
    2.  按一下 [ **使用者** ] 索引標籤。
    3.  在 **使用者** 文字方塊中，輸入使用者的電子郵件地址。
    4.  按一下 [ **指派** 為使用者指派至角色。
    5.  按一下 [ **儲存**。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 SAP HANA 雲端平台，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 **SAP HANA Cloud Platform** 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
