---
title: Az Azure Managed Disks kiszolgálóoldali titkosítása – Azure parancssori felület
description: Az Azure Storage védi az adatait úgy, hogy titkosítja a nyugalmát, mielőtt megőrzi azt a Storage-fürtökön. A felügyelt lemezek titkosításához a Microsoft által felügyelt kulcsokat használhatja, vagy az ügyfél által felügyelt kulcsokkal kezelheti a titkosítást a saját kulcsaival.
author: roygara
ms.date: 07/10/2020
ms.topic: conceptual
ms.author: rogarana
ms.service: virtual-machines-linux
ms.subservice: disks
ms.custom: references_regions
ms.openlocfilehash: e0a1f97cc7467d115ecc8462a301e45f90d73818
ms.sourcegitcommit: cee72954f4467096b01ba287d30074751bcb7ff4
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 07/30/2020
ms.locfileid: "87449156"
---
# <a name="server-side-encryption-of-azure-disk-storage"></a>Azure Disk Storage kiszolgálóoldali titkosítása

A kiszolgálóoldali titkosítás (SSE) védi az adatait, és segít a szervezeti biztonsági és megfelelőségi kötelezettségek teljesítésében. Az SSE alapértelmezés szerint automatikusan titkosítja az Azure Managed Disks (operációs rendszer és adatlemezek) tárolt adatait a felhőben való megőrzéskor. 

