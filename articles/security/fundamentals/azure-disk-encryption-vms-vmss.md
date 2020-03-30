---
title: Azure lemeztitkosítás virtuális gépekhez és virtuálisgép-méretezési készletekhez
description: Ez a cikk áttekintést nyújt az Azure lemeztitkosításról
author: msmbaldwin
ms.service: security
ms.topic: article
ms.author: mbaldwin
ms.date: 10/15/2019
ms.custom: seodec18
ms.openlocfilehash: c881b2b9743766e4d35e6cb05f6f3469803850bc
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "80062128"
---
# <a name="azure-disk-encryption-for-virtual-machines-and-virtual-machine-scale-sets"></a>Azure lemeztitkosítás virtuális gépekhez és virtuálisgép-méretezési készletekhez

Az Azure Disk titkosítás linuxos és Windows-os virtuális gépekre, valamint virtuálisgép-méretezési csoportokra is alkalmazható. 

## <a name="linux-virtual-machines"></a>Linux rendszerű virtuális gépek

Az alábbi cikkek útmutatást nyújtanak a Linux virtuális gépek titkosításához.

### <a name="current-version-of-azure-disk-encryption"></a>Az Azure lemeztitkosítás jelenlegi verziója

- [A Linux rendszerű virtuális gépekhez készült Azure Disk Encryption áttekintése](../../virtual-machines/linux/disk-encryption-overview.md)
- [Azure Disk Encryption-forgatókönyvek Linux rendszerű virtuális gépekhez](../../virtual-machines/linux/disk-encryption-linux.md)
- [Linux rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel](../../virtual-machines/linux/disk-encryption-cli-quickstart.md)
- [Linux rendszerű virtuális gép létrehozása és titkosítása az Azure PowerShell-lel](../../virtual-machines/linux/disk-encryption-powershell-quickstart.md)
- [Linux rendszerű virtuális gép létrehozása és titkosítása az Azure Portallal](../../virtual-machines/linux/disk-encryption-portal-quickstart.md)
- [Azure lemeztitkosítási bővítmény sémája Linuxhoz](../../virtual-machines/extensions/azure-disk-enc-linux.md)
- [Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz](../../virtual-machines/linux/disk-encryption-key-vault.md)
- [Azure Disk Encryption – mintaszkriptek](../../virtual-machines/linux/disk-encryption-sample-scripts.md)
- [Azure Disk Encryption – hibaelhárítás](../../virtual-machines/linux/disk-encryption-troubleshooting.md)
- [Azure Disk Encryption – gyakori kérdések](../../virtual-machines/linux/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure lemeztitkosítás az Azure AD-vel (korábbi verzió)

- [Az Azure lemeztitkosítás áttekintése az Azure AD linuxos virtuális gépekkel](../../virtual-machines/linux/disk-encryption-overview-aad.md)
- [Azure lemeztitkosítás Azure AD-forgatókönyvekkel Linuxos virtuális gépeken](../../virtual-machines/linux/disk-encryption-linux.md)
- [Az Azure Disk Encryption kulcstárolójának létrehozása és konfigurálása az Azure AD-vel (előző kiadás)](../../virtual-machines/linux/disk-encryption-key-vault-aad.md)

## <a name="windows-virtual-machines"></a>Windows rendszerű virtuális gépek

Az alábbi cikkek útmutatást nyújtanak a Windows virtuális gépek titkosításához.

### <a name="current-version-of-azure-disk-encryption"></a>Az Azure lemeztitkosítás jelenlegi verziója

- [A Windows rendszerű virtuális gépekhez készült Azure Disk Encryption áttekintése](../../virtual-machines/windows/disk-encryption-overview.md)
- [Azure Disk Encryption-forgatókönyvek Windows rendszerű virtuális gépekhez](../../virtual-machines/windows/disk-encryption-windows.md)
- [Windows rendszerű virtuális gép létrehozása és titkosítása az Azure CLI-vel](../../virtual-machines/windows/disk-encryption-cli-quickstart.md)
- [Windows rendszerű virtuális gép létrehozása és titkosítása az Azure PowerShell-lel](../../virtual-machines/windows/disk-encryption-powershell-quickstart.md)
- [Windows rendszerű virtuális gép létrehozása és titkosítása az Azure Portallal](../../virtual-machines/windows/disk-encryption-portal-quickstart.md)
- [Azure lemeztitkosítási bővítmény sémája Windows hoz](../../virtual-machines/extensions/azure-disk-enc-windows.md)
- [Key Vault létrehozása és konfigurálása az Azure Disk Encryptionhöz](../../virtual-machines/windows/disk-encryption-key-vault.md)
- [Azure Disk Encryption – mintaszkriptek](../../virtual-machines/windows/disk-encryption-sample-scripts.md)
- [Azure Disk Encryption – hibaelhárítás](../../virtual-machines/windows/disk-encryption-troubleshooting.md)
- [Azure Disk Encryption – gyakori kérdések](../../virtual-machines/windows/disk-encryption-faq.md)

### <a name="azure-disk-encryption-with-azure-ad-previous-version"></a>Azure lemeztitkosítás az Azure AD-vel (korábbi verzió)

- [Az Azure lemeztitkosítás áttekintése az Azure AD for Windows virtuális gépekhez](../../virtual-machines/windows/disk-encryption-overview-aad.md)
- [Azure lemeztitkosítás Azure AD-forgatókönyvekkel Windows virtuális gépeken](../../virtual-machines/windows/disk-encryption-windows.md)
- [Az Azure Disk Encryption kulcstárolójának létrehozása és konfigurálása az Azure AD-vel (előző kiadás)](../../virtual-machines/windows/disk-encryption-key-vault-aad.md)

## <a name="virtual-machine-scale-sets"></a>Virtuálisgép-méretezési csoportok

Az alábbi cikkek útmutatást nyújtanak a virtuálisgép-méretezési csoportok titkosításához.

- [Az Azure lemeztitkosítás áttekintése a virtuálisgép-méretezési csoportokhoz](../../virtual-machine-scale-sets/disk-encryption-overview.md) 
- [Virtuálisgép-méretezési csoportok titkosítása az Azure CLI használatával](../../virtual-machine-scale-sets/disk-encryption-cli.md) 
- [Virtuálisgép-méretezési csoportok titkosítása az Azure Powershell használatával.](../../virtual-machine-scale-sets/disk-encryption-powershell.md)
- [Virtuálisgép-méretezési csoportok titkosítása az Azure Resource Manager használatával](../../virtual-machine-scale-sets/disk-encryption-azure-resource-manager.md)
- [Az Azure Disk Encryption kulcstárolójának létrehozása és konfigurálása](../../virtual-machine-scale-sets/disk-encryption-key-vault.md)
- [Az Azure Disk Encryption használata a virtuálisgép-méretezési csoport bővítményének szekvenálásával](../../virtual-machine-scale-sets/disk-encryption-extension-sequencing.md)

## <a name="next-steps"></a>További lépések

- [Azure-titkosítás – áttekintés](encryption-overview.md)
- [Adattitkosítás inaktív](encryption-atrest.md)
- [Az adatbiztonsággal és a titkosítással kapcsolatos ajánlott eljárások](data-encryption-best-practices.md)
