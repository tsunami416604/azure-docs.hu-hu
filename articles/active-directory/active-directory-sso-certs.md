<properties
    pageTitle="如何在 Azure AD 中管理同盟憑證 | Microsoft Azure"
    description="了解如何自訂同盟憑證的到期日期，以及如何更新即將到期的憑證。"
    services="active-directory"
    documentationCenter=""
    authors="liviodlc"
    manager="terrylan"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="09/29/2015"
    ms.author="liviodlc"/>

#在 Azure Active Directory 中管理同盟單一登入的憑證

本文涵蓋各種與 Azure Active Directory 建立憑證，以對 SaaS 應用程式建立同盟單一登入 (SSO) 相關的常見問題。

這篇文章只是設定為使用的應用程式有關 **Azure AD 單一登入**, ，如下列範例所示 ︰

![Azure AD 單一登入](./media/active-directory-sso-certs/fed-sso.PNG)

##如何自訂同盟憑證的到期日期

根據預設，憑證會設定為兩年之後到期。 您可以依照下列步驟，為您的憑證選擇不同的到期日期。 包含的螢幕擷取畫面使用 Salesforce 做為範例，但是這些步驟可以套用在任何同盟的 SaaS 應用程式。

1. 在 Azure Active Directory 中，在您的應用程式的快速入門] 頁面上按一下 **設定單一登入**。

    ![開啟 SSO 組態精靈。](./media/active-directory-sso-certs/config-sso.png)

2. 選取 **Azure AD 單一登入**, ，然後按一下 [ **下一步**。

3. 輸入 **登入 URL** 應用程式，然後選取核取方塊， **設定用於同盟單一登入的憑證**。 然後按一下 [ **下一步**。

    ![Azure AD 單一登入](./media/active-directory-sso-certs/new-app-config-sso.PNG)

4. 在下一個頁面上，選取 **產生新的憑證**, ，然後選取您想要有效的憑證的時間。 然後按一下 [ **下一步**。

    ![產生新的憑證](./media/active-directory-sso-certs/new-app-config-cert.PNG)

5. 接下來，按一下 [ **下載憑證**。 若要了解如何將憑證上傳至特定 SaaS 應用程式，按一下 [ **檢視組態指示**。

    ![下載，然後上傳憑證](./media/active-directory-sso-certs/new-app-config-app.PNG)

6. 憑證要到您選取對話方塊底部的確認核取方塊，然後按下送出之後，才會啟用。

##如何更新即將到期的憑證

下面顯示的更新步驟在理想情況下應該不會讓您的使用者有任何明顯的停機時間。 本節中使用的螢幕擷取畫面採用 Salesforce 做為範例，但這些步驟可以套用到任何同盟的 SaaS 應用程式。

1. 在 Azure Active Directory 中，在您的應用程式的快速入門] 頁面上按一下 **設定單一登入**。

    ![開啟 SSO 組態精靈](./media/active-directory-sso-certs/renew-sso-button.PNG)

2. 在對話方塊中，第一頁 **Azure AD 單一登入** 應已選取狀態，因此按一下 **下一步**。

3. 在第二個頁面上，選取核取方塊， **設定用於同盟單一登入的憑證**。 然後按一下 [ **下一步**。

    ![Azure AD 單一登入](./media/active-directory-sso-certs/renew-config-sso.PNG)

4. 在下一個頁面上，選取 **產生新的憑證**, ，然後選取您想要新的憑證有效的時間。 然後按一下 [ **下一步**。

    ![產生新的憑證](./media/active-directory-sso-certs/new-app-config-cert.PNG)

5. 按一下 [ **下載憑證**。 若要成功更新您的憑證，您必須執行下列兩個步驟：

    - 將新的憑證上傳至 SaaS 應用程式的單一登入組態畫面。 若要了解如何為特定 SaaS 應用程式這樣做，按一下 [ **檢視組態指示**。

    - 在 Azure AD 中，選取 [啟用新的憑證，對話方塊底部的確認核取方塊，然後按一下 **下一步** 送出。

    > [AZURE.IMPORTANT] 單一登入至應用程式將會停用這兩個步驟的其中一項當下已經完成，但是它會重新啟用完成第二個步驟。 因此，為了減少停機時間，請準備好在短時間內互相完成這兩個步驟。

    ![下載，然後上傳憑證](./media/active-directory-sso-certs/renew-config-app.PNG)

[AZURE.INCLUDE [saas-toc](../../includes/active-directory-saas-toc.md)]


