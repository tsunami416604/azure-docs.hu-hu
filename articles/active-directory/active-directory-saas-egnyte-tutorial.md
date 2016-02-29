<properties 
    pageTitle="教學課程：Azure Active Directory 與 Egnyte 整合 | Microsoft Azure" 
    description="了解如何使用 Egnyte 搭配 Azure Active Directory 來啟用單一登入、自動化佈建和更多功能！" 
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

#教學課程：Azure Active Directory 與 Egnyte 整合
  
本教學課程的目的是要示範 Azure 與 Egnyte 的整合。  
本教學課程中說明的案例假設您已經具有下列項目：

-   有效的 Azure 訂閱
-   已啟用 Egnyte 單一登入的訂用帳戶
  
完成本教學課程之後, 您已指派給 Egnyte 的 Azure AD 使用者將能夠登入位於您 Egnyte 公司網站 (服務提供者起始登入)，應用程式的單一登入或使用 [存取面板簡介](active-directory-saas-access-panel-introduction.md)
  
本教學課程中說明的案例由下列建置組塊組成：

1.  啟用 Egnyte 的應用程式整合
2.  設定單一登入
3.  設定使用者佈建
4.  指派使用者

![案例](./media/active-directory-saas-egnyte-tutorial/IC787812.png "Scenario")
##啟用 Egnyte 的應用程式整合
  
本節的目的是要說明如何啟用 Egnyte 的應用程式整合。

###若要啟用 Egnyte 的應用程式整合，請執行下列步驟：

