---
title: Az Azure Managed Disks kiszolgálóoldali titkosítása – PowerShell
description: Az Azure Storage védi az adatait úgy, hogy titkosítja a nyugalmát, mielőtt megőrzi azt a Storage-fürtökön. A felügyelt lemezek titkosításához a Microsoft által felügyelt kulcsokat használhatja, vagy az ügyfél által felügyelt kulcsokkal kezelheti a titkosítást a saját kulcsaival.
author: roygara
ms.date: 07/10/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: d0625c817703fe5d5645bcfdab962591cb184ae2
ms.sourcegitcommit: f7e160c820c1e2eb57dc480b2a8fd6bef7053e91
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/10/2020
ms.locfileid: "86232729"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Azure Disk Storage kiszolgálóoldali titkosítása

A kiszolgálóoldali titkosítás (SSE) védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében. Az SSE alapértelmezés szerint automatikusan titkosítja az Azure Managed Disks (operációs rendszer és adatlemezek) tárolt adatait a felhőben való megőrzéskor. 

Az Azure Managed Disks szolgáltatásban tárolt adatforgalom transzparens módon, 256 bites [AES-titkosítással](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), az egyik legerősebb blokk titkosítási algoritmussal, valamint az FIPS 140-2-kompatibilis. Az Azure Managed Disks mögöttes titkosítási modulokkal kapcsolatos további információkért lásd: a [kriptográfiai API: következő generáció](https://docs.microsoft.com/windows/desktop/seccng/cng-portal)

A kiszolgálóoldali titkosítás nem befolyásolja a felügyelt lemezek teljesítményét, és nincs további díj. 

> [!NOTE]
> Az ideiglenes lemezek nem felügyelt lemezek, és nem az SSE titkosítással vannak titkosítva, kivéve, ha engedélyezi a titkosítást a gazdagépen.

## <a name="about-encryption-key-management"></a>A titkosítási kulcsok kezelése

A felügyelt lemez titkosításához a platform által felügyelt kulcsokat használhatja, vagy a titkosítást saját kulcsok használatával is kezelheti. Ha úgy dönt, hogy a titkosítást a saját kulcsaival kezeli, megadhat egy *ügyfél által felügyelt kulcsot* , amelyet a felügyelt lemezeken lévő összes érték titkosítására és visszafejtésére használhat. 

A következő szakaszok részletesebben ismertetik a kulcskezelő lehetőségeit.

### <a name="platform-managed-keys"></a>Platform által felügyelt kulcsok

Alapértelmezés szerint a felügyelt lemezek platform által felügyelt titkosítási kulcsokat használnak. 2017. június 10-én a meglévő felügyelt lemezekre írt összes új felügyelt lemez, pillanatkép, kép és új adatok automatikusan titkosítva vannak a platform által felügyelt kulcsokkal.

### <a name="customer-managed-keys"></a>Felhasználó által kezelt kulcsok

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Korlátozások

Egyelőre az ügyfél által felügyelt kulcsokra a következő korlátozások vonatkoznak:

- Ha ez a funkció engedélyezve van a lemezen, nem tiltható le.
    Ha ennek megkerülésére van szüksége, az összes olyan felügyelt lemezre [át kell másolnia az összes](disks-upload-vhd-to-managed-disk-powershell.md#copy-a-managed-disk) olyan felügyelt lemezt, amely nem használja az ügyfél által felügyelt kulcsokat.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Titkosítás a virtuális gép adatai számára a gazdagép-végpontok közötti titkosításnál

A végpontok közötti titkosítás a virtuálisgép-gazdagépről, a virtuális gép által lefoglalt Azure-kiszolgálóról indul el. A rendszer a virtuális gép gazdagépén tárolja a temp lemezeken és az operációs rendszer/adatlemez gyorsítótárában lévő adatait. Ha engedélyezi a végpontok közötti titkosítást, a rendszer az összes adattitkosítást és a tárolási szolgáltatásba titkosított folyamatokat is titkosítja, ahol az adatok megmaradnak. A végpontok közötti titkosítás nem használja a virtuális gép PROCESSZORát, és nem befolyásolja a virtuális gép teljesítményét. 

Az ideiglenes lemezek a platform által felügyelt kulcsokkal titkosítva vannak, amikor engedélyezi a végpontok közötti titkosítást. Az operációs rendszer és az adatlemez gyorsítótárai a titkosítás típusától függően az ügyfél által felügyelt vagy a platform által felügyelt kulcsokkal vannak titkosítva. Ha például egy lemez az ügyfél által felügyelt kulcsokkal van titkosítva, akkor a lemez gyorsítótára az ügyfél által felügyelt kulcsokkal van titkosítva, és ha a lemez a platform által felügyelt kulcsokkal van titkosítva, akkor a lemez gyorsítótára a platform által felügyelt kulcsokkal van titkosítva.

### <a name="restrictions"></a>Korlátozások

[!INCLUDE [virtual-machines-disks-encryption-at-host-restrictions](../../../includes/virtual-machines-disks-encryption-at-host-restrictions.md)]

#### <a name="supported-regions"></a>Támogatott régiók

[!INCLUDE [virtual-machines-disks-encryption-at-host-regions](../../../includes/virtual-machines-disks-encryption-at-host-regions.md)]

#### <a name="supported-vm-sizes"></a>Támogatott virtuálisgép-méretek

[!INCLUDE [virtual-machines-disks-encryption-at-host-suported-sizes](../../../includes/virtual-machines-disks-encryption-at-host-suported-sizes.md)]

## <a name="double-encryption-at-rest"></a>Dupla titkosítás a nyugalmi állapotban

A fokozott biztonságú bizalmas ügyfelek, akik az adott titkosítási algoritmushoz, megvalósításhoz vagy kulcshoz kapcsolódó kockázatokat érintik, mostantól további titkosítási réteget is választhatnak, ha az infrastruktúra rétegében a platform által felügyelt titkosítási kulcsok használatával más titkosítási algoritmust/üzemmódot használnak. Ezt az új réteget a lemezekre, pillanatképekre és képekre lehet alkalmazni, amelyek mindegyike titkosítva marad a kettős titkosítással.

### <a name="supported-regions"></a>Támogatott régiók

[!INCLUDE [virtual-machines-disks-double-encryption-at-rest-regions](../../../includes/virtual-machines-disks-double-encryption-at-rest-regions.md)]

## <a name="server-side-encryption-versus-azure-disk-encryption"></a>Kiszolgálóoldali titkosítás és az Azure Disk Encryption

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) a Windows [BitLocker](https://docs.microsoft.com/windows/security/information-protection/bitlocker/bitlocker-overview) szolgáltatásával titkosítja a felügyelt lemezeket az ügyfél által felügyelt KULCSOKKAL a vendég virtuális gépen belül.  Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás javítja az ADE-t, mivel lehetővé teszi a virtuális gépekhez tartozó operációsrendszer-típusok és-lemezképek használatát a Storage szolgáltatásban tárolt adattitkosítással.

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure-erőforrások felügyelt identitásait, Azure Active Directory (Azure AD) funkcióját használják. Az ügyfél által felügyelt kulcsok konfigurálásakor a rendszer automatikusan hozzárendel egy felügyelt identitást a borítók alá tartozó erőforrásokhoz. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a felügyelt lemezt egyik Azure AD-címtárból a másikba, a felügyelt lemezekhez társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok nem fognak működni. További információ: [előfizetés továbbítása az Azure ad-címtárak között](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).


## <a name="next-steps"></a>További lépések

- [Gazdagép-alapú titkosítás engedélyezése](disks-enable-host-based-encryption-powershell.md)
- [Azure PowerShell – a kettős titkosítás engedélyezése a REST által felügyelt lemezeken](disks-enable-double-encryption-at-rest-powershell.md)
- [Ügyfél által felügyelt kulcsok engedélyezése a felügyelt lemezhez – PowerShell](disks-enable-customer-managed-keys-powershell.md)
- [Az ügyfél által felügyelt kulcsok – felügyelt lemezek engedélyezése](disks-enable-customer-managed-keys-portal.md)
- [Az ügyfél által felügyelt kulcsokkal rendelkező titkosított lemezek létrehozásához Azure Resource Manager-sablonok megismerése](https://github.com/ramankumarlive/manageddiskscmkpreview)
- [Mi az Azure Key Vault?](../../key-vault/general/overview.md)