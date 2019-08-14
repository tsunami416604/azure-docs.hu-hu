---
title: Virtuális gép üzembe helyezése a virtuális merevlemezekről az Azure Marketplace-en
description: A cikk azt ismerteti, hogyan regisztrálhat egy virtuális gépet egy Azure-ban üzembe helyezett VHD-ről.
services: Azure, Marketplace, Cloud Partner Portal,
author: qianw211
ms.service: marketplace
ms.topic: article
ms.date: 08/08/2019
ms.author: evansma
ms.openlocfilehash: 1aa946c813de41423d4fb2ba5b3aa5274db90f39
ms.sourcegitcommit: 13a289ba57cfae728831e6d38b7f82dae165e59d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 08/09/2019
ms.locfileid: "68934968"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Virtuális gép üzembe helyezése a virtuális merevlemezekről

Ez a szakasz azt ismerteti, hogyan lehet virtuális gépet (VM) üzembe helyezni egy Azure-központilag telepített virtuális merevlemezről (VHD).  Felsorolja a szükséges eszközöket, valamint azt, hogyan használhatja őket felhasználói virtuálisgép-lemezkép létrehozásához, majd hogyan telepítheti azt az Azure-ba PowerShell-parancsfájlok használatával.

Miután feltöltötte a virtuális merevlemezeket (VHD-k) – az általánosított operációs rendszer VHD-jét és a nulla vagy annál több adatlemezt a VHD-hez, felhasználói virtuálisgép-lemezképként regisztrálhatja őket. Ezt követően tesztelheti a rendszerképet. Mivel az operációs rendszer VHD-je általánosítva van, a virtuális gép közvetlenül nem helyezhető üzembe a VHD URL-címének megadásával.

A virtuálisgép-lemezképekkel kapcsolatos további tudnivalókért tekintse meg a következő blogbejegyzéseket:

- [VM-rendszerkép](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [Virtuálisgép-rendszerkép PowerShell – útmutató](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)

[!INCLUDE [updated-for-az](../../../../includes/updated-for-az.md)]

## <a name="prerequisite-install-the-necessary-tools"></a>Előfeltétel: a szükséges eszközök telepítése

Ha még nem tette meg, telepítse a Azure PowerShell és az Azure CLI-t az alábbi utasítások használatával:

- [Az Azure PowerShell telepítése](https://docs.microsoft.com/powershell/azure/install-Az-ps)
- [Az Azure parancssori felület telepítése](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="deployment-steps"></a>A központi telepítés lépései

A felhasználói virtuálisgép-lemezképek létrehozásához és üzembe helyezéséhez a következő lépéseket fogja használni:

1. Hozza létre a felhasználói virtuálisgép-rendszerképet, amely a rendszerkép rögzítését és általánosítását vonja maga után. 
2. Hozzon létre tanúsítványokat, és tárolja őket egy új Azure Key Vaultban. A virtuális géphez biztonságos WinRM-kapcsolatok létrehozásához tanúsítvány szükséges.  A rendszer Azure Resource Manager sablont és egy Azure PowerShell parancsfájlt biztosít. 
3. Telepítse a virtuális gépet egy felhasználói virtuálisgép-lemezképből a megadott sablonnal és parancsfájl használatával.

A virtuális gép üzembe helyezése után készen áll a virtuálisgép- [lemezkép tanúsítására](./cpp-certify-vm.md).

1. Kattintson az **új** elemre, és keresse meg a **sablon központi telepítését**, majd válassza **a saját sablon létrehozása a szerkesztőben**lehetőséget.  <br/>
   ![VHD központi telepítési sablon létrehozása a Azure Portalban](./media/publishvm_021.png)

1. Másolja és illessze be ezt a [JSON-sablont](./cpp-deploy-json-template.md) a Szerkesztőbe, majd kattintson a **Save (Mentés**) gombra. <br/>
   ![VHD központi telepítési sablon mentése a Azure Portal](./media/publishvm_022.png)

1. Adja meg a paraméterek értékeit az **Egyéni központi telepítési** tulajdonságlapon.

   <table> <tr> <td valign="top"> <img src="./media/publishvm_023.png" alt="Custom deployment property page 1"> </td> <td valign="top"> <img src="./media/publishvm_024.png" alt="Custom deployment property page 2"> </td> </tr> </table> <br/> 

   |  **A paraméter**              |   **Leírás**                                                            |
   |  -------------              |   ---------------                                                            |
   | Felhasználói Storage-fiók neve   | A Storage-fiók neve, ahol az általánosított VHD található                    |
   | Felhasználói tároló tárolójának neve | A tároló neve, ahol az általánosított VHD található                          |
   | Nyilvános IP-cím DNS-neve      | Nyilvános IP-cím DNS-neve. A DNS-név a virtuális gép, ezt az Azure Portalon kell megadnia az ajánlat üzembe helyezése után.  |
   | Rendszergazdai felhasználónév             | Rendszergazdai fiók felhasználóneve az új virtuális géphez                                  |
   | Rendszergazdai jelszó              | Rendszergazdai fiók jelszava az új virtuális géphez                                  |
   | Operációs rendszer típusa                     | VM operációs rendszer: `Windows` \|`Linux`                                    |
   | Előfizetés azonosítója             | A kiválasztott előfizetés azonosítója                                      |
   | Location                    | Az üzemelő példány földrajzi helye                                        |
   | VM mérete                     | [Azure-beli virtuális gép mérete](https://docs.microsoft.com/azure/virtual-machines/windows/sizes), például:`Standard_A2` |
   | Nyilvános IP-cím neve      | A nyilvános IP-cím neve                                               |
   | Virtuális gép neve                     | Az új virtuális gép neve                                                           |
   | Virtuális hálózat neve        | A virtuális gép által használt virtuális hálózat neve                                   |
   | Hálózati adapter neve                    | A virtuális hálózatot futtató hálózati kártya neve               |
   | VHD URL                     | OPERÁCIÓSRENDSZER-lemez virtuális merevlemezének teljes URL-címe                                                     |
   |  |  |
            
1. Az értékek megadása után kattintson a **vásárlás**elemre. 

Az Azure megkezdi az üzembe helyezést: egy új virtuális gépet hoz létre a megadott nem felügyelt VHD-vel a megadott Storage-fiók elérési útjában.  A Azure Portal előrehaladását a portál bal oldalán található **Virtual Machines** gombra kattintva követheti nyomon.  A virtuális gép létrehozása után az állapot a-ból `Starting` értékre `Running`változik. 


### <a name="deploy-a-vm-from-powershell"></a>Virtuális gép üzembe helyezése a PowerShellből

Ha nagy méretű virtuális gépet szeretne üzembe helyezni az imént létrehozott általánosított VM-rendszerképből, használja a következő parancsmagokat.

``` powershell
    $img = Get-AzureVMImage -ImageName "myVMImage"
    $user = "user123"
    $pass = "adminPassword123"
    $myVM = New-AzureVMConfig -Name "VMImageVM" -InstanceSize "Large" -ImageName $img.ImageName | Add-AzureProvisioningConfig -Windows -AdminUsername $user -Password $pass
    New-AzureVM -ServiceName "VMImageCloudService" -VMs $myVM -Location "West US" -WaitForBoot
```


## <a name="next-steps"></a>További lépések

Ezután [létre fog hozni egy felhasználói](cpp-create-user-image.md) virtuálisgép-rendszerképet a megoldásához.

