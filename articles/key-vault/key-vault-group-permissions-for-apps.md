---
title: Engedélyek biztosítása nagyobb számos alkalmazás egy Azure key vault – Azure Key Vault eléréséhez |} A Microsoft Docs
description: Ismerje meg, hogyan biztosítson engedélyt számú alkalmazás részére egy kulcstartó eléréséhez
services: key-vault
documentationcenter: ''
author: amitbapat
manager: barbkess
tags: azure-resource-manager
ms.assetid: 785d4e40-fb7b-485a-8cbc-d9c8c87708e6
ms.service: key-vault
ms.workload: identity
ms.tgt_pltfrm: na
ms.topic: conceptual
ms.date: 01/07/2019
ms.author: ambapat
ms.openlocfilehash: 187d455003cf8b1c9402e24755c5f15b703cd9ad
ms.sourcegitcommit: fec0e51a3af74b428d5cc23b6d0835ed0ac1e4d8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/12/2019
ms.locfileid: "56114399"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>A key vault több alkalmazások hozzáférés biztosítása

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

Hozzáférés-vezérlési házirend segítségével számos alkalmazások hozzáférést egy kulcstartót. Egy hozzáférés-vezérlési szabályzat legfeljebb 1024 alkalmazásokat, és konfigurációja a következő:

1. Hozzon létre egy Azure Active Directory biztonsági csoportot. 
2. Az alkalmazás összes társított az egyszerű szolgáltatások a biztonsági csoport hozzáadása.
3. A Key Vault a biztonsági csoport hozzáférési jogot.

## <a name="prerequisites"></a>Előfeltételek

Az Előfeltételek a következők:
* [Telepítse az Azure PowerShellt](/powershell/azure/overview).
* [Az Azure Active Directory V2 PowerShell modul telepítése](https://www.powershellgallery.com/packages/AzureAD).
* Csoportok az Azure Active Directory-bérlő létrehozása/szerkesztése jogosultság. Ha nem rendelkezik engedélyekkel, szükség lehet az Azure Active Directory-rendszergazdától. Lásd: [kapcsolatos Azure Key Vault-kulcsok, titkos kulcsok és tanúsítványok](about-keys-secrets-and-certificates.md) részleteiről a Key Vault hozzáférési házirend engedélyek.

## <a name="granting-key-vault-access-to-applications"></a>Alkalmazásokhoz való hozzáférést a Key Vault

A PowerShellben futtassa a következő parancsokat:

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
Set-AzKeyVaultAccessPolicy –VaultName ContosoVault –ObjectId $aadGroup.ObjectId `
-PermissionsToKeys decrypt,encrypt,unwrapKey,wrapKey,verify,sign,get,list,update,create,import,delete,backup,restore,recover,purge `
–PermissionsToSecrets get,list,set,delete,backup,restore,recover,purge `
–PermissionsToCertificates get,list,delete,create,import,update,managecontacts,getissuers,listissuers,setissuers,deleteissuers,manageissuers,recover,purge,backup,restore `
-PermissionsToStorage get,list,delete,set,update,regeneratekey,getsas,listsas,deletesas,setsas,recover,backup,restore,purge 
 
# Of course you can adjust the permissions as required 
```

Ha különböző engedélykészletet az alkalmazások azon csoportját, biztosítania kell, hozzon létre egy külön Azure Active Directory biztonsági csoportot az ilyen alkalmazásokhoz.

## <a name="next-steps"></a>További lépések

Ismerje meg, hogyan [kulcstartó védelme](key-vault-secure-your-key-vault.md).
