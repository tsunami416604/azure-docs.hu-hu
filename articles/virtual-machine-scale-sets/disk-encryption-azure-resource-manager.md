---
title: Virtuálisgép-méretezési készlet létrehozása és titkosítása Azure Resource Manager-sablonokkal
description: Ebben a rövid útmutatóban megtudhatja, hogyan hozhat létre és titkosíthat az Azure Resource Manager-sablonokkal egy virtuálisgép-méretezési készletet.
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.date: 10/10/2019
ms.openlocfilehash: aeba9aee8dd10b1245fb0a1e1cc98a1951382c72
ms.sourcegitcommit: c2065e6f0ee0919d36554116432241760de43ec8
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/26/2020
ms.locfileid: "72529999"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Virtuálisgép-méretezési csoportok titkosítása az Azure Resource Managerrel

Az Azure Resource Manager-sablonok használatával titkosíthatja vagy visszafejtheti a Linux virtuálisgép-méretezési csoportokat.

## <a name="deploying-templates"></a>Sablonok telepítése

Először válassza ki a forgatókönyvnek megfelelő sablont.

- [Lemeztitkosítás engedélyezése egy futó Linux-os virtuálisgép-méretezési csoporton](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Lemeztitkosítás engedélyezése windowsos virtuálisgép-méretezési csoporton](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Linuxos virtuális gépek virtuális gépméret-készletének üzembe helyezése ugródobozzal, és lehetővé teszi a titkosítást a Linux virtuálisgép-méretezési készleteken](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [A Windows virtuális gépek virtuális gépméretezési készletének telepítése ugródobozzal, és lehetővé teszi a titkosítást a Windows virtuálisgép-méretezési készleteken](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Lemeztitkosítás letiltása egy futó Linux-os virtuálisgép-méretezési csoporton](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Lemeztitkosítás letiltása windowsos virtuálisgép-méretezési csoporton](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

Ez után kövesse az alábbi lépéseket:

     1. Click **Deploy to Azure**.
     2. Fill in the required fields then agree to the terms and conditions.
     3. Click **Purchase** to deploy the template.

## <a name="next-steps"></a>További lépések

- [Azure lemeztitkosítás a virtuálisgép-méretezési készletekhez](disk-encryption-overview.md)
- [Virtuálisgép-méretezési csoportok titkosítása az Azure CLI használatával](disk-encryption-cli.md)
- [Virtuálisgép-méretezési csoportok titkosítása az Azure PowerShell használatával](disk-encryption-powershell.md)
- [Az Azure Disk Encryption kulcstárolójának létrehozása és konfigurálása](disk-encryption-key-vault.md)
- [Az Azure Disk Encryption használata a virtuálisgép-méretezési csoport bővítményének szekvenálásával](disk-encryption-extension-sequencing.md)