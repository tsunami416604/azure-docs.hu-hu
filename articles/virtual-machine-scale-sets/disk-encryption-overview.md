---
title: Virtual Machine Scale Sets Azure Disk Encryption engedélyezése
description: Ez a cikk útmutatást nyújt a Microsoft Azure lemezek titkosításának engedélyezéséhez Virtual Machine Scale Sets
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: article
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 0097d0e1d5ea7de092da14683d4bab3d673b2219
ms.sourcegitcommit: 98ce5583e376943aaa9773bf8efe0b324a55e58c
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/30/2019
ms.locfileid: "73177777"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets Azure Disk Encryption

A Azure Disk Encryption mennyiségi titkosítást biztosít a virtuális gépek operációs rendszerének és adatlemezei számára, és segít megvédeni és védeni az adatait a szervezeti biztonsági és megfelelőségi kötelezettségvállalások teljesítése érdekében. További információ: [Azure Disk Encryption: Linux rendszerű virtuális gépek](../virtual-machines/linux/disk-encryption-overview.md) és [Azure Disk Encryption: Windowsos virtuális gépek](../virtual-machines/windows/disk-encryption-overview.md)  

A Azure Disk Encryption a Windows és a Linux rendszerű virtuálisgép-méretezési csoportokra is alkalmazható, ezekben a példányokban:
- Felügyelt lemezekkel létrehozott méretezési csoportok. Az Azure Disk Encryption nem támogatott natív (vagy nem felügyelt) lemezes méretezési csoportok esetén.
- Operációs rendszer-és adatkötetek a Windows-méretezési csoportokban.
- Adatkötetek a Linux-méretezési csoportokban. Az operációsrendszer-lemez titkosítása jelenleg nem támogatott Linux-méretezési csoportokhoz.

A virtuálisgép-méretezési csoportokra vonatkozó Azure Disk Encryption alapjai néhány perc alatt megismerhetik a [virtuálisgép-méretezési csoportok titkosítása az Azure CLI használatával](disk-encryption-cli.md) vagy a virtuálisgép-méretezési csoportok [titkosítása a Azure PowerShell használatával](disk-encryption-powershell.md) oktatóanyagok.

## <a name="next-steps"></a>Következő lépések

- [Virtuálisgép-méretezési csoportok titkosítása a Azure Resource Manager használatával](disk-encryption-azure-resource-manager.md)
- [Kulcstartó létrehozása és konfigurálása Azure Disk Encryptionhoz](disk-encryption-key-vault.md)
- [A Azure Disk Encryption használata a virtuálisgép-méretezési csoport bővítményének előkészítésével](disk-encryption-extension-sequencing.md)
