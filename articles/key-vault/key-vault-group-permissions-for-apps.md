---
title: Engedélyek biztosítása nagyobb számos alkalmazás egy Azure key vault – Azure Key Vault eléréséhez |} A Microsoft Docs
description: Ismerje meg, hogyan biztosítson engedélyt számú alkalmazás részére egy kulcstartó eléréséhez
services: key-vault
documentationcenter: ''
author: amitbapat
manager: mbaldwin
tags: azure-resource-manager
ms.assetid: 785d4e40-fb7b-485a-8cbc-d9c8c87708e6
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: cd680f24eafe61bc73fa6eb91df4b4dfa5f5399b
ms.sourcegitcommit: fbf0124ae39fa526fc7e7768952efe32093e3591
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 01/08/2019
ms.locfileid: "54073428"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>A key vault több alkalmazások hozzáférés biztosítása

Hozzáférés-vezérlési házirend segítségével számos alkalmazások hozzáférést egy kulcstartót. Egy hozzáférés-vezérlési szabályzat legfeljebb 1024 alkalmazásokat, és konfigurációja a következő:

1. Hozzon létre egy Azure Active Directory biztonsági csoportot. 
2. Az alkalmazás összes társított az egyszerű szolgáltatások a biztonsági csoport hozzáadása.
3. A Key Vault a biztonsági csoport hozzáférési jogot.

Az Előfeltételek a következők:
* [Az Azure Active Directory V2 PowerShell modul telepítése](https://www.powershellgallery.com/packages/AzureAD).
* [Telepítse az Azure PowerShellt](/powershell/azure/overview).
* Futtassa a következő parancsokat, csoportok az Azure Active Directory-bérlő létrehozása/szerkesztése engedélyre van szükségük. Ha nem rendelkezik engedélyekkel, szükség lehet az Azure Active Directory-rendszergazdától. Lásd: [kapcsolatos Azure Key Vault-kulcsok, titkos kulcsok és tanúsítványok](about-keys-secrets-and-certificates.md) részleteiről a Key Vault hozzáférési házirend engedélyek.

Most futtassa a következő parancsokat a PowerShellben:

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
Set-AzureRmKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId `
-PermissionsToKeys decrypt,encrypt,unwrapKey,wrapKey,verify,sign,get,list,update,create,import,delete,backup,restore,recover,purge `
–PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge `
–PermissionsToCertificates get,list,delete,create,import,update,managecontacts,getissuers,listissuers,setissuers,deleteissuers,manageissuers,recover,purge,backup,restore `
-PermissionsToStorage get,list,delete,set,update,regeneratekey,getsas,listsas,deletesas,setsas,recover,backup,restore,purge 
 
# Of course you can adjust the permissions as required 
```

Ha különböző engedélykészletet az alkalmazások azon csoportját, biztosítania kell, hozzon létre egy külön Azure Active Directory biztonsági csoportot az ilyen alkalmazásokhoz.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [kulcstartó védelme](key-vault-secure-your-key-vault.md).
