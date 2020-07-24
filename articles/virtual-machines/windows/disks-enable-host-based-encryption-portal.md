---
title: Végpontok közötti titkosítás engedélyezése a gazdagép-Azure Portal által felügyelt lemezek titkosításával
description: Használjon titkosítást a gazdagépen az Azure Managed Disks szolgáltatásban a végpontok közötti titkosítás engedélyezéséhez.
author: roygara
ms.service: virtual-machines
ms.topic: how-to
ms.date: 07/23/2020
ms.author: rogarana
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: 96506e0ca09e9491135faa69d0b690b91ccc8177
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136227"
---
# <a name="enable-end-to-end-encryption-using-encryption-at-host---azure-portal"></a>A végpontok közötti titkosítás engedélyezése a következő gazdagépen lévő titkosítás használatával: Azure Portal

Amikor engedélyezi a titkosítást a gazdagépen, a virtuálisgép-gazdagépen tárolt adatok titkosítva maradnak a tárolási szolgáltatásba titkosított adatforgalomban. A gazdagépen található titkosítással, valamint az egyéb felügyelt lemezes titkosítási típusokkal kapcsolatos elméleti információkat lásd: [titkosítás a gazdagép-végpontok közötti titkosítással a virtuális gép adataihoz](disk-encryption.md#encryption-at-host---end-to-end-encryption-for-your-vm-data).

[!INCLUDE [virtual-machines-disks-encryption-at-host-portal](../../../includes/virtual-machines-disks-encryption-at-host-portal.md)]

## <a name="next-steps"></a>További lépések

[Azure Resource Manager sablon mintái](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/EncryptionAtHost)