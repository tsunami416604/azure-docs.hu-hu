---
title: Virtuális gép üzembe helyezése a VHD-ből az Azure Marketplace-en |} A Microsoft Docs
description: Azt ismerteti, hogyan kell regisztrálni a virtuális gép Azure által üzembe helyezett virtuális merevlemezről.
services: Azure, Marketplace, Cloud Partner Portal,
documentationcenter: ''
author: pbutlerm
manager: Patrick.Butler
editor: ''
ms.assetid: ''
ms.service: marketplace
ms.workload: ''
ms.tgt_pltfrm: ''
ms.devlang: ''
ms.topic: article
ms.date: 10/19/2018
ms.author: pbutlerm
ms.openlocfilehash: 2771549af29b3e717d117afb42de6db03fbee226
ms.sourcegitcommit: 17633e545a3d03018d3a218ae6a3e4338a92450d
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/22/2018
ms.locfileid: "49639776"
---
# <a name="deploy-a-vm-from-your-vhds"></a>Virtuális gép üzembe helyezése a VHD-ből

Ez a cikk azt ismerteti, hogyan regisztrálni egy virtuális gépet (VM) egy Azure által üzembe helyezett virtuális merevlemezről (VHD).  Felsorolja a szükséges eszközöket és azok használatát egy felhasználói Virtuálisgép-lemezkép létrehozásához, majd üzembe helyezése az Azure-ban vagy a [Microsoft Azure-portálon](https://ms.portal.azure.com/) vagy a PowerShell-parancsfájlokat. 

Miután a virtuális merevlemezek (VHD) feltöltése – az általánosított operációs rendszer virtuális Merevlemeze és nulla vagy több lemez VHD-k – az Azure storage-fiók, regisztrálhatja azokat felhasználói Virtuálisgép-lemezképként. Ezután tesztelheti ezt a lemezképet. Az operációs rendszer virtuális Merevlemeze általánosítva van, mert Ön nem helyezheti közvetlenül üzembe a virtuális gép azáltal, hogy a virtuális merevlemez URL-CÍMÉT.

Virtuálisgép-rendszerképek kapcsolatos további információkért tekintse meg a következő blogbejegyzésekben:

- [Virtuálisgép-lemezkép](https://azure.microsoft.com/blog/vm-image-blog-post/)
- [VM-lemezkép PowerShell "How To:](https://azure.microsoft.com/blog/vm-image-powershell-how-to-blog-post/)


## <a name="set-up-the-necessary-tools"></a>A szükséges eszközök beállításához

Ha még nem tette meg, telepítse az Azure PowerShell-lel és az Azure CLI, az alábbi utasítások szerint:

<!-- TD: Change the following URLs (in this entire topic) to relative paths.-->

- [Az Azure PowerShell telepítése a Windows a Powershellgettel](https://docs.microsoft.com/powershell/azure/install-azurerm-ps)
- [Telepítse az Azure CLI 2.0-t.](https://docs.microsoft.com/cli/azure/install-azure-cli)


## <a name="create-a-user-vm-image"></a>Felhasználói Virtuálisgép-lemezkép létrehozása

Ezután létrehozhat egy nem felügyelt rendszerkép az általánosított virtuális merevlemezből.

#### <a name="capture-the-vm-image"></a>A virtuális gép lemezképének rögzítése

A rögzítés a virtuális gép, amely megfelel a hozzáférési módszer a következő cikkben szereplő útmutatások segítségével:

-  PowerShell: [egy nem felügyelt Virtuálisgép-rendszerkép létrehozása Azure virtuális gépből](../../../virtual-machines/windows/capture-image-resource.md)
-  Az Azure CLI: [hogyan hozhat létre virtuális gépet vagy virtuális merevlemez képe](../../../virtual-machines/linux/capture-image.md)
-  API: [virtuális gépek – rögzítése](https://docs.microsoft.com/rest/api/compute/virtualmachines/capture)

### <a name="generalize-the-vm-image"></a>A Virtuálisgép-lemezkép általánossá tétele

A felhasználói lemezkép korábban általános virtuális merevlemezből hozott létre, mert azt kell is általánosítva van.  Újra válassza ki a következő cikkben, amely megfelel a hozzáférés-mechanizmus.  (, Előfordulhat, hogy rendelkezik már általánosítva a lemezt rögzített, amikor.)

-  PowerShell: [a virtuális gép általánosítása](https://docs.microsoft.com/azure/virtual-machines/windows/sa-copy-generalized#generalize-the-vm)
-  Az Azure CLI: [2. lépés: hozzon létre Virtuálisgép-lemezkép](https://docs.microsoft.com/azure/virtual-machines/linux/capture-image#step-2-create-vm-image)
-  API: [virtuális gépek – Generalize](https://docs.microsoft.com/rest/api/compute/virtualmachines/generalize)


## <a name="deploy-a-vm-from-a-user-vm-image"></a>Virtuális gép üzembe helyezése egy felhasználói Virtuálisgép-rendszerképből

Ezután telepíti a virtuális gép felhasználói Virtuálisgép-lemezkép, az Azure Portalon vagy a PowerShell használatával.

<!-- TD: Recapture following hilited images and replace with red-box. -->

### <a name="deploy-a-vm-from-azure-portal"></a>Virtuális gép üzembe helyezése az Azure Portalról

Az alábbi eljárás segítségével telepítheti a felhasználói virtuális gép az Azure Portalról.

1.  Jelentkezzen be az [Azure Portalra](https://portal.azure.com).

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

<!-- TD: The following is a marketplace-publishing article and may be out-of-date.  TD: update and move topic.
For help with issues, see [Troubleshooting common issues encountered during VHD creation](https://docs.microsoft.com/azure/marketplace-publishing/marketplace-publishing-vm-image-creation-troubleshooting) for additional assistance.
-->

## <a name="next-steps"></a>További lépések

A virtuális gép üzembe helyezését követően készen áll [konfigurálhatja a virtuális Gépet](./cpp-configure-vm.md).
