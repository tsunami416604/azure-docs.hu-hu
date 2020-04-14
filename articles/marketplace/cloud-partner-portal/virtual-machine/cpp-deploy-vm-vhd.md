---
title: Virtuális gép üzembe helyezése a virtuális gépekről az Azure Piactérre
description: Bemutatja, hogyan regisztrálhat virtuális gépet egy Azure által telepített virtuális merevlemezről.
author: qianw211
ms.service: marketplace
ms.subservice: partnercenter-marketplace-publisher
ms.topic: conceptual
ms.date: 08/08/2019
ms.author: dsindona
ms.openlocfilehash: f13e4066137e0d76612040d9f6e5ff3d0aa399c8
ms.sourcegitcommit: 530e2d56fc3b91c520d3714a7fe4e8e0b75480c8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/14/2020
ms.locfileid: "81273902"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Virtuális gép üzembe helyezése a virtuális gépekről

> [!IMPORTANT]
> 2020. április 13-tól megkezdjük az Azure virtuálisgép-ajánlatok partnerközpontba való áthelyezését. Az áttelepítés után a Partnerközpontban hozza létre és kezelheti ajánlatait. Kövesse az [Azure Virtuálisgép technikai eszközeinek létrehozása](https://aka.ms/AzureVMTechAsset) az áttelepített ajánlatok kezeléséhez című, az Utasításokat.

Ez a szakasz bemutatja, hogyan telepíthet egy virtuális gépet (VM) egy Azure által telepített virtuális merevlemezről (VHD).  Felsorolja a szükséges eszközöket, és hogyan használhatja őket egy felhasználói virtuálisgép-lemezkép létrehozásához, majd telepítse az Azure-ba a PowerShell-parancsfájlok használatával.

Miután feltöltötte a virtuális merevlemezeket (VHD)- az általános operációs rendszer Virtuális merevlemezét és a nulla vagy több adatlemez Virtuálismerevlemezt – az Azure tárfiókjába, felhasználói virtuálisgép-lemezként regisztrálhatja őket. Akkor tesztelheted azt a képet. Mivel az operációs rendszer virtuális merevlemeze általános, nem telepítheti közvetlenül a virtuális gép a virtuális merevlemez URL-címét.

Ha többet szeretne megtudni a virtuális gép képeiről, olvassa el az alábbi blogbejegyzéseket:

- [Virtuálisgép-lemezkép](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM Image PowerShell "Hogyan"](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisite-install-the-necessary-tools"></a>Előfeltétel: telepítse a szükséges eszközöket

Ha még nem tette meg, telepítse az Azure PowerShellt és az Azure CLI-t az alábbi utasítások használatával:

- [Az Azure PowerShell telepítése](https://docs.microsoft.com/powershell/azure/install-Az-ps)
- [Telepítse az Azure CLI-t](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>A központi telepítés lépései

A következő lépésekkel hozzon létre és telepítsen egy felhasználói virtuálisgép-lemezképet:

1. Hozza létre a felhasználói virtuálisgép-lemezképet, amely a lemezkép rögzítését és általánosítását vonja maga után. 
2. Hozzon létre tanúsítványokat, és tárolja őket egy új Azure Key Vaultban. A virtuális gép biztonságos Rendszer-eredményeképpen létesítési kapcsolat létrehozásához tanúsítvány szükséges.  Egy Azure Resource Manager-sablon és egy Azure PowerShell-parancsfájl áll rendelkezésre. 
3. Telepítse a virtuális gép egy felhasználói virtuális gép lemezkép, a megadott sablon és parancsfájl használatával.

A virtuális gép üzembe helyezése után készen áll [a virtuális gép lemezképének hitelesítésére.](./cpp-certify-vm.md)

1. Kattintson **az Új** gombra, és keresse meg a **Sablon központi telepítését,** majd válassza **a Saját sablon létrehozása a Szerkesztőben**lehetőséget.  <br/>
   ![VHD-s telepítési sablon létrehozása az Azure Portalon](./media/publishvm_021.png)

1. Másolja és illessze be ezt a [JSON-sablont](./cpp-deploy-json-template.md) a szerkesztőbe, majd kattintson a **Mentés gombra.** <br/>
   ![VHD-telepítési sablon mentése az Azure Portalon](./media/publishvm_022.png)

1. Adja meg a megjelenített **egyéni központi telepítési** tulajdonságlapok paraméterértékeit.

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **Paraméter**              |   **Leírás**                                                            |
   |  -------------              |   ---------------                                                            |
   | Felhasználói tárfiók neve   | Tárfiók neve, ahol az általános virtuális merevlemez található                    |
   | Felhasználói tároló tárolójának neve | A tároló neve, ahol az általános virtuális merevlemez található                          |
   | Nyilvános IP DNS-neve      | Nyilvános IP DNS-név. A DNS-név a virtuális gép, ezt az Azure Portalon fogja meghatározni, miután az ajánlat üzembe helyezése.  |
   | Rendszergazdai felhasználónév             | Rendszergazdai fiók felhasználóneve az új virtuális géphez                                  |
   | Rendszergazdai jelszó              | Rendszergazdai fiók jelszava az új virtuális géphez                                  |
   | Operációs rendszer típusa                     | VM operációs `Windows` \| rendszer:`Linux`                                    |
   | Előfizetés azonosítója             | A kijelölt előfizetés azonosítója                                      |
   | Hely                    | A telepítés földrajzi helye                                        |
   | Virtuális gép mérete                     | [Az Azure virtuális gép mérete](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), például`Standard_A2` |
   | Nyilvános IP-cím neve      | A nyilvános IP-cím neve                                               |
   | Virtuális gép neve                     | Az új virtuális gép neve                                                           |
   | Virtuális hálózat neve        | A virtuális gép által használt virtuális hálózat neve                                   |
   | Hálózati adapter neve                    | A virtuális hálózatot futtató hálózati kártya neve               |
   | Virtuális merevlemez URL-címe                     | Az operációs rendszer lemezének virtuális merevlemezének teljes URL-címe                                                     |
   |  |  |
            
1. Miután megadta ezeket az értékeket, kattintson **a Vásárlás**gombra. 

Az Azure megkezdi a központi telepítést: létrehoz egy új virtuális gép a megadott nem felügyelt virtuális merevlemez, a megadott tárfiók elérési útját.  Az Azure Portalon nyomon követheti a folyamatot a **virtuális gépek** a portál bal oldalán található.  A virtuális gép létrehozása után az állapot `Starting` `Running`a állásról a . 


### <a name="deploy-a-vm-from-powershell"></a>Virtuális gép üzembe helyezése a PowerShellből

Egy nagy virtuális gép üzembe helyezéséhez az általánosított virtuális gépről most létrehozott, használja a következő parancsmagokat.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>További lépések

Ezután [hozzon létre egy felhasználói virtuálisgép-lemezképet](cpp-create-user-image.md) a megoldáshoz.

