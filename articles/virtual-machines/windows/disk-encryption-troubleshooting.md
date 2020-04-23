---
title: Az Azure lemeztitkosításhiba-elhárítási útmutatója
description: Ez a cikk hibaelhárítási tippeket tartalmaz a Microsoft Azure lemeztitkosítás windowsos virtuális gépekhez.
author: msmbaldwin
ms.service: virtual-machines-windows
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: 11c1e0bf10725173a2a341addf4c3f845bbb7fba
ms.sourcegitcommit: 09a124d851fbbab7bc0b14efd6ef4e0275c7ee88
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 04/23/2020
ms.locfileid: "82085688"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Az Azure lemeztitkosításhiba-elhárítási útmutatója

Ez az útmutató informatikai szakembereknek, információbiztonsági elemzőknek és felhőrendszergazdáknak szól, akiknek a szervezetei az Azure Disk Encryption szolgáltatást használják. Ez a cikk a lemeztitkosítással kapcsolatos problémák elhárításában nyújt segítséget.

Az alábbi lépések bármelyikének megtétele előtt először győződjön meg arról, hogy a titkosítani kívánt virtuális gépek a [támogatott virtuálisgép-méretek és operációs rendszerek](disk-encryption-overview.md#supported-vms-and-operating-systems)közé tartoznak, és hogy minden előfeltételnek megfelelt:

- [Hálózati követelmények](disk-encryption-overview.md#networking-requirements)
- [Csoportházirend-követelmények](disk-encryption-overview.md#group-policy-requirements)
- [Titkosítási kulcs tárolási követelményei](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Az Azure lemeztitkosítás tűzfal mögötti hibaelhárítása

Ha a kapcsolatot tűzfal, proxykövetelmény vagy hálózati biztonsági csoport (NSG) beállításai korlátozzák, a bővítmény szükséges feladatok végrehajtására való képessége megszakadhat. Ez a megszakítás állapotüzeneteket eredményezhet, például a "Bővítmény állapota nem érhető el a virtuális gépen." A várt esetekben a titkosítás nem fejeződik be. Az alábbi szakaszokban gyakori tűzfalproblémák merülhetnek fel, amelyeket megvizsgálhat.

### <a name="network-security-groups"></a>Network security groups (Hálózati biztonsági csoportok)
Az alkalmazott hálózati biztonsági csoport beállításoknak továbbra is lehetővé kell tenniük, hogy a végpont megfeleljen a lemeztitkosítás dokumentált hálózati [konfigurációs előfeltételeinek.](disk-encryption-overview.md#networking-requirements)

### <a name="azure-key-vault-behind-a-firewall"></a>Azure Key Vault tűzfal mögött

Ha az Azure [AD-hitelesítő adatokkal](disk-encryption-windows-aad.md#)engedélyezve van a titkosítás, a célvirtuális gépnek engedélyeznie kell a kapcsolatot az Azure Active Directory végpontjaihoz és a Key Vault-végpontokhoz. Az Aktuális Azure Active Directory hitelesítési végpontok az [Office 365 URL-címeinek és IP-címtartományainak](https://docs.microsoft.com/office365/enterprise/urls-and-ip-address-ranges) dokumentációjának 56. A Key Vault-útmutató a [tűzfal mögötti Azure Key Vault elérésére](../../key-vault/general/access-behind-firewall.md)vonatkozó dokumentációban található.

### <a name="azure-instance-metadata-service"></a>Azure-példány metaadat-szolgáltatása 
A virtuális gépnek hozzá kell tudnia férni az [Azure Instance metaadatszolgáltatás-végpontjához,](../windows/instance-metadata-service.md) amely egy jól ismert, nem irányítható IP-címet (`169.254.169.254`) használ, amely csak a virtuális gépen belül érhető el.  Nem támogatottak azok a proxykonfigurációk, amelyek módosítják a helyi HTTP-forgalmat erre a címre (például x-forwarded-for fejléc hozzáadása).

## <a name="troubleshooting-windows-server-2016-server-core"></a>A Windows Server 2016 Server Core hibáinak elhárítása

Windows Server 2016 Server Core rendszerben a bdehdcfg összetevő alapértelmezés szerint nem érhető el. Ezt az összetevőt az Azure Disk Encryption megköveteli. A rendszerkötet felosztására szolgál az operációs rendszer kötete, amely csak egyszer történik a virtuális gép élettartama. Ezek a bináris fájlok nem szükségesek a későbbi titkosítási műveletek során.

A probléma kerülő megoldásához másolja a következő négy fájlt a Windows Server 2016 Data Center virtuális gépről ugyanarra a helyre a Server Core szolgáltatásban:

   ```
   \windows\system32\bdehdcfg.exe
   \windows\system32\bdehdcfglib.dll
   \windows\system32\en-US\bdehdcfglib.dll.mui
   \windows\system32\en-US\bdehdcfg.exe.mui
   ```

1. Írja be a következő parancsot:

   ```
   bdehdcfg.exe -target default
   ```

1. Ez a parancs 550 MB-os rendszerpartíciót hoz létre. Indítsa újra a rendszert.

1. A Kötetek ellenőrzéséhez használja a DiskPart programot, majd folytassa a folytatást.  

Például:

```
DISKPART> list vol

  Volume ###  Ltr  Label        Fs     Type        Size     Status     Info
  ----------  ---  -----------  -----  ----------  -------  ---------  --------
  Volume 0     C                NTFS   Partition    126 GB  Healthy    Boot
  Volume 1                      NTFS   Partition    550 MB  Healthy    System
  Volume 2     D   Temporary S  NTFS   Partition     13 GB  Healthy    Pagefile
```

## <a name="troubleshooting-encryption-status"></a>Titkosítási állapot elhárítása 

Előfordulhat, hogy a portál egy lemezt titkosítottként jelenít meg, még akkor is, ha a virtuális gépen belül titkosítatlan.  Ez akkor fordulhat elő, ha alacsony szintű parancsokat használnak a lemez közvetlen titkosításának feloldására a virtuális gépen belül, a magasabb szintű Azure Lemeztitkosítás-kezelési parancsok használata helyett.  A magasabb szintű parancsok nem csak a virtuális gépen belüli lemez titkosításának feloldása, hanem a virtuális gépen kívül is frissítik a fontos platformszintű titkosítási beállításokat és a virtuális géphez társított bővítménybeállításokat.  Ha ezek nem tartják az összehangolás, a platform nem lesz képes jelenteni a titkosítási állapot, vagy a virtuális gép kiépítése megfelelően.   

Az Azure Disk Encryption letiltásához a PowerShell segítségével használja [az Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsot, majd az [Remove-AzVMDiskEncryptionExtension parancsot.](/powershell/module/az.compute/remove-azvmdiskencryptionextension) Az Remove-AzVMDiskEncryptionExtension futtatása a titkosítás letiltása előtt sikertelen lesz.

Az Azure Disk Encryption cli-vel történő letiltásához használja [az az vm titkosításletiltást.](/cli/azure/vm/encryption) 

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban többet megtudhat az Azure disk encryption néhány gyakori problémájáról, és arról, hogyan háríthatja el ezeket a problémákat. A szolgáltatásról és annak képességeiről az alábbi cikkekben talál további információt:

- [Lemeztitkosítás alkalmazása az Azure Security Centerben](../../security-center/security-center-apply-disk-encryption.md)
- [Azure-adattitkosítás inaktív helyen](../../security/fundamentals/encryption-atrest.md)
