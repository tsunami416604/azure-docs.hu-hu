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


# 教學課程：Azure Active Directory 與 SAP HANA 雲端平台整合

本教學課程的目的是要示範 Azure 與 SAP HANA 雲端平台的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   SAP HANA 雲端平台帳戶


>[AZURE.IMPORTANT]您需要在 SAP HANA 雲端平台帳戶上部署您自己的應用程式或訂閱應用程式來測試單一登入。 在本教學課程中，帳戶中已部署一個應用程式。

本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 SAP HANA 雲端平台的應用程式整合
2.  設定單一登入
3.  將角色指派給使用者
4.  指派使用者

![案例](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790795.png "Scenario")
## 啟用 SAP HANA 雲端平台的應用程式整合

本節的目的是要說明如何啟用 SAP HANA 雲端平台的應用程式整合。

### 若要啟用 SAP HANA 雲端平台的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站的左方瀏覽窗格中，按一下 [Active Directory]****。

    ![Active Directory](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700993.png "Active Directory")

2.  從 [目錄]**** 清單中，選取要啟用目錄整合的目錄。

3.  若要開啟應用程式檢視，請在目錄檢視中，按一下頂端功能表中的 [應用程式]****。

    ![應用程式](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC700994.png "Applications")

4.  按一下頁面底部的 [新增]****。

    ![新增應用程式](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749321.png "Add application")

5.  在 [欲執行動作]**** 對話方塊中，按一下 [從資源庫中新增應用程式]****。

    ![從組件庫新增應用程式](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC749322.png "Add an application from gallerry")

6.  在 [搜尋方塊]**** 中輸入**SAP HANA 雲端平台**。

    ![應用程式資源庫](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790796.png "Application Gallery")

7.  在結果窗格中，選取 [SAP HANA 雲端平台]****，然後按一下 [完成]**** 來新增應用程式。

    ![SAP Hana](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793929.png "SAP Hana")
## 設定單一登入

本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己在 Azure AD 中的帳戶在 SAP HANA 雲端平台中進行驗證。  
在此程序中，您需要將 base-64 編碼憑證上傳到您的 SAP HANA 雲端平台租用戶。  


### 若要設定單一登入，請執行下列步驟：

1.  

    ![設定單一登入](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC778552.png "Configure single sign-on")

2.  

    ![設定單一登入](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790797.png "Configure Single Sign-On")

3.  

4.  按一下 [信任]**** 索引標籤。

    ![信任](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790800.png "Trust")

5.  在 [信任管理] 區段中，執行下列步驟：

    ![取得中繼資料](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793930.png "Get Metadata")

    1.  按一下 [本機服務提供者]**** 索引標籤。
    2.  若要下載 SAP HANA 雲端平台中繼資料檔案，請按一下 [取得中繼資料]****。

6.  

    ![設定應用程式 URL](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790798.png "Configure App URL")

    1.  這是您的 SAP HANA 雲端平台應用程式中受保護資源的帳戶特定 URL。
        >[AZURE.NOTE]這是您 SAP HANA 雲端平台應用程式中需要使用者以進行驗證的 URL。

    2.  開啟已下載的 SAP HANA 雲端平台中繼資料檔案，然後再找到**ns3:AssertionConsumerService**標記。
    3.  複製 [位置]**** 屬性的值，然後再將其貼至 [SAP HANA 雲端平台回覆 URL]**** 文字方塊中。

7.  

    ![設定單一登入](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790799.png "Configure Single Sign-On")

8.  

    ![信任管理](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793931.png "Trust Management")

    1.  按一下 [**編輯**]。
    2.  針對 [組態類型]****，選取 [自訂]****。
    3.  針對 [本機提供者名稱]****，保留預設值。
    4.  若要產生**簽署金鑰**和**簽署憑證**金鑰組，請按一下 [產生金鑰組]****。
    5.  針對 [主體傳播]****，選取 [已停用]****。
    6.  針對 [強制驗證]****，選取 [已停用]****。
    7.  按一下 [儲存]****。

9.  按一下 [信任的識別提供者]****索引標籤，然後再按一下 [新增信任的識別提供者]****。

    ![信任管理](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790802.png "Trust Management")
    >[AZURE.NOTE]

10. 按一下 [一般]**** 索引標籤，然後再按一下 [瀏覽]**** 來上傳下載的中繼資料檔案。

    ![信任管理](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC793932.png "Trust Management")
    >[AZURE.NOTE] 上傳中繼資料檔之後，**單一登入 URL**、**單一登出 URL** 和**簽署憑證**的值會自動填入。

11. 按一下 [屬性] ****索引標籤。

12. 在 [屬性]**** 索引標籤上，執行下列步驟：

    ![屬性](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790804.png "Attributes")

    1.  透過按一下 [新增判斷提示式的屬性]****，新增下列判斷提示式的屬性：

        | 判斷提示屬性| 主體屬性|
        |-------------------|--------------------|
        | | firstname| --------------------| --------------------|
        | | lastname| -----------|
        | | 電子郵件|


    >[AZURE.NOTE]屬性的設定取決於 HCP 上的應用程式是如何開發的，意即其預期在 SAML 回應中的是哪個屬性，以及其在程式碼中哪個名稱 (主體屬性) 之下存取該屬性。
    >  
    >a. 螢幕擷取畫面中的 [預設屬性]**** 僅供說明之用。 其並不是案例進行所必須的要素。  
    >
    >b. 螢幕擷取畫面所顯示的**主體屬性**名稱與值取決於應用程式是如何開發的。 您的應用程式很可能需要不同的對應。

13. 

    ![設定單一登入](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC796933.png "Configure Single Sign-On")

在選擇性的步驟中，您可以為 Azure Active Directory 識別提供者設定判斷提示式群組
>[AZURE.NOTE]使用 SAP HANA 雲端平台上的群組可讓您在 SAP HANA 雲端平台應用程式中以動態方式將一或多個使用者指派給一或多個角色，這是由 SAML 2.0 判斷提示中的屬性值所決定。 例如，如果判斷提示包含屬性「*合約＝暫存*」，您可能會希望將所有受影響的使用者加入「*暫存*」群組。 「*暫存*」群組可能包含來自一或多個部署在 SAP HANA 雲端平台帳戶中之應用程式的一或多個角色。
>  
>如果您想要大量指派許多使用者給 SAP HANA 雲端平台帳戶中之應用程式的一個或多個角色，請使用判斷提示式的群組。

## 將角色指派給使用者

若要讓 Azure AD 使用者能夠登入 SAP HANA 雲端平台，您必須將 SAP HANA 雲端平台中的角色指派給他們。

### 若要指派角色給使用者，請執行下列步驟：

1.  登入您的**SAP HANA 雲端平台**駕駛艙。

2.  執行下列步驟：

    ![授權](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790805.png "Authorizations")

    1.  按一下 [授權]****。
    2.  按一下 [使用者]**** 索引標籤。
    3.  在 [使用者]**** 文字方塊中，輸入使用者的電子郵件地址。
    4.  按一下 [指派]**** 將使用者指派給角色。
    5.  按一下 [儲存]****。

## 指派使用者

若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

### 若要將使用者指派給 SAP HANA 雲端平台，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  

    ![指派使用者](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC790806.png "Assign Users")

3.  選取測試使用者，按一下 [指派]****，然後按一下 [是]**** 以確認指派。

    ![是](./media/active-directory-saas-sap-hana-cloud-platform-tutorial/IC767830.png "Yes")

如果要測試您的單一登入設定，請開啟存取面板。




