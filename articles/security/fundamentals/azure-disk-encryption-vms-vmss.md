---
title: Azure Disk Encryption virtuális gépekhez és virtuálisgép-méretezési csoportokhoz
description: Ez a cikk áttekintést nyújt Azure Disk Encryption
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: 239bfb4f5a0e9115c113bb2d5ddbc931f41c0f3d
ms.sourcegitcommit: b4f201a633775fee96c7e13e176946f6e0e5dd85
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 10/18/2019
ms.locfileid: "72599954"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>Azure Disk Encryption virtuális gépekhez és virtuálisgép-méretezési csoportokhoz

Az Azure Disk Encryption a Linux és a Windows rendszerű virtuális gépekre, valamint a virtuálisgép-méretezési csoportokra is alkalmazható. 

## <a name="linux-virtual-machines"></a>Linux-alapú virtuális gépek

A következő cikkek útmutatást nyújtanak a linuxos virtuális gépek titkosításához.

### <a name="current-version-of-azure-disk-encryption"></a>A Azure Disk Encryption aktuális verziója

- [A Linux rendszerű virtuális gépek Azure Disk Encryption áttekintése](../../virtual-machines/linux/disk-encryption-overview.md)
- [Azure Disk Encryption forgatókönyvek Linux rendszerű virtuális gépeken](../../virtual-machines/linux/disk-encryption-linux.md)
- [Linux rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Linux rendszerű virtuális gép létrehozása és titkosítása Azure PowerShell](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Linux rendszerű virtuális gép létrehozása és titkosítása a Azure Portal](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Kulcstartó létrehozása és konfigurálása Azure Disk Encryptionhoz](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Azure Disk Encryption minta parancsfájlok](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Hibaelhárítás Azure Disk Encryption](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Azure Disk Encryption gyakori kérdések](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption az Azure AD-vel (előző verzió)

- [A Linux rendszerű virtuális gépekhez készült Azure AD-Azure Disk Encryption áttekintése](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Azure AD-forgatókönyvek Azure Disk Encryption Linux rendszerű virtuális gépeken](../../virtual-machines/linux/disk-encryption-linux.md)
- [Kulcstartó létrehozása és konfigurálása az Azure AD-vel való Azure Disk Encryptionhoz (előző kiadás)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Windows Virtual Machines

A következő cikkek útmutatást nyújtanak a Windows rendszerű virtuális gépek titkosításához.

### <a name="current-version-of-azure-disk-encryption"></a>A Azure Disk Encryption aktuális verziója

- [A Windows rendszerű virtuális gépek Azure Disk Encryption áttekintése](../../virtual-machines/windows/disk-encryption-overview.md)
- [Azure Disk Encryption forgatókönyvek Windows rendszerű virtuális gépeken](../../virtual-machines/windows/disk-encryption-windows.md)
- [Windows rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Windows rendszerű virtuális gép létrehozása és titkosítása Azure PowerShell](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Windows rendszerű virtuális gép létrehozása és titkosítása a Azure Portal](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Kulcstartó létrehozása és konfigurálása Azure Disk Encryptionhoz](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Azure Disk Encryption minta parancsfájlok](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Hibaelhárítás Azure Disk Encryption](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Azure Disk Encryption gyakori kérdések](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure Disk Encryption az Azure AD-vel (előző verzió)

- [A Windows rendszerű virtuális gépekhez készült Azure AD-Azure Disk Encryption áttekintése](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Azure Disk Encryption Azure AD-forgatókönyvekkel Windows rendszerű virtuális gépeken](../../virtual-machines/windows/disk-encryption-windows.md)
- [Kulcstartó létrehozása és konfigurálása az Azure AD-vel való Azure Disk Encryptionhoz (előző kiadás)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>Virtual Machine Scale Sets

A következő cikkek útmutatást nyújtanak a virtuálisgép-méretezési csoportok titkosításához.

- [A virtuálisgép-méretezési csoportokra vonatkozó Azure Disk Encryption áttekintése](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Virtuálisgép-méretezési csoportok titkosítása az Azure CLI használatával](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [Virtuálisgép-méretezési csoportok titkosítása az Azure PowerShell használatával](../../virtual-machine-scale-sets/disk-encryption-powershell.md).
- [Virtuálisgép-méretezési csoportok titkosítása a Azure Resource Manager használatával](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Kulcstartó létrehozása és konfigurálása Azure Disk Encryptionhoz](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [A Azure Disk Encryption használata a virtuálisgép-méretezési csoport bővítményének előkészítésével](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>Következő lépések

- [Az Azure-titkosítás áttekintése](encryption-overview.md)
- [Adattitkosítás inaktív állapotban](encryption-atrest.md)
- [Az adatbiztonsággal és a titkosítással kapcsolatos ajánlott eljárások](data-encryption-best-practices.md)
