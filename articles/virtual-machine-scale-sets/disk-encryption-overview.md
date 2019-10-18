---
title: Virtual Machine Scale Sets Azure Disk Encryption engedélyezése
description: Ez a cikk útmutatást nyújt a Microsoft Azure lemezek titkosításának engedélyezéséhez Virtual Machine Scale Sets
author: msmbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: article
ms.author: mbaldwin
ms.date: 10/10/2019
ms.custom: seodec18
ms.openlocfilehash: 69197b9c0d2a60e1833d03a3f12802221a8307e2
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72530835"
---
# <a name="azure-disk-encryption-for-virtual-machine-scale-sets"></a>Virtual Machine Scale Sets Azure Disk Encryption

A Azure Disk Encryption mennyiségi titkosítást biztosít a virtuális gépek operációs rendszerének és adatlemezei számára, és segít megvédeni és védeni az adatait a szervezeti biztonsági és megfelelőségi kötelezettségvállalások teljesítése érdekében. További információ: [Azure Disk Encryption: Linux rendszerű virtuális gépek](../virtual-machines/linux/disk-encryption-overview.md) és [Azure Disk Encryption: Windowsos virtuális gépek](../virtual-machines/windows/disk-encryption-overview.md)  

A Azure Disk Encryption a Windows és a Linux rendszerű virtuálisgép-méretezési csoportokra is alkalmazható, ezekben a példányokban:
- Felügyelt lemezekkel létrehozott méretezési csoportok. Az Azure Disk Encryption nem támogatott natív (vagy nem felügyelt) lemezes méretezési csoportok esetén.
- Operációs rendszer-és adatkötetek a Windows-méretezési csoportokban.
- Adatkötetek a Linux-méretezési csoportokban. Az operációsrendszer-lemez titkosítása jelenleg nem támogatott Linux-méretezési csoportokhoz.

A virtuálisgép-méretezési csoportokra vonatkozó Azure Disk Encryption alapjai néhány perc alatt megismerhetik a [virtuálisgép-méretezési csoportok titkosítása az Azure CLI használatával](disk-encryption-cli.md) vagy a [virtuálisgép-méretezési csoportok titkosítása az Azure CLI](disk-encryption-powershell.md) -oktatóanyagok segítségével című témakört.

## <a name="next-steps"></a>Következő lépések

- [Virtuálisgép-méretezési csoportok titkosítása a Azure Resource Manager használatával](disk-encryption-azure-resource-manager.md)
- [Kulcstartó létrehozása és konfigurálása Azure Disk Encryptionhoz](disk-encryption-key-vault.md)
- [A Azure Disk Encryption használata a virtuálisgép-méretezési csoport bővítményének előkészítésével](disk-encryption-extension-sequencing.md)