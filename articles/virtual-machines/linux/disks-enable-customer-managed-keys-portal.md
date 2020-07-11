---
title: Azure Portal – ügyfél által felügyelt kulcsok engedélyezése az SSE által felügyelt lemezekkel
description: Az ügyfél által felügyelt kulcsok engedélyezése a felügyelt lemezeken a Azure Portalon keresztül.
author: roygara
ms.date: 07/10/2020
ms.topic: how-to
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.openlocfilehash: 0128d2b88d6eb2464ef95824330cfab07ee3703d
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86235990"
---
# <a name="enable-customer-managed-keys-with-server-side-encryption---managed-disks---portal"></a>Ügyfél által felügyelt kulcsok engedélyezése kiszolgálóoldali titkosítással felügyelt lemezekkel – portál

A Azure Disk Storage lehetővé teszi a saját kulcsok kezelését, ha kiszolgálóoldali titkosítást (SSE) használ a felügyelt lemezekhez, ha a lehetőséget választja. Az ügyfelek által felügyelt kulcsokkal és az egyéb felügyelt lemezes titkosítási típusokkal kapcsolatos koncepcionális információk a lemezes titkosításról szóló cikkben, az [ügyfél által felügyelt kulcsok](disk-encryption.md#customer-managed-keys) című szakaszban találhatók.

## <a name="restrictions"></a>Korlátozások

Egyelőre az ügyfél által felügyelt kulcsokra a következő korlátozások vonatkoznak:

- Ha ez a funkció engedélyezve van a lemezen, nem tiltható le.
    Ha ennek megkerülésére van szüksége, az összes olyan felügyelt lemezre [át kell másolnia az összes](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) olyan felügyelt lemezt, amely nem használja az ügyfél által felügyelt kulcsokat.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

[!INCLUDE [virtual-machines-disks-encryption-portal](../../../includes/virtual-machines-disks-encryption-portal.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure-erőforrások felügyelt identitásait, Azure Active Directory (Azure AD) funkcióját használják. Az ügyfél által felügyelt kulcsok konfigurálásakor a rendszer automatikusan hozzárendel egy felügyelt identitást a borítók alá tartozó erőforrásokhoz. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a felügyelt lemezt egyik Azure AD-címtárból a másikba, a felügyelt lemezekhez társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok nem fognak működni. További információ: [előfizetés továbbítása az Azure ad-címtárak között](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="next-steps"></a>További lépések

- [Ismerkedjen meg a Azure Resource Manager-sablonokkal, amelyekkel titkosított lemezeket hozhat létre az ügyfél által felügyelt kulcsokkal](https://github.com/ramankumarlive/manageddiskscmkpreview))
- [Gépek replikálása az ügyfél által felügyelt kulcsokkal rendelkező lemezekkel](../../site-recovery/azure-to-azure-how-to-enable-replication-cmk-disks.md)
- [A VMware virtuális gépek vész-helyreállításának beállítása az Azure-ba a PowerShell használatával](../../site-recovery/vmware-azure-disaster-recovery-powershell.md#replicate-vmware-vms)
- [Az Azure-ba irányuló vész-helyreállítás beállítása a Hyper-V virtuális gépekhez a PowerShell és a Azure Resource Manager használatával](../../site-recovery/hyper-v-azure-powershell-resource-manager.md#step-7-enable-vm-protection)
