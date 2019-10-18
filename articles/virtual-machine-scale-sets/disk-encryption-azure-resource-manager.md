---
title: Virtuálisgép-méretezési csoport létrehozása és titkosítása Azure Resource Manager-sablonokkal
description: Ebből a rövid útmutatóból megtudhatja, hogyan hozhat létre és titkosíthat virtuálisgép-méretezési készleteket Azure Resource Manager sablonok használatával
author: msmbaldwin
ms.author: mbaldwin
ms.service: virtual-machine-scale-sets
ms.topic: quickstart
ms.date: 10/10/2019
ms.openlocfilehash: aeba9aee8dd10b1245fb0a1e1cc98a1951382c72
ms.sourcegitcommit: f29fec8ec945921cc3a89a6e7086127cc1bc1759
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/17/2019
ms.locfileid: "72529999"
---
# <a name="encrypt-virtual-machine-scale-sets-with-azure-resource-manager"></a>Virtuálisgép-méretezési csoportok titkosítása Azure Resource Manager

A Linux rendszerű virtuálisgép-méretezési csoportok titkosítása vagy visszafejtése Azure Resource Manager-sablonok használatával végezhető el.

## <a name="deploying-templates"></a>Sablonok üzembe helyezése

Először válassza ki azt a sablont, amely megfelel a forgatókönyvnek.

- [Lemez titkosításának engedélyezése futó linuxos virtuálisgép-méretezési csoporton](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-linux)

- [Lemez titkosításának engedélyezése egy futó Windowsos virtuálisgép-méretezési csoporton](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-running-vmss-windows)

  - [Linux rendszerű virtuális gépek virtuálisgép-méretezési csoportjának üzembe helyezése Jumpbox és a titkosítás engedélyezése linuxos virtuálisgép-méretezési csoportokban](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-linux-jumpbox)

  - [Windows rendszerű virtuális gépek virtuálisgép-méretezési csoportjának üzembe helyezése Jumpbox és a titkosítás engedélyezése a Windowsos virtuálisgép-méretezési csoportokban](https://github.com/Azure/azure-quickstart-templates/tree/master/201-encrypt-vmss-windows-jumpbox)

- [Lemez titkosításának letiltása egy futó linuxos virtuálisgép-méretezési csoporton](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-linux)

- [Lemez titkosításának letiltása egy futó Windowsos virtuálisgép-méretezési csoporton](https://github.com/Azure/azure-quickstart-templates/tree/master/201-decrypt-vmss-windows)

Ezután kövesse az alábbi lépéseket:

     1. Click **Deploy to Azure**.
     2. Fill in the required fields then agree to the terms and conditions.
     3. Click **Purchase** to deploy the template.

## <a name="next-steps"></a>Következő lépések

- [Azure Disk Encryption a virtuálisgép-méretezési csoportokhoz](disk-encryption-overview.md)
- [Virtuálisgép-méretezési csoportok titkosítása az Azure CLI használatával](disk-encryption-cli.md)
- [Virtuálisgép-méretezési csoportok titkosítása a Azure PowerShell használatával](disk-encryption-powershell.md)
- [Kulcstartó létrehozása és konfigurálása Azure Disk Encryptionhoz](disk-encryption-key-vault.md)
- [A Azure Disk Encryption használata a virtuálisgép-méretezési csoport bővítményének előkészítésével](disk-encryption-extension-sequencing.md)