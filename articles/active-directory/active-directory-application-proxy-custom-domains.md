<properties
    pageTitle="使用 Azure AD 應用程式 Proxy 中的自訂網域 | Microsoft Azure"
    description="說明如何使用 Azure AD 應用程式 Proxy 中的自訂網域。"
    services="active-directory"
    documentationCenter=""
    authors="kgremban"
    manager="StevenPo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="10/19/2015"
    ms.author="kgremban"/>

# 使用 Azure AD 應用程式 Proxy 中的自訂網域
> [AZURE.NOTE] 應用程式 Proxy 是您升級至 Premium 或 Basic 版本的 Azure Active Directory 時，才可以使用的功能。 如需詳細資訊，請參閱 [Azure Active Directory 版本](active-directory-editions.md)。

使用預設網域，可讓您將相同的 URL 設為可供存取應用程式的內部或外部 URL，而您的使用者在存取應用程式時只需記住一個 URL (不論他們是從哪裡進行存取)，還可讓您在應用程式的 [存取面板] 中建立單一捷徑。 如果您使用 Azure AD 應用程式 Proxy 所提供的預設網域，則無需進行其他設定來啟用您的網域。 在使用自訂網域的情況下，您需要執行一些動作，以確保應用程式 Proxy 能辨識您的網域並驗證其憑證。

## 選取自訂網域

1. 根據「使用應用程式 Proxy 發佈應用程式」中的指示，發佈您的應用程式。
2. 應用程式出現在清單中的應用程式之後，請選取它，然後按一下 **設定**。
3. 在 **外部 URL**, ，輸入您的自訂網域。
4. 如果您的外部 URL 是 https，系統將提示您上傳憑證，以便 Azure 驗證應用程式的 URL。 您也可以上傳符合應用程式外部 URL 的萬用字元憑證。 這個網域必須是清單內的程式 [Azure 驗證網域](https://msdn.microsoft.com/library/azure/jj151788.aspx)。 Azure 必須有應用程式之網域 URL 的憑證或符合應用程式之 [外部 URL] 的萬用字元憑證。
5. 請務必新增可將內部 URL 路由傳送至應用程式的 DNS 記錄，讓您有相同的 URL 以供內部和外部存取，以及在使用者的應用程式清單中有應用程式的單一捷徑。

## 有關使用自訂網域的常見問題

問：是否可以選取已上傳的憑證，而不再重新上載？ <br>
A ︰ 先前上傳的憑證會自動繫結至應用程式，而且只有一個相符的應用程式的主機名稱的憑證。 <br>
…<br>
問：如何加入憑證，而已匯出的憑證應以何種格式上傳？ <br>
答 ︰ 憑證然後應該從應用程式設定] 頁面上傳。 憑證應該為 PFX 檔案。 <br>
…<br>
問：是否可以使用 ECC 憑證？ <br>
答 ︰ 簽章的方法沒有明確的限制。 <br>
…<br>
問：是否可以使用 SAN 憑證？ <br>
答： 會。<br>
…<br>
問：是否可以使用萬用字元憑證？ <br>
答： 會。 <br>
…<br>
問：是否可以在每個應用程式上使用不同的憑證？ <br>
答 ︰ 是的除非兩個應用程式共用相同的外部主機。 <br>
…..<br>
問：如果我註冊新的網域，是否可以使用該網域？ <br>
答 ︰ 可以，送入的網域清單是從是租用戶的已驗證的網域清單。 <br>
…<br>
問 ︰ 憑證到期時，會發生什麼事？ <br>
答：您會在應用程式組態頁面的 [憑證] 區段中收到警告。 當使用者嘗試存取應用程式時，隨即出現安全性警告。 <br>
…<br>
問 ︰ 我該怎麼辦如果我想要取代給定的應用程式的憑證？ <br>
答 ︰ 上傳新憑證從應用程式設定頁面<br>
…<br>
問：我是否可以刪除憑證並取代它？ <br>
答 ︰ 當您上傳新的憑證，如果舊的憑證不在另一個應用程式，它會自動刪除<br>
…<br>
問 ︰ 什麼情況的憑證已撤銷<br>
答：不會對憑證執行撤銷檢查。 當使用者嘗試存取應用程式，根據瀏覽器中，可能會出現安全性警告。<br>
…<br>
問：是否可以使用自我簽署憑證？ <br>
答：可以，可以使用自我簽署憑證。 請注意，是否您使用私人憑證授權單位，憑證的 CDP （憑證撤銷點發佈點） 必須是公用。 <br>
...<br>
問：是否有地方可以查看我的租用戶的所有憑證？ <br>
答︰目前的版本不支援此功能。<br>



## 另請參閱
應用程式 Proxy 還有其他更多用途：

- [使用應用程式 Proxy 發行應用程式](active-directory-application-proxy-publish.md)
- [啟用單一登入](active-directory-application-proxy-sso-using-kcd.md)
- [啟用條件式存取](active-directory-application-proxy-conditional-access.md)
- [宣告感知應用程式使用](active-directory-application-proxy-claims-aware-apps.md)- [疑難排解的問題，您無法使用應用程式 Proxy](active-directory-application-proxy-troubleshoot.md)

## 深入了解應用程式 Proxy
- [看看我們在這裡的線上說明](active-directory-application-proxy-enable.md)
- [查閱應用程式 Proxy 部落格](http://blogs.technet.com/b/applicationproxyblog/)
- [觀看我們在 Channel 9 上的影片！](http://channel9.msdn.com/events/Ignite/2015/BRK3864)

## 其他資源

* [以組織方式註冊 Azure](sign-up-organization.md)
* [Azure 身分識別](fundamentals-identity.md)


