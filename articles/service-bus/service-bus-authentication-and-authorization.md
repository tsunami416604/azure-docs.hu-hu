<properties 
   pageTitle="服務匯流排驗證和授權 | Microsoft Azure"
   description="共用存取簽章 (SAS) 驗證的概觀。"
   services="service-bus"
   documentationCenter="na"
   authors="sethmanheim"
   manager="timlt"
   editor="tysonn" />
<tags 
   ms.service="service-bus"
   ms.devlang="na"
   ms.topic="article"
   ms.tgt_pltfrm="na"
   ms.workload="na"
   ms.date="12/09/2015"
   ms.author="sethm" />


# 服務匯流排驗證和授權

應用程式可以使用共用存取簽章 (SAS) 驗證，或透過 Azure Active Directory 存取控制 (也稱為存取控制服務或 ACS) 向 Azure 服務匯流排進行驗證。 共用存取簽章 (SAS) 驗證可讓應用程式使用在命名空間或在與特定權限相關聯的實體上設定的存取金鑰，向服務匯流排進行驗證。 您可以接著使用此金鑰來產生共用存取簽章權杖，以便用戶端用來向服務匯流排進行驗證。

建議您透過 ACS 使用 SAS，因為它提供服務匯流排簡單、有彈性且容易使用的驗證配置。 在應用程式不需要管理已授權「使用者」概念的情況下，可以使用 SAS。

## 共用存取簽章驗證

[SAS 驗證](service-bus-sas-overview.md) 可讓您授予使用者存取以特定權限的服務匯流排資源。 服務匯流排中的 SAS 驗證牽涉到在服務匯流排資源上設定具有相關權限的密碼編譯金鑰。 接著用戶端可以藉由提出 SAS 權杖 (其中包含正在存取的資源 URI 及利用設定金鑰簽署的過期) 存取該資源。

您可以在服務匯流排命名空間上設定 SAS 的金鑰。 金鑰套用至該命名空間中的所有訊息實體。 您也可以在服務匯流排佇列和主題上設定金鑰。 服務匯流排轉送也支援 SAS。

若要使用 SAS，您可以設定 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 上命名空間、 佇列或主題，包含下列物件:

- 可識別規則的 *KeyName*。

- *PrimaryKey* 是用來簽署/驗證 SAS 權杖的密碼編譯金鑰。

- *SecondaryKey* 是用來簽署/驗證 SAS 權杖的密碼編譯金鑰。

- *權限*表示接聽、傳送或管理授與權限的集合。

在命名空間層級設定的授權規則可以授與命名空間中所有實體的存取權給具備使用對應金鑰簽署之權杖的用戶端。 在服務匯流排命名空間、佇列或主題上最多可以設定 12 條這類授權規則。 根據預設， [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx) 著作權所有，並使用已針對每個命名空間時先佈建。

若要存取實體，用戶端需要使用特定產生 SAS 權杖 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)。 SAS 權杖乃是使用資源字串的 HMAC-SHA256 所產生，該字串由宣告存取的資源 URI 以及具備與授權規則相關聯之密碼編譯金鑰的過期所組成。

服務匯流排的 SAS 驗證支援包含在 Azure .NET SDK 2.0 版或更新版本中。 SAS 包括支援 [SharedAccessAuthorizationRule](https://msdn.microsoft.com/library/azure/microsoft.servicebus.messaging.sharedaccessauthorizationrule.aspx)。 所有接受連接字串做為參數的 API 包括 SAS 連接字串的支援。

## ACS 驗證

透過 ACS 的服務匯流排驗證可透過隨附的「-sb」ACS 命名空間管理。 如果您想要建立服務匯流排命名空間隨附 ACS 命名空間，您無法建立您的服務匯流排命名空間，使用 Azure 傳統入口網站。您必須建立命名空間使用 [New-azuresbnamespace](https://msdn.microsoft.com/library/azure/dn495165.aspx) PowerShell cmdlet。 例如：

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $true
```

若要避免建立 ACS 命名空間，請發出下列命令：

```
New-AzureSBNamespace <namespaceName> "<Region>” -CreateACSNamespace $false
```

例如，如果您建立稱為 **contoso.servicebus.windows.net** 的服務匯流排命名空間，隨即會自動佈建稱為 **contoso sb.accesscontrol.windows.net** 的隨附 ACS 命名空間。 對於 2014 年 8 月之前建立的所有命名空間，也會建立隨附的 ACS 命名空間。

根據預設，此隨附 ACS 命名空間中會佈建具備所有權限的預設服務身分識別 "owner"。 您可以藉由設定適當的信任關係來透過 ACS 取得任何服務匯流排實體的細微控制。 您可以設定其他服務身分識別以管理服務匯流排實體的存取權。

若要存取實體，用戶端會藉由出示其認證，利用適當宣告向 ACS 要求 SWT 權杖。 SWT 權杖必須做為要求的一部分傳送至服務匯流排，以啟用用於存取實體的用戶端授權。

服務匯流排的 ACS 驗證支援包含在 Azure .NET SDK 2.0 版或更新版本中。 此驗證包括支援 [SharedSecretTokenProvider](https://msdn.microsoft.com/library/azure/microsoft.servicebus.sharedsecrettokenprovider.aspx)。 所有接受連接字串做為參數的 API 包括 ACS 連接字串的支援。

## 後續步驟

請繼續閱讀 [使用服務匯流排的共用存取簽章驗證](service-bus-shared-access-signature-authentication.md) 如需有關 SAS 的詳細資訊。

在服務匯流排 SAS 的高階概觀，請參閱 [共用存取簽章](service-bus-sas-overview.md)。

您可以找到 ACS 權杖中的詳細資訊 [How to: 從透過 OAuth WRAP 通訊協定的 ACS 要求權杖](https://msdn.microsoft.com/library/hh674475.aspx)。








