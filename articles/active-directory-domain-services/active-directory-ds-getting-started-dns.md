<properties
    pageTitle="Azure Active Directory 網域服務預覽：開始使用 | Microsoft Azure"
    description="開始使用 Azure Active Directory 網域服務"
    services="active-directory-ds"
    documentationCenter=""
    authors="mahesh-unnikrishnan"
    manager="udayh"
    editor="inhenk"/>

<tags
    ms.service="active-directory-ds"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/12/2015"
    ms.author="mahesh-unnikrishnan"/>

# Azure AD 網域服務 *（預覽）* -快速入門

## 步驟 4 - 更新 Azure 虛擬網路的 DNS 設定
現在您已成功啟用目錄的 Azure AD 網域服務，下一個步驟就是確保虛擬網路內的電腦可以連接並取用這些服務。 若要這樣做，您必須更新虛擬網路的 DNS 伺服器設定，以指向虛擬網路上可以使用 Azure AD 網域服務的 IP 位址。

> [AZURE.NOTE] 請記下顯示在 Azure AD 網域服務的 IP 位址 **設定** 目錄，在啟用 Azure AD 網域服務目錄之後的索引標籤。

執行下列組態步驟，以便為已啟用 Azure AD 網域服務的虛擬網路更新 DNS 伺服器設定。

1. 瀏覽至 **Azure 管理入口網站** ([https://manage.windowsazure.com](https://manage.windowsazure.com))。
2. 選取 **網路** 在左窗格中的節點。

    ![虛擬網路節點](./media/active-directory-domain-services-getting-started/virtual-network-select.png)

3. 在 **虛擬網路** 索引標籤上，選取您已啟用 Azure AD 網域服務，以檢視其內容的虛擬網路。
4. 按一下 [ **設定** ] 索引標籤。

    ![虛擬網路節點](./media/active-directory-domain-services-getting-started/virtual-network-configure-tab.png)

5. 在 **DNS 伺服器** 區段中，輸入 Azure AD 網域服務的 IP 位址。
6. 請確定您輸入顯示在兩個 IP 位址 **網域服務** 區段 **設定** 您目錄的索引標籤。
7. 按一下 [ **儲存** 才能儲存此虛擬網路的 DNS 伺服器設定頁面底部的工作窗格。

   ![更新虛擬網路的 DNS 伺服器設定。](./media/active-directory-domain-services-getting-started/update-dns.png)

> [AZURE.NOTE] 在更新之後的虛擬網路的 DNS 伺服器設定，可能需要更多虛擬機器的一段時間才能取得更新的 DNS 設定網路。 如果虛擬機器無法連線到網域，您可以 （例如排清 DNS 快取。 ipconfig /flushdns) 上虛擬機器，以強制重新整理虛擬機器上的 DNS 設定。

---
[**下一步-啟用密碼同步處理至 Azure AD 網域服務。**](active-directory-ds-getting-started-password-sync.md)


