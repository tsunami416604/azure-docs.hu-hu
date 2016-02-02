<properties
    pageTitle="在 Azure Active Directory 中設定密碼到期原則 | Microsoft Azure"
    description="瞭解如何針對單一或大量 Azure Active Directory 密碼，檢查到期原則和變更使用者密碼到期的相關資訊"
    services="active-directory"
    documentationCenter=""
    authors="curtand"
    manager="msStevenPo"
    editor=""/>

<tags
    ms.service="active-directory"
    ms.workload="identity"
    ms.tgt_pltfrm="na"
    ms.devlang="na"
    ms.topic="article"
    ms.date="11/16/2015"
    ms.author="curtand"/>



# 在 Azure Active Directory 中設定密碼到期原則

> [AZURE.NOTE] 本主題針對仰賴 Microsoft Azure Active Directory 提供身分識別與目錄服務的雲端服務 (例如 Microsoft Intune 和 Office 365)，提供相關線上說明內容。

身為 Microsoft 雲端服務的全域管理員，您可使用「適用於 Windows PowerShell 的 Microsoft Azure Active Directory 模組」，將使用者密碼設為不會到期。 您亦可使用 Windows PowerShell Cmdlet 移除永不到期組態，或是查看哪些使用者密碼設為不會到期。
  > [AZURE.NOTE] 您僅可將未透過目錄同步作業執行同步處理的使用者帳戶密碼，設定為不會到期。 如需目錄同步作業的詳細資訊，請參閱中的主題清單 [目錄同步作業藍圖](https://msdn.microsoft.com/library/azure/hh967642.aspx)。

若要使用 Windows PowerShell Cmdlet，您必須先安裝它們。

## 欲執行動作

- [如何檢查密碼到期原則](#how-to-check-expiration-policy-for-a-password)

- [設定密碼過期](#set-a-password-to-expire)

- [設定密碼，使它不會過期](#set-a-password-not-to-expire)

## 如何檢查密碼到期原則

1.  使用您公司的管理員認證連線至 Windows PowerShell。

2.  執行下列其中一項：

    - 若要查看單一使用者的密碼是否設定為永不過期，執行下列指令程式使用使用者主要名稱 (UPN) (例如 aprilr@contoso.onmicrosoft.com) 或您想要檢查使用者的使用者識別碼: `Get-msoluser-UserPrincipalName < 使用者識別碼 > |選取 PasswordNeverExpires`

    - 若要查看所有使用者的 「 密碼永不過期 」 設定，請執行下列 cmdlet: `Get-msoluser |選取 [UserPrincipalName]、 [PasswordNeverExpires`

## 設定密碼到期

1.  使用您公司的管理員認證連線至 Windows PowerShell。

2.  執行下列其中一項：

    - 若要設定一個使用者的密碼，使密碼到期，請執行下列 cmdlet 使用使用者主要名稱 (UPN) 或使用者的使用者識別碼: `Set-msoluser-UserPrincipalName < 使用者識別碼 >-PasswordNeverExpires \$false`

    - 若要設定組織中所有使用者的密碼，將會過期，請使用下列 cmdlet: `Get-msoluser |Set-msoluser-PasswordNeverExpires \$false`

## 設定密碼為永久有效

1. 使用您公司的管理員認證連線至 Windows PowerShell。

2.  執行下列其中一項：

    - 若要設定為永不過期的某位使用者的密碼，請執行下列 cmdlet 使用使用者主要名稱 (UPN) 或使用者的使用者識別碼: `Set-msoluser-UserPrincipalName < 使用者識別碼 >-PasswordNeverExpires \$true`

    - 若要設定為永不過期的組織中的所有使用者的密碼，執行下列 cmdlet: `Get-msoluser |Set-msoluser-PasswordNeverExpires \$true`





