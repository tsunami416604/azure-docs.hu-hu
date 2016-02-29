<properties
    pageTitle="密碼原則和 Azure Active Directory 中的限制 | Microsoft Azure"
    description="描述適用於 Azure Active Directory 中的密碼的原則，包括允許的字元、長度和有效期限"
  services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="stevenpo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/03/2015"
    ms.author="curtand"/>


# 密碼原則和 Azure Active Directory 中的限制

本文說明和儲存在 Azure AD 目錄中的使用者帳戶相關聯的密碼原則和複雜性需求。

## 適用於所有使用者帳戶的 UserPrincipalName 原則

每個需要登入 Azure AD 驗證系統的使用者帳戶，都必須具有與該帳戶相關聯的唯一使用者主體名稱 (UPN) 屬性值。 下表列出原則套用至這兩個內部部署 Active Directory 來源使用者帳戶 (同步至雲端)，以及僅限雲端的使用者帳戶。

|   屬性           |     UserPrincipalName 需求  |
|   ----------------------- |   ----------------------- |
|  允許的字元    |  <ul> <li>A – Z</li> <li>-z </li><li>0 – 9</li> <li> . - \_ ! \# ^ \~</li></ul> |
|  不允許的字元  | <ul> <li>@</li> <li>不能包含句號字元 '。 '前面' @' 符號</li></ul> |
| 長度限制  |       <ul> <li>總長度不得超過 113 個字元</li><li>前的 64 個字元 ' @' 符號</li><li>之後的 48 個字元 ' @' 符號</li></ul>

## 僅適用於雲端使用者帳戶的密碼原則

下表描述可用於建立和管理 Azure AD 中的使用者帳戶的密碼原則設定。

|  屬性       |    需求          |
|   ----------------------- |   ----------------------- |
|  允許的字元   |   <ul><li>A – Z</li><li>-z </li><li>0 – 9</li> <li>@ # $ % ^ & * - _ ! + = [ ] { } &#124; \ : ‘ , . ? / ` ~ “ ( ) ;</li></ul> |
|  不允許的字元   |       <ul><li>Unicode 字元</li><li>空格</li><li>空格</li><li> **強式密碼只**: 不能包含點字元 '。 '前面' @' 符號</li></ul> |
|   密碼限制 | <ul><li>8 個字元的最小和最多 16 個字元</li><li>**強式密碼只**: 需要 3 的下列 4:<ul><li>小寫字元</li><li>大寫字元</li><li>數字 (0-9)</li><li>符號 (請參閱上面的密碼限制)</li></ul></li></ul> |
| 密碼到期時間      | <ul><li>預設值: **90** 天 </li><li>值是使用 Azure Active Directory 的 Windows powershell 模組 Set-msolpasswordpolicy 指令程式可設定。</li></ul> |
| 密碼到期通知 |  <ul><li>預設值: **14** 天 (密碼到期前)</li><li>值是可設定使用 Set-msolpasswordpolicy 指令程式。</li></ul> |
| 密碼到期 |  <ul><li>預設值: **false** 天 (表示已啟用該密碼到期) </li><li>值可設定為使用 Set-msoluser 指令程式的個別使用者帳戶。 </li></ul> |
|  密碼歷程記錄  | 無法再次使用上次密碼。 |
|  密碼歷程記錄期間 | 不限次數 |
|  帳戶鎖定 | <ul><li>10 次登入嘗試次數 (錯誤密碼) 之後，使用者必須登入過程中解決 CAPTCHA 對話方塊。</li><li>之後失敗 10 次登入嘗試 (錯誤密碼)，並正確解決 CAPTCHA 對話方塊中，使用者就會被鎖定一段時間。 進一步不正確的密碼將會導致鎖定期間急速增加。</li></ul> |


## 後續步驟

* [從任何地方管理您的密碼](active-directory-passwords.md)
* [密碼管理如何運作](active-directory-passwords-how-it-works.md)
* [開始使用密碼管理](active-directory-passwords-getting-started.md)
* [自訂密碼管理](active-directory-passwords-customize.md)
* [密碼管理最佳作法](active-directory-passwords-best-practices.md)
* [如何使用密碼管理報告取得 Operational Insights](active-directory-passwords-get-insights.md)
* [密碼管理常見問題集](active-directory-passwords-faq.md)
* [疑難排解密碼管理](active-directory-passwords-troubleshoot.md)
* [詳細資訊](active-directory-passwords-learn-more.md)

