---
title: Virtuális gép üzembe helyezése a VHD-ből az Azure Marketplace-en |} A Microsoft Docs
description: Azt ismerteti, hogyan kell regisztrálni a virtuális gép Azure által üzembe helyezett virtuális merevlemezről.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: v-miclar
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 11/30/2018
ms.author: pbutlerm
ms.openlocfilehash: 9157ce7f8f16bc60a6d5c16fa992a5402cf2d7ad
ms.sourcegitcommit: 5b869779fb99d51c1c288bc7122429a3d22a0363
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 12/10/2018
ms.locfileid: "53190730"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Virtuális gép üzembe helyezése a VHD-ből

Ez a szakasz azt ismerteti, hogyan helyezhet üzembe egy virtuális gépet (VM) egy Azure által üzembe helyezett virtuális merevlemezről (VHD).  Felsorolja a szükséges eszközöket és a egy felhasználói Virtuálisgép-lemezkép létrehozásához, majd üzembe helyezése az Azure-ban a PowerShell-parancsfájlok használatával.

Miután a virtuális merevlemezek (VHD) feltöltése – az általánosított operációs rendszer virtuális Merevlemeze és nulla vagy több lemez VHD-k – az Azure storage-fiók, regisztrálhatja azokat felhasználói Virtuálisgép-lemezképként. Ezután tesztelheti ezt a lemezképet. Az operációs rendszer virtuális Merevlemeze általánosítva van, mert Ön nem helyezheti közvetlenül üzembe a virtuális gép azáltal, hogy a virtuális merevlemez URL-CÍMÉT.

Virtuálisgép-rendszerképek kapcsolatos további információkért tekintse meg a következő blogbejegyzésekben:

- [Virtuálisgép-lemezkép](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM-lemezkép PowerShell "How To:](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)


## <a name="prerequisite-install-the-necessary-tools"></a>Előfeltétel: a szükséges eszközök telepítése

Ha még nem tette meg, telepítse az Azure PowerShell-lel és az Azure CLI, az alábbi utasítások szerint:

- [Az Azure PowerShell telepítése a Windows a Powershellgettel](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)
- [Telepítse az Azure CLI 2.0-t.](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>A központi telepítés lépései

Az alábbi lépéseket létrehozása és üzembe helyezése felhasználói Virtuálisgép-lemezkép fog használni:

1. Hozzon létre a virtuális gép rögzítése és a lemezkép általánosítása vonja maga után felhasználói lemezkép. 
2. Tanúsítványok létrehozása és a egy új Azure Key vaultban tárolja őket. Egy tanúsítványra szükség a Rendszerfelügyeleti webszolgáltatások biztonságos kapcsolatot a virtuális géphez.  Azure Resource Manager-sablon és a egy Azure PowerShell-szkript-okat. 
3. Telepítse a virtuális Gépet egy felhasználói Virtuálisgép-lemezkép a megadott sablon és parancsfájl használatával.

A virtuális gép üzembe helyezését követően készen áll [igazolja, a Virtuálisgép-lemezkép](./cpp-certify-vm.md).

2.  Kattintson a **új** és keressen rá a **sablonalapú telepítés**, majd **szerkesztőben saját sablon készítése**.  <br/>
  ![Virtuális merevlemez központi telepítési sablont az Azure Portalon hozhat létre](./media/publishvm_021.png)

3. Másolja és illessze be a [JSON-sablon](./cpp-deploy-json-template.md) a szerkesztőben, majd kattintson az **mentése**. <br/>
  ![Virtuális merevlemez üzembe helyezési sablon mentésére az Azure Portalon](./media/publishvm_022.png)

4. Adja meg a paraméter értékét a megjelenített **egyéni üzembe helyezés** tulajdonságait tartalmazó oldalakon.

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **A paraméter**              |   **Leírás**                                                            |
   |  -------------              |   ---------------                                                            |
   | Felhasználói Tárfiók neve   | Ahol a általánosított virtuális merevlemez tárfiók neve                    |
   | Felhasználói tároló neve | Tároló neve, ahol az általánosított virtuális merevlemez                          |
   | Nyilvános IP-cím DNS-neve      | Nyilvános IP-DNS-név                                                           |
   | Rendszergazdai felhasználónév             | Új virtuális gép rendszergazdai fiók felhasználóneve                                  |
   | Rendszergazdai jelszó              | Új virtuális gép rendszergazdai fiók jelszavát                                  |
   | Operációs rendszer típusa                     | Virtuális gép operációs rendszer: `Windows` \| `Linux`                                    |
   | Előfizetés azonosítója             | A kiválasztott előfizetés azonosítója                                      |
   | Hely                    | Az üzembe helyezés földrajzi helye                                        |
   | Virtuális gép mérete                     | [Az Azure Virtuálisgép-méret](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), például `Standard_A2` |
   | Nyilvános IP-cím neve      | A nyilvános IP-cím neve                                               |
   | Virtuális gép neve                     | Az új virtuális gép neve                                                           |
   | Virtuális hálózat neve        | A virtuális gép által használt virtuális hálózat neve                                   |
   | Hálózati adapter neve                    | A hálózati kártyát a virtuális hálózaton futó neve               |
   | VIRTUÁLIS MEREVLEMEZ URL-CÍME                     | Hajtsa végre az operációsrendszer-lemez VHD URL-címe                                                     |
   |  |  |
            
5. Ezeket az értékeket ad meg, miután kattintson **beszerzési**. 

Azure megkezdődik az üzembe helyezés: a megadott felügyelt tartalmazó virtuális Merevlemezt, a megadott tárfiók elérési útja, létrehoz egy új virtuális Gépet.  Követheti a folyamat állapotát, az Azure Portalon kattintson a **virtuális gépek** a portál bal oldalán.  Amikor a virtuális gép létrejött, az állapot változik `Starting` való `Running`. 


### <a name="deploy-a-vm-from-powershell"></a>Virtuális gép üzembe helyezése a PowerShell

Nagy virtuális gép üzembe helyezése az imént létrehozott általánosított Virtuálisgép-rendszerképek, használja a következő parancsmagokat.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>További lépések

A rendszer ezután [felhasználói Virtuálisgép-lemezkép létrehozása](cpp-create-user-image.md) a megoldáshoz.