1.  在 Azure 管理入口網站中，在左的導覽窗格中，按一下 [ **Active Directory**。

    ![Active Directory](./media/active-directory-saas-egnyte-tutorial/IC700993.png "Active Directory")

2.  從 **目錄** 清單中，選取您要啟用目錄整合的目錄。

3.  若要開啟 [應用程式] 檢視中，目錄檢視中，按一下 [ **應用程式** 上方功能表中。

    ![應用程式](./media/active-directory-saas-egnyte-tutorial/IC700994.png "Applications")

4.  按一下 [ **新增** 頁面的底部。

    ![新增應用程式](./media/active-directory-saas-egnyte-tutorial/IC749321.png "Add application")

5.  在 **您想要** ] 對話方塊中，按一下 [ **從資源庫新增應用程式**。

    ![從組件庫新增應用程式](./media/active-directory-saas-egnyte-tutorial/IC749322.png "Add an application from gallerry")

6.  在 **搜尋方塊**, ，型別 **egnyte**。

    ![應用程式庫](./media/active-directory-saas-egnyte-tutorial/IC787813.png "Application Gallery")

7.  在 [結果] 窗格中，選取 **Egnyte**, ，然後按一下 [ **完成** 加入應用程式。

    ![Egnyte](./media/active-directory-saas-egnyte-tutorial/IC787814.png "Egnyte")
##設定單一登入
  
本節的目的是要說明如何依據 SAML 通訊協定來使用同盟，讓使用者能夠用自己的 Azure AD 帳戶驗證到 Egnyte。  
在此程序中，您必須建立 base-64 編碼的憑證檔案。  
如果您不熟悉此程序，請參閱 [如何將二進位檔案憑證轉換成文字檔](http://youtu.be/PlgrzUZ-Y1o)。

###若要設定單一登入，請執行下列步驟：

1.  在 Azure AD 入口網站上 **Egnyte** 應用程式整合頁面上，按一下 [ **設定單一登入** 開啟 * * 設定單一登入 * * 對話方塊。

    ![設定單一登入](./media/active-directory-saas-egnyte-tutorial/IC787815.png "Configure Single Sign-On")

2.  在 **您希望使用者如何登入 Egnyte** 頁面上，選取 **Microsoft Azure AD 單一登入**, ，然後按一下 [ **下一步**。

    ![設定單一登入](./media/active-directory-saas-egnyte-tutorial/IC787816.png "Configure Single Sign-On")

3.  在 **設定應用程式 URL** 頁面上，於 **Egnyte 登入 URL** 文字方塊中，輸入您的 URL 使用下列模式 」*https://company.egnyte.com*」，然後按一下 [ **下一步**。

    ![設定應用程式 URL](./media/active-directory-saas-egnyte-tutorial/IC787817.png "Configure App URL")

4.  在 **Egnyte 在設定單一登入** 頁面上，按一下 **下載憑證**, ，然後儲存您的電腦上的憑證檔案。

    ![設定單一登入](./media/active-directory-saas-egnyte-tutorial/IC787818.png "Configure Single Sign-On")

5.  在不同的 Web 瀏覽器視窗中，以系統管理員身分登入您的 Egnyte 公司網站。

6.  按一下 [ **設定**。

    ![設定](./media/active-directory-saas-egnyte-tutorial/IC787819.png "Settings")

7.  在功能表中，按一下 [ **設定**。

    ![設定](./media/active-directory-saas-egnyte-tutorial/IC787820.png "Settings")

8.  按一下 [ **組態** 索引標籤，然後再按一下 **安全性**。

    ![安全性](./media/active-directory-saas-egnyte-tutorial/IC787821.png "Security")

9.  在 **單一登入驗證** 區段中，執行下列步驟:

    ![單一登入驗證](./media/active-directory-saas-egnyte-tutorial/IC787822.png "Single Sign On Authentication")

    1.  做為 **單一登入驗證**, ，請選取 **SAML 2.0**。
    2.  做為 **身分識別提供者**, ，請選取 **AzureAD**。
    3.  在 Azure 網站中，在 **Egnyte 在設定單一登入** 對話方塊頁面中，複製 **遠端登入 URL** 值，並接著將它貼到 * * 身分識別提供者登入 URL * * 文字方塊。
    4.  在 Azure 網站中，在 **Egnyte 在設定單一登入** 對話方塊頁面中，複製 **實體識別碼** 值，並接著將它貼入 **身分識別提供者的實體識別碼** 文字方塊。
    5.  建立 **base 64 編碼** 檔案下載的憑證。  

        >[AZURE.TIP]如需詳細資訊，請參閱 [如何轉換的文字檔案中的二進位檔案的憑證](http://youtu.be/PlgrzUZ-Y1o)

    6.  在記事本中開啟 base-64 編碼的憑證，將它的內容複製到剪貼簿，然後將它貼到 **身分識別提供者憑證** 文字方塊。
    7.  做為 **預設使用者對應**, ，請選取 **電子郵件地址**。
    8.  做為 **使用網域特定簽發者值**, ，請選取 **停用**。
    9.  按一下 [ **儲存**。

10. 在 Azure AD 入口網站中，選取單一登入設定確認，，然後按一下 [ **完成** 關閉 **設定單一登入** ] 對話方塊。

    ![設定單一登入](./media/active-directory-saas-egnyte-tutorial/IC787823.png "Configure Single Sign-On")
##設定使用者佈建
  
若要讓 Azure AD 使用者可以登入 Egnyte，則必須將他們佈建到 Egnyte。  
Egnyte 需以手動的方式佈建。

###若要佈建使用者帳戶，請執行下列步驟：

1.  登入您 **Egnyte** Egnyte 公司網站，以系統管理員。

2.  移至 **設定 \ > 使用者和群組**。

3.  按一下 [ **新增使用者**, ，然後選取您想要新增的使用者的類型。

    ![使用者](./media/active-directory-saas-egnyte-tutorial/IC787824.png "Users")

4.  在 **新的標準使用者** 區段中，執行下列步驟:

    ![新的標準使用者](./media/active-directory-saas-egnyte-tutorial/IC787825.png "New Standard User")

    1.  型別 **電子郵件**, ，**Username** 和您想要佈建的有效 Azure Active Directory 帳戶的其他詳細資料。
    2.  按一下 [ **儲存**。

    >[AZURE.NOTE] Azure Active Directory 帳戶持有者將會收到電子郵件通知。

>[AZURE.NOTE] 您可以使用任何其他 Egnyte 使用者帳戶建立工具或 Api 來佈建 AAD 使用者帳戶 Egnyte 所提供。

##指派使用者
  
若要測試您的組態，則需指派您所允許使用您應用程式的 Azure AD 使用者，藉此授予其存取組態的權限。

###若要將使用者指派給 Egnyte，請執行下列步驟：

1.  在 Azure AD 入口網站中建立測試帳戶。

2.  在 * * Egnyte * * 應用程式整合頁面上，按一下 [ **指派使用者**。

    ![指派使用者](./media/active-directory-saas-egnyte-tutorial/IC787826.png "Assign Users")

3.  選取測試使用者，請按一下 **指派**, ，然後按一下 [ **是** 以確認您的作業。

    ![是](./media/active-directory-saas-egnyte-tutorial/IC767830.png "Yes")
  
如果要測試您的單一登入設定，請開啟存取面板。 如需有關存取面板的詳細資訊，請參閱 [存取面板簡介](active-directory-saas-access-panel-introduction.md)。
