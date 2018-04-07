---
title: Ellenőrizze virtulálisgép-skálázási készletekben Azure verem használható |} Microsoft Docs
description: Ismerje meg, hogy a felhő üzemeltetője adhat hozzá virtuálisgép-méretezési verem Azure piactérről
services: azure-stack
author: brenduns
manager: femila
editor: ''
ms.assetid: ''
ms.service: azure-stack
ms.topic: article
ms.date: 04/06/2018
ms.author: brenduns
ms.reviewer: anajod
keywords: ''
ms.openlocfilehash: cdabd2a9d336cdd8ac83d27460fe129c45b7e1c6
ms.sourcegitcommit: 3a4ebcb58192f5bf7969482393090cb356294399
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/06/2018
---
# <a name="make-virtual-machine-scale-sets-available-in-azure-stack"></a>Azure-készletben elérhetővé virtuálisgép-méretezési csoportok

*A következőkre vonatkozik: Azure verem integrált rendszerek és az Azure verem szoftverfejlesztői készlet*

Virtuálisgép-méretezési csoportok az Azure-verem számítási erőforrás. Üzembe helyezését, és az azonos virtuális gépek kezelésére használhatja őket. Az összes virtuális gép-e konfigurálva a, méretezési készlet nem szükséges előre létesítési virtuális gépek. Megkönnyíti a nagy méretű szolgáltatások nagy számítási, a big Data típusú adatok és a tárolóalapú munkafolyamatok célzó.

Ez a cikk végigvezeti a folyamat méretezési csoportok elérhetővé a verem Azure piactéren. Ez az eljárás befejezése után a felhasználók hozzáadhatnak virtuálisgép-méretezési állítja az előfizetések.

Virtuálisgép-méretezési csoportok Azure veremben olyanok, mint az Azure virtuálisgép-méretezési készlet. További információkért tekintse meg a következő videók:
* [Mark Russinovich talks Azure Scale Sets](https://channel9.msdn.com/Blogs/Regular-IT-Guy/Mark-Russinovich-Talks-Azure-Scale-Sets/) (Mark Russinovich ismerteti az Azure-alapú méretezési csoportokat)
* [Virtual Machine Scale Sets with Guy Bowerman](https://channel9.msdn.com/Shows/Cloud+Cover/Episode-191-Virtual-Machine-Scale-Sets-with-Guy-Bowerman) (A virtuálisgép-méretezési csoportokról Guy Bowerman mesél)

Azure-veremben virtuálisgép-méretezési készlet nem támogatja az automatikus skálázása. A méretezési készletben, a verem Azure portál, a Resource Manager-sablonok vagy a PowerShell használatával adhat hozzá további példányokat.

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

Select-AzureRmSubscription -SubscriptionName "Default Provider Subscription"

Add-AzsVMSSGalleryItem -Location $Location
```

## <a name="update-images-in-a-virtual-machine-scale-set"></a>A virtuálisgép-méretezési csoportban lévő lemezképek frissítéséhez 
A virtuálisgép-méretezési csoport létrehozása után a felhasználók frissítheti a méretezési készletben nélkül a méretezési készletben, hogy újból létre kell hozni a lemezképeket. A lemezképek folyamata attól függ, hogy a következő esetekben:

1. Virtuálisgép-méretezési csoport központi telepítési sablont **határozza meg a legújabb** a *verzió*:  

   Ha a *verzió* be van állítva az **legújabb** a a *imageReference* a sablon egy bővített szakasz beállításához műveleteket végez a méretezési készlet használata növelheti az elérhető legújabb verzióra a kép a bővített példányok megadása Egy felskálázott befejezése után törölheti a régebbi virtuálisgép-méretezési készletek példánya.  (A értékeinek *publisher*, *kínálnak*, és *sku* változatlanok maradnak). 

   Az alábbiakban egy példa megadó *legújabb*:  

          "imageReference": {
             "publisher": "[parameters('osImagePublisher')]",
             "offer": "[parameters('osImageOffer')]",
             "sku": "[parameters('osImageSku')]",
             "version": "latest"
             }

   Felskálázott használhatja új lemezképet, akkor le kell töltenie az új lemezkép:  

   - A piactéren kép verziója újabb, mint a lemezképet a méretezési csoportban lévő esetén: az új lemezképet, amely a felváltja a régebbi kép letöltése. Után váltja fel a lemezképet, a felhasználó vertikális felskálázás lépne. 

   - Ha a lemezképet a piactér verziója ugyanaz, mint a lemezképet a méretezési csoportban lévő: törli a lemezképet, amelyet a méretezési csoportban lévő használ, és töltse le az új lemezképet. Az a kép eltávolítása az eredeti és az új lemezkép letöltése közötti időszakban nem vertikális felskálázás. 
      
     A folyamat be nem szükséges resyndicate lemezképeket, amelyek segítségével a ritka fájlformátum, bevezetett 1803 verziójával. 
 

2. Virtuálisgép-méretezési csoport központi telepítési sablont **nem adja meg a legújabb** a *verzió* és Ehelyett adja meg egy verziószámot:  

     Ha letöltötte a lemezkép egy újabb verziójával (amely megváltoztatja a rendelkezésre álló verzió), a méretezési készlet nem növelheti. Ez az elvárt működés, mivel a méretezési készlet sablonban megadott lemezkép verzió elérhetőnek kell lennie.  

További információkért lásd: [operációsrendszer-lemezek és lemezképek](.\user\azure-stack-compute-overview.md#operating-system-disks-and-images).  


## <a name="remove-a-virtual-machine-scale-set"></a>A virtuálisgép-méretezési csoport törlése

Távolítsa el a virtuális gépek méretezési készlet gyűjteményelem, és futtassa a következő PowerShell-parancsot:

    Remove-AzsVMSSGalleryItem

> [!NOTE]
> A gyűjteményelem azonnal nem lehet eltávolítani. Éjszakai kell többször is frissítenie a portál az elem látható a piactérről eltávolítása előtt.


## <a name="next-steps"></a>További lépések
[Gyakori kérdések az Azure-verem](azure-stack-faq.md)

