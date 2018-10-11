---
title: Engedélyek biztosítása nagyobb számos alkalmazás egy Azure key vault eléréséhez |} A Microsoft Docs
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
ms.devlang: na
ms.topic: conceptual
ms.date: 10/09/2018
ms.author: ambapat
ms.openlocfilehash: 639dfb6e3231a5eba3d6ecb9cd0198f5718b4aef
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49079021"
---
# <a name="grant-permission-to-many-applications-to-access-a-key-vault"></a>Engedély megadása a számú alkalmazás részére egy kulcstartó eléréséhez

## <a name="q-i-have-several-applications-that-need-to-access-a-key-vault-how-can-i-give-these-applications-up-to-1024-access-to-key-vault"></a>K: van több kulcstároló elérését igénylő alkalmazások, hogyan tudom ezeket az alkalmazásokat (legfeljebb 1024) hozzáférést biztosíthat a Key Vault?

A Key Vault hozzáférés-vezérlési szabályzat legfeljebb 1024 bejegyzések támogatja. Ugyanakkor létrehozhat egy Azure Active Directory biztonsági csoportot is. A társított szolgáltatás rendszerbiztonsági tagok hozzáadása a biztonsági csoport, és ezután a Key Vault a biztonsági csoport hozzáférési jogot.

Az Előfeltételek a következők:
* [Az Azure Active Directory V2 PowerShell modul telepítése](https://www.powershellgallery.com/packages/AzureAD).
* [Telepítse az Azure PowerShellt](/powershell/azure/overview).
* Futtassa a következő parancsokat, csoportok az Azure Active Directory-bérlő létrehozása/szerkesztése engedélyre van szükségük. Ha nem rendelkezik engedélyekkel, szükség lehet az Azure Active Directory-rendszergazdától. Lásd: [kapcsolatos Azure Key Vault-kulcsok, titkos kulcsok és tanúsítványok](about-keys-secrets-and-certificates.md) részleteiről a Key Vault hozzáférési házirend engedélyek.

Most futtassa a következő parancsokat a PowerShellben.

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
