---
title: Számos alkalmazás egy az Azure key vault elérésére engedélyt |} Microsoft Docs
description: Megtudhatja, hogyan számos alkalmazás kulcstároló elérésére engedélyt szeretne megadni
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 785d4e40-fb7b-485a-8cbc-d9c8c87708e6
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.devlang: na
ms.topic: article
ms.date: 12/01/2016
ms.author: ambapat
ms.openlocfilehash: ddeaf184138bd48d324799ddb45248b0a0ee8eeb
ms.sourcegitcommit: 48ab1b6526ce290316b9da4d18de00c77526a541
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/23/2018
---
# <a name="grant-permission-to-many-applications-to-access-a-key-vault"></a>Számos alkalmazás kulcstároló elérésére engedélyt

## <a name="q-i-have-several-over-16-applications-that-need-to-access-a-key-vault-since-key-vault-only-allows-16-access-control-entries-how-can-i-achieve-that"></a>K: van több kulcstároló eléréséhez szükséges (több mint 16) alkalmazásokhoz. Hogyan gondoskodhatok erről, ha a Key Vault csak 16 hozzáférés-vezérlési bejegyzést engedélyez?

Key Vault hozzáférés-vezérlési házirend csak a 16 bejegyzések támogatja. Azonban létrehozhat egy Azure Active Directory biztonsági csoport. A társított szolgáltatás rendszerbiztonsági tagok hozzáadása ehhez a biztonsági csoporthoz, és majd a hozzáférési jogot a Key Vault biztonsági csoport.

Az alábbiakban az Előfeltételek:
* [Azure Active Directory V2 PowerShell-modul telepítéséhez](https://www.powershellgallery.com/packages/AzureAD).
* [Telepítse az Azure PowerShellt](/powershell/azure/overview).
* A következő parancsokat, a csoportokat az Azure Active Directory-bérlő létrehozása/szerkesztése engedélyekre van szükség. Ha nem rendelkezik engedélyekkel, szükség lehet az Azure Active Directory-rendszergazdától.

Most futtassa a következő parancsokat a PowerShell.

```powershell
# Connect to Azure AD 
Connect-AzureAD 
 
# Create Azure Active Directory Security Group 
$aadGroup = New-AzureADGroup -Description "Contoso App Group" -DisplayName "ContosoAppGroup" -MailEnabled 0 -MailNickName none -SecurityEnabled 1 
 
# Find and add your applications (ServicePrincipal ObjectID) as members to this group 
$spn = Get-AzureADServicePrincipal –SearchString "ContosoApp1" 
Add-AzureADGroupMember –ObjectId $aadGroup.ObjectId -RefObjectId $spn.ObjectId 
 
# You can add several members to this group, in this fashion. 
 
# Set the Key Vault ACLs 
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId -PermissionsToKeys all –PermissionsToSecrets all –PermissionsToCertificates all 
 
# Of course you can adjust the permissions as required 
```

Ha szeretne egy másik engedélycsoportja a szükséges engedélyek az alkalmazások azon csoportját, az ilyen alkalmazások külön Azure Active Directory biztonsági csoport létrehozása.

## <a name="next-steps"></a>További lépések

További tudnivalók a [a kulcstartót biztonságos](key-vault-secure-your-key-vault.md).
