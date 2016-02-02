<properties 
    pageTitle="Azure Multi-Factor Authentication 報告" 
    description="說明如何變更使用者設定，如強迫使用者重複 proof-up 程序。" 
    documentationCenter="" 
    services="multi-factor-authentication" 
    authors="billmath" 
    manager="stevenpo" 
    editor="curtand"/>

<tags 
    ms.service="multi-factor-authentication" 
    ms.workload="identity" 
    ms.tgt_pltfrm="na" 
    ms.devlang="na" 
    ms.topic="article" 
    ms.date="11/19/2015" 
    ms.author="billmath"/>


# 管理雲端中 Azure Multi-Factor Authentication 的使用者設定

身為管理員，您可以管理下列使用者和裝置設定。

- [要求選定的使用者再次提供連絡方法](#require-selected-users-to-provide-contact-methods-again)
- [刪除使用者現有的應用程式密碼](#delete-users-existing-app-passwords)
- [還原所有暫停之裝置的使用者 (公用預覽) 中的 MFA](#restore-mfa-on-all-suspended-devices-for-a-user)






當電腦或裝置遺失、遭竊或您需要移除使用者存取權限時，這是很實用的方法。


## 要求選定使用者再次提供連絡方法

此設定會強制使用者在登入時再次完成註冊程序。 請注意，如果使用者擁有非瀏覽器應用程式的應用程式密碼，這些應用程式仍然可以繼續運作。 您也可以選取 [**刪除選定使用者產生的所有現有應用程式密碼**] 來刪除使用者的應用程式密碼。

### 如何要求使用者再次提供連絡方法

<ol>
<li>登入 Azure 管理入口網站。</li>
<li>在左側，按一下 [Active Directory。</li>
<li>下，按一下您想要再次提供連絡方法所需的使用者的目錄上。</li>
<li>在頂端，按一下 [使用者]。</li>
<li>在頁面底部，按一下 [管理 Multi-factor auth]。這會開啟 Multi-Factor Authentication 頁面。
<li>尋找您想要管理，並在名稱旁的方塊打勾的使用者。您可能需要在頂端變更檢視。</li>
<li>這會顯示 **管理使用者設定** 右邊的連結。按一下此按鈕。</li> 
<li>勾選 **要求選定的使用者再次提供連絡方法**。</li>

![提供連絡方法](./media/multi-factor-authentication-manage-users-and-devices/reproofup.png)

<li>按一下 [儲存]。</li>
<li>按一下 [關閉]</li>

## 刪除使用者現有的應用程式密碼

這會刪除使用者建立的所有應用程式密碼。 與這些應用程式密碼相關的非瀏覽器應用程式將會停止運作，直到建立新應用程式密碼為止。

### 如何刪除使用者的現有應用程式密碼

<ol>
<li>登入 Azure 管理入口網站。</li>
<li>在左側，按一下 [Active Directory。</li>
<li>下，按一下您想要刪除應用程式密碼的使用者的目錄上。</li>
<li>在頂端，按一下 [使用者]。</li>
<li>在頁面底部，按一下 [管理 Multi-factor auth]。這會開啟 Multi-Factor Authentication 頁面。
<li>尋找您想要管理，並在名稱旁的方塊打勾的使用者。您可能需要在頂端變更檢視。</li>
<li>這會顯示 **管理使用者設定** 右邊的連結。按一下此按鈕。</li> 
<li>勾選 **刪除所選使用者產生的所有現有的應用程式密碼**。</li>
![刪除應用程式密碼](./media/multi-factor-authentication-manage-users-and-devices/deleteapppasswords.png)
<li>按一下 [儲存]。</li>
<li>按一下 [關閉]。</li>





## 還原使用者所有暫停之裝置的 MFA

系統管理員可以重設使用者裝置和瀏覽器上的 Multi-Factor Authentication。 這可藉由還原使用者裝置和瀏覽器的 Multi-Factor Authentication 來達成。 這樣做會移除使用者所有裝置和瀏覽器的暫停效果。

### 如何還原使用者所有暫停之裝置上的 MFA

<ol>
<li>登入 Azure 管理入口網站。</li>
<li>在左側，按一下 [Active Directory。</li>
<li>下，按一下您想要還原 mfa 使用者的目錄上。</li>
<li>在頂端，按一下 [使用者]。</li>
<li>在頁面底部，按一下 [管理 Multi-factor auth]。這會開啟 Multi-Factor Authentication 頁面。
<li>尋找您想要管理，並在名稱旁的方塊打勾的使用者。您可能需要在頂端變更檢視。</li>
<li>這會顯示 **管理使用者設定** 右邊的連結。按一下此按鈕。</li> 
<li>勾選所有暫停之裝置上還原多重要素驗證。</li>
![刪除應用程式密碼](./media/multi-factor-authentication-manage-users-and-devices/rememberdevices.png)
<li>按一下 [儲存]。</li>
<li>按一下 [關閉]。</li>







