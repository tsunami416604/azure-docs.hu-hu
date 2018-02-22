---
title: "Ellenőrizze virtulálisgép-skálázási készletekben Azure verem használható |} Microsoft Docs"
description: "Ismerje meg, hogy a felhő üzemeltetője adhat hozzá virtuálisgép-méretezési verem Azure piactérről"
services: azure-stack
author: brenduns
manager: femila
editor: 
ms.assetid: 
ms.service: azure-stack
ms.topic: article
ms.date: 9/25/2017
ms.author: brenduns
ms.reviewer: anajod
keywords: 
ms.openlocfilehash: 6e4c1145b8ff4665024b4532cf9cc9ac6a0a24a1
ms.sourcegitcommit: d87b039e13a5f8df1ee9d82a727e6bc04715c341
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 02/21/2018
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Azure-készletben elérhetővé virtuálisgép-méretezési csoportok

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Virtuálisgép-méretezési csoportok az Azure-verem számítási erőforrás. Üzembe helyezését, és az azonos virtuális gépek kezelésére használhatja őket. Az összes virtuális gép-e konfigurálva a, méretezési készlet nem szükséges előre létesítési virtuális gépek. Megkönnyíti a nagy méretű szolgáltatások nagy számítási, a big Data típusú adatok és a tárolóalapú munkafolyamatok célzó.

Ez a témakör végigvezeti Önt az Azure-verem piactéren méretezési csoportok elérhetővé a folyamat. Ez az eljárás befejezése után a felhasználók hozzáadhatnak virtuálisgép-méretezési állítja az előfizetések.

Virtuálisgép-méretezési csoportok Azure veremben olyanok, mint az Azure virtuálisgép-méretezési készlet. További információkért tekintse meg a következő videók:
* [Mark Russinovich talks Azure Scale Sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) (Mark Russinovich ismerteti az Azure-alapú méretezési csoportokat)
* [Virtual Machine Scale Sets with Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman) (A virtuálisgép-méretezési csoportokról Guy Bowerman mesél)

Azure-veremben a virtuálisgép-méretezési csoportok nem támogatják az automatikus skálázása. A méretezési készletben, a verem Azure portál, a Resource Manager-sablonok vagy a PowerShell használatával adhat hozzá további példányokat.

## <a name="prerequisites"></a>Előfeltételek
* **PowerShell és az eszközök**

   Azure verem konfigurált PowerShell és a telepítése és az Azure-verem eszközök. Lásd: [, amelyekből megismerheti a PowerShell használatával a Azure verem](azure-stack-powershell-configure-quickstart.md).

   Az Azure-verem eszközök telepítése után ellenőrizze, hogy a következő PowerShell-modul importálása (relatív elérési útja a. a AzureStack főkiszolgálós eszközök \ComputeAdmin mappájára):

        Import-Module .\AzureStack.ComputeAdmin.psm1

* **Operációsrendszer-lemezkép**

   Ha még nem adott hozzá az operációs rendszer lemezképét az Azure-verem piactéren, lásd: [a Windows Server 2016 Virtuálisgép-lemezkép hozzáadása a verem Azure piactér](azure-stack-add-default-image.md).

   A Linux-támogatás, Ubuntu Server 16.04 töltse le, majd vegye fel azt használatával ```Add-AzsVMImage``` a következő paraméterekkel: ```-publisher "Canonical" -offer "UbuntuServer" -sku "16.04-LTS"```.

## <a name="add-the-virtual-machine-scale-set"></a>A virtuálisgép-méretezési csoport hozzáadása

A következő PowerShell parancsfájlt a rendszerkörnyezetnek szerkesztése, és futtassa a virtuálisgép-méretezési beállítása az Azure-verem piactér hozzáadása. 

``$User`` az a fiók a felügyeleti portál kapcsolódni. Például: serviceadmin@contoso.onmicrosoft.com.

```
$Arm = "https://adminmanagement.local.azurestack.external"
$Location = "local"

Add-AzureRMEnvironment -Name AzureStackAdmin -ArmEndpoint $Arm

$Password = ConvertTo-SecureString -AsPlainText -Force "<your Azure Stack administrator password>"

$User = "<your Azure Stack service administrator user name>"

$Creds =  New-Object System.Management.Automation.PSCredential $User, $Password

$AzsEnv = Get-AzureRmEnvironment AzureStackAdmin
$AzsEnvContext = Add-AzureRmAccount -Environment $AzsEnv -Credential $Creds
Select-AzureRmProfile -Profile $AzsEnvContext

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
```

## <a name="remove-a-virtual-machine-scale-set"></a>A virtuálisgép-méretezési csoport törlése

Távolítsa el a virtuális gépek méretezési készlet gyűjteményelem, és futtassa a következő PowerShell-parancsot:

    Remove-AzsVMSSGalleryItem

> [!NOTE]
> A gyűjteményelem azonnal nem lehet eltávolítani. Szükség lehet többször is frissítenie a portál a piactérről eltávolítása előtt.


## <a name="next-steps"></a>További lépések
[Gyakori kérdések az Azure-verem](azure-stack-faq.md)

