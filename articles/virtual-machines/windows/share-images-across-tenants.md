---
title: Image z galerie megosztása a bérlők számára az Azure-ban |} A Microsoft Docs
description: Ismerje meg, hogyan oszthat meg Virtuálisgép-rendszerképek használatával megosztott kép katalógusok Azure bérlők között.
services: virtual-machines-windows
author: cynthn
manager: jeconnoc
ms.service: virtual-machines-windows
ms.workload: infrastructure-services
ms.tgt_pltfrm: vm-windows
ms.topic: article
ms.date: 04/05/2019
ms.author: cynthn
ms.openlocfilehash: 56c0e906203a010a1356beae4b6e5d66e34cfc2b
ms.sourcegitcommit: d4dfbc34a1f03488e1b7bc5e711a11b72c717ada
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 06/13/2019
ms.locfileid: "65159840"
---
# <a name="share-gallery-vm-images-across-azure-tenants"></a>Az Azure-bérlőre kiterjedő katalógus Virtuálisgép-rendszerképek megosztása

[!INCLUDE [virtual-machines-share-images-across-tenants](../../../includes/virtual-machines-share-images-across-tenants.md)]


## <a name="create-a-vm-using-powershell"></a>Hozzon létre egy virtuális Gépet PowerShell-lel


Jelentkezzen be az Alkalmazásazonosítót használja mindkét bérlők titkos kulcs és a bérlő azonosítóját. 

```azurepowershell-interactive
$applicationId = '<App ID>'
$secret = <Secret> | ConvertTo-SecureString -AsPlainText -Force
$tenant1 = "<Tenant 1 ID>"
$tenant2 = "<Tenant 2 ID>"
$cred = New-Object -TypeName PSCredential -ArgumentList $applicationId, $secret
Clear-AzContext
Connect-AzAccount -ServicePrincipal -Credential $cred  -Tenant "<Tenant 1 ID>"
Connect-AzAccount -ServicePrincipal -Credential $cred -Tenant "<Tenant 2 ID>"
```

A virtuális gép létrehozásához, amely engedéllyel rendelkezik az alkalmazás regisztrációját az erőforráscsoportban. Cserélje le az adatokat ebben a példában a saját.

```azurepowershell-interactive
$resourceGroup = "myResourceGroup"
$image = "/subscriptions/<Tenant 1 subscription>/resourceGroups/<Resource group>/providers/Microsoft.Compute/galleries/<Gallery>/images/<Image definition>/versions/<version>"
New-AzVm `
   -ResourceGroupName "myResourceGroup" `
   -Name "myVMfromImage" `
   -Image $image `
   -Location "South Central US" `
   -VirtualNetworkName "myImageVnet" `
   -SubnetName "myImageSubnet" `
   -SecurityGroupName "myImageNSG" `
   -PublicIpAddressName "myImagePIP" `
   -OpenPorts 3389
```

## <a name="next-steps"></a>További lépések

Is létrehozhat megosztott kép használatával galéria-erőforrásai a [az Azure portal](shared-images-portal.md).