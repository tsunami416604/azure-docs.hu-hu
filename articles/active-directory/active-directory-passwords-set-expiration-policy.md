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
> [AZURE.NOTE] 本主題提供雲端服務，例如 Microsoft Intune 和 Office 365 身分識別和目錄服務依賴 Microsoft Azure Active Directory 線上說明內容。

身為 Microsoft 雲端服務的全域管理員，您可使用「適用於 Windows PowerShell 的 Microsoft Azure Active Directory 模組」，將使用者密碼設為不會到期。 您亦可使用 Windows PowerShell Cmdlet 移除永不到期組態，或是查看哪些使用者密碼設為不會到期。

  > [AZURE.NOTE] 只有未透過目錄同步作業同步處理的使用者帳戶的密碼可以設定不會為過期。 如需目錄同步作業的詳細資訊，請參閱中的主題清單 [目錄同步作業藍圖](https://msdn.microsoft.com/library/azure/hh967642.aspx)。

若要使用 Windows PowerShell Cmdlet，您必須先安裝它們。

## 欲執行動作

- [如何檢查密碼到期原則](#how-to-check-expiration-policy-for-a-password)

- [設定密碼到期](#set-a-password-to-expire)

- [設定密碼使其不會到期](#set-a-password-not-to-expire)

## 如何檢查密碼到期原則

1.  使用您公司的管理員認證連線至 Windows PowerShell。

2.  執行下列其中一項：

    - 若要查看單一使用者的密碼是否設定為永不過期，執行下列指令程式使用使用者主要名稱 (UPN) (例如 aprilr@contoso.onmicrosoft.com) 或使用者的使用者識別碼，您想要檢查 ︰ `Get-MSOLUser -UserPrincipalName <user ID> | Select PasswordNeverExpires`

    - 若要查看所有使用者的「密碼永久有效」設定，請執行下列 Cmdlet：`Get-MSOLUser | Select UserPrincipalName, PasswordNeverExpires`

## 設定密碼到期

1.  使用您公司的管理員認證連線至 Windows PowerShell。

2.  執行下列其中一項：

    - 若要將某位使用者的密碼設為會到期，請透過使用使用者主體名稱 (UPN) 或使用者的使用者識別碼，執行下列 Cmdlet：`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires \$false`

    - 若要將組織中所有使用者的密碼設為會到期，請使用下列 Cmdlet：`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires \$false`

## 設定密碼為永久有效

1. 使用您公司的管理員認證連線至 Windows PowerShell。

2.  執行下列其中一項：

    - 若要將某位使用者的密碼設為永久有效，請透過使用使用者主體名稱 (UPN) 或使用者的使用者識別碼，來執行下列 Cmdlet：`Set-MsolUser -UserPrincipalName <user ID> -PasswordNeverExpires \$true`

    - 若要將組織中所有使用者的密碼設為永久有效，請執行下列 Cmdlet：`Get-MSOLUser | Set-MsolUser -PasswordNeverExpires \$true`


