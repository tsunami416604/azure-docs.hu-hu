<properties 
    pageTitle="取得深入了解：Azure AD 密碼管理報告 |Microsoft Azure" 
    description="本文說明如何使用報告，在您的組織中取得密碼管理作業的深入了解。" 
    services="active-directory" 
    documentationCenter="" 
    authors="asteen" 
    manager="kbrint" 
    editor="billmath"/>

<tags 
    ms.service="active-directory" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/16/2015" 
    ms.author="asteen"/>

# 如何使用密碼管理報告取得 Operational Insights
本章節描述如何使用 Azure Active Directory 的密碼管理報告，來檢視使用者如何使用密碼重設及您的組織中的變更。

- [**密碼管理報告概觀**](#overview-of-password-management-reports)
- [**如何檢視密碼管理報告**](#how-to-view-password-management-reports)
- [**檢視密碼重設註冊活動在您的組織**](#view-password-reset-registration-activity)
- [**檢視密碼重設您的組織中的活動**](#view-password-reset-activity)

## 密碼管理報告概觀
一旦部署密碼重設之後，其中一個最常見的下一步是查看它在您的組織中的使用情形。  例如，您可能想要取得使用者如何註冊密碼重設，或過去幾天內已完成多少密碼重設等深入了解。  以下是一些常見問題，您可以使用密碼管理報告存在於回答 [Azure 管理入口網站](https://manage.windowsazure.com) 今天 ︰

- 有多少人已註冊密碼重設？
- 有誰已註冊密碼重設？
- 什麼資料是人員註冊？
- 有多少人在過去 7 天內重設其密碼？
- 使用者或系統管理員用來重設其密碼最常見的方法是什麼？
- 當使用者或系統管理員嘗試使用密碼重設時所面臨的常見問題是什麼？
- 哪些系統管理員經常重設自己的密碼？
- 密碼重設時是否有任何可疑的活動？


## 如何檢視密碼管理報告
若要尋找密碼管理報告，請遵循下列步驟：

1.  按一下 [ **Active Directory** 中的延伸模組 [Azure 管理入口網站](https://manage.windowsazure.com)。
2.  從入口網站顯示的清單中選取您的目錄。
3.  按一下 [ **報表** ] 索引標籤。
4.  請查看 **活動記錄檔** 一節。
5.  選取 [ **密碼重設活動** 報表或 **密碼重設註冊活動** 報表。

    ![][001]

## 如何從 API 存取密碼管理報告
自 2015 年 8 月起，Azure AD 的報告和事件現在支援擷取密碼重設和密碼重設註冊報告中包含的所有資訊。

若要存取此資料，您必須撰寫小型的應用程式或指令碼，以從我們的伺服器擷取資料。 [了解如何開始使用 Azure AD 報告 API](active-directory-reporting-api-getting-started.md)。

一旦您擁有有效的指令碼，您接下來要檢查您可以擷取密碼重設和註冊事件以滿足您的案例。

- [SsprActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprActivityEvent)︰ 密碼重設事件會列出可用的資料行
- [SsprRegistrationActivityEvent](https://msdn.microsoft.com/library/azure/mt126081.aspx#BKMK_SsprRegistrationActivityEvent)︰ 密碼重設登錄事件會列出可用的資料行

## 檢視密碼重設註冊活動

密碼重設註冊活動報告會顯示您的組織中發生的所有密碼重設註冊。  密碼重設註冊會顯示這份報表中的任何使用者都已順利登錄驗證資訊，在密碼重設註冊入口網站 ([http://aka.ms/ssprsetup](http://aka.ms/ssprsetup))。

- **時間範圍最大值**: 1 個月
- **資料列的最大數目**︰ 無限制
- **可下載**︰ 是，透過 CSV 檔案

    ![][002]

### 報告資料行說明
下列清單詳細說明每個報告資料行：

- **使用者** – 嘗試密碼重設註冊作業。
- **角色** – 在目錄中使用者的角色。
- **日期和時間** – 嘗試的時間與日期。
- **已註冊資料** – 使用者在密碼中提供哪些驗證資料重設註冊。

### 報告值說明
下表描述每個資料行允許的不同值：

欄|允許的值及其意義
---|---
已註冊資料| **備用電子郵件** – 使用者用來驗證的備用電子郵件或驗證電子郵件<p><p>**辦公室電話**– 使用者用來驗證的辦公室電話<p>**行動電話** -使用者使用行動電話或驗證的驗證電話<p>**安全性問題** – 使用者用來驗證安全性問題<p>**上述項目的任何組合 （例如替代電子郵件 + 行動電話）** – 當指定 2 個閘道原則，並顯示使用者使用哪兩個方法來驗證其密碼重設要求。

## 檢視密碼重設活動

此報告會顯示您的組織中發生的所有密碼重設嘗試。

- **時間範圍最大值**: 1 個月
- **資料列的最大數目**︰ 無限制
- **可下載**︰ 是，透過 CSV 檔案

    ![][003]

### 報告資料行說明
下列清單詳細說明每個報告資料行：

1. **使用者** – 嘗試密碼重設作業 （根據使用者重設密碼時提供使用者識別碼欄位）。
2. **角色** – 在目錄中使用者的角色。
3. **日期和時間** – 嘗試的時間與日期。
4. **使用方法** – 使用者用於此驗證方法重設作業。
5. **結果** – 最終結果的密碼重設作業。
6. **詳細資料** – 為什麼密碼重設的詳細資料產生一樣的值。  也包含任何防範措施，您可能會採取以解決未預期的錯誤。

### 報告值說明
下表描述每個資料行允許的不同值：

欄|允許的值及其意義
---|---
使用方法|**備用電子郵件** – 使用者用來驗證的備用電子郵件或驗證電子郵件<p>**辦公室電話** – 使用者用來驗證的辦公室電話<p>**行動電話** – 使用者使用行動電話或驗證的驗證電話<p>**安全性問題** – 使用者用來驗證安全性問題<p>**上述項目的任何組合 （例如替代電子郵件 + 行動電話）** – 當指定 2 個閘道原則，並顯示使用者使用哪兩個方法來驗證其密碼重設要求。
結果|**已放棄** – 使用者開始重設密碼，但是又中途停止而無法完成<p>**封鎖** – 使用者的帳戶，所以無法使用密碼重設，因為嘗試使用密碼重設頁面或單一密碼重設閘道太多次在 24 小時內<p>**取消** – 使用者開始重設密碼，但是隨後按一下 [取消] 按鈕，取消部分到工作階段 <p>**連絡管理員** – 使用者在他無法解決的工作階段期間發生了問題，所以使用者按一下 「 連絡您的系統管理員 」 連結，而不是完成密碼重設流程<p>**無法** – 使用者無法重設密碼，可能是因為使用者未設定使用功能 （例如沒有授權、 遺失驗證資訊、 在內部管理密碼但是回寫已關閉）。<p>**成功** – 密碼重設成功。
詳細資料|請參閱下表

### 允許的詳細資料資料行的值
以下是使用密碼重設活動報告時，您預期的結果類型清單：

詳細資料 | 結果類型
----|----
在完成電子郵件驗證選項之後使用者已放棄  | Abandoned
在完成行動 SMS 驗證選項之後使用者已放棄|Abandoned
在完成行動語音通話驗證選項之後使用者已放棄 | Abandoned
在完成辦公室語音通話驗證選項之後使用者已放棄 | Abandoned
在完成安全性問題選項之後使用者已放棄|Abandoned
在輸入其使用者識別碼之後使用者已放棄| Abandoned
在開始電子郵件驗證選項之後使用者已放棄|Abandoned
在開始行動 SMS 驗證選項之後使用者已放棄|Abandoned
在開始行動語音通話驗證選項之後使用者已放棄|Abandoned
在開始辦公室語音通話驗證選項之後使用者已放棄|Abandoned
在開始安全性問題選項之後使用者已放棄| Abandoned
選取新密碼之前使用者已放棄| Abandoned
選取新密碼之際使用者已放棄| Abandoned
使用者輸入太多無效的 SMS 驗證碼，因此封鎖 24 小時|Blocked
使用者嘗試行動電話語音驗證太多次，因此封鎖 24 小時|Blocked
使用者嘗試辦公室電話語音驗證太多次，因此封鎖 24 小時 |Blocked
使用者嘗試回答安全性問題太多次，因此封鎖 24 小時| Blocked
使用者嘗試驗證電話號碼太多次，因此封鎖 24 小時|Blocked
在傳遞必要的驗證方法之前使用者已取消|Cancelled
在提交新密碼之前使用者已取消|Cancelled
在嘗試電子郵件驗證選項之後使用者連絡系統管理員 |Contacted admin
在嘗試行動 SMS 驗證選項之後使用者連絡系統管理員|Contacted admin
在嘗試行動語音通話驗證選項之後使用者連絡系統管理員|Contacted admin
在嘗試辦公室語音通話驗證選項之後使用者連絡系統管理員 |Contacted admin
在嘗試安全性問題驗證選項之後使用者連絡系統管理員|Contacted admin
這位使用者未啟用密碼重設。 在 [設定] 索引標籤底下啟用密碼重設以解決此問題|  Failed
使用者沒有授權。 您可以新增授權給使用者以解決此問題|Failed
使用者嘗試從未啟用 cookie 的裝置重設| Failed
使用者的帳戶已定義驗證方法不足。 新增驗證資訊以解決此問題|Failed
使用者的密碼是在內部部署進行管理。 您可以啟用密碼回寫以解決此問題|Failed
我們無法取得您的內部部署密碼重設服務。 請檢查您的同步處理電腦的事件記錄檔|Failed
我們在重設使用者的內部部署密碼時發現問題。 請檢查您的同步處理電腦的事件記錄檔 | Failed
此使用者不是密碼重設使用者群組的成員。 將此使用者加入至該群組中以解決此問題。|Failed
已針對此租用戶完全停用密碼重設。 請參閱 [這裡](http://aka.ms/ssprtroubleshoot) 以解決此問題。 | Failed
使用者成功重設密碼|Succeeded

## 密碼重設文件的連結
以下是所有 Azure AD 密碼重設文件頁面的連結： 

* [**您自己的密碼重設**](active-directory-passwords-update-your-own-password.md) -深入了解如何重設或變更您自己的密碼為系統的使用者
* [**它的運作方式**](active-directory-passwords-how-it-works.md) -了解六個不同元件服務，以及每個未
* [**開始使用**](active-directory-passwords-getting-started.md) -了解如何讓使用者重設及變更雲端或內部部署密碼
* [**自訂**](active-directory-passwords-customize.md) -了解如何自訂外觀和行為的服務，貴組織的需求
* [**最佳作法**](active-directory-passwords-best-practices.md) -了解如何快速部署且有效管理組織中的密碼
* [**常見問題集**](active-directory-passwords-faq.md) -取得常見問題的解答
* [**疑難排解**](active-directory-passwords-troubleshoot.md) -了解如何快速移難排解服務的問題
* [**了解詳細**](active-directory-passwords-learn-more.md) -深入的技術詳細資料的服務的運作方式



[001]: ./media/active-directory-passwords-get-insights/001.jpg "Image_001.jpg"
[002]: ./media/active-directory-passwords-get-insights/002.jpg "Image_002.jpg"
[003]: ./media/active-directory-passwords-get-insights/003.jpg "Image_003.jpg"


