---
title: fájl belefoglalása
description: fájl belefoglalása
services: virtual-machines
author: cynthn
ms.service: virtual-machines
ms.topic: include
ms.date: 11/07/2018
ms.author: cynthn
ms.custom: include file
ms.openlocfilehash: a55653c0f23be594fe65e7a322c11edc37ee1ce6
ms.sourcegitcommit: be32c9a3f6ff48d909aabdae9a53bd8e0582f955
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/26/2020
ms.locfileid: "67179081"
---
## <a name="shared-image-management"></a>Megosztott rendszerképek kezelése 

Íme néhány példa a gyakori felügyeleti feladatokra és a PowerShell használatával történő végrehajtására.

Az összes gyűjtemény listázása név szerint.

```azurepowershell-interactive
$galleries = Get-AzResource -ResourceType Microsoft.Compute/galleries
$galleries.Name
```

Listázza az összes képdefiníciót név szerint.

```azurepowershell-interactive
$imageDefinitions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images
$imageDefinitions.Name
```

A rendszerkép összes verziójának listázása név szerint.

```azurepowershell-interactive
$imageVersions = Get-AzResource -ResourceType Microsoft.Compute/galleries/images/versions
$imageVersions.Name
```

Rendszerkép verziójának törlése. Ez a példa törli a *1.0.0*nevű rendszerkép-verziót.

```azurepowershell-interactive
Remove-AzGalleryImageVersion `
   -GalleryImageDefinitionName myImageDefinition `
   -GalleryName myGallery `
   -Name 1.0.0 `
   -ResourceGroupName myGalleryRG
```





