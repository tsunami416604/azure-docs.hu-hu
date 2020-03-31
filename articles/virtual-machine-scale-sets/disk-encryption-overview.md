---
title: Az Azure lemeztitkosítás engedélyezése a virtuálisgép-méretezési készletekhez
description: Ez a cikk a Microsoft Azure lemezes titkosítás virtuálisgép-méretezési készletekhez való engedélyezésére vonatkozó utasításokat tartalmazza
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: conceptual
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 36eb83d99af299260ebbbd74fe56d69921ba389b
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/27/2020
ms.locfileid: "76278978"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Azure lemeztitkosítás a virtuálisgép-méretezési készletekhez

Az Azure Disk Encryption kötettitkosítást biztosít a virtuális gépek operációs rendszerének és adatlemezeinek, így megvédheti és megvédheti az adatokat a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítéséhez. További információ: [Azure Disk Encryption: Linux virtuális gépek](../virtual-machines/linux/disk-encryption-overview.md) és [Azure lemeztitkosítás: Windows virtuális gépek](../virtual-machines/windows/disk-encryption-overview.md)  

Az Azure Disk Encryption windowsos és Linuxos virtuálisgép-méretezési csoportokra is alkalmazható a következő esetekben:
- Felügyelt lemezekkel létrehozott méretezési csoportok. Az Azure Disk titkosítása nem támogatott a natív (vagy nem felügyelt) lemezméretezési csoportok.
- Operációs rendszer és adatkötetek a Windows méretezési készleteiben.
- Adatkötetek Linux méretezési készletekben. Az operációs rendszer lemeztitkosítása jelenleg nem támogatott linuxos méretezési csoportok esetén.

Az Azure Disk Encryption alapjait a virtuális gép méretezési készletek néhány perc alatt a [virtuális gép méretezési készletek titkosítása az Azure CLI](disk-encryption-cli.md) vagy a [titkosítása egy virtuális gép méretezési készletek az Azure PowerShell](disk-encryption-powershell.md) oktatóanyagok használatával.

## <a name="next-steps"></a>További lépések

- [Virtuálisgép-méretezési csoportok titkosítása az Azure Resource Manager használatával](disk-encryption-azure-resource-manager.md)
- [Az Azure Disk Encryption kulcstárolójának létrehozása és konfigurálása](disk-encryption-key-vault.md)
- [Az Azure Disk Encryption használata a virtuálisgép-méretezési csoport bővítményének szekvenálásával](disk-encryption-extension-sequencing.md)
