---
title: Az Azure lemeztitkosítás linuxos virtuális gépekhez történő hibaelhárítása
description: Ez a cikk hibaelhárítási tippeket tartalmaz a Microsoft Azure lemeztitkosítás linuxos virtuális gépekhez.
author: msmbaldwin
ms.service: virtual-machines-linux
ms.subservice: security
ms.topic: article
ms.author: mbaldwin
ms.date: 08/06/2019
ms.custom: seodec18
ms.openlocfilehash: eeacea9e3305865881747801100dc17770b7df63
ms.sourcegitcommit: 2ec4b3d0bad7dc0071400c2a2264399e4fe34897
ms.translationtype: MT
ms.contentlocale: hu-HU
ms.lasthandoff: 03/28/2020
ms.locfileid: "78970478"
---
# <a name="azure-disk-encryption-troubleshooting-guide"></a>Az Azure lemeztitkosításhiba-elhárítási útmutatója

Ez az útmutató informatikai szakembereknek, információbiztonsági elemzőknek és felhőrendszergazdáknak szól, akiknek a szervezetei az Azure Disk Encryption szolgáltatást használják. Ez a cikk a lemeztitkosítással kapcsolatos problémák elhárításában nyújt segítséget.

Az alábbi lépések bármelyikének megtétele előtt először győződjön meg arról, hogy a titkosítani kívánt virtuális gépek a [támogatott virtuálisgép-méretek és operációs rendszerek](disk-encryption-overview.md#supported-vms-and-operating-systems)közé tartoznak, és hogy minden előfeltételnek megfelelt:

- [A virtuális gépekre vonatkozó további követelmények](disk-encryption-overview.md#supported-vms-and-operating-systems)
- [Hálózati követelmények](disk-encryption-overview.md#networking-requirements)
- [Titkosítási kulcs tárolási követelményei](disk-encryption-overview.md#encryption-key-storage-requirements)

 

## <a name="troubleshooting-linux-os-disk-encryption"></a>Linux os lemeztitkosítás – hibaelhárítás

A Linux operációs rendszer (OS) lemeztitkosításának le kell választania az operációs rendszer meghajtóját, mielőtt végigfutna a teljes lemeztitkosítási folyamaton. Ha nem tudja leválasztani a meghajtót, egy hibaüzenet a "nem sikerült leválasztani után ..." valószínűleg bekövetkezik.

Ez a hiba akkor fordulhat elő, ha az operációs rendszer lemeztitkosítását megkísérli egy virtuális gép olyan környezetben, amely megváltozott a támogatott készletgyűjtemény-lemezképről. A támogatott lemezképtől való eltérések zavarhatják a bővítmény azon képességét, hogy leszeretné választani az operációs rendszer meghajtóját. Az eltérések közé tartozhatnak például a következő elemek:
- A testreszabott képek már nem egyeznek meg a támogatott fájlrendszerrel vagy particionálási sémával.
- A nagy alkalmazások, például az SAP, a MongoDB, az Apache Cassandra és a Docker nem támogatottak, ha titkosítás előtt telepítve vannak és futnak az operációs rendszerben. Az Azure Disk Encryption nem tudja biztonságosan leállítani ezeket a folyamatokat, ahogy az az operációsrendszer-meghajtó lemeztitkosításhoz szükséges előkészítése. Ha még mindig vannak aktív folyamatok, amelyek az operációs rendszer meghajtójára nyitott fájlleírókat tartanak, az operációs rendszer meghajtója nem bontható le, ami az operációs rendszer meghajtójának titkosításának sikertelenvé vált. 
- Egyéni parancsfájlok, amelyek szoros anamúgy futnak, hogy a titkosítás engedélyezve van, vagy ha bármilyen más változás történik a virtuális gép en a titkosítási folyamat során. Ez az ütközés akkor fordulhat elő, ha egy Azure Resource Manager-sablon több bővítményegyidejű végrehajtását határozza meg, vagy ha egy egyéni parancsfájl-bővítmény vagy más művelet egyidejűleg fut lemeztitkosításra. Az ilyen lépések szerializálása és elkülönítése megoldhatja a problémát.
- A Security Enhanced Linux (SELinux) nem lett letiltva a titkosítás engedélyezése előtt, így a leválasztási lépés sikertelen. Az SELinux a titkosítás befejezése után újra engedélyezhető.
- Az operációs rendszer lemeze logikai kötetkezelő (LVM) sémát használ. Bár az LVM-adatlemez-támogatás korlátozott, az LVM operációs rendszer lemeze nem.
- A minimális memóriakövetelmények nem teljesülnek (az operációs rendszer lemeztitkosításához 7 GB javasolt).
- Az adatmeghajtókre rekurzív módon van csatlakoztatva az /mnt/ könyvtár ba vagy egymáshoz (például /mnt/data1, /mnt/data2, /data3 + /data3/data4).

## <a name="update-the-default-kernel-for-ubuntu-1404-lts"></a>Az Ubuntu 14.04 LTS alapértelmezett rendszermagjának frissítése

Az Ubuntu 14.04 LTS image a 4.4 alapértelmezett kernel verziójával szállít. Ez a kernel verzió egy ismert probléma, amelyben Out of Memory Killer helytelenül leállítja a dd parancsot az operációs rendszer titkosítási folyamat során. Ezt a hibát a legutóbbi Azure-hangolt Linux kernel javította. A hiba elkerülése érdekében a rendszerkép titkosításának engedélyezése előtt frissítsen az [Azure 4.15-ös](https://packages.ubuntu.com/trusty/linux-azure) vagy újabb kernelére a következő parancsokkal:

```
sudo apt-get update
sudo apt-get install linux-azure
sudo reboot
```

Miután a virtuális gép újraindult az új kernelbe, az új kernelverzió a következő használatával megerősíthető:

```
uname -a
```

## <a name="update-the-azure-virtual-machine-agent-and-extension-versions"></a>Az Azure virtuálisgép-ügynök és a bővítmény verzióinak frissítése

Az Azure Disk Encryption műveletek sikertelenek lehetnek a virtuálisgép-lemezképek en az Azure virtuálisgép-ügynök nem támogatott verzióival. A 2.2.38-nál korábbi ügynökverzióval rendelkező Linux-lemezképeket a titkosítás engedélyezése előtt frissíteni kell. További információ: [Az Azure Linux-ügynök frissítése virtuális gépen](../extensions/update-linux-agent.md) és [minimális verziótámogatás virtuálisgép-ügynökök az Azure-ban.](https://support.microsoft.com/en-us/help/4049215/extensions-and-virtual-machine-agent-minimum-version-support)

A Microsoft.Azure.Security.AzureDiskEncryption vagy a Microsoft.Azure.Security.AzureDiskEncryptionForLinux vendégügynök-bővítmény megfelelő verziója is szükséges. A bővítményverziókat a platform automatikusan karbantartja és frissíti, ha az Azure Virtual Machine ügynök előfeltételei teljesülnek, és a virtuálisgép-ügynök támogatott verzióját használja.

A Microsoft.OSTCExtensions.AzureDiskEncryptionForLinux bővítmény elavult, és már nem támogatott.  

## <a name="unable-to-encrypt-linux-disks"></a>Nem lehet titkosítani a Linux-lemezeket

Bizonyos esetekben úgy tűnik, hogy a Linux lemeztitkosítás beragadt az "OS lemeztitkosítás indításakor", és az SSH le van tiltva. A titkosítási folyamat 3-16 órát is igénybe vehet egy stock gallery kép. Több terabájtos adatlemezek hozzáadása esetén a folyamat napokig is eltarthat.

A Linux operációs rendszer lemeztitkosítási szekvencia ideiglenesen leoldja az operációsrendszer-meghajtót. Ezután végrehajtja a teljes operációsrendszer-lemez blokkról blokkra titkosítását, mielőtt titkosított állapotába újracsatlakoztatná. A Linux disk encryption nem teszi lehetővé a virtuális gép egyidejű használatát, amíg a titkosítás folyamatban van. A virtuális gép teljesítményjellemzői jelentős különbséget tehetnek a titkosítás befejezéséhez szükséges idő között. Ezek a jellemzők magukban foglalják a lemez méretét, és azt, hogy a tárfiók szabványos vagy prémium (SSD) tároló.These characteristics include the size of the disk and whether the storage account is standard or premium (SSD) storage.

A titkosítási állapot ellenőrzéséhez jelölje ki a [Get-AzVmDiskEncryptionStatus](/powershell/module/az.compute/get-azvmdiskencryptionstatus) parancsból visszaadott **ProgressMessage** mezőt. Az operációs rendszer meghajtója titkosítás alatt van, a virtuális gép karbantartási állapotba lép, és letiltja az SSH-t a folyamatban lévő folyamat megszakításának megakadályozása érdekében. A **EncryptionInProgress** üzenet az idő nagy részében jelentést tesz, amíg a titkosítás folyamatban van. Néhány órával később egy **VMRestartPending** üzenet kéri, hogy indítsa újra a virtuális gép. Példa:


```azurepowershell
PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : EncryptionInProgress
DataVolumesEncrypted       : EncryptionInProgress
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk encryption started

PS > Get-AzVMDiskEncryptionStatus -ResourceGroupName "MyVirtualMachineResourceGroup" -VMName "VirtualMachineName"
OsVolumeEncrypted          : VMRestartPending
DataVolumesEncrypted       : Encrypted
OsVolumeEncryptionSettings : Microsoft.Azure.Management.Compute.Models.DiskEncryptionSettings
ProgressMessage            : OS disk successfully encrypted, please reboot the VM
```

Miután a rendszer kéri, hogy indítsa újra a virtuális gépet, és a virtuális gép újraindítása után, meg kell várnia 2-3 percet az újraindítás, és a végső lépéseket kell végrehajtani a célon. Az állapotüzenet megváltozik, amikor a titkosítás végre befejeződik. Miután ez az üzenet elérhetővé válik, a titkosított operációsrendszer-meghajtó várhatóan készen áll a használatra, és a virtuális gép készen áll az újra használatra.

A következő esetekben azt javasoljuk, hogy állítsa vissza a virtuális gép vissza a pillanatkép vagy biztonsági mentés közvetlenül a titkosítás előtt:
   - Ha a korábban leírt újraindítási folyamat nem történik meg.
   - Ha a rendszerindítási információk, a folyamatindító üzenet vagy más hibajelzők arról számolnak be, hogy az operációs rendszer titkosítása nem sikerült a folyamat közepén. Egy üzenet például a "nem sikerült leválasztani" hiba, amely az útmutatóban ismertet.

A következő kísérlet előtt értékelje újra a virtuális gép jellemzőit, és győződjön meg arról, hogy az összes előfeltétel teljesül.

## <a name="troubleshooting-azure-disk-encryption-behind-a-firewall"></a>Az Azure lemeztitkosítás tűzfal mögötti hibaelhárítása

Lásd: [Lemeztitkosítás elszigetelt hálózaton](disk-encryption-isolated-network.md)

## <a name="troubleshooting-encryption-status"></a>Titkosítási állapot elhárítása 

Előfordulhat, hogy a portál egy lemezt titkosítottként jelenít meg, még akkor is, ha a virtuális gépen belül titkosítatlan.  Ez akkor fordulhat elő, ha alacsony szintű parancsokat használnak a lemez közvetlen titkosításának feloldására a virtuális gépen belül, a magasabb szintű Azure Lemeztitkosítás-kezelési parancsok használata helyett.  A magasabb szintű parancsok nem csak a virtuális gépen belüli lemez titkosításának feloldása, hanem a virtuális gépen kívül is frissítik a fontos platformszintű titkosítási beállításokat és a virtuális géphez társított bővítménybeállításokat.  Ha ezek nem tartják az összehangolás, a platform nem lesz képes jelenteni a titkosítási állapot, vagy a virtuális gép kiépítése megfelelően.   

Az Azure Disk Encryption letiltásához a PowerShell segítségével használja [az Disable-AzVMDiskEncryption](/powershell/module/az.compute/disable-azvmdiskencryption) parancsot, majd az [Remove-AzVMDiskEncryptionExtension parancsot.](/powershell/module/az.compute/remove-azvmdiskencryptionextension) Az Remove-AzVMDiskEncryptionExtension futtatása a titkosítás letiltása előtt sikertelen lesz.

Az Azure Disk Encryption cli-vel történő letiltásához használja [az az vm titkosításletiltást.](/cli/azure/vm/encryption) 

## <a name="next-steps"></a>További lépések

Ebben a dokumentumban többet megtudhat az Azure disk encryption néhány gyakori problémájáról, és arról, hogyan háríthatja el ezeket a problémákat. A szolgáltatásról és annak képességeiről az alábbi cikkekben talál további információt:

- [Lemeztitkosítás alkalmazása az Azure Security Centerben](../../security-center/security-center-apply-disk-encryption.md)
- [Azure-adattitkosítás inaktív helyen](../../security/fundamentals/encryption-atrest.md)
