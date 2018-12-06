---
title: Az Azure Stack felhasználói előfizetés tulajdonosának frissítése |} A Microsoft Docs
description: Módosítsa a számlázási tulajdonosi az Azure Stack felhasználói előfizetések.
services: azure-stack
documentationcenter: ''
author: sethmanheim
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.workload: na
ms.tgt_pltfrm: na
ms.devlang: PowerShell
ms.topic: get-started-article
ms.date: 10/19/2018
ms.author: sethm
ms.reviewer: shnatara
ms.openlocfilehash: 46dd9c786fddb2f6ce9fb8a761a1b9f135a82d74
ms.sourcegitcommit: 5d837a7557363424e0183d5f04dcb23a8ff966bb
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/06/2018
ms.locfileid: "52959196"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Módosíthatja a tulajdonost az Azure Stack felhasználói előfizetés

Az Azure Stack-operátorok a PowerShell használatával módosíthatja a felhasználói előfizetés számlázási tulajdonosa. A tulajdonos módosítása egyik oka például, cserélje le a felhasználó elhagyja a szervezetet.   

Két típusa van *tulajdonosok* előfizetéshez társított:

- **Számlázási tulajdonosi**: alapértelmezés szerint a számlázási tulajdonosi a felhasználói fiók, amely beolvassa az előfizetés egy ajánlatra, és majd az adott előfizetéshez tartozó számlázási kapcsolat tulajdonosa. Ez a fiók akkor is az előfizetés rendszergazdája. Csak egy felhasználói fiók a megjelölés rendelkezhet egy adott előfizetés. A számlázási tulajdonosi gyakran egy szervezet vagy csapat vezető. 

  PowerShell-parancsmagot használhatja a [Set-AzsUserSubscription](/powershell/module/azs.subscriptions.admin/set-azsusersubscription) számlázási tulajdonosát a következőre módosítani.  

- **RBAC-szerepkörök keresztül hozzáadva tulajdonosok** – további felhasználók is megadható a **tulajdonosa** szerepkör a [szerepköralapú hozzáférés-vezérlés](azure-stack-manage-permissions.md) (RBAC) rendszer. Tetszőleges számú további felhasználói fiókokat a számlázási tulajdonosi segítőosztályok tulajdonosként is hozzáadhatók. További tulajdonosok is az előfizetés rendszergazdája, és minden az előfizetést, kivéve, törölheti a számlázási tulajdonosi jogosultságokkal rendelkeznek. 

  Használhatja a Powershellt további tulajdonosok kezeléséhez, olvassa el [szerepköralapú hozzáférés-vezérlés kezelése az Azure PowerShell-lel](/azure/role-based-access-control/role-assignments-powershell).

## <a name="change-the-billing-owner"></a>A számlázási tulajdonosi módosítása

Futtassa a következő parancsfájlt, módosíthatja a felhasználói előfizetés számlázási tulajdonosa. A számítógépen, amely a parancsfájl futtatásához használt kell csatlakozás az Azure Stack, és futtassa az Azure Stack PowerShell modul 1.3.0 vagy újabb. További információkért lásd: [telepítse az Azure Stack PowerShell](azure-stack-powershell-install.md). 

> [!Note]
>  Egy több-bérlős Azure Stack az új tulajdonos ugyanabban a könyvtárban, a meglévő tulajdonosának kell lennie. Mielőtt az előfizetés tulajdonjogának egy felhasználónak, amely egy másik címtárban, előbb futtatnia kell [meghívni, hogy a felhasználó vendégként a címtárba](../active-directory/b2b/add-users-administrator.md). 

Cserélje le a következő értékeket a szkriptben, futtatásuk előtt: 
 
- **$ArmEndpoint**: Adja meg a Resource Manager-végpontot a környezethez.  
- **$TenantId**: Adja meg a bérlő azonosítója. 
- **$SubscriptionId**: Adja meg az előfizetés-azonosítójára.
- **$OwnerUpn**: Adjon meg egy fiókot, **user@example.com** új számlázási tulajdonosként hozzáadni.  

```PowerShell   
# Set up Azure Stack admin environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select admin subscription
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context to the Default Provider Subscription: $providerSubscriptionId" 
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change user subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```

## <a name="next-steps"></a>További lépések

[Szerepköralapú hozzáférés-vezérlés kezelése](azure-stack-manage-permissions.md)
