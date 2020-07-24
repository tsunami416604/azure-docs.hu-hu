---
title: A kettős titkosítás engedélyezése a REST-Azure Portal által felügyelt lemezeken
description: A felügyelt lemez adataihoz a Azure Portal használatával engedélyezze a kettős titkosítást a nyugalmi állapotban.
author: roygara
ms.date: 07/23/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: b244ef36e4e1a15327c053402b28ee2272fc71f0
ms.sourcegitcommit: 0e8a4671aa3f5a9a54231fea48bcfb432a1e528c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/24/2020
ms.locfileid: "87136217"
---
# <a name="azure-portal---enable-double-encryption-at-rest-on-your-managed-disks"></a>Azure Portal – a kettős titkosítás engedélyezése a felügyelt lemezeken a REST szolgáltatásban

Azure Disk Storage támogatja a kettős titkosítást a felügyelt lemezeken. A kettős titkosítással kapcsolatos fogalmakról, valamint a felügyelt lemezes titkosítási típusokról további információt a lemezes titkosításról szóló cikkben, a [Rest-titkosítást](disk-encryption.md#double-encryption-at-rest) ismertető szakaszban talál.

[!INCLUDE [virtual-machines-double-encryption-at-rest-portal](../../../includes/virtual-machines-disks-double-encryption-at-rest-portal.md)]

## <a name="next-steps"></a>További lépések

- [Azure PowerShell – ügyfél által felügyelt kulcsok engedélyezése kiszolgálóoldali titkosítással felügyelt lemezekkel](disks-enable-customer-managed-keys-powershell.md)
- [Azure Resource Manager sablon mintái](https://github.com/Azure-Samples/managed-disks-powershell-getting-started/tree/master/DoubleEncryption)