Az Azure Managed Disks szolgáltatásban tárolt adatforgalom transzparens módon, 256 bites [AES-titkosítással](https://en.wikipedia.org/wiki/Advanced_Encryption_Standard), az egyik legerősebb blokk titkosítási algoritmussal, valamint az FIPS 140-2-kompatibilis. Az Azure Managed Disks mögöttes titkosítási modulokkal kapcsolatos további információkért lásd: a [kriptográfiai API: következő generáció](/windows/desktop/seccng/cng-portal)

A kiszolgálóoldali titkosítás nem befolyásolja a felügyelt lemezek teljesítményét, és nincs további díj. 

## <a name="about-encryption-key-management"></a>A titkosítási kulcsok kezelése

A felügyelt lemez titkosításához a platform által felügyelt kulcsokat használhatja, vagy a titkosítást saját kulcsok használatával is kezelheti. Ha úgy dönt, hogy a titkosítást a saját kulcsaival kezeli, megadhat egy *ügyfél által felügyelt kulcsot* , amelyet a felügyelt lemezeken lévő összes érték titkosítására és visszafejtésére használhat. 

A következő szakaszok részletesebben ismertetik a kulcskezelő lehetőségeit.

### <a name="platform-managed-keys"></a>Platform által felügyelt kulcsok

Alapértelmezés szerint a felügyelt lemezek platform által felügyelt titkosítási kulcsokat használnak. Az összes felügyelt lemez, pillanatkép, lemezkép és a meglévő felügyelt lemezekre írt adatok automatikusan titkosítva vannak a platform által felügyelt kulcsokkal.

### <a name="customer-managed-keys"></a>Felhasználó által kezelt kulcsok

[!INCLUDE [virtual-machines-managed-disks-description-customer-managed-keys](../../../includes/virtual-machines-managed-disks-description-customer-managed-keys.md)]

#### <a name="restrictions"></a>Korlátozások

Egyelőre az ügyfél által felügyelt kulcsokra a következő korlátozások vonatkoznak:

- Ha ez a funkció engedélyezve van a lemezen, nem tiltható le.
    Ha ennek megkerülésére van szüksége, az összes olyan felügyelt lemezre [át kell másolnia az összes](disks-upload-vhd-to-managed-disk-cli.md#copy-a-managed-disk) olyan felügyelt lemezt, amely nem használja az ügyfél által felügyelt kulcsokat.
[!INCLUDE [virtual-machines-managed-disks-customer-managed-keys-restrictions](../../../includes/virtual-machines-managed-disks-customer-managed-keys-restrictions.md)]

> [!IMPORTANT]
> Az ügyfél által felügyelt kulcsok az Azure-erőforrások felügyelt identitásait, Azure Active Directory (Azure AD) funkcióját használják. Az ügyfél által felügyelt kulcsok konfigurálásakor a rendszer automatikusan hozzárendel egy felügyelt identitást a borítók alá tartozó erőforrásokhoz. Ha ezt követően áthelyezi az előfizetést, az erőforráscsoportot vagy a felügyelt lemezt egyik Azure AD-címtárból a másikba, a felügyelt lemezekhez társított felügyelt identitás nem kerül át az új bérlőre, így az ügyfél által felügyelt kulcsok nem fognak működni. További információ: [előfizetés továbbítása az Azure ad-címtárak között](../../active-directory/managed-identities-azure-resources/known-issues.md#transferring-a-subscription-between-azure-ad-directories).

## <a name="encryption-at-host---end-to-end-encryption-for-your-vm-data"></a>Titkosítás a virtuális gép adatai számára a gazdagép-végpontok közötti titkosításnál

Ha engedélyezi a titkosítást a gazdagépen, a titkosítás a virtuális gép gazdagépén indul el, az Azure-kiszolgáló, amelyhez a virtuális gép hozzá van rendelve. Az ideiglenes lemez és az operációs rendszer/adatlemez-gyorsítótárak adatait a rendszer a virtuális gép gazdagépén tárolja. Miután engedélyezte a titkosítást a gazdagépen, az összes adatok titkosítva maradnak a nyugalmi állapotban és a tárolási szolgáltatásba titkosított folyamatokban. A gazdagép titkosítása lényegében teljes körűen titkosítja adatait. A gazdagépen lévő titkosítás nem használja a virtuális gép PROCESSZORát, és nem befolyásolja a virtuális gép teljesítményét. 

Az ideiglenes lemezek a platform által felügyelt kulcsokkal titkosítva vannak, amikor engedélyezi a végpontok közötti titkosítást. Az operációs rendszer és az adatlemez gyorsítótárai a kiválasztott lemez titkosítási típusától függően az ügyfél által felügyelt vagy a platform által felügyelt kulcsokkal vannak titkosítva. Ha például egy lemez az ügyfél által felügyelt kulcsokkal van titkosítva, akkor a lemez gyorsítótára az ügyfél által felügyelt kulcsokkal van titkosítva, és ha a lemez a platform által felügyelt kulcsokkal van titkosítva, akkor a lemez gyorsítótára a platform által felügyelt kulcsokkal van titkosítva.

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

[Azure Disk Encryption](../../security/fundamentals/azure-disk-encryption-vms-vmss.md) a Linux [dm-crypt](https://en.wikipedia.org/wiki/Dm-crypt) szolgáltatásával titkosítja a felügyelt lemezeket az ügyfél által felügyelt KULCSOKKAL a vendég virtuális gépen belül.  Az ügyfél által felügyelt kulcsokkal rendelkező kiszolgálóoldali titkosítás javítja az ADE-t, mivel lehetővé teszi a virtuális gépekhez tartozó operációsrendszer-típusok és-lemezképek használatát a Storage szolgáltatásban tárolt adattitkosítással.

## <a name="next-steps"></a>További lépések

- Engedélyezheti a végpontok közötti titkosítást a gazdagépen a [CLI](disks-enable-host-based-encryption-cli.md) vagy a [Azure Portal](disks-enable-host-based-encryption-portal.md)használatával történő titkosítással.
- A [CLI](disks-enable-double-encryption-at-rest-cli.md) vagy a [Azure Portal](disks-enable-double-encryption-at-rest-portal.md)használatával a felügyelt lemezek esetében engedélyezze a kettős titkosítást.
- Az ügyfél által felügyelt kulcsok engedélyezése a [parancssori](disks-enable-customer-managed-keys-cli.md) felülettel vagy a [Azure Portalsal](disks-enable-customer-managed-keys-portal.md)rendelkező felügyelt lemezeken.
- [Mi az Azure Key Vault?](../../key-vault/general/overview.md)
