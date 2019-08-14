---
title: Engedélyek engedélyezése számos alkalmazás számára az Azure Key Vault eléréséhez – Azure Key Vault | Microsoft Docs
description: Megtudhatja, hogyan biztosíthat engedélyt számos alkalmazás számára egy kulcstartó eléréséhez
services: key-vault
author: amitbapat
manager: rkarlin
tags: azure-resource-manager
ms.service: key-vault
ms.topic: tutorial
ms.date: 08/12/2019
ms.author: ambapat
ms.openlocfilehash: 07ee544057ffeb0a5859cc771b124523ec79c9c0
ms.sourcegitcommit: 62bd5acd62418518d5991b73a16dca61d7430634
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/13/2019
ms.locfileid: "68976401"
---
# <a name="grant-several-applications-access-to-a-key-vault"></a>Több alkalmazás megadása a kulcstartóhoz

[!INCLUDE [updated-for-az](../../includes/updated-for-az.md)]

A hozzáférés-vezérlési szabályzat használatával több alkalmazás is biztosítható a kulcstartóhoz. Egy hozzáférés-vezérlési házirend akár 1024 alkalmazást is képes támogatni, és a következőképpen van konfigurálva:

1. Hozzon létre egy Azure Active Directory biztonsági csoportot. 
2. Adja hozzá az összes alkalmazáshoz társított egyszerű szolgáltatást a biztonsági csoporthoz.
3. Adja meg a biztonsági csoport hozzáférését a Key Vaulthoz.

## <a name="prerequisites"></a>Előfeltételek

Az előfeltételek a következők:
* [Telepítse az Azure PowerShellt](/powershell/azure/overview).
* [Telepítse a Azure Active Directory v2 PowerShell](https://www.powershellgallery.com/packages/AzureAD)-modult.
* A csoportok létrehozásához és szerkesztéséhez szükséges engedélyek a Azure Active Directory bérlőben. Ha nem rendelkezik megfelelő engedélyekkel, előfordulhat, hogy kapcsolatba kell lépnie Azure Active Directory rendszergazdájával. Key Vault hozzáférési házirend engedélyeivel kapcsolatos részletekért tekintse meg a [Azure Key Vault kulcsok, titkok és tanúsítványok](about-keys-secrets-and-certificates.md) című témakört.

## <a name="granting-key-vault-access-to-applications"></a>Key Vault hozzáférés biztosítása az alkalmazásokhoz

Futtassa a következő parancsokat a PowerShellben:

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

Ha más engedélyeket kell biztosítania az alkalmazások egy csoportjára, hozzon létre külön Azure Active Directory biztonsági csoportot az ilyen alkalmazásokhoz.

## <a name="next-steps"></a>További lépések

További információ a [Key Vault biztonságossá](key-vault-secure-your-key-vault.md)tételéről.
