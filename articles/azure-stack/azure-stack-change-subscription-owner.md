---
title: Az Azure Stack felhasználói előfizetés tulajdonosának frissítése |} A Microsoft Docs
description: Módosítsa a számlázási tulajdonosi az Azure STack felhasználói előfizetések.
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
ms.date: 06/12/2018
ms.author: sethm
ms.reviewer: shnatara
ms.openlocfilehash: a784f169bfdf23255b27d50f0e135384db6b2b88
ms.sourcegitcommit: 4b1083fa9c78cd03633f11abb7a69fdbc740afd1
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/10/2018
ms.locfileid: "49077630"
---
# <a name="change-the-owner-for-an-azure-stack-user-subscription"></a>Módosíthatja a tulajdonost az Azure Stack felhasználói előfizetés

Az Azure Stack-operátorok a PowerShell használatával módosíthatja a felhasználói előfizetés számlázási tulajdonosa. A tulajdonos módosítása egyik oka, cserélje le a felhasználó elhagyja a szervezetet.   

Két típusa van *tulajdonosok* előfizetéshez társított:

- **Számlázási tulajdonosi** – alapértelmezés szerint a számlázási tulajdonosi a felhasználói fiók, amely beolvassa az előfizetés egy ajánlatra, és majd az adott előfizetéshez tartozó számlázási kapcsolat tulajdonosa. Ez a fiók akkor is az előfizetés rendszergazdája.  Csak egy felhasználói fiók a megjelölés rendelkezhet egy adott előfizetés. A számlázási tulajdonosi gyakran egy szervezet vagy csapat vezető. 

  PowerShell-parancsmagot használhatja a **Set-AzsUserSubscription** számlázási tulajdonosát a következőre módosítani.  

- **RBAC-szerepkörök keresztül hozzáadva tulajdonosok** – további felhasználók is megadható, hogy a tulajdonosi szerepkör segítségével a [szerepköralapú hozzáférés-vezérlés](azure-stack-manage-permissions.md) (RBAC) rendszert.  Tetszőleges számú további felhasználói fiókokat a számlázási tulajdonosi segítőosztályok tulajdonosként is hozzáadhatók. További tulajdonosok is az előfizetés rendszergazdái és az összes jogosult az előfizetés kivételével törli a számlázási tulajdonosi engedélyekkel. 

  Használhatja a Powershellt további tulajdonosok kezeléséhez, olvassa el [szerepköralapú hozzáférés-vezérlés kezelése az Azure PowerShell-lel]( https://docs.microsoft.com/azure/role-based-access-control/role-assignments-powershell).


## <a name="change-the-billing-owner"></a>A számlázási tulajdonosi módosítása
Futtassa a következő parancsfájlt, módosíthatja a felhasználói előfizetés számlázási tulajdonosa.  A számítógépen, amely a parancsfájl futtatásához használt kell csatlakozás az Azure Stack, és futtassa az Azure Stack PowerShell modul 1.3.0 vagy újabb. További információkért lásd: [telepítse az Azure Stack PowerShell](azure-stack-powershell-install.md). 

> [!Note]  
>  A több-bérlős Azure Stack az új tulajdonos ugyanabban a könyvtárban, a meglévő tulajdonosának kell lennie. Mielőtt az előfizetés tulajdonjogának egy felhasználónak, amely egy másik címtárban, előbb futtatnia kell [meghívni, hogy a felhasználó vendégként a címtárba](https://docs.microsoft.com/azure/active-directory/b2b/add-users-administrator). 


Cserélje le a következő értékeket a szkriptben, futtatásuk előtt: 
 
- **$ArmEndpoint** – adja meg a Resource Manager-végpontot a környezethez.  
- **$TenantId** – adja meg a bérlő azonosítója. 
- **$SubscriptionId** – adja meg az előfizetés-azonosítójára.
- **$OwnerUpn** – adjon meg egy fiókot, *user@example.com* új számlázási tulajdonosként hozzáadni.  


```PowerSHell   
# Setup Azure Stack Admin Environment
Add-AzureRmEnvironment -ARMEndpoint $ArmEndpoint -Name AzureStack-admin
Add-AzureRmAccount -Environment AzureStack-admin -TenantId $TenantId

# Select Admin Subscriptionr
$providerSubscriptionId = (Get-AzureRmSubscription -SubscriptionName "Default Provider Subscription").Id
Write-Output "Setting context the Default Provider Subscription: $providerSubscriptionId" 
Set-AzureRmContext -Subscription $providerSubscriptionId

# Change User Subscription owner
$subscription = Get-AzsUserSubscription -SubscriptionId $SubscriptionId
$Subscription.Owner = $OwnerUpn
Set-AzsUserSubscription -InputObject $subscription
```


## <a name="next-steps"></a>További lépések
[Szerepköralapú hozzáférés-vezérlés kezelése](azure-stack-manage-permissions.md)